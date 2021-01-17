---
title: "JAVA 스터디 2일차: 클래스"
excerpt: "클래스, try-catch"

categories:
    - webprogramming
tags:
    - JAVA
    - 스터디
date: 2021-01-17 23:35:00 +0900
---
클래스 생성 및 오류 발생과 관련된 try-catch문에 대해서 알아보자.
```java
public class Book{
    private String title = null;
    private int price = 0;
    private String author = null;
    private float discountRate = 0.0f;
    private String position = null;
    // 생성자, getter-setter, toString 생략

    public int sellingPrice() { // 오류가 발생하면 프로그램을 닫음
		try {
			if(discountRate < 0) { // 오류 발생 시점(discountRate가 0보다 작을 경우)
				throw new Exception("discountRate error"); // 에러 출력
			}
			int tmp = price - (int)(price*discountRate);
			return tmp;
		}
		catch(Exception e) {
			//오류발생에 알림창을 표시
			//개발자가 확인하기 위한 용도
			//e.getMessage() : 오류가 발생한 메시지
			System.err.println( e.getMessage() );
			return -1;
		}
	}
}
```
Book 클래스 안의 sellingPrice 메소드를 보면 try-catch라는 문법이 들어있는 것을 볼 수 있다. try-catch는 오류가 발생했을 때를 대비해 사용한다. 만일 메소드가 원하는 방식으로 동작하지 않을 경우 에러 메시지를 출력해 어느 부분에 문제가 있는지 확인할 수 있다. 그래서 메소드를 작성할 때 가능하면 try-catch를 써주는 것이 좋으며 에러 발생 여부를 main에서 체크하기 위해 메소드의 반환형을 int 혹은 boolean으로 해서 값을 반환해 주는 것도 좋은 방법이다.  
  
다른 예문도 확인해보자.
```java
public class BookStore {
	private String name = null; // 서점명
	private String tel = null; // 연락처
	
	private Book[] books = new Book[100]; //최대 보유 가능책
	private int bookCnt = 0; //보유하고 있는 책 수량
	
	// 등록된 책 출력하기
	// 반복문을 등록한 개수만큼 회전하고 한개를 꺼내서 임시변수 tmp보관 후 getter을 이용하여 출력
	public void printBooks() throws Exception {
		//책이 없는 경우에 오류를 발생시켜 던짐.
		if(bookCnt < 1 ) {
			throw new Exception("printBooks error");
		}
		// 한 권씩 tmp의 toString을 이용해 책 정보를 출력
		for(int i=0; i<bookCnt; i++) {
			Book tmp = books[i];
			System.out.println(tmp.toString());
		}
	}	
	
	//책을 등록한다.
	public void registerBook(Book book){
		book.setPosition("위치1");
		//books라는 배열에 bookCnt위치에 1권 추가
		books[bookCnt] = book;
		//위치를 1증가시킴: 다음에 등록할 배열의 인덱스
		bookCnt++; 
	}
```
위 예문은 try-catch를 사용하지 않고 바로 throw new Exception을 사용한다. 이런 경우에는 throws Exception을 메소드 이름 옆에 작성해줘야한다. 
