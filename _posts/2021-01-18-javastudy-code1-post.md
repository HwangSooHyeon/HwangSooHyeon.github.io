---
title: "JAVA 스터디: DB-Java 연결 - 1"
excerpt: "JFrame으로 DB 데이터 출력"

categories:
    - webprogramming
tags:
    - JAVA
    - 스터디
date: 2021-01-19 01:15:00 +0900
---
Java의 JFrame를 이용해 DB의 데이터를 출력하는 코드를 작성한다.  

먼저 자바 프로젝트를 생성하는 부분부터 짚고 가도록 하자.

### 0. 자바 프로젝트 생성

* pom.xml 생성: 자바 프로젝트 우클릭 `=>` Configure `=>` Convert to maven project `=>` finish
* pom.xml 설정: pom.xml 클릭 -> `</build>`와 `</project>` 사이에 `<repositories>`와 `<dependencies>`를 삽입(사용하고자 하는 툴)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>java02</groupId>
  <artifactId>java02</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <build>
    <sourceDirectory>src</sourceDirectory>
    <plugins>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
          <release>15</release>
        </configuration>
      </plugin>
    </plugins>
  </build>

  <!-- 여기서부터 추가-->
  <repositories>
		<repository>
			<id>oracle</id>
			<name>ORACLE JDBC Repository</name>
			<url>http://maven.jahia.org/maven2</url>
		</repository>
	</repositories>

	<dependencies>
		<dependency>
			<groupId>com.oracle</groupId>
			<artifactId>ojdbc7</artifactId>
			<version>12.1.0.2</version>
		</dependency>
	</dependencies>  
  <!-- 여기까지 추가-->

</project>
```

* main class 생성: 자바 프로젝트 우클릭 `=>` new - class `=>` Name에 Main 입력 `=>` Which method stubs would you like to create 항목에서 public static void main(String[] args) 체크

main에 아무코드 입력 후 한 번은 Run As로 들어가서 Java application을 클릭 해줘야 가장 최큰 프로젝트로 main이 설정되어 단축 아이콘을 쓸 수 있음

자바 프로젝트 내 패키지 이름은 com.회사이름.역할 방식으로 작성 

### 1. DB 연결 설정
```java
// 파일 이름: DBConn.java
package com.example.db;

import java.sql.Connection;
import java.sql.DriverManager;

public class DBConn {
	// 정적 변수 생성: DB 연결 역할만 하므로 static으로 생성
	private static Connection connection = null;
	
	// 정적 메소드 생성
	public static Connection getConnection() {
		try {
			// 1. 드라이브 로드(ClassNotFoundException)
			Class.forName("oracle.jdbc.driver.OracleDriver");
			
			// 2. DB 접속(SQLException) => 서버주소:포트번호:SID, 사용자아이디, 사용자암호
			connection = DriverManager.getConnection("jdbc:oracle:thin:@1.234.5.158:11521:xe", "id18", "pw18");
			
			// 3. autocommit 설정 해제
			connection.setAutoCommit(false);
									
			return connection;
		} catch (Exception e) { //1, 2, 3번 수행 시 오류가 발생하면 호출
			e.printStackTrace();
			return null;
		}
	}
}
```

### 2. DAO 설정
```java
// 파일 이름: MemberDAO.java
// DAO(Data Access Object; 데이터 접근 객체)
package com.example.member;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;

public class MemberDAO {
    // DB 연결 정보를 받을 변수
	private Connection connection = null;
	
	// 생성자; DB 연결 정보를 받아옴 
	public MemberDAO(Connection connection) {
		super();
		this.connection = connection;
	}
	
	// 회원 가입
	public void insertMember() {};	

	// 회원 수정
	public void updateMember() {};
	
	// 회원 삭제
	public void deleteMember() {};
	
	// 회원 목록: 받아온 member 정보를 ArrayList 형태로 반환
	public ArrayList<String[]> selectMember() {
		try {
			String sql = "SELECT * FROM MEMBERTBL"; // sql문 입력
			PreparedStatement ps = connection.prepareStatement(sql);
			// ps 실행 후 결과 값이 rs에 보관됨
			ResultSet rs = ps.executeQuery();
			
            // 결과 값을 닮을 memberList 정의
			ArrayList<String[]> memberList = new ArrayList<String[]>();
			
			while(rs.next()) { // select로 실행된 결과를 한 줄씩 가져와서 memberList에 저장
				String[] memberStr = new String[7];
				memberStr[0] = rs.getString("USER_ID");
				memberStr[1] = rs.getString("USER_PW");
				memberStr[2] = String.valueOf(rs.getString("USER_AGE")); // 형변환
				memberStr[3] = rs.getString("USER_TEL");
				memberStr[4] = rs.getString("USER_DATE");
				memberStr[5] = rs.getString("USER_ADDR");
				memberStr[6] = rs.getString("USER_NAME");
				
				memberList.add(memberStr);				
			}
			return memberList;
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	};
}
```

### 3. GUI 설정
```java
// 파일 이름: MyFrame.java
package com.example.frame;

import java.awt.HeadlessException;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.sql.Connection;
import java.util.ArrayList;

import javax.swing.JFrame;
import javax.swing.JMenu;
import javax.swing.JMenuBar;
import javax.swing.JMenuItem;
import javax.swing.JOptionPane;
import javax.swing.KeyStroke;

import com.example.db.DBConn;
import com.example.member.MemberDAO;

// MyFrame은 JFrame 클래스와 ActionListener 인터페이스를 상속받음
// ActionListener는 특정 동작을 했을 때 발생하는 이벤트를 정의하는 인터페이스로
// ActionPerformed 메소드를 오버라이딩하여 직접 내부를 작성하여 사용함
public class MyFrame extends JFrame implements ActionListener{

	private static final long serialVersionUID = 1L;
	
	// 글로벌 변수
	JMenuBar menuBar = null;
	JMenu menu1 = null;
	JMenuItem menuItem1 = null;
	JMenuItem menuItem2 = null;
	JMenuItem menuItem3 = null;
	JMenuItem menuItem4 = null;
	
	JMenuItem menuItem5 = null;
	
	Connection conn = null;

	// Source - Generate Constructors from Superclass 클릭해 부모 클래스의 생성자를 호출
	public MyFrame(String title) throws HeadlessException {
		// JFrame의 생성자 중 매개변수가 String title인 것을 호출한다는 의미
		super(title);
		// 실제 메뉴를 만드는 클래스를 호출
		createMenu();
		
		this.setSize(500, 600); // 화면 크기 설정
		this.setVisible(true);  // 화면 표시
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 닫기를 누르면 자바 프로그램 종료
	}
	
	private void createMenu() {
		// JMenuBar -> JMenu -> JMenuItem 순서로 만들어야함
        // JMenuBar: 아무것도 없는 화면에 메뉴 바를 깔아 줌
		menuBar = new JMenuBar();
        // JMenu: 깔려 있는 메뉴바에 상위 메뉴를 추가함
        // 예시) file, edit, window, help, etc.
		menu1 = new JMenu("회원");
		
        // JMenuItem: JMenu로 만든 상위 메뉴의 하위 메뉴들
        // 예시) file 하단의 new file, open file, save as, exit, etc.
		menuItem1 = new JMenuItem("DB 연결");
        // addActionListener: 이벤트가 동작할 수 있게 선언해주는 것
        // 이게 빠지면 메뉴를 눌러도 아무일이 안일어남
		menuItem1.addActionListener(this);
		
		menuItem2 = new JMenuItem("로그인");
		menuItem2.addActionListener(this);
		
		menuItem3 = new JMenuItem("회원가입");
		menuItem3.addActionListener(this);
		
		menuItem5 = new JMenuItem("회원목록");	
		menuItem5.addActionListener(this);
		
		menuItem4 = new JMenuItem("종료");
		// 종료 버튼 단축키 설정 ctrl + x
		menuItem4.setAccelerator(KeyStroke.getKeyStroke(KeyEvent.VK_X, ActionEvent.CTRL_MASK));
		menuItem4.addActionListener(this);
		
		// 아래 과정을 통해 실제 상위 메뉴에 하위 메뉴를 추가함		
		menu1.add(menuItem1); // DB 연결
		menu1.add(menuItem2); // 로그인
		menu1.add(menuItem3); // 회원가입
		menu1.add(menuItem5); // 회원목록
		menu1.addSeparator(); // 구분선
		menu1.add(menuItem4); // 종료
		
        // 메뉴 바에 상위 메뉴를 추가함
		menuBar.add(menu1);
        // 설정이 다 끝난 메뉴바를 설정 완료함
		this.setJMenuBar(menuBar);
	}
	
	@Override
	public void actionPerformed(ActionEvent e) {
		// 메뉴가 클릭되었을 때 수행해야할 소스코드 작성
		
        // menuItem1을 실행했을 때
		if(e.getSource() == menuItem1) { // DB 연결
            // 현재 연결 상태를 확인함
            // 연결이 실패하면 null이 반환되므로 이를 이용해 DB 접속 여부 판단
			conn = DBConn.getConnection();
			if(conn != null) {
				JOptionPane.showMessageDialog(this, "DB 접속 성공", "성공", JOptionPane.INFORMATION_MESSAGE);
			}else {
				JOptionPane.showMessageDialog(this, "DB 접속 실패", "실패", JOptionPane.ERROR_MESSAGE);
			}
;		}else if(e.getSource() == menuItem2) {
			
		}else if(e.getSource() == menuItem3) {
			
		}else if(e.getSource() == menuItem5) { // 회원목록
            // 현재 conn이 글로벌 변수이고 menuItem1을 먼저 클릭해서
            // conn의 연결 정보를 받아 두었으므로 정상적으로 연결되었으면
            // null이 나올 수 없음 이를 이용해 이벤트 작성
			if(conn != null) {
                // memberDAO의 select 결과 값을 list로 받음
				MemberDAO memberDAO = new MemberDAO(conn);
				ArrayList<String[]> list = memberDAO.selectMember();
							
			/*	for(String[] tmp : list) {
					for(int i=0; i<tmp.length; i++) {
						System.out.println(tmp[i]);
					}									
				}*/

                // list 내의 정보를 출력		
				for(String[] tmp : list) {
					System.out.print("ID: "+tmp[0]+", ");
					System.out.print("PW: "+tmp[1]+", ");
					System.out.print("AGE: "+tmp[2]+", ");
					System.out.print("TEL: "+tmp[3]+", ");
					System.out.print("DATE: "+tmp[4]+", ");
					System.out.print("ADDR: "+tmp[5]+", ");
					System.out.print("NAME: "+tmp[6]);
					System.out.println();
				}			
								
			}else {
				JOptionPane.showMessageDialog(this, "DB 접속을 먼저 하세요", "실패", JOptionPane.ERROR_MESSAGE);
			}			
		}else if(e.getSource() == menuItem4) { // 종료
			this.setVisible(false);
			this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
			System.exit(1);
		}		
		// 다른 클래스를 호출하는 부분
		// -> 모든 사용자의 이벤트를 처리하는 부분		
	}
}
```

오늘은 메뉴를 만들고 DB연결 및 SQL문을 작성 후 데이터를 받아오는 것까지 진행하였고, 내일 실제 화면으로 SELECT 결과를 출력하는 코드를 작성할 것이다. 다음 진행 내용은 다음 포스트에 이어서 작성하도록 하겠다.