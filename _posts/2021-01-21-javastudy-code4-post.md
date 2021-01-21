---
title: "JAVA 스터디: DB-Java 연결 - 4"
excerpt: "DB-Java 테이블 Join 후 출력"

categories:
    - webprogramming
tags:
    - JAVA
    - 스터디
date: 2021-01-22 01:40:00 +0900
---

ORDERTBL을 ITEMTBL과 JOIN 시킨 후 결과를 출력한다. ORDERTBL의 구조에 대해 분석해보자.

### 1. Order.java
```java
package com.example.shop;

public class Order {
	private int ord_no = 0;
	private int ord_cnt = 0;
	private String ord_date = null;
	private Item itm_no = null;
	private Customer cst_id = null;
	
	private String itm_name = null;
	private long itm_price = 0;
	
	public Order() {
		super();
	}

	public Order(int ord_no, int ord_cnt, String ord_date, Item itm_no, Customer cst_id) {
		super();
		this.ord_no = ord_no;
		this.ord_cnt = ord_cnt;
		this.ord_date = ord_date;
		this.itm_no = itm_no;
		this.cst_id = cst_id;
	}
	
	public Order(int ord_no, int ord_cnt, String ord_date, Item itm_no, Customer cst_id, String itm_name,
			long itm_price) {
		super();
		this.ord_no = ord_no;
		this.ord_cnt = ord_cnt;
		this.ord_date = ord_date;
		this.itm_no = itm_no;
		this.cst_id = cst_id;
		this.itm_name = itm_name;
		this.itm_price = itm_price;
	}
    // getter, setter, toString은 생략
}
```
Oracle의 ORDERTBL 데이터를 받아오기 위한 클래스이다. 여태까지 다른 클래스들과의 다른 점은 ITEMTBL, CUSTOMERTBL 두 테이블과 조인하기 위해 클래스를 변수로서 받고 있다는 것이다. 이 때문에 일반적인 하나의 테이블에서 데이터를 가져오는 것과 차이를 보이게 된다. 실제 itm_no에 대한 정보를 가져오기 위해선 `order.getItm_no().getItm_no`과 같이 클래스 내부의 클래스의 변수 값을 받아오는 식으로 작성해야한다.

### 2. OrderDAO.java(Interface)
```java
package com.example.shop;

import java.util.List;

public interface OrderDAO {
	// 주문하기: 주문내역정보가 오면 DB에 추가한 후 리턴(0 or 1)
	public int insertOrder(Order order) throws Exception;
	
	// 주문취소
	public int deleteOrder(Order order) throws Exception;
	
	// 주문수량변경
	public int updateOrder(Order order) throws Exception;
	
	// 주문내역(고객본인)
	public List<Order> selectOrder(Customer customer) throws Exception;
	
	// 고객별 주문내역
	public List<Order> selectOrderCustomer() throws Exception;
	
	// 날짜별 주문내역
	public List<Order> selectOrderDate() throws Exception;
	
	// 재고수량 파악
	public List<Order> selectOrderCnt() throws Exception;
}
```
OrderDAO 인터페이스이다. 각 클래스들의 역할을 생각해보며 인터페이스를 먼저 작성한다. insert, delete, update는 명령을 실행하기 위해 주문정보를 받아야 하므로 매개변수로 `Order order`를 받는 것이고 성공 여부를 확인하기 위해 반환형을 `int`로 잡는 것이다. selectOrder는 고객이 본인의 주문 내역을 조회하는 것으로 어느 고객이 조회하고자 하는지 알기 위해 매개변수를 `Customer customer`로 받는 것이며 명령 실행 후 얻은 고객의 주문 내역을 보내줘야 하므로 `List<Order>`를 반환형으로 잡았다.

### 3. OrderDAOImpl.java
```java
package com.example.shop;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;
import java.util.List;

import com.example.db.DBConn;

public class OrderDAOImpl implements OrderDAO{

	private Connection conn = DBConn.getConnection();

	@Override
	public int insertOrder(Order order) throws Exception {
		String sql = "INSERT INTO ORDERTBL(ORD_NO, CST_ID, ITM_NO, ORD_CNT, ORD_DATE)"+
					 " VALUES(SEQ_ORDERTBL_ORD_NO.NEXTVAL, ?, ?, ?, CURRENT_DATE)";
		PreparedStatement ps = conn.prepareStatement(sql);
		ps.setLong(1, order.getCst_id().getCst_id());
		ps.setLong(2, order.getItm_no().getItm_no());
		ps.setInt(3, order.getOrd_cnt());
		
		int result = ps.executeUpdate();
		conn.commit();
				
		return result;
	}

	@Override
	public int deleteOrder(Order order) throws Exception {
		String sql = "DELETE FROM ORDERTBL WHERE ORD_NO = ?";
		PreparedStatement ps = conn.prepareStatement(sql);
		ps.setLong(1, order.getOrd_no());

		int result = ps.executeUpdate();
		conn.commit();

		return result;
	}

	@Override
	public int updateOrder(Order order) throws Exception {
		String sql = "UPDATE ORDERTBL SET ORD_CNT = ? WHERE ORD_NO = ?";
		PreparedStatement ps = conn.prepareStatement(sql);
		ps.setInt(1, order.getOrd_cnt());
		ps.setInt(2, order.getOrd_no());

		int result = ps.executeUpdate();
		conn.commit();

		return result;
	}

	@Override
	public List<Order> selectOrder(Customer customer) throws Exception {
		String sql = "SELECT ORDERTBL.ORD_NO, ORDERTBL.ORD_CNT, ORDERTBL.ORD_DATE, "
				+ "ITEMTBL.ITM_NAME, ITEMTBL.ITM_PRICE "
				+ "FROM ORDERTBL INNER JOIN ITEMTBL "
				+ "ON ORDERTBL.ITM_NO = ITEMTBL.ITM_NO "
				+ "WHERE ORDERTBL.CST_ID = ? ORDER BY ORDERTBL.ORD_NO ASC";
		
		PreparedStatement ps = conn.prepareStatement(sql);
		ps.setLong(1, customer.getCst_id());
		
		ResultSet rs = ps.executeQuery();
				
		List<Order> orderList = new ArrayList<Order>();
		
		while(rs.next()) {
			Order order = new Order(
				rs.getInt("ORD_NO"),
				rs.getInt("ORD_CNT"),
				rs.getString("ORD_DATE"),
				null,
				null				
			);
			Item item = new Item(
				0,
				rs.getString("ITM_NAME"),
				null,
				rs.getInt("ITM_PRICE"),
				0,
				null
			);
			
			order.setItm_no(item);
			order.setCst_id(customer);
			
			orderList.add(order);			
		}
		
		return orderList;
	}

	@Override
	public List<Order> selectOrderCustomer() throws Exception {
		String sql = 
				"SELECT CUSTOMERTBL.CST_ID, CUSTOMERTBL.CST_NAME, "+
				"ORDERTBL.ORD_NO, ORDERTBL.ORD_CNT, ORDERTBL.ORD_DATE, "+
				"ITEMTBL.ITM_NO, ITEMTBL.ITM_NAME, ITEMTBL.ITM_PRICE, ITEMTBL.ITM_CNT "+
				"FROM CUSTOMERTBL "+
				"INNER JOIN ORDERTBL ON CUSTOMERTBL.CST_ID = ORDERTBL.CST_ID "+
				"INNER JOIN ITEMTBL ON ORDERTBL.ITM_NO = ITEMTBL.ITM_NO "+
				"ORDER BY CST_ID ASC";
		
		PreparedStatement ps = conn.prepareStatement(sql);
		
		ResultSet rs = ps.executeQuery();
		
		List<Order> orderList = new ArrayList<Order>();
		
		while(rs.next()) {
			Order order = new Order();
			Item item = new Item();
			Customer customer = new Customer();
			customer.setCst_id(rs.getLong("CST_ID"));
			customer.setCst_name(rs.getString("CST_NAME"));
			order.setOrd_no(rs.getInt("ORD_NO"));
			order.setOrd_cnt(rs.getInt("ORD_CNT"));
			order.setOrd_date(rs.getString("ORD_DATE"));
			item.setItm_no(rs.getLong("ITM_NO"));
			item.setItm_name(rs.getString("ITM_NAME"));
			item.setItm_price(rs.getLong("ITM_PRICE"));
			item.setItm_cnt(rs.getInt("ITM_CNT"));
			order.setCst_id(customer);
			order.setItm_no(item);
			
			orderList.add(order);			
		}			
				
		return orderList;
	}

	@Override
	public List<Order> selectOrderDate() throws Exception {
		
		return null;
	}

	@Override
	public List<Order> selectOrderCnt() throws Exception {
		
		return null;
	}
}
```
OrderDAO의 인터페이스를 구현한 클래스 OrderDAOImpl이다. 확실히 이전 DAOImpl들과 비교해 차이점을 볼 수 있는데 select관련 메소드를 보면 쿼리 실행 결과를 `ResultSet rs`로 받아와 이를 orderList에 저장하기 위해 while문 내부에 `Order order` 뿐만 아니라 order 내부의 클래스인 `Item item, Customer customer`도 생성하여 값을 초기화한 뒤 order에 setter를 이용해 넣어주는 것을 알 수 있다. 이처럼 Jave에서 JOIN을 구현하기 위해 이중으로 초기화 해주는 과정을 거쳐야 한다.

### 4. Main
```java
public class Main{
    public static void main(String[] args){
        try{
            orderDAOImpl orderDAO = new OrderDAOImpl();

            System.out.println("<주문 조회(고객본인)>");
			System.out.println("고객 번호를 입력하세요 ex) 1001");
					
			long inputDate = in.nextLong();
					
			Order order = new Order();
			Customer customer = new Customer();
			customer.setCst_id(inputDate);
			order.setCst_id(customer);
			
			List<Order> orderList = new ArrayList<Order>();
			orderList = orderDAO.selectOrder(customer);
					
			for(Order tmp : orderList) {
				System.out.println(
					"주문번호: "+tmp.getOrd_no()+
					", 주문수량: "+tmp.getOrd_cnt()+
					", 주문일자: "+tmp.getOrd_date()+
					", 물품명: "+tmp.getItm_no().getItm_name()+
					", 물품가격: "+tmp.getItm_no().getItm_price()
				);
			}
        }catch(Exception e){
            e.printStackTrace();
        }finally{
            System.out.println("프로그램 종료");
        }
    }
}
```
Main은 OrderDAOImpl의 selectOrder 메소드만 이용하고 있다. 결과 또한 출력하기 위해 클래스 내부의 클래스에서 getter로 값을 가져오는 것을 확인할 수 있다.
