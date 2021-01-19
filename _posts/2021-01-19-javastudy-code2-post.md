---
title: "JAVA 스터디: DB-Java 연결 - 2"
excerpt: "Thread, "

categories:
    - webprogramming
tags:
    - JAVA
    - 스터디
date: 2021-01-20 02:03:00 +0900
---

### 0. 번외; Thread, Runnable

Thread는 오늘 진행하는 DB-Java 연결에서 사용하진 않지만 개념은 알아두도록 하자. 

##### 0.1. extends Thread
```java
// 파일 이름: MyThread1.java
package com.example.thread;

public class MyThread1 extends Thread{
	private int type = 0;
	
	// 생성자: 정수값을 받아서 type 변수에 넣음
	public MyThread1(int type) {
		this.type = type;
	}

	// Thread를 사용하기 위해 Thread 클래스의 메소드 run()을 오버라이드 해야됨
	// 무한 반복, 1초 간격으로 type의 값을 출력
	@Override
	public void run() {
		super.run();
		
		while(true) {
			System.out.println(type);
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}	
}
```

##### 0.2. implements Runnable
```java
// 파일 이름: MyThread2.java
package com.example.thread;

public class MyThread2 implements Runnable{ 
/* 
Thread를 extends로 상속 받으면 다른 클래스들을 상속 받을 수 없기 때문에
Runnable이란 interface로 상속받을 수 있게 만듦
아래 생성자와 메소드의 기능은 MyThread1과 동일   
*/
	private int type = 0;
	
	public MyThread2(int type) {
		this.type = type;
	}
	
	@Override
	public void run() {		
		while(true) {
			System.out.println(type);
			try {
				Thread.sleep(1000);
			} catch (Exception e) {
				e.printStackTrace();
			}
		}	
	}
}
```

##### 0.3. main
```java
// 파일 이름: Main.java
package com.example;

import com.example.thread.MyThread2;

public class Main {

	public static void main(String[] args) {
		try {
			// Thread를 extends로 상속 받았을 때 정의 방법
			MyThread1 obj1 = new MyThread1(1);
			obj1.start();
			
			MyThread1 obj2 = new MyThread1(2);
			obj2.start();
			
			MyThread1 obj3 = new MyThread1(3);
			obj3.start();

			// Runnable을 implements로 상속	받았을 때 정의 방법
			// thread는 run을 호출하는게 아니라 start를 호출해야 실행 됨
			// main -> start -> run 순으로 진행됨
			Thread obj1 = new Thread(new MyThread2(1));
			obj1.start();
			Thread obj2 = new Thread(new MyThread2(2));
			obj2.start();
			Thread obj3 = new Thread(new MyThread2(3));
			obj3.start();

		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```
 
DB-Jave 연결을 다시 진행하도록 하자. 첫 번째 포스트에서는 회원 목록을 콘솔에 출력하는 것까지 진행했었다. 두 번째 포스트에서는 GUI 버튼에 액션을 적용하여 DB INSERT, UPDATE, DELETE를 적용해본다.

### 1. GUI 설정
```java
// 파일 이름: MyFrame.java
package com.example.frame;

import java.awt.HeadlessException;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.sql.Connection;
import java.util.ArrayList;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JMenu;
import javax.swing.JMenuBar;
import javax.swing.JMenuItem;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JPasswordField;
import javax.swing.JScrollPane;
import javax.swing.JTable;
import javax.swing.JTextField;
import javax.swing.KeyStroke;

import com.example.db.DBConn;
import com.example.member.Member;
import com.example.member.MemberDAO;

public class MyFrame extends JFrame implements ActionListener{

	private static final long serialVersionUID = 1L;

	// final; 상수. 한 번 설정 되면 못 바꿈
	// 대문자로 써두면 구분하기가 좋음
	final String[] COLUMN = {"아이디", "비밀번호", "나이", "전화번호", "가입날짜", "주소", "이름"};
	
	// 글로벌 변수: 여러 메소드에서 사용해야하므로 
	JMenuBar menuBar = null;
	JMenu menu1 = null;
	JMenuItem menuItem1 = null;
	JMenuItem menuItem2 = null;
	JMenuItem menuItem3 = null;
	JMenuItem menuItem4 = null;
	JMenuItem menuItem5 = null;
	
	JPanel panel = new JPanel();
	
	ArrayList<String[]> memberList = null;
	
	Connection conn = null;
	
	JTextField userText1;
	JTextField userText2;
	JTextField userText3;
	JTextField passwordText;
	JButton registerButton;
	JButton updateButton;
	JButton deleteButton;

	// Source - Generate Constructors from Superclass 클릭해 부모 클래스의 생성자를 호출
	public MyFrame(String title) throws HeadlessException {
		// JFrame의 생성자 중 매개변수가 String title인 것을 호출한다는 의미
		super(title);
		// DB 연결 정보를 받아옴
		// DBConn이 Static 이므로 객체 생성 필요 없이 바로 사용 가능
		conn = DBConn.getConnection();
		
		// 메뉴 생성
		createMenu();
		
		this.setSize(500, 600); // 화면 크기 설정
		this.setVisible(true);  // 화면 표시
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 닫기를 누르면 자바 프로그램 종료
	}
	
	private void createMenu() {
		// JMenuBar -> JMenu -> JMenuItem 순서로 만들어야함
		menuBar = new JMenuBar();
		menu1 = new JMenu("회원");
		
		menuItem1 = new JMenuItem("DB 연결");
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
		
		menu1.add(menuItem1); // DB 연결
		menu1.add(menuItem2); // 로그인
		menu1.add(menuItem3); // 회원가입
		menu1.add(menuItem5); // 회원목록
		menu1.addSeparator(); // 구분선
		menu1.add(menuItem4); // 종료
		
		menuBar.add(menu1);
		
		panel.setLayout(null); // 좌표를 이용한 절대 위치 배치
		this.setJMenuBar(menuBar);
		this.add(panel);
	}

	@Override
	public void actionPerformed(ActionEvent e) {
		// 메뉴가 클릭되었을 때 수행해야할 소스코드 작성
		
		if(e.getSource() == menuItem1) { // DB 연결
			conn = DBConn.getConnection();
			if(conn != null) {
				JOptionPane.showMessageDialog(this, "DB 접속 성공", "성공", JOptionPane.INFORMATION_MESSAGE);
			}else {
				JOptionPane.showMessageDialog(this, "DB 접속 실패", "실패", JOptionPane.ERROR_MESSAGE);
			}
		}else if(e.getSource() == menuItem2) {
			
		}else if(e.getSource() == menuItem3) { // 회원가입
			if(conn != null) {
				createMemberJoinPanel();
			}else {
				JOptionPane.showMessageDialog(this, "DB 접속을 먼저 하세요", "실패", JOptionPane.ERROR_MESSAGE);
			}	
		}else if(e.getSource() == menuItem5) { // 회원목록
			if(conn != null) {
				MemberDAO memberDAO = new MemberDAO(conn);
				memberList = memberDAO.selectMember();
				
				createMemberListPanel();
	
			}else {
				JOptionPane.showMessageDialog(this, "DB 접속을 먼저 하세요", "실패", JOptionPane.ERROR_MESSAGE);
			}			
		}else if(e.getSource() == menuItem4) { // 종료
			this.setVisible(false);
			this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
			System.exit(1);
		}else if(e.getSource() == registerButton) { // 회원가입 버튼
			String s1 = userText1.getText(); // 아이디
			int s2 = Integer.parseInt(userText2.getText()); // 나이
			String s3 = userText3.getText(); // 이름
			String s4 = passwordText.getText(); // 비밀번호
			
			Member member = new Member(s1, s4, s2, null, null, null, s3);
			MemberDAO memberDAO = new MemberDAO(conn);
			
			int result = memberDAO.insertMember(member);
			if(result > 0) {
				JOptionPane.showMessageDialog(this, "회원가입 성공", "성공", JOptionPane.INFORMATION_MESSAGE);
				userText1.setText("");
				userText2.setText("");
				userText3.setText("");
				passwordText.setText("");
			}else {
				JOptionPane.showMessageDialog(this, "회원가입 실패", "실패", JOptionPane.ERROR_MESSAGE);
			}
		}
		// 아래 정보수정과 회원탈퇴는 수업에서 진행한 것이 아닌 개인적으로 작성한 코드
		else if(e.getSource() == updateButton) { // 정보수정 버튼
			String s1 = userText1.getText(); // 아이디
			int s2 = Integer.parseInt(userText2.getText());
			String s3 = userText3.getText(); // 이름
			String s4 = passwordText.getText(); // 비밀번호
			
			// 위에서 받아온 정보를 member 변수에 넣음
			Member member = new Member(s1, s4, s2, null, null, null, s3);
			MemberDAO memberDAO = new MemberDAO(conn);
			
			// memberDAO의 updateMember() 메소드를 실행하고 결과를 반환 받음
			int result = memberDAO.updateMember(member);
			if(result > 0) {
				JOptionPane.showMessageDialog(this, "정보수정 성공", "성공", JOptionPane.INFORMATION_MESSAGE);
				// userText에 
				userText1.setText("");
				userText2.setText("");
				userText3.setText("");
				passwordText.setText("");
			}else {
				JOptionPane.showMessageDialog(this, "정보수정 실패", "실패", JOptionPane.ERROR_MESSAGE);
			}
		}else if(e.getSource() == deleteButton) { // 회원탈퇴 버튼
			String s1 = userText1.getText(); // 아이디
			
			// 위에서 받아온 정보를 member 변수에 넣음
			Member member = new Member(s1, null, 0, null, null, null, null);
			MemberDAO memberDAO = new MemberDAO(conn);
			
			// memberDAO의 deleteMember() 메소드를 실행하고 결과를 반환 받음
			int result = memberDAO.deleteMember(member);
			if(result > 0) {
				JOptionPane.showMessageDialog(this, "회원탈퇴 성공", "성공", JOptionPane.INFORMATION_MESSAGE);
				userText1.setText("");
			}else {
				JOptionPane.showMessageDialog(this, "회원탈퇴 실패", "실패", JOptionPane.ERROR_MESSAGE);
			}
		}
	}
	
	public void createMemberListPanel() {
		// 현재 출력 화면이 누적되는 것을 막기 위해 현재 화면의 내용을 지워줌
		panel.removeAll();
		JLabel lbl = new JLabel("회원목록");
		lbl.setBounds(4, 4, 100, 30);
		
		// memberList의 내용을 2차 String 배열로 전환하기 위한 변수
		String[][] data = new String[memberList.size()][7];
		
		// memberList에 1칸 씩 들어있는 내용을 2차 String 배열에 하나 씩 저장
		for(int i=0; i<memberList.size(); i++) {
			data[i] = memberList.get(i);
		}
		
		// table을 생성하고 scroll에 붙인 뒤 좌표를 맞춰 줌
		JTable table = new JTable(data, COLUMN);
		JScrollPane scroll = new JScrollPane(table);
		scroll.setBounds(4, 34, 480, 140);
		
		// 패널에 스크롤과 라벨을 붙임
		panel.add(scroll);
		panel.add(lbl);
	}
	
	public void createMemberJoinPanel() {
		// 현재 출력 화면이 누적되는 것을 막기 위해 현재 화면의 내용을 지워줌
		panel.removeAll();
		JLabel lbl = new JLabel("회원가입");
		lbl.setBounds(4, 4, 100, 30);
		panel.add(lbl);
		
		JLabel userLabel = new JLabel("아이디");
		userLabel.setBounds(10, 80, 80, 25);
		panel.add(userLabel);
		
		userText1 = new JTextField(20);
		userText1.setBounds(100, 80, 160, 25);
		panel.add(userText1);
		
		JLabel passwordLabel = new JLabel("비밀번호");
		passwordLabel.setBounds(10, 120, 80, 25);		
		panel.add(passwordLabel);
		
		passwordText = new JPasswordField(20);
		passwordText.setBounds(100, 120, 160, 25);		
		panel.add(passwordText);
		
		JLabel userLabel1 = new JLabel("나이");
		userLabel1.setBounds(10, 160, 80, 25);
		panel.add(userLabel1);
		
		userText2 = new JTextField(20);
		userText2.setBounds(100, 160, 160, 25);
		panel.add(userText2);
		
		JLabel userLabel2 = new JLabel("이름");
		userLabel2.setBounds(10, 200, 80, 25);
		panel.add(userLabel2);
		
		userText3 = new JTextField(20);
		userText3.setBounds(100, 200, 160, 25);
		panel.add(userText3);
				
		registerButton = new JButton("회원가입");
		registerButton.setBounds(10, 240, 100, 30);
		registerButton.addActionListener(this);		
		panel.add(registerButton);
		
		// 추가한 내용
		updateButton = new JButton("정보수정");
		updateButton.setBounds(120, 240, 100, 30);
		updateButton.addActionListener(this);
		panel.add(updateButton);
		
		deleteButton = new JButton("회원탈퇴");
		deleteButton.setBounds(230, 240, 100, 30);
		deleteButton.addActionListener(this);
		panel.add(deleteButton);
	}
}
```

### 2. MemberDAO 설정

DAO는 실제 SQL문을 실행하고 적용하는 클래스이다.

```java
package com.example.member;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;

public class MemberDAO {
	private Connection connection = null;
	
	// 생성자: DB 연결 정보 connection 객체를 받음
	public MemberDAO(Connection connection) {
		super();
		this.connection = connection;
	}
	
	// 회원 가입
	public int insertMember(Member member) {
		try {
			String sql = "INSERT INTO MEMBERTBL(USER_ID, USER_PW, USER_AGE, USER_TEL, USER_DATE, USER_ADDR, USER_NAME) VALUES(?, ?, ?, ?, CURRENT_DATE, ?, ?)";
			// sql문에 정보를 입력
			// insert 이므로 모든 정보를 다 넣어 줌
			PreparedStatement ps = connection.prepareStatement(sql);
			ps.setString(1, member.getId());
			ps.setString(2, member.getPw());
			ps.setInt(3, member.getAge());
			ps.setString(4, member.getTel());
			ps.setString(5, member.getAddr());
			ps.setString(6, member.getName());
			// sql문 실행
			int result = ps.executeUpdate(); // INSERT, UPDATE, DELETE일 경우 사용
			connection.commit();
			return result;
			
		} catch (Exception e) {
			e.printStackTrace();
			return 0;
		}
	}	

	// 회원 수정
	public int updateMember(Member member) {
		try {
			// sql문에 정보를 입력
			// update 이므로 수정할 정보들을 입력한 후 id 정보를 넣음
			String sql = "UPDATE MEMBERTBL SET USER_PW = ?, USER_AGE = ?, USER_TEL = ?, USER_ADDR = ?, USER_NAME = ? WHERE USER_ID = ?";
			PreparedStatement ps = connection.prepareStatement(sql);
			ps.setString(1, member.getPw());
			ps.setInt(2, member.getAge());
			ps.setString(3, member.getTel());
			ps.setString(4, member.getAddr());
			ps.setString(5, member.getName());
			ps.setString(6, member.getId());
			// sql문 실행
			int result = ps.executeUpdate();
			connection.commit();
			return result;
		} catch (Exception e) {
			e.printStackTrace();
			return 0;
		}
	}
	
	// 회원 삭제
	public int deleteMember(Member member) {
		try {
			// sql문에 정보를 입력
			// delete 이므로 id 정보만 넣으면 됨
			String sql = "DELETE FROM MEMBERTBL WHERE USER_ID = ?";
			PreparedStatement ps = connection.prepareStatement(sql);
			ps.setString(1, member.getId());
			// sql문 실행
			int result = ps.executeUpdate();
			connection.commit();
			return result;
		} catch (Exception e) {
			e.printStackTrace();
			return 0;
		}
	}
	
	// 회원 목록
	public ArrayList<String[]> selectMember() {
		try {
			String sql = "SELECT * FROM MEMBERTBL";
			PreparedStatement ps = connection.prepareStatement(sql);
			// 결과 값이 rs에 보관됨
			ResultSet rs = ps.executeQuery();
			
			ArrayList<String[]> memberList = new ArrayList<String[]>();
			
			while(rs.next()) { // 한 줄씩 가져옴
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
	}
	
	// 로그인
	public Member selectMemberLogin(Member member) {
		try {
			String sql = "SELECT * FROM MEMBERTBL WHERE USER_ID = ? AND USER_PW = ?";
			PreparedStatement ps = connection.prepareStatement(sql);
			
			ps.setString(1, member.getId());
			ps.setString(2, member.getPw());
						
			ResultSet rs = ps.executeQuery();
						
			if(rs.next()) { // 1개인 경우에는 if문으로 처리
				Member tmp = new Member();
				tmp.setId(rs.getString("USER_ID"));
				tmp.setPw(rs.getString("USER_PW"));
				tmp.setAge(Integer.valueOf(rs.getString("USER_AGE"))); // 형변환
				tmp.setTel(rs.getString("USER_TEL"));
				tmp.setDate(rs.getString("USER_DATE"));
				tmp.setAddr(rs.getString("USER_ADDR"));
				tmp.setName(rs.getString("USER_NAME"));
				return tmp;
			}
			return null;
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}
		
	// 회원정보가져오기
	public Member selectMemberOne(Member member) {
		try {
			try {
				String sql = "SELECT * FROM MEMBERTBL WHERE USER_ID = ?";
				PreparedStatement ps = connection.prepareStatement(sql);
				
				ps.setString(1, member.getId());

				ResultSet rs = ps.executeQuery();
							
				if(rs.next()) { // 1개인 경우에는 if문으로 처리
					Member tmp = new Member();
					tmp.setId(rs.getString("USER_ID"));
					tmp.setPw(rs.getString("USER_PW"));
					tmp.setAge(Integer.valueOf(rs.getString("USER_AGE"))); // 형변환
					tmp.setTel(rs.getString("USER_TEL"));
					tmp.setDate(rs.getString("USER_DATE"));
					tmp.setAddr(rs.getString("USER_ADDR"));
					tmp.setName(rs.getString("USER_NAME"));
					return tmp;
				}
				return null;
			} catch (Exception e) {
				e.printStackTrace();
				return null;
			}			
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}
}
```

```java
// 파일 이름: Main.java
package com.example;

import com.example.frame.MyFrame;

public class Main {

	public static void main(String[] args) {
		new MyFrame("java 실습");
	}

}
```

프로그램은 다음과 같이 진행된다.

1. 메인을 통해 MyFrame(GUI)을 실행한다.
2. GUI의 메뉴를 클릭 혹은 실행하면 actionListener(이벤트)가 활성화된다.
3. 메뉴에 맞는 이벤트가 actionPerformed의 if문을 통해 진행된다.
4. if문 내에서 DAO의 메소드를 실행한다.
5. DAO 메소드에서 sql문이 통신을 통해 db로 전달되고 결과가 DAO로 반환된다.
6. DAO로 반환된 결과 값이 actionPerformed에서 사용될 수 있도록 변환되어 보내진다.
7. actionPerformed에서 받은 결과 값을 이용해 테이블 등을 출력하거나 이벤트 성공 혹은 실패 메시지를 출력한다.  

정리하면   
"GUI `=>` actionPerformed `=>` DAO `=>` actionPerformed `=>` GUI"
순으로 진행된다고 할 수 있다.  
앞으로 코딩을 진행할 때 위와 같은 구조를 숙지하는 것이 중요할 것이다.