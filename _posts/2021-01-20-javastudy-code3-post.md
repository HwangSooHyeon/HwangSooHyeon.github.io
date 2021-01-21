---
title: "JAVA 스터디: DB-Java 연결 - 3"
excerpt: "DB-Java 테이블 Join을 위한 코드 작성"

categories:
    - webprogramming
tags:
    - JAVA
    - 스터디
date: 2021-01-21 00:00:00 +0900
---

오늘은 ORDERTBL의 JOIN을 이용하기 위해 필요한 CUSTOMERTBL, ITEMTBL을 관리하는 코드를 작성했다. 

### 1. CUSTOMERTBL

#### 1.1. Customer.java
```java
// 파일 이름: Customer.java
package com.example.shop;

public class Customer {
	private long cst_id = 0;
	private String cst_name = null;
	private int cst_age = 0;    
	private String cst_date = null;
	
	public Customer() {
		super();
	}

	public Customer(long cst_id, String cst_name, int cst_age, String cst_date) {
		super();
		this.cst_id = cst_id;
		this.cst_name = cst_name;
		this.cst_age = cst_age;
		this.cst_date = cst_date;
	}
// getter, setter, toString은 생략
}
```

Customer 클래스는 CUSTOMERTBL의 데이터를 받을 변수로서 사용하기 위해 작성했다.

#### 1.2. CustomerDAO.java(interface)
```java
// 파일 이름: CustomerDAO.java
package com.example.shop;

import java.util.ArrayList;

public interface CustomerDAO {
	// 고객 등록
	public int insertCustomer(Customer customer) throws Exception;
	
	// 고객 삭제
	public int deleteCustomer(Customer customer) throws Exception;
		
	// 고객 수정
	public int updateCustomer(Customer customer) throws Exception;
	
	// 고객 목록
	public ArrayList<Customer> selectCustomer() throws Exception;
}
```

인터페이스(interface)는 일종의 설계도와 같다. 객체지향적으로 설계를 하기 위해선 먼저 시스템에 어떤 유형들이 존재하는지 파악해야한다. 그 다음 유형들간의 관계를 고민하고 그것들의 동작과 속성에 대해 고민해야한다. 그래서 유형이 떠오르면 바로 그 유형으로 인터페이스를 만들고 관계가 필요하면 상속으로 이어준 뒤 각 유형에 메소드의 시그니쳐를 추가한다. 이후 인터페이스보다 추상 클래스나 일반 클래스가 어울리는 유형들만 적절히 전환해준 후 남은 인터페이스를 실제로 구현하면 API 수준의 설계를 완료할 수 있다.  
또한 유지보수 또는 프로그램이 확장된다면 인터페이스를 통해 변화에 유연하게 대응할 수 있고 협업에 있어서 일관성 있는 작업을 진행하는데 도움을 줄 수 있다.

#### 1.3. CustomerDAOImpl.java
```java
package com.example.shop;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;

public class CustomerDAOImpl implements CustomerDAO {
	// DB 연결 정보를 보관할 객체
	private Connection conn = null;
		
	// 생성자를 통해 DB 객체를 받음
	public CustomerDAOImpl(Connection conn) {
		super();
		this.conn = conn;
	}
    
    // CustomerDAO의 메소드들을 구현
	@Override
	public int insertCustomer(Customer customer) throws Exception {
		String sql = "INSERT INTO CUSTOMERTBL(CST_ID, CST_NAME, CST_AGE, CST_DATE) VALUES(?, ?, ?, CURRENT_DATE)";
		PreparedStatement ps = conn.prepareStatement(sql);
		ps.setLong(1, customer.getCst_id());
		ps.setString(2, customer.getCst_name());
		ps.setInt(3, customer.getCst_age());
		
		int result = ps.executeUpdate();
		conn.commit();
		return result;
	}

	@Override
	public int deleteCustomer(Customer customer) throws Exception {
		String sql = "DELETE FROM CUSTOMERTBL WHERE CST_ID = ?";
		PreparedStatement ps = conn.prepareStatement(sql);
		ps.setLong(1, customer.getCst_id());
				
		int result = ps.executeUpdate();
		conn.commit();
		return result;
	}

	@Override
	public int updateCustomer(Customer customer) throws Exception {
		String sql = "UPDATE CUSTOMERTBL SET CST_NAME = ?, CST_AGE = ? WHERE CST_ID = ?";
		PreparedStatement ps = conn.prepareStatement(sql);
		ps.setString(1, customer.getCst_name());
		ps.setInt(2, customer.getCst_age());
		ps.setLong(3, customer.getCst_id());
		
		int result = ps.executeUpdate();
		conn.commit();
		return result;
	}

	@Override
	public ArrayList<Customer> selectCustomer() throws Exception {
		String sql = "SELECT * FROM CUSTOMERTBL ORDER BY CST_ID ASC";
		PreparedStatement ps = conn.prepareStatement(sql);
		
        // SELECT는 데이터를 받아와야 하므로 ResultSet을 사용
		ResultSet rs = ps.executeQuery();
		
		ArrayList<Customer> customerList = new ArrayList<Customer>();
		
		while(rs.next()) {
			Customer customer = new Customer(
				rs.getLong("CST_ID"),
				rs.getString("CST_NAME"),
				rs.getInt("CST_AGE"),
				rs.getString("CST_DATE")
			);
			customerList.add(customer);
		}
		return customerList;
	}
}
```
CustomerDAO 인터페이스의 메소드를 구현하는 클래스이다. 

### 2. ITEMTBL

#### 2.1. Item.java
```java
package com.example.shop;

public class Item {
	private long itm_no = 0; 
	private String itm_name = null; 
	private String itm_content = null; 
	private long itm_price = 0; 
	private int itm_cnt = 0;
	private String itm_date = null;
	
	public Item() {
		super();
	}

	public Item(long itm_no, String itm_name, String itm_content, long itm_price, int itm_cnt, String itm_date) {
		super();
		this.itm_no = itm_no;
		this.itm_name = itm_name;
		this.itm_content = itm_content;
		this.itm_price = itm_price;
		this.itm_cnt = itm_cnt;
		this.itm_date = itm_date;
	}
// getter, setter, toString은 생략
}
```
Item 클래스는 ITEMTBL의 데이터를 받을 변수로서 사용하기 위해 작성했다.

#### 2.2. ItemDAO.java(abstract class)
```java
package com.example.shop;

import java.util.List;

public abstract class ItemDAO {
	// 물품 추가
	public abstract int insertItem(Item item);
	
	// 물품 삭제
	public abstract int deleteItem(Item item);
	
	// 물품 수정
	public abstract int updateItem(Item item);
	
	// 물품 목록
	public abstract List<Item> selectItem(); // 전체 조회
	public abstract List<Item> selectItem(String searchText); // 검색 조회
	public abstract List<Item> selectItem(int start, int end); // 시작과 끝 설정 후 조회
	
	public void print() {
		System.out.println("추상클래스는 메소드 구현도 가능");
	}
}
```
추상 클래스와 인터페이스는 둘 다 객체를 사용할 수 없으며 상속 받은 자식 클래스들이 무언가를 반드시 구현하도록 위임해야할 때 사용한다. 이런 공통점도 있지만 뚜렷한 차이점도 존재한다.  
먼저 추상 클래스는 단일 상속만 가능하며 일반 변수를 가질 수 있고 동일한 부모를 가지는 클래스를 묶는 개념으로 상속을 받아서 기능을 확장시키는 것이 목적이다.
다음으로 인터페이스는 추상 클래스보다 한 단계 더 추상화된 클래스로 인터페이스 내부의 모든 메소드들은 추상 메소드들로 간주된다. 또한 다중 상속이 가능하며 구현 객체가 같은 동작을 하도록 보장하는 것이 목적이다.  
즉, 추상 클래스의 목적은 상속을 받아서 기능을 확장시키는 것(부모의 유전자를 물려받는 것)이고 인터페이스의 목적은 구현하는 모든 클래스에 대해 특정한 메소드가 만드시 존재하도록 강제하는 역할(사교적으로 필요에 따라 결합하는 관계)라고 할 수 있다.

#### 2.3. ItemDAOImpl.java
```java
package com.example.shop;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class ItemDAOImpl extends ItemDAO{
	private Connection conn = null;
		
	public ItemDAOImpl(Connection conn) {
		super();
		this.conn = conn;
	}

	@Override
	public int insertItem(Item item) {
		
		try {
			String sql = "INSERT INTO ITEMTBL(ITM_NO, ITM_NAME, ITM_CONTENT, ITM_PRICE, ITM_CNT, ITM_DATE)"+
					 " VALUES(SEQ_ITEMTBL_ITM_NO.NEXTVAL, ?, ?, ?, ?, CURRENT_DATE)";
			PreparedStatement ps = conn.prepareStatement(sql);
			ps.setString(1, item.getItm_name());
			ps.setString(2, item.getItm_content());
			ps.setLong(3, item.getItm_price());
			ps.setInt(4, item.getItm_cnt());
			
			int result = ps.executeUpdate();
			conn.commit();
			return result;
			
		} catch (SQLException e) {
			e.printStackTrace();
			return 0;
		}		
	}

	@Override
	public int deleteItem(Item item) {
		try {
			String sql = "DELETE FROM ITEMTBL WHERE ITM_NO = ?";
			PreparedStatement ps = conn.prepareStatement(sql);
			ps.setLong(1, item.getItm_no());
			
			int result = ps.executeUpdate();
			conn.commit();
			return result;
			
		} catch (SQLException e) {
			e.printStackTrace();
			return 0;
		}	
	}

	@Override
	public int updateItem(Item item) {
		try {
			String sql = "UPDATE ITEMTBL SET ITM_NAME = ?, ITM_CONTENT = ?, ITM_PRICE = ?, ITM_CNT = ? WHERE ITM_NO = ?";
			PreparedStatement ps = conn.prepareStatement(sql);
			ps.setString(1, item.getItm_name());
			ps.setString(2, item.getItm_content());
			ps.setLong(3, item.getItm_price());
			ps.setInt(4, item.getItm_cnt());
			ps.setLong(5, item.getItm_no());
			
			int result = ps.executeUpdate();
			conn.commit();
			return result;
			
		} catch (SQLException e) {
			e.printStackTrace();
			return 0;
		}	
	}

	@Override
	public List<Item> selectItem() {
		try {
			String sql = "SELECT * FROM ITEMTBL ORDER BY ITM_NO ASC";
			PreparedStatement ps = conn.prepareStatement(sql);
			
			ResultSet rs = ps.executeQuery();
			
			List<Item> itemList = new ArrayList<Item>();
			
			while(rs.next()) {
				Item item = new Item(
					rs.getLong("ITM_NO"),
					rs.getString("ITM_NAME"),
					rs.getString("ITM_CONTENT"),
					rs.getLong("ITM_PRICE"),
					rs.getInt("ITM_CNT"),
					rs.getString("ITM_DATE")
				);
				itemList.add(item);
			}
			return itemList;
			
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}

	@Override
	public List<Item> selectItem(String searchText) {
		try {
			String sql = "SELECT * FROM ITEMTBL WHERE ITM_NAME LIKE '%' || ? || '%' ORDER BY ITM_NO ASC";
			PreparedStatement ps = conn.prepareStatement(sql);
			ps.setString(1, searchText);
			
			ResultSet rs = ps.executeQuery();
			
			List<Item> itemList = new ArrayList<Item>();
			
			while(rs.next()) {
				Item item = new Item(
					rs.getLong("ITM_NO"),
					rs.getString("ITM_NAME"),
					rs.getString("ITM_CONTENT"),
					rs.getLong("ITM_PRICE"),
					rs.getInt("ITM_CNT"),
					rs.getString("ITM_DATE")
				);
				itemList.add(item);
			}
			return itemList;
			
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}

	@Override
	public List<Item> selectItem(int start, int end) {
		try {
			String sql = "SELECT * FROM"+
						 " (SELECT ITM_NO, ITM_NAME, ITM_CONTENT, ITM_PRICE, ITM_CNT, ITM_DATE,"+ 
					     " ROW_NUMBER() OVER (ORDER BY ITM_NO ASC) ITM_ROWN FROM ITEMTBL) WHERE ITM_ROWN BETWEEN ? AND ?";
			PreparedStatement ps = conn.prepareStatement(sql);
			
			ps.setInt(1, start);
			ps.setInt(2, end);
			
			ResultSet rs = ps.executeQuery();
			
			List<Item> itemList = new ArrayList<Item>();
			
			while(rs.next()) {
				Item item = new Item(
					rs.getLong("ITM_NO"),
					rs.getString("ITM_NAME"),
					rs.getString("ITM_CONTENT"),
					rs.getLong("ITM_PRICE"),
					rs.getInt("ITM_CNT"),
					rs.getString("ITM_DATE")
				);
				itemList.add(item);
			}
			return itemList;
			
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}
}
```
ItemDAO 추상 클래스의 추상 메소드를 구현하는 클래스이다. 

#### 2.4. Main
```java
package com.example;

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

import com.example.db.DBConn;
import com.example.shop.Customer;
import com.example.shop.CustomerDAOImpl;
import com.example.shop.Item;
import com.example.shop.ItemDAOImpl;

public class Main {

	public static void main(String[] args) {
		try {
			CustomerDAOImpl customerDAO = new CustomerDAOImpl(DBConn.getConnection());
			ItemDAOImpl itemDAO = new ItemDAOImpl(DBConn.getConnection());
			
			while(true) {
				System.out.println("0. 종료");
				System.out.println("1. 고객 등록, 2. 고객 삭제, 3. 고객 수정, 4. 고객 목록");
				System.out.println("11. 물품등록, 12. 물품삭제, 13. 물품수정, 14. 물품목록(전체), 15. 물품목록(검색), 16. 물품목록(범위)");
				System.out.println("메뉴를 선택하세요 >> ");
				Scanner in = new Scanner(System.in);
				int menu = in.nextInt();
								
				if(menu == 0) {
					break;
				}else if(menu == 1) {
					System.out.println("<고객 등록>");
					System.out.println("고객 번호, 이름, 나이 순으로 입력하세요 ex) 1001,홍길동,12");
					String inputData = in.next();
					String[] inputArray = inputData.split(","); // 공백 인식이 안되는 것 같음 인터넷에 확인해볼 것
					
					// split이 제대로 됐는지 확인
					for(int i=0; i<inputArray.length; i++) {
						System.out.println(inputArray[i]);
					}					
					
					Customer customer = new Customer(
						Long.parseLong(inputArray[0]),
						inputArray[1],
						Integer.parseInt(inputArray[2]),
						null
					);
					
					int result = customerDAO.insertCustomer(customer);
					
					if(result > 0) {
						System.out.println("고객이 등록되었습니다");
					}else {
						System.out.println("고객 등록에 실패했습니다.");
					}
				}else if(menu == 2) {
					System.out.println("<고객 삭제>");
					System.out.println("고객 번호를 입력하세요 ex) 1001");
					long inputData = in.nextLong();
					Customer customer = new Customer(inputData, null, 0, null);
					
					int result = customerDAO.deleteCustomer(customer);
					
					if(result > 0) {
						System.out.println("고객이 삭제되었습니다");
					}else {
						System.out.println("고객 삭제를 실패했습니다.");
					}
				}else if(menu == 3) {
					System.out.println("<고객 수정>");
					System.out.println("고객번호와 수정하고자하는 정보를 입력하세요 ex) 1001,홍길동,12");
					String inputData = in.next();
					
					String[] inputArray = inputData.split(",");
					
					Customer customer = new Customer(
						Long.parseLong(inputArray[0]),
						inputArray[1],
						Integer.parseInt(inputArray[2]),
						null
					);
					
					int result = customerDAO.updateCustomer(customer);
					
					if(result > 0) {
						System.out.println("고객 정보가 수정되었습니다");
					}else {
						System.out.println("고객 정보 수정을 실패했습니다.");
					}
				}else if(menu == 4) {
					System.out.println("<고객 목록 조회>");
					ArrayList<Customer> customerList = customerDAO.selectCustomer();
					
					for(Customer tmp : customerList) {
						System.out.println("고객 번호: "+tmp.getCst_id()+
										   ", 고객 이름: "+tmp.getCst_name()+
										   ", 고객 나이: "+tmp.getCst_age()+
										   ", 가입 날짜: "+tmp.getCst_date());
					}
				}else if(menu == 11) {
					System.out.println("<물품등록>");
					System.out.println("물품이름, 내용, 가격, 수량 순으로 입력하세요 ex) 물품1,내용1,1000,10");
					String inputData = in.next();
					
					String[] inputArray = inputData.split(",");
					
					Item item = new Item(
						0,
						inputArray[0],
						inputArray[1],
						Long.parseLong(inputArray[2]),
						Integer.parseInt(inputArray[3]),
						null
					);
					
					int result = itemDAO.insertItem(item);
					
					if(result > 0) {
						System.out.println("물품등록 성공");
					}else {
						System.out.println("물품등록 실패");
					}
				}else if(menu == 12) {
					System.out.println("<물품삭제>");
					System.out.println("물품번호를 입력하세요 ex) 1");
					Long inputData = in.nextLong();
					
					Item item = new Item(
						inputData,
						null,
						null,
						0,
						0,
						null
					);
					
					int result = itemDAO.deleteItem(item);
					
					if(result > 0) {
						System.out.println("물품삭제 성공");
					}else {
						System.out.println("물품삭제 실패");
					}
				}else if(menu == 13) {
					System.out.println("<물품수정>");
					System.out.println("물품번호, 이름, 내용, 가격, 수량 순으로 입력하세요 ex) 1,물품1,내용1,1000,10");
					String inputData = in.next();
					
					String[] inputArray = inputData.split(",");
					
					Item item = new Item(
						Long.parseLong(inputArray[0]),
						inputArray[1],
						inputArray[2],
						Long.parseLong(inputArray[3]),
						Integer.parseInt(inputArray[4]),
						null
					);
					
					int result = itemDAO.updateItem(item);
					
					if(result > 0) {
						System.out.println("물품수정 성공");
					}else {
						System.out.println("물품수정 실패");
					}					
				}else if(menu == 14) {
					System.out.println("<물품목록(전체)>");
					List<Item> itemList = itemDAO.selectItem();
					
					for(Item tmp : itemList) {
						System.out.println("물품번호: "+tmp.getItm_no()+
										   ", 물품이름: "+tmp.getItm_name()+
										   ", 물품내용: "+tmp.getItm_content()+
										   ", 물품가격: "+tmp.getItm_price()+
										   ", 물품수량: "+tmp.getItm_cnt()+
										   ", 등록날짜: "+tmp.getItm_date()										   
										  );
					}
				}else if(menu == 15) {
					System.out.println("<물품목록(검색)>");
					System.out.println("검색할 이름을 입력하세요");
					String inputData = in.next();
					List<Item> itemList = itemDAO.selectItem(inputData);
					
					for(Item tmp : itemList) {
						System.out.println("물품번호: "+tmp.getItm_no()+
										   ", 물품이름: "+tmp.getItm_name()+
										   ", 물품내용: "+tmp.getItm_content()+
										   ", 물품가격: "+tmp.getItm_price()+
										   ", 물품수량: "+tmp.getItm_cnt()+
										   ", 등록날짜: "+tmp.getItm_date()										   
										  );
					}
				}else if(menu == 16) {
					System.out.println("<물품목록(범위)>");
					System.out.println("검색할 범위를 입력하세요");
					System.out.println("시작: ");
					int inputStart = in.nextInt();
					System.out.println("끝: ");
					int inputEnd = in.nextInt();
					List<Item> itemList = itemDAO.selectItem(inputStart, inputEnd);
					
					for(Item tmp : itemList) {
						System.out.println("물품번호: "+tmp.getItm_no()+
										   ", 물품이름: "+tmp.getItm_name()+
										   ", 물품내용: "+tmp.getItm_content()+
										   ", 물품가격: "+tmp.getItm_price()+
										   ", 물품수량: "+tmp.getItm_cnt()+
										   ", 등록날짜: "+tmp.getItm_date()										   
										  );
					}
				}else {
					System.out.println("잘못된 번호입니다. 다시 입력해주세요");
				}
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			System.out.println("프로그램 종료");
		}
	}
}
```

Main에서는 CustomerDAOImpl과 ItemDAOImpl의 메소드를 가져와 데이터를 입력하거나 출력하고 있다. 길이는 길지만 동일한 내용을 반복하고 있다.