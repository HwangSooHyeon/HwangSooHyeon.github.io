---
title: "JAVA 스터디: 자바 활용 평가"
excerpt: "여태까지 배운 자바에 대한 평가 진행"

categories:
    - webprogramming
tags:
    - JAVA
    - 스터디
    - EXAM
date: 2021-01-26 00:30:00 +0900
---
21년 1월 25일까지 배운 내용을 바탕으로 프로그래밍언어활용 평가를 진행했다. 평가 내용이 실제 기업 코딩테스트에서 기초 지식을 물어보는데 사용되는 문제들로 구성되었다고 들어 문제와 정답에 관해 정리해보고자 한다.

##### 1. 다음 중에서 올바른 주석이 아닌 것은?
1. /** 주석 */
2. /* 주석 */
3. /* 주석
4. // 주석

> 3번이 올바르지 않은 주석이다. 1번의 경우 `/* */` 내부에 `*`이 있는 것이므로 어차피 주석처리 되므로 의미가 없다. `/*` 주석은 `*/`으로 닫히지 않으면 모두 주석처리된다.

##### 2. 자바가 지원하는 기초 자료형은?
논리형(1개):
문자형(1개):
정수형(4개): byte
실수형(2개): double

> 논리형: boolean  
> 문자형: char  
> 정수형: byte, short, int, long  
> 실수형: double, float  
> 자바의 정수형에는 unsigned 형이 없고 부호있는 정수형만 있다. (자료 구조의 단순함을 유지하기 위해)  
> int; 32비트, long; 64비트  
> 자바의 char 형은 유니코드 한 문자를 표현할 수 있는 자료형으로서 2byte 크기이다.  

##### 3. 변수명으로 적절하지 않는 것은? (모두 선택)
1. class
2. _of_workers
3. countOfLettersInString
4. 1stLevel
5. person#

> 1번, 4번, 5번이 적절하지 않다. 자바에서 변수 명명 규칙은 다음과 같다.  
> * 대소문자가 구분되며 길이에 제한이 없다.  
> * 첫 글자는 문자나 '_, $' 두 특수문자로 시작되어야 하며 숫자로 시작할 수 없다.  
> * 첫 문자가 아니라면 숫자도 사용할 수 있다.  
> * 변수명의 길이는 제한이 없고 공백은 포함할 수 없다.  
> * 예약어를 사용할 수 없다.  

##### 4. 다음과 같은 문장이 순차적으로 실행되는 경우, 실행 후 변수 값은?
```java
int x = 10, y = 2, z = 2;
z = ++x / y; // 1번 x, y, z = ?
x *= y+1; // 2번 x, y, z = ?
z = ++x + y++; // 3번 x, y, z = ?
```
>1. x = 11, y = 2, z = 5  
>2. x = 30, y = 2, z = 2  
>3. x = 11, y = 3, z = 13  

기본적으로 연산자에는 우선순위가 있으며 괄호의 우선순위가 제일 높고, 산술 > 비교 > 논리 > 대입의 순서이며 단항 > 이항 > 삼항의 순서이다. 연산자의 연산 진행 방향은 왼쪽에서 오른쪽으로 수행되며 단한 연산자와 대입 연산자의 경우에는 오른쪽에서 왼쪽으로 수행된다.


| 우선순위 | 연산자 | 내용 |
|:---:|:---:|:---:|
| 1 | `()`, `[]` | 괄호 / 대괄호 |
| 2 | `!`, `~`, `++`, `--` | 부정 / 증감 연산자 |
| 3 | `*`, `/`, `%` | 곱셈 / 나눗셈 연산자 |
| 4 | `+`, `-` | 덧셈 / 뺄셈 연산자 |
| 5 | `<<`, `>>`, `>>>` | 비트단위의 쉬프트 연산자 |
| 6 | `<`, `<=`, `>`, `>=` | 관계 연산자 |
| 7 | `==`, `!=` |  |
| 8 | `&` | 비트 단위의 논리 연산자 |
| 9 | `^` |  |
| 10 | `|` |  |
| 11 | `&&` | 논리곱 연산자 |
| 12 | `||` | 논리합 연산자 |
| 13 | `?:` | 조건 연산자 |
| 14 | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `<<=`, `>>=`, `&=`, `^=`, `~=` |대입 / 할당 연산자|

연산자 우선순위는 17년도 부터 봐왔는데도 아직 이해가 잘 안된다. ++가 =과 붙어있지 않고 뒤에 있으면 제일 마지막에 = 이후에 연산된다라고 학부때 받아들이고 넘어갔다. 감으로 파악하고 있어서 나중에 다시 한 번 자세하게 정리해야할 부분이다. 

##### 5. 다음의 코드를 컴파일 할 때, 오류 발생 이유는?
```java
public class Main{
    public static void main(String[] args){
        int x;
        System.out.println(x);
    }
}
```
> x가 특정한 값으로 초기화되지 않았기 때문이다. C에서는 초기화하지 않으면 쓰레기 값이 뜨는데 자바에서는 아예 컴파일이 진행되지 않는다.

##### 6. 다음 클래스를 컴파일 한 후 실행 결과는?
```java
public class Main{
    public static void main(String[] args){
        int[] a = {1, 2 ,3, 4, 5, 6};
        int i = a.length - 1;
        while(i >= 0){
            System.out.print(a[i] + " ");
            i--;
        }
    }
}
```
> 정답은 6 5 4 3 2 1 이다. i에 배열의 제일 끝 인덱스를 할당하고 while을 반복할 때마다 i를 감소시킨다.  
> 어려운 문제도 아니고 답도 알고 있었는데 제출한 답지에는 1 2 3 4 5 6 이라고 작성해버린걸 방금 확인했다. 아 진짜 병신도 아니고 ㅋㅋㅋ 정신차리자 항상 꼼꼼함이 부족해서 이렇게 실수를 하고 만다. 악마는 디테일에 있다는 말을 기억하자.

##### 7. 자바에서 다음 클래스를 컴파일 한 후 실행 결과는?
```java
public class Main {
    public static void main(String[] args) {
        int[] a = {12,13,32,17,56};
        int tmp = a[0];
        for(int i=1; i<a.length; i++) {
            if(a[i] > tmp) {
                tmp = a[i];
            }
        }
        System.out.println(tmp);
    }
}
```
1. 12
2. 32
3. 17
4. 56

> 정답은 56이다. for문 안의 if문을 보면 a[i]값이 현재 tmp 값보다 크면 tmp 값을 a[i]값으로 갱신시키고 있으므로 최종적으론 배열 a 내의 최대 값이 tmp에 저장될 것이다. 따라서 답은 56이 된다.

##### 8. 객체를 초기화하는 용도로 사용되는 생성자(Constructor)의 특징으로 잘못된 것은?
1. 생성자의 이름은 클래스의 이름과 동일하다.
2. 클래스 정의 시 생성자를 만들지 않으면 객체가 생성될 때 컴파일로가 자동으로 만든다.
3. 반환 값(return type)을 갖는다.
4. 객체를 새성할 때 자동으로 호출된다.

> 답은 3번이다. 생성자는 정의할 때 반환형을 작성하지 않으므로 반환 값을 가질 수가 없다.

##### 9. 접근 제어자(Access Modifier)는 클래스의 변수 또는 메소드들에 대한 접근 정도를 지정할 때 사용한다. 같은 패키지 내에서나 상속받는 경우에만 접근할 수 있는 접근 제어자는?

> 답은 protected이다. private은 해당 클래스의 메소드에서만 접근할 수 있고 public은 누구나 접근할 수 있다.

##### 10. 다음은 자바 예약어(Keyword)에 대한 설명이다. (ㄱ), (ㄴ)에 들어갈 적절한 예약어는?
* 클래스 제어자(Modifier)로 (ㄱ)이 명시되면, 다른 클래스들은 해당 클래스를 상속 받을 수 없다. 메소드의 Modifier로 (ㄱ)이 지정되면 자식 클래스는 해당 메소드를 오버라이딩할 수 없다.
* (ㄴ) 메소드가 하나 이상 존재하는 클래스는 클래스 선언부에 (ㄴ) 제어자(Modifier)가 선언되어야 한다. (ㄴ) 메소드가 존재하는 클래스를 상속받는 자식 클래스에서는 해당 (ㄴ) 메소드를 구현하거나 해당 클래스의 선언부에 (ㄴ) 제어자(Modifier)가 명시되어야 한다.
  
> (ㄱ)은 final이다. final 클래스는 변경, 확장될 수 없는 클래스가 되며 다른 클래스의 조상이 될 수 없다. final method는 변경 될 수 없는 메소드 이므로 오버라이딩을 통해 재정의 될 수 없다.  
> (ㄴ)은 abstract이다. abstract 메소드가 하나라도 존재하는 클래스는 클래스 선언부에 abstract를 선언해야 하며 abstract 클래스를 상속받은 클래스는 부모 클래스의 abstract 메소드를 구현하거나 선언부에 abstract를 작성해야 한다.

자바의 제어자와 접근 제어자에 대해 정리해보자.
1. 제어자(modifier)
클래스, 변수, 메소드의 선언부에 사용되어 부가적인 의미를 부여한다. 제어자의 종류는 다음과 같다.
```java
// 접근제어자
public, protected, default, private

// 그 외 제어자
static, final, abstract, native, transient, synchronized, volatile, strictfp
```
* static
  static이 붙은 멤버 변수와 메소드, 초기화 블럭은 인스턴스를 생성하지 않고도 사용할 수 있다.
  * static 멤버변수
  모든 인스턴스에 공통적으로 사용되는 클래스 변수가 된다.  
  클래스 변수는 인스턴스를 생성하지 않고도 사용가능하다.  
  클래스가 메모리에 로드될 때 생성된다.  
  * static 메소드
  인스턴스를 생성하지 않고도 호출이 가능한 static 메소드가 된다.  
  static 메소드 내에서는 인스턴스 멤버들을 직접 사용할 수 없다.  

* final
  * final 클래스
  변경될 수 없는 클래스. 확장될 수 없는 클래스가 된다.  
  다른 클래스의 조상이 될 수 없다.
  * final 메소드
  변경될 수 없는 메소드. 오버라이딩을 통해 재정의 될 수 없다.
  * final 멤버 변수, 지역 변수
  변경할 수 없는 상수가 된다. final이 붙은 변수는 상수이므로 보통은 선언과 초기화를 동시에 하지만, 인스턴스 변수의 경우 생성자에서 초기화 할 수 있다.

* abstract
  메소드의 선언부만 작성하고 실제 수행 내용은 구현하지 않은 추상 메소드를 선언하는데 사용한다.
  * abstract 클래스
  클래스 내에 추상 메소드가 선언되어 있음을 의미한다.
  * abstract 메소드
  선언부만 작성하고 구현부는 작성하지 않은 추상 메소드임을 알린다.

2. 접근 제어자(access modifier)
멤버 또는 클래스에 사용하며 외부에서 접근하지 못하도록 제한한다. 클래스, 멤버변수, 메소드, 생성자에 사용되고 지정되어 있지 않다면 default 임을 뜻한다.
* public: 접근 제한이 전혀 없다.
* protected: 같은 패키지 내에서, 다른 패키지의 자손클래스에서 접근이 가능하다.
* default: 같은 패키지 내에서만 접근이 가능하다.
* private: 같은 클래스 내에서만 접근이 가능하다.

##### 11. 다음 중 인터페이스에 대한 설명 중 틀린 것은?
1. 인터페이스는 다중상속의 장점인 다양한 계층구조 형성을 지원한다.
2. 인터페이스는 추상 메소드와 구현부가 있는 메소드를 가질 수 있다.
3. 하나의 클래스는 여러 개의 인터페이스를 구현할 수 있다.
4. 인터페이스는 다른 인터페이스를 상속할 수 있다.

> 답은 2번이다. 인터페이스는 기본적으로 내부의 모든 메소드가 abstract이므로 구현부를 가질 수 없다.

##### 12. 다음은 예외 처리 설명이다. 적절하지 않은 것은?
1. try: 예외 발생 가능성이 있는 소스코드 부분이 들어가는 keyword이다.
2. catch: 예외 처리 코드가 들어가는 keyword이다.
3. throws: 메소드가 호출된 곳으로 예외를 던질 때 사용하는 keyword이다.
4. finally: 예외가 발생하지 않으면 실행되지 않는다.

> 답은 4번이다. finally는 정상 작동, 예외 발생 상관 없이 최종적으로 실행된다.

##### 13. 다음이 설명하는 특징을 가진 인터페이스는?
*  key와 value의 매핑으로 관리된다.
*  key는 중복될 수 없다.
*  Collection 인터페이스를 상속하지는 않는다.
1. Set
2. Map
3. List
4. Iterator

> 답은 2번이다. Map의 가장 큰 특징은 인덱스로서 key 값을 가진다는 것이며 key 값은 중복될 수 없다. 또한 Map의 경우 Collection 인터페이스를 상속받고 있지 않지만 Collection으로 분류된다.

##### 14. 출력 결과가 나오기 위한 알맞은 소스코드는?
```java
class Person {
    String name;
    int age;
    public Person(String name, int age) {
        this.name=name;
        this.age=age;
    }
    public void show() {
        System.out.print( name + " " + age + " ");
    }
}

class Student extends Person {
    String major;
    (ㄱ) // 생성자 작성
    (ㄴ) // show() 함수 오버라이딩
}
public class Main {
    public static void main(String[] args) {
        Person p = new Student(“홍길동”, 20, “컴퓨터공학”);
        p.show();
    }
}
```

> 답은 아래와 같다.
```java
class Student extends Person {
    String major;
    public Student(String name, int age, String major) { // (ㄱ)
		super(name, age);
		this.name = name;
		this.age = age;
		this.major = major;		
	}

	@Override
	public void show() { // (ㄴ)
		System.out.println(name+" "+age+" "+major);
	}
}
```

##### 15. Scanner 클래스에 대한 설명 중 잘못된 것은?
1. Scanner 클래스는 default 생성자를 가지고 있다.
2. 생성자에 System.in 매개값을 넘겨주면 키보드로 입력한 값을 받을 수 있다.
3. next()메소드는 리턴값이 String이다.
4. java.util.Scanner 클래스를 import 해야 된다.
   
> 이 문제는 정확하게 모르겠어서 1번으로 했다. 2 ~ 4번은 맞는 이야기 이므로 1번이 아닐 것이라고 생각한다. 특히 Scanner 클래스의 경우 입력받은 값을 처리하는 클래스 이므로 System.in을 매개변수로 받기 때문에 default 생성자를 가지지 않지 않을까라고 생각했다. 답은 채점 이후에 답지를 받으면 확인해보자.

##### 16. java.lang 패키지에 속해 있으며 자바의 최상위 클래스는 무엇인가?

> 답은 Object 클래스이다.

##### 17. 다음 중 Thread와 관련된 설명이 잘못된 것은 무엇인가?
1. Thread란 하나의 프로세스를 여러 작업 단위로 나눈 것을 의미한다.
2. 자바에서 Thread를 구현하기 위해서는 Thread 클래스를 상속받거나 Runnable 인터페이스를 구현해야 한다.
3. Runnable 인터페이스를 구현할 경우 다른 클래스를 상속받을 수 있다.
4. 멀티 Thread 구현은 프로그래머가 작성하지 않고 자동으로 이루어진다.
   
> 답은 4번이다. 멀티 쓰레드는 프로그래머가 작성해야 구현된다.

##### 18. 다음은 라이브러리를 이용한 DB 연동 코드이다. (ㄱ)과 (ㄴ)에 알맞은 코드는?
```java
public class DBConn {
    private static Connection conn = null;
    public static (ㄱ) getConnection() {
        try {
            Class.forName("oracle.jdbc.driver.OracleDriver");
            conn = DriverManager.getConnection("jdbc:oracle:thin:@서버주소:포트번호:SID", "아이디", "암호");
            conn.setAutoCommit(false);
            return (ㄴ) ;
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }  
    }
}
```

> 답은 아래와 같다.
```java
public class DBConn {
    private static Connection conn = null;
    public static Connection getConnection() {
        try {
            Class.forName("oracle.jdbc.driver.OracleDriver");
            conn = DriverManager.getConnection("jdbc:oracle:thin:@서버주소:포트번호:SID", "아이디", "암호");
            conn.setAutoCommit(false);
            return conn ;
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }  
    }
}
```

##### 19. 자바의 상속 특징 중에서 틀린 것은?
1. 자바에서는 클래스의 다중 상속을 지원한다.
2. 자바에서는 상속 횟수에 대한 제한이 없다.
3. 자바에서 부모 생성자 호출은 super(매개값) 이다.
4. 자바에서 상속을 표현하는 키워드는 extends 이다.

> 답은 1번이다. 자바는 클래스의 다중 상속을 지원하지 않고 인터페이스의 다중 상속을 지원한다.

##### 20. 객체지향언의 특성 3가지는?

> 답은 캡슐화, 상속, 다형성이다. 자세한 내용은 java 스터디 첫번째 포스트에 작성해 두었다.

이번 서술 평가를 통해 생각보다 개념적인 측면에서 아직 부족한 부분이 많다는 것을 깨달았다. 주로 실습 위주로 수업을 진행하니 코드를 짜는 실력은 늘지 몰라도 개념적인 측면을 간과하고 만다는 단점이 생긴다. 그나마 매일 이렇게 포스트를 정리하면서 부족한 부분을 조금이라도 짚어갈 수 있다는 것에 위안을 가진다. 단순히 코딩 실력만 늘려선 결국 코드 몽키가 되어서 부품처럼 소비되고 말 것이다. 항상 개념적인 측면을 숙고하면서 진행하자.

> What time is it?  
> Game time! WOO!