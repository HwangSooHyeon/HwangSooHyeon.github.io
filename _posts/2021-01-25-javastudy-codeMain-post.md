---
title: "JAVA 스터디: main 코드 저장"
excerpt: "json에서 데이터 읽어오기"

categories:
    - webprogramming
tags:
    - JAVA
    - 스터디
date: 2021-01-25 01:40:00 +0900
---
```java
package com.example;

import org.json.JSONArray;
import org.json.JSONObject;

import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import okhttp3.ResponseBody;

public class Main {

	public static void main(String[] args) {
		try {
			// 정보를 가져올 서버 URL 주소
			String url = "http://ihongss.com/json/exam1.json";
			
			// 다른 서버를 호출할 클라이언트 객체 생성 okhttp3-10.0.jar라이브러리 이용
			OkHttpClient client = new OkHttpClient();
			
			// 요청 방법 설정: GET방식
			Request.Builder builder = new Request.Builder().url(url).get();
			
			// Request 객체 생성
			Request request = builder.build();
			
			// Response 객체 생성
			Response response = client.newCall(request).execute();
			
			// 가져온 결과가 성공이라면
			if(response.isSuccessful()) {
				// body에 담겨진 내용 출력하기
				ResponseBody body = response.body();
				String bodyString = body.string();
				
				//String => JSONObject(HashMap과 비슷함)
				JSONObject jsonObject = new JSONObject(bodyString);
				System.out.println(bodyString);
				System.out.println(jsonObject.get("ret")+", "+jsonObject.get("data"));				
			}
			
		} catch (Exception e) {
			e.printStackTrace();
		}
		
		try {
			// 정보를 가져올 서버 URL 주소
			String url = "http://ihongss.com/json/exam2.json";

			// 다른 서버를 호출할 클라이언트 객체 생성 okhttp3-10.0.jar라이브러리 이용
			OkHttpClient client = new OkHttpClient();

			// 요청 방법 설정: GET방식
			Request.Builder builder = new Request.Builder().url(url).get();

			// Request 객체 생성
			Request request = builder.build();

			// Response 객체 생성
			Response response = client.newCall(request).execute();

			// 가져온 결과가 성공이라면
			if(response.isSuccessful()) {
				// body에 담겨진 내용 출력하기
				ResponseBody body = response.body();
				String bodyString = body.string();
				System.out.println(bodyString);

				// String => JSONObject(HashMap과 비슷함)
				// ArrayList<Map<String, Object>>
				JSONArray jsonArray = new JSONArray(bodyString);
				JSONObject jsonObject0 = new JSONObject(jsonArray.get(0).toString());
				JSONObject jsonObject1 = new JSONObject(jsonArray.get(1).toString());
				
				System.out.println(jsonObject0);
				System.out.println(jsonObject1);
				
				System.out.println(jsonObject1.get("ret"));
				// 실습: data에서 파싱해서 234를 출력하세요.
				System.out.println(jsonObject1.get("data"));
			}			
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

```java
try {
			// 정보를 가져올 서버 URL 주소
			String url = "http://ihongss.com/json/exam1.json";
			
			// 다른 서버를 호출할 클라이언트 객체 생성 okhttp3-10.0.jar라이브러리 이용
			OkHttpClient client = new OkHttpClient();
			
			// 요청 방법 설정: GET방식
			Request.Builder builder = new Request.Builder().url(url).get();
			
			// Request 객체 생성
			Request request = builder.build();
			
			// Response 객체 생성
			Response response = client.newCall(request).execute();
			
			// 가져온 결과가 성공이라면
			if(response.isSuccessful()) {
				// body에 담겨진 내용 출력하기
				ResponseBody body = response.body();
				String bodyString = body.string();
				
				//String => JSONObject(HashMap과 비슷함)
				JSONObject jsonObject = new JSONObject(bodyString);
				System.out.println(bodyString);
				System.out.println(jsonObject.get("ret")+", "+jsonObject.get("data"));				
			}
			
		} catch (Exception e) {
			e.printStackTrace();
		}
		
		try {
			// 정보를 가져올 서버 URL 주소
			String url = "http://ihongss.com/json/exam2.json";

			// 다른 서버를 호출할 클라이언트 객체 생성 okhttp3-10.0.jar라이브러리 이용
			OkHttpClient client = new OkHttpClient();

			// 요청 방법 설정: GET방식
			Request.Builder builder = new Request.Builder().url(url).get();

			// Request 객체 생성
			Request request = builder.build();

			// Response 객체 생성
			Response response = client.newCall(request).execute();

			// 가져온 결과가 성공이라면
			if(response.isSuccessful()) {
				// body에 담겨진 내용 출력하기
				ResponseBody body = response.body();
				String bodyString = body.string();
				System.out.println(bodyString);

				// String => JSONObject(HashMap과 비슷함)
				// ArrayList<Map<String, Object>>
				JSONArray jsonArray = new JSONArray(bodyString);
				//JSONObject jsonObject0 = new JSONObject(jsonArray.get(0).toString());
				//JSONObject jsonObject1 = new JSONObject(jsonArray.get(1).toString());
				
				//System.out.println(jsonObject0);
				//System.out.println(jsonObject1);
				
				//System.out.println(jsonObject1.get("ret"));
				// 실습: data에서 파싱해서 234를 출력하세요.
				//System.out.println(jsonObject1.get("data"));
				
				for(int i = 0; i < jsonArray.length(); i++) {
					JSONObject jsonObject = new JSONObject(jsonArray.get(i).toString());
					System.out.println(jsonObject.get("ret")+", "+jsonObject.get("data"));
				}
			}			
		} catch (Exception e) {
			e.printStackTrace();
		}
		// http://ihongss.com/json/exam4.json
		// 실습 exam4.json에서 회원정보(아이디, 이름, 나이)를 가져와서 7명을 출력하시오.
		try {
			// 정보를 가져올 서버 URL 주소
			String url = "http://ihongss.com/json/exam4.json";

			// 다른 서버를 호출할 클라이언트 객체 생성 okhttp3-10.0.jar라이브러리 이용
			OkHttpClient client = new OkHttpClient();

			// 요청 방법 설정: GET방식
			Request.Builder builder = new Request.Builder().url(url).get();

			// Request 객체 생성
			Request request = builder.build();

			// Response 객체 생성
			Response response = client.newCall(request).execute();
			
			if(response.isSuccessful()) {
				ResponseBody body = response.body();
				String bodyString = body.string();
				System.out.println(bodyString);
				
				JSONObject jsonObject = new JSONObject(bodyString);
				String ret = jsonObject.get("ret").toString();
				String ret1 = jsonObject.get("ret1").toString();
				
				JSONArray jsonArray = new JSONArray(ret);
				
				for(int i = 0; i < jsonArray.length(); i++) {
					JSONObject tmpObject = new JSONObject(jsonArray.get(i).toString());
					System.out.println("id: "+tmpObject.get("id")+", name: "+tmpObject.get("name")+", age: "+tmpObject.get("age"));
				}
				
				jsonArray = new JSONArray(ret1);
				
				for(int i = 0; i < jsonArray.length(); i++) {
					JSONObject tmpObject = new JSONObject(jsonArray.get(i).toString());
					System.out.println("id: "+tmpObject.get("id")+", name: "+tmpObject.get("name")+", age: "+tmpObject.get("age"));
				}
			}			
		} catch (Exception e) {
			
		}
		// exam21.json
		// 10개의 영화정보를 출력하시오. rank, movieNm, openDt
		try {
			// 정보를 가져올 서버 URL 주소
			String url = "http://ihongss.com/json/exam21.json";

			// 다른 서버를 호출할 클라이언트 객체 생성 okhttp3-10.0.jar라이브러리 이용
			OkHttpClient client = new OkHttpClient();

			// 요청 방법 설정: GET방식
			Request.Builder builder = new Request.Builder().url(url).get();

			// Request 객체 생성
			Request request = builder.build();

			// Response 객체 생성
			Response response = client.newCall(request).execute();
			
			if(response.isSuccessful()) {
				ResponseBody body = response.body();
				String bodyString = body.string();
				System.out.println(bodyString);
								
				JSONObject jsonObject = new JSONObject(bodyString);
				String boxOfficeResult = jsonObject.get("boxOfficeResult").toString();
				
				JSONObject jsonObject1 = new JSONObject(boxOfficeResult);
				String dailyBoxOfficeList = jsonObject1.get("dailyBoxOfficeList").toString();
				
				JSONArray jsonArray = new JSONArray(dailyBoxOfficeList);
				
				for(int i = 0; i < jsonArray.length(); i++) {
					JSONObject tmpObject = new JSONObject(jsonArray.get(i).toString());
					System.out.println("rank: "+tmpObject.get("rank")+", movieNm: "+tmpObject.get("movieNm")+", openDt: "+tmpObject.get("openDt"));
				}
			}			
		} catch (Exception e) {
			
		}
		// exam10.json
		// 아이디, 이름, 나이, 수학, 영어, 국어 (평균) => 5명
		try {
			
			
			// 정보를 가져올 서버 URL 주소
			String url = "http://ihongss.com/json/exam10.json";

			// 다른 서버를 호출할 클라이언트 객체 생성 okhttp3-10.0.jar라이브러리 이용
			OkHttpClient client = new OkHttpClient();

			// 요청 방법 설정: GET방식
			Request.Builder builder = new Request.Builder().url(url).get();

			// Request 객체 생성
			Request request = builder.build();

			// Response 객체 생성
			Response response = client.newCall(request).execute();
			
			if(response.isSuccessful()) {
				ResponseBody body = response.body();
				String bodyString = body.string();
				//System.out.println(bodyString);
				
				JSONObject jsonObject1 = new JSONObject(bodyString);
				String data = jsonObject1.get("data").toString();
				//System.out.println(data);
				
				JSONArray jsonArray1 = new JSONArray(data);
				
				System.out.println("jsonArray 길이: "+jsonArray1.length());
				
				for (int i = 0; i < jsonArray1.length(); i++) {
					JSONObject tmpObject = new JSONObject(jsonArray1.get(i).toString());
					System.out.print("id: "+tmpObject.get("id")+", name: "+tmpObject.get("name")+", age: "+tmpObject.get("age")+", ");
					JSONObject tmpObject1 = new JSONObject(tmpObject.get("score").toString());
					System.out.print("math: "+tmpObject1.get("math")+", eng: "+tmpObject1.get("eng")+", kor: "+tmpObject1.get("kor")+", ");
					System.out.println("평균: "+
					(float)(((Integer)(tmpObject1.get("math"))+(Integer)(tmpObject1.get("eng"))+(Integer)(tmpObject1.get("kor")))/3.0));
				}
				
			}
			
		} catch (Exception e) {

		}
		
```