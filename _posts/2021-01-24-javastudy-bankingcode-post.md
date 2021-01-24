---
title: "JAVA 스터디: 은행 입출금 프로그램"
excerpt: "지금까지 배운 내용을 이용해 은행 입출금 프로그램을 구현"

categories:
    - webprogramming
tags:
    - JAVA
    - 스터디
    - 프로젝트
date: 2021-01-25 01:04:00 +0900
---
1월 22일 금요일까지 Java-DB를 연결하여 Java에서 코딩을 통해 DB에 있는 데이터를 다루는 것을 배웠다. 이번 주말에는 이를 응용해 은행 입출금 프로그램을 구현해보았다.  
구현 조건은 다음과 같다.  
* 은행 입출금 프로그램
  * 1번 메뉴: 잔액 조회
  * 2번 메뉴: 입금
  * 3번 메뉴: 출금
  * 4번 메뉴: 최근 거래 10개 출력
  * Eclipse Java project로 개발
  * 1, 2, 3, 4를 제외한 값이 들어오면 프로그램 종료
  * 로그인 기능 구현
  * jdbc로 oracle 연결

위 조건에서 제일 까다로운 부분은 최근 거래 출력이다. 1 ~ 3번 메뉴만 있으면 단순 연산을 통해 개인 계좌에 정보를 저장하면 되지만 4번 메뉴가 추가되면 거래 내역을 저장하는 테이블이 필요해진다. 따라서 코딩을할 때 해당부분을 계속 고려하면서 진행해야했다. 자세한 내용은 코드를 분석하면서 서술하도록 한다.

### 1. bankingDAO.java (프로그램 전체 구조 잡기)
```java
package com.example.interfaces;

import java.util.List;

import com.example.classes.Account;
import com.example.classes.History;

public interface bankingDAO {
	public Account balanceInquiry(Account account); // 잔액 조회
	
	public History deposit(Account account, long deposit); // 입금
	
	public History withdraw(Account account, long withdraw); // 출금
	
	public List<History> accountInquiry(); // 거래내역 조회
	
	public Account signIn (Account account); // 로그인 기능
}
```
제일 먼저 프로그램 전체의 구조를 잡기 위해 bankingDAO.java 클래스를 작성했다. 해당 메소드들의 시그니쳐는 다음과 같은 사고를 통해 작성했다.
1. 잔액 조회를 위해서는 어느 계좌인지 알아야 하므로 계좌의 정보를 가져오기 위해 이를 저장하는 클래스의 변수(Account account)를 매개변수로서 받아와야한다. 그리고 조회 이후 해당 계좌의 정보와 잔액을 모두 반환하기 위해 반환형도 계좌 정보를 저장하는 클래스로 잡았다.
2. 입금은 넣고자 하는 계좌의 정보 뿐만 아니라 입금액도 알아야 하므로 account와 deposit이라는 변수를 받았다. 반환형은 단순히 int와 같은 정수형 변수를 반환해도 되지만 어차피 거래내역을 저장해야하고 실제 은행에서는 입출금이 발생했을 때 계좌 정보, 거래 내역, 거래 일자 등 많은 정보를 표시하므로 반환형을 거래내역을 저장하는 클래스 History로 잡았다.
3. 출금은 입금과 거의 동일하지만 변수명만 다르다.
4. 거래내역 조회는 최근 10개의 리스트를 출력해야하므로 List<Histroy>로 잡았다. 해당 메소드에 매개변수가 없는 이유는 현재 하나의 계좌정보만 저장되어 있으므로 받지 않았다. 만약, 여러개의 계좌가 생기고 각 계좌에 해당하는 거래 내역 테이블이 생성되면 계좌 정보를 받아올 수 있도록 수정해야할 것이다.
5. 로그인 기능은 입력한 계좌의 정보를 받아야 하므로 매개변수로 account를 설정했고 반환형은 정상적으로 로그인 되었을 때 계좌 정보를 받아와 반환할 수 있도록 Account로 설정했다. 

### 2. Account.java (계좌 정보를 저장하는 클래스)
```java
public class Account {	
	private long acctNum = 0; // 계좌번호
	private long acctPw = 0; // 계좌 비밀번호
	private String acctHolder = null; // 예금주
	private long acctBalance = 0; // 잔액
	private String acctDate = null; // 계좌개설일
    // 생성자, getter, setter, toString 생략
}
```
계좌 정보를 저장하는 클래스 Account이다. 변수들은 실제 은행 계좌에 있을 법한 정보들을 작성해보았다. 원래 비밀번호 변수는 없었지만 로그인 기능을 구현하기 위해 넣었다.

### 3. History.java (거래 내역을 저장하는 클래스)
```java
public class History {
	private long histNum = 0; // 거래번호
	private long histDps = 0; // 입금액
	private long histWdraw = 0; // 출금액
	private String histDate = null; // 거래일자
	private Account account = null; // 계좌 정보와 연동
    // 생성자, getter, setter, toString 생략
}
```
거래 내역을 저장하는 클래스 History이다. 입출금 각각에 대해 내역을 저장해야하므로 거래번호와 거래일자를 넣었고 여러 계좌가 생겼을 때 해당 계좌와 거래 내역을 JOIN해야할 경우를 대비해 Account 변수도 작성했다.

### 4. bankingDAOImpl.java (실제 기능을 구현한 클래스)
```java
package com.example.classes;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;
import java.util.List;

import com.example.db.DBConn;
import com.example.interfaces.bankingDAO;

public class bankingDAOImpl implements bankingDAO{
	private Connection conn = DBConn.getConnection();
	
	@Override
	public Account balanceInquiry(Account account) { // 잔액 조회
		try {
                        // PACCOUNT: 계좌 정보를 저장하는 테이블
                        // ACCT_NUM: 계좌 번호
                        // ACCT_BAL: 현재 잔액
			String sql = "SELECT ACCT_NUM, ACCT_BAL FROM PACCOUNT WHERE ACCT_NUM = ?";
			PreparedStatement ps = conn.prepareStatement(sql);
			ps.setLong(1, account.getAcctNum());
			
			ResultSet rs = ps.executeQuery();

                        // 하나의 계좌 정보만 가져오면 되므로 if 사용
                        // 계좌 번호와 현재 잔액을 갱신 받은 후 return
			if(rs.next()) {
				Account tmp = new Account();
				tmp.setAcctNum(rs.getLong("ACCT_NUM"));				
				tmp.setAcctBalance(rs.getLong("ACCT_BAL"));
				
				return tmp;
			}			
			return null;	
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}

	@Override
	public History deposit(Account account, long deposit) { // 입금
		try {
			// 1. 현재 잔액을 받아옴
			String sql = "SELECT ACCT_BAL FROM PACCOUNT WHERE ACCT_NUM = ?";
			PreparedStatement ps = conn.prepareStatement(sql);
			ps.setLong(1, account.getAcctNum());
			
			ResultSet rs = ps.executeQuery();
			
                        // 현재 잔액을 account 변수에 갱신
			if(rs.next()) {
				account.setAcctBalance(rs.getLong("ACCT_BAL"));
			}
                        // 정상적으로 정보를 받았는지 확인차 출력
			System.out.println("현재 잔액: "+account.getAcctBalance());
			System.out.println("입금할 금액: "+deposit);
			
			// 2. History 테이블에 입금액과 계산된 잔액을 넣음
			String sql1 = "INSERT INTO HISTORY(HIST_NUM, HIST_DPS, HIST_WDRAW, HIST_DATE, ACCT_NUM, HIST_BAL) "+
						 "VALUES(SEQ_HISTORY_HIST_NO.NEXTVAL, ?, NULL, CURRENT_DATE, ?, ?)";
			ps = conn.prepareStatement(sql1);
			ps.setLong(1, deposit);
			ps.setLong(2, account.getAcctNum());
                        // 갱신된 잔액에 main으로부터 받아온 입금액을 더한 뒤 입력
			ps.setLong(3, (account.getAcctBalance()+deposit));
			int result = ps.executeUpdate();
			// ******commit; 매우 중요******
			conn.commit();
			
			if(result > 0) {
				System.out.println("입금 성공");
				// 잔액을 paccount에 업데이트
				String sql2 = "UPDATE PACCOUNT SET ACCT_BAL = ? WHERE ACCT_NUM = ?";
				ps = conn.prepareStatement(sql2);
				ps.setLong(1, (account.getAcctBalance()+deposit));
				ps.setLong(2, account.getAcctNum());
				ps.executeUpdate();				
				conn.commit();
				
				// ROW_NUMBER()를 이용해 history에서 가장 최근 갱신된 입금 금액과 잔액을 가져옴
				String sql3 = "SELECT * FROM "+
						      "(SELECT HIST_DPS, HIST_BAL, HIST_DATE, ROW_NUMBER() OVER (ORDER BY HIST_DATE DESC) HIST_ROWN FROM HISTORY) "+
						      "WHERE HIST_ROWN = 1";
				ps = conn.prepareStatement(sql3);
				
				rs = ps.executeQuery();

				History histTmp = new History();
				Account acctTmp = new Account();
                                // main으로 반환할 임시 변수에 계좌번호, 입금액, 잔액을 갱신해줌
				acctTmp.setAcctNum(account.getAcctNum());
				if(rs.next()) {
					histTmp.setHistDps(rs.getLong("HIST_DPS"));
					acctTmp.setAcctBalance(rs.getLong("HIST_BAL"));
					histTmp.setAccount(acctTmp);					
				}
				return histTmp;
			}else {
				System.out.println("입금 실패");
				return null;
			}			
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}

	@Override
	public History withdraw(Account account, long withdraw) { // 출금
		try {
			// 1. 현재 잔액을 가져옴
			String sql = "SELECT ACCT_BAL FROM PACCOUNT WHERE ACCT_NUM = ?";
			PreparedStatement ps = conn.prepareStatement(sql);
			ps.setLong(1, account.getAcctNum());

			ResultSet rs = ps.executeQuery();
                        // 현재 잔액을 account 변수에 갱신
			if(rs.next()) {
				account.setAcctBalance(rs.getLong("ACCT_BAL"));
			}

			System.out.println("현재 잔액: "+account.getAcctBalance());
			System.out.println("출금할 금액: "+withdraw);

			// 2. history 테이블에 출금액과 계산된 잔액을 넣음
			String sql1 = "INSERT INTO HISTORY(HIST_NUM, HIST_DPS, HIST_WDRAW, HIST_DATE, ACCT_NUM, HIST_BAL) "+
					"VALUES(SEQ_HISTORY_HIST_NO.NEXTVAL, NULL, ?, CURRENT_DATE, ?, ?)";
			ps = conn.prepareStatement(sql1);
			ps.setLong(1, withdraw);
			ps.setLong(2, account.getAcctNum());
                        // 갱신된 잔액에 main으로부터 받아온 출금액을 더한 뒤 입력
			ps.setLong(3, (account.getAcctBalance()-withdraw));
			int result = ps.executeUpdate();
			// commit 까먹지 말 것
			conn.commit();

			if(result > 0) {
				System.out.println("출금 성공");
				// 잔액을 paccount에 업데이트
				String sql2 = "UPDATE PACCOUNT SET ACCT_BAL = ? WHERE ACCT_NUM = ?";
				ps = conn.prepareStatement(sql2);
				ps.setLong(1, (account.getAcctBalance()-withdraw));
				ps.setLong(2, account.getAcctNum());
				ps.executeUpdate();				
				conn.commit();

				// history에서 갱신된 출금 금액과 잔액을 가져옴
				String sql3 = "SELECT * FROM "+
				"(SELECT HIST_WDRAW, HIST_BAL, HIST_DATE, ROW_NUMBER() OVER (ORDER BY HIST_DATE DESC) HIST_ROWN FROM HISTORY) "+
				"WHERE HIST_ROWN = 1";
				ps = conn.prepareStatement(sql3);

				rs = ps.executeQuery();

				History histTmp = new History();
				Account acctTmp = new Account();
				acctTmp.setAcctNum(account.getAcctNum());
				if(rs.next()) {
					histTmp.setHistWdraw(rs.getLong("HIST_WDRAW"));
					acctTmp.setAcctBalance(rs.getLong("HIST_BAL"));
					histTmp.setAccount(acctTmp);					
				}
				return histTmp;
			}else {
				System.out.println("입금 실패");
				return null;
			}
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}

	@Override
	public List<History> accountInquiry() { // 거래내역 조회
		try {
                        // 최근 10개 거래 내역을 가져오기 위해 ROW_NUMBER 사용
			String sql = "SELECT * FROM "+
			"(SELECT HIST_NUM, HIST_DPS, HIST_WDRAW, HIST_BAL, HIST_DATE, ROW_NUMBER() OVER (ORDER BY HIST_DATE DESC) HIST_ROWN FROM HISTORY) "+
			"WHERE HIST_ROWN BETWEEN 1 AND 10";
			PreparedStatement ps = conn.prepareStatement(sql);
			
			ResultSet rs = ps.executeQuery();
			
			List<History> histList = new ArrayList<History>();

                        // 받아온 거래 내역을 임시 변수 histList에 갱신 및 반환
			while(rs.next()) {
				History histTmp = new History();
				Account acctTmp = new Account();
				histTmp.setHistNum(rs.getLong("HIST_NUM"));
				histTmp.setHistDps(rs.getLong("HIST_DPS"));
				histTmp.setHistWdraw(rs.getLong("HIST_WDRAW"));
				acctTmp.setAcctBalance(rs.getLong("HIST_BAL"));
				histTmp.setAccount(acctTmp);
				histTmp.setHistDate(rs.getString("HIST_DATE"));
				
				histList.add(histTmp);
			}
				
			return histList;
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}

	}

	@Override
	public Account signIn(Account account) { // 로그인 기능
		try {
                        // select를 이용해 받아온 계좌 정보의 계좌번호, 비밀번호가 동시에 일치하는지 체크
			String sql = "SELECT * FROM PACCOUNT WHERE ACCT_NUM = ? AND ACCT_PW = ?";
			PreparedStatement ps = conn.prepareStatement(sql);
			
			ps.setLong(1, account.getAcctNum());
			ps.setLong(2, account.getAcctPw());
			
			ResultSet rs = ps.executeQuery();
			// 받아온 계좌 정보를 account 변수에 갱신
			if(rs.next()) {
				account.setAcctNum(rs.getLong("ACCT_NUM"));
				account.setAcctHolder(rs.getString("ACCT_HDER"));
				account.setAcctBalance(rs.getLong("ACCT_BAL"));
				account.setAcctDate(rs.getString("ACCT_DATE"));
				account.setAcctPw(rs.getLong("ACCT_PW"));
			}		
                        // 어떤 값을 가져오는지 확인하기 위해 임시로 작성
			//System.out.println("ACCT_NUM: "+account.getAcctNum());
			//System.out.println("ACCT_HDER: "+account.getAcctHolder());
			//System.out.println("ACCT_BAL: "+account.getAcctBalance());
			//System.out.println("ACCT_DATE: "+account.getAcctDate());
			//System.out.println("ACCT_PW: "+account.getAcctPw());
			return account;
		} catch (Exception e) {
			System.out.println("잘못된 계좌번호 혹은 비밀번호 입니다. 다시 입력해주십시오.");
			return null;
		}
	}
}
```
bankingDAO에서 설계한 기능들을 실제로 구현하는 클래스이다. implements로 상속 받았으므로 bankingDAO의 메소드를 전부 구현해야한다. 프로그램을 통해 은행에서 볼 수 있는 것처럼 다양한 정보를 출력하고 싶어서 하나의 기능에서 여러 개의 sql을 구현해 코드가 복잡해졌다. 이 과정에서 알게된 것이 있는데 바로 commit의 중요성이다. sqldeveloper를 통해 데이터를 update, delete하면 commit 없이 계속할 수 있지만 java를 통해 코드로 update, delete를 진행하면 어느 시점에서 무한로딩이 걸리는 현상이 발생했다. 찾아보니 작업이 밀려서 그렇다고 하는데 실제로 조작한 해당 테이블에 가서 정보를 수정하려고 하면 작업이 밀린 것처럼 표시되면서 무한로딩에 빠진다. 이를 해결하기 위해선 컴퓨터를 껐다 켜야된다. 항상 잊지말고 쿼리를 실행하면 commit을 하도록 하자.

### 5. DBConn.java (DB와 연결을 위한 클래스)
```java
package com.example.db;

import java.sql.Connection;
import java.sql.DriverManager;

public class DBConn {
	private static Connection connection = null;
	
	public static Connection getConnection() {
		try {
			// 1. 드라이브 로드(ClassNotFoundException)
			Class.forName("oracle.jdbc.driver.OracleDriver");
			
			// 2. DB 접속(SQLException) => 서버주소:포트번호:SID, 사용자아이디, 사용자암호
			connection = 
			DriverManager.getConnection("jdbc:oracle:thin:@1.234.5.158:11521:xe", "id18", "pw18");
			
			// 3. autocommit 해제
			connection.setAutoCommit(false);
			
			System.out.println("DB 접속 성공");
			
			return connection;
		} catch (Exception e) { // 1, 2, 3번 수행 시 오류가 발생하면 호출
			System.out.println("DB 접속 실패");
			return null;
		}
	}
}
```
수업시간에 배운 db 연결 코드를 그대로 사용했다. 원래는 mariadb로 연결하려고 했는데 오라클과 문법이 달라서 그런지 테이블 만드는데에 계속 오류가 터져서 열받아서 oracle로 바꿨다. 추후 mariadb로 바꾸면 위 클래스 뿐만아니라 bankingDAOImpl의 sql문도 수정해야할 것이다.

### 6. Main.java
```java
package com.example;

import java.sql.Connection;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

import com.example.classes.Account;
import com.example.classes.History;
import com.example.classes.bankingDAOImpl;
import com.example.db.DBConn;

public class Main {
	public static void main(String[] args) {
		try {			
			// Connection conn = DBConn.getConnection();
			Scanner input = new Scanner(System.in);
			bankingDAOImpl bankingDAO = new bankingDAOImpl();
			
			
			while(true) {
				
				System.out.println("[입출금 및 거래내역 확인 프로그램]");
                                // 로그인
				System.out.println("===============================");
				System.out.println("[로그인]");				
				System.out.println("계좌번호를 입력하십시오.");
				long inputAcctNum = input.nextLong();
				System.out.println("계좌 비밀번호를 입력하십시오.");
				long inputAcctPw = input.nextLong();
                                // 입력 받은 계좌 번호와 비밀번호를 이용해 계좌 체크 변수 생성
				Account chkAcct = new Account(inputAcctNum, inputAcctPw);
                                // 로그인 기능 실행 
				Account chkresult = bankingDAO.signIn(chkAcct);
				System.out.println("===============================");
                                // 예금주 정보가 존재하면 로그인 성공 및 입출금 메뉴로 진행
				if(chkresult.getAcctHolder() != null) {
					System.out.println("로그인 성공");
					System.out.println("로그인한 계좌번호: "+chkresult.getAcctNum());
					System.out.println("로그인한 예금주: "+chkresult.getAcctHolder());
					System.out.println("===============================");
					
					while(chkresult.getAcctHolder() != null) {
                                                // 메뉴 선택
						System.out.println("메뉴를 선택하십시오.");
						System.out.println("1. 잔액조회, 2. 입금, 3. 출금, 4. 거래내역; 이외 번호는 종료");
						System.out.println("번호 입력 후 엔터");
						System.out.println("===============================");
						int menuSelect = input.nextInt();

						if(menuSelect == 1) {
							System.out.println("[잔액조회]");
							//System.out.println("계좌번호를 입력하십시오.");
							//long inputData = input.nextLong();

							Account account = new Account();
							account.setAcctNum(chkresult.getAcctNum());

							Account result = bankingDAO.balanceInquiry(account);

							System.out.println("계좌번호: "+result.getAcctNum());
							System.out.println("잔액: "+result.getAcctBalance());	
							System.out.println("===============================");

						}else if(menuSelect == 2) {
							System.out.println("[입금]");
							//System.out.println("계좌번호를 입력하십시오.");
							//long inputData = input.nextLong();

							Account account = new Account();
							account.setAcctNum(chkresult.getAcctNum());

							System.out.println("입금할 금액을 입력하십시오.");
							long inputData = input.nextLong();

							long deposit = inputData;

							History result = bankingDAO.deposit(account, deposit);

							System.out.println(result.getAccount().getAcctNum()+" 구좌 입금액: "+
									result.getHistDps()+", 잔액: "+result.getAccount().getAcctBalance());					
							System.out.println("===============================");
						}else if(menuSelect == 3) {
							System.out.println("[출금]");
							//System.out.println("계좌번호를 입력하십시오.");
							//long inputData = input.nextLong();

							Account account = new Account();
							account.setAcctNum(chkresult.getAcctNum());

							System.out.println("출금할 금액을 입력하십시오.");
							long inputData = input.nextLong();

							long withdraw = inputData;

							History result = bankingDAO.withdraw(account, withdraw);

							System.out.println(result.getAccount().getAcctNum()+" 구좌 출금액: "+
									result.getHistWdraw()+", 잔액: "+result.getAccount().getAcctBalance());	
							System.out.println("===============================");
						}else if(menuSelect == 4) {
							System.out.println("[거래내역]");
							System.out.println("최근 10개의 거래 내역을 출력합니다.");

							List<History> histList = new ArrayList<History>();

							histList = bankingDAO.accountInquiry();

							for (History tmp : histList) {
								System.out.print("거래번호: "+tmp.getHistNum());
								System.out.print(", 입금액: "+tmp.getHistDps());
								System.out.print(", 출금액: "+tmp.getHistWdraw());
								System.out.print(", 잔액: "+tmp.getAccount().getAcctBalance());
								System.out.print(", 거래일: "+tmp.getHistDate());
								System.out.println();
							}					
							System.out.println("===============================");
						}else {
							System.out.println("메뉴 이외의 번호가 입력되었습니다. 프로그램을 종료합니다.");
							System.out.println("프로그램 종료");
							System.exit(0);
						}				
					}
				}else {
					System.out.println("잘못된 계좌번호 혹은 비밀번호 입니다.");
					System.out.println("이전 메뉴로 돌아가려면 1, 프로그램을 종료하려면 0을 입력하십시오.");
					int inputdata = input.nextInt();
					if(inputdata == 1) {
						continue;
					}else {
						break;
					}
				}
			}			
		} catch (Exception e) {
			System.out.println("에러 발생");
			e.printStackTrace();
		} finally {
			System.out.println("프로그램 종료");
		}
	}
}
```
구현한 기능들을 적용하는 main 클래스이다. 처음에 입출금 기능을 구현하고 마지막에 로그인 기능을 만들어서 중간에 계좌번호를 입력하라는 부분이 주석처리 되어있다. main에서 확인할 점은 로그인 성공 여부를 예금주 값이 null인지 아닌지로 체크한다는 것이다. 이는 bankingDAOImpl 클래스의 signIn 메소드에서 입력한 계좌 정보로 select를 진행한 뒤 받아온 정보를 account 변수에 갱신 받을 때 이미 account에 값이 있으면 select로 들어온 값이 null일 때(계좌 번호나 비밀번호가 틀려서 계좌 정보 조회를 실패했을 때) null로 갱신하지 못하기 때문이다. 이 부분을 개선하기 위해선 매개변수로 들어온 account에 값을 재갱신하는 것이 아니라 임시변수 tmp를 만들어서 거기에 정보를 갱신한 뒤 반환하면 해결할 수 있다.  

이번 주 토, 일요일 동안 입출금 및 거래 내역 조회라는 간단한 프로그램을 구현하는 프로젝트를 진행해보았다. 이를 통해 실제 배운 내용을 얼마나 사용할 수 있는지 체크할 수 있었고 commit의 중요성, rs.get 메소드들의 데이터 갱신 방식 등 수업 시간에는 알 수 없었던 부분도 짚고 넘어갈 수 있었다. 아직 위 코드도 개선할 점은 많다. 조금만 손보면 더 완성도를 올릴 수 있을 부분도 여럿 보인다. 추후 mariadb를 연습할 때 더 개선해보도록 하자.   

다음 주부터 조별 프로젝트를 진행한다는데 주말동안 진행한 프로젝트를 통해 더 양질의 프로그램을 만들 수 있을거라 생각된다. 솔직히 commit 때문에 몇시간 날리고 한밤중에 샷건도 겁나치고 그랬는데 그래도 어느정도 완성하고나니 첫 프로그램을 자력으로 도움없이 만들었다는 점에 열받았던건 다 날라가고 뿌듯한 기분만 남는다. 꿀잠자고 내일부터 또 열심히 배우자. 

>What time is it?
>Game time! WOO! 