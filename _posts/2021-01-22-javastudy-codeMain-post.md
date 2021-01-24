---
title: "JAVA 스터디: main 코드 저장"
excerpt: "수업 시간에 진행한 main 코드 저장"

categories:
    - webprogramming
tags:
    - JAVA
    - 스터디
date: 2021-01-22 01:40:00 +0900
---

### 2021-01-22 10:10:00 main code
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
import com.example.shop.Order;
import com.example.shop.OrderDAOImpl;

public class Main {

	public static void main(String[] args) {
		
		try {
			CustomerDAOImpl customerDAO = new CustomerDAOImpl(DBConn.getConnection());
			ItemDAOImpl itemDAO = new ItemDAOImpl(DBConn.getConnection());
			OrderDAOImpl orderDAO = new OrderDAOImpl();
			
			while(true) {
				System.out.println("0. 종료");
				System.out.println("1. 고객 등록, 2. 고객 삭제, 3. 고객 수정, 4. 고객 목록");
				System.out.println("11. 물품등록, 12. 물품삭제, 13. 물품수정, 14. 물품목록(전체), 15. 물품목록(검색), 16. 물품목록(범위)");
				System.out.println("21. 주문하기, 22. 주문취소, 23, 주문수량변경 24. 주문조회(고객본인) 25. 주문조회(판매자)");
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
				}else if(menu == 21) {
					System.out.println("<주문 하기>");
					System.out.println("고객 번호, 물품번호, 주문수량 순으로 입력하세요 ex) 1001,1,5");
					String inputData = in.next();
					String[] inputArray = inputData.split(",");
					
					// split이 제대로 됐는지 확인
					for(int i=0; i<inputArray.length; i++) {
						System.out.println(inputArray[i]);
					}					
					
					Order order = new Order();
					
					Customer customer = new Customer();
					customer.setCst_id(Long.parseLong(inputArray[0]));
					order.setCst_id(customer);
					
					Item item = new Item();
					item.setItm_no(Long.parseLong(inputArray[1]));
					order.setItm_no(item);
					
					order.setOrd_cnt(Integer.parseInt(inputArray[2]));
										
					int result = orderDAO.insertOrder(order);
					
					if(result > 0) {
						System.out.println("주문 성공");
					}else {
						System.out.println("주문 실패");
					}
				}else if(menu == 22) {
					System.out.println("<주문 취소>");
					System.out.println("주문번호를 입력하세요 ex) 20");
					int inputData = in.nextInt();	
					
					Order order = new Order();
					order.setOrd_no(inputData);
					
					int result = orderDAO.deleteOrder(order);
					
					if(result > 0) {
						System.out.println("주문 취소 성공");
					}else {
						System.out.println("주문 취소 실패");
					}
				}else if(menu == 23) {
					System.out.println("<주문 수량 변경>");
					System.out.println("변경할 수량, 주문번호 순으로 입력하세요 ex) 15,20");
					
					String inputData = in.next();
					String[] inputArray = inputData.split(",");
					
					// split이 제대로 됐는지 확인
					for(int i=0; i<inputArray.length; i++) {
						System.out.println(inputArray[i]);
					}					
					
					Order order = new Order();
					order.setOrd_cnt(Integer.parseInt(inputArray[0]));
					order.setOrd_no(Integer.parseInt(inputArray[1]));
										
					int result = orderDAO.updateOrder(order);
					
					if(result > 0) {
						System.out.println("주문 수량 변경 성공");
					}else {
						System.out.println("주문 수량 변경 실패");
					}					
				}else if(menu == 24) {
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
				}else if(menu == 25) {
					System.out.println("<주문 조회(판매자)>");
					
					List<Order> orderList = new ArrayList<Order>();
					
					orderList = orderDAO.selectOrderCustomer();
					
					for(Order tmp : orderList) {
						System.out.println(
							"주문번호: "+tmp.getOrd_no()+
							", 물품번호: "+tmp.getItm_no().getItm_no()+
							", 믈픔명: "+tmp.getItm_no().getItm_name()+
							", 물품가격: "+tmp.getItm_no().getItm_price()+
							", 물품수량: "+tmp.getItm_no().getItm_cnt()+
							", 주문수량: "+tmp.getOrd_cnt()+
							", 주문날짜: "+tmp.getOrd_date()+
							", 고객번호: "+tmp.getCst_id().getCst_id()+
							", 고객이름: "+tmp.getCst_id().getCst_name()						
						);
					}
				}
				
				
				else {
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

### 2021-01-22 11:15:00 main code
```java
        // List는 동적할당
		// 갯수를 설정할 필요가 없다. 타입도 같을 필요가 없다.
		// 같은 것으로는 Vector
		
		List<String> list = new ArrayList<String>();
		
		List<String[]> list1 = new ArrayList<String[]>();
		
		// map은 key와 value 값을 가짐
		// 순차적이지 않음
		// 키는 중복될 수 없음
		Map<String, String[]> map = new HashMap<String, String[]>();
		
		// 추가하기(put)
		map.put("A1", new String[] {"a", "b", "c"});
		map.put("A2", new String[] {"a", "b", "c"});
		
		// 가져오기(get)
		String[] tmp = map.get("A1");
		String[] tmp1 = map.get("A2");
		
		for(int i = 0; i < tmp.length; i++) {
			System.out.println(tmp[i]);
		}
		for(int i = 0; i < tmp1.length; i++) {
			System.out.println(tmp1[i]);
		}
```

