---
layout: post
title:  "JAVA Collection Framework"
date:   2017-09-13
excerpt: "자바 컬렉션"
tag:
- JAVA
- class
- Collection
comments: true
---

### JAVA Collection Framework

: 데이터의 저장, 이와 관련 있는 알고리즘을 구조화 해 놓은 프레임워크입니다.

- 자료구조의 정형화된 데이터의 저장방식

  - 배열(Array), 리스트(List), 스택(Stack), 큐(Queue), 트리(Tree), 해시(Hash)

- 알고리즘

  - 정렬(Sort), 탐색(Search), 최대(Max), 최소(Min) 검색

- 컬렉션 프레임워크의 인터페이스 구조(기본 골격)

  ![5_coll_interface](/assets/img/data/5_coll_interface.png)

<E>, <K, V>는 모든 인터페이스가 제네릭으로 정의되었음을 표현한다. 대부분은 java.util 패키지에 묶여있다.

![5_collection](/Users/todayistheday/Documents/스터디/mdFile_pic/5_collection.jpg)

- List<E> 인터페이스와 이를 구현하는 제네릭 클래스 ArrayList<E>, LinkedList<E>

  - 특징1: 동일한 인스턴스의 중복 저장을 허용한다.

  - 특징2: 인스턴스의 저장 순서가 유지된다.

    ```
    ArrayList<Integer> list = new ArrayList<Integer>();
    LinkedList<Integer> list2 = new LinkedList<Integer>();

    list.add(new Integer(3));
    list.remove(0);

    list2.add(new Integer(3));
    list2.remove(0);
    ```

  - ArrayList<E>와 LinkedList<E>의 차이점
    : 내부적으로 인스턴스를 저장하는 방식에서 큰 차이를 보인다. ArrayList는 배열을 기반으로 한다. 내부적으로 배열을 이용해서 인스턴스의 참조 값을 저장하기 때문에 특징이 있다.

    - 단: 저장소의 용량을 늘리는 과정에서 많은 시간이 소요된다.
    - 단: 데이터의 삭제에 필요한 연산과정이 매우 길다
    - 장: 데이터의 참조가 용이해서 빠른 참조가 가능하다.

  - LinkedList<E>의 특성

    - 장: 저장소의 용량을 늘리는 과정이 매우 간단하다.
    - 장: 데이터의 삭제가 매우 간단하다.
    - 단: 데이터의 참조가 다소 불편하다.

- Iterator를 이용한 인스턴스의 순차적 접근
  : iterator 메소드가 호출되면 인스턴스가 하나 생성되는데, 이 인스턴스는 Iterator<E> 인터페이스를 구현하는 클래스의 인스턴스야! 그리고 iterator 메소드는 이 인스턴스의 참조 값을 반환해!

  - Boolean hasNext()	: 참조할 다음 번 요소(element)가 존재하면 true를 반환
  - E next()                           : 다음 번 요소를 반환
  - void remove()               : 현재 위치의 요소를 삭제

  ```
  LinkedList<String> list = new LinkedList<String>();
  list.add("First");
  list.add("Second");
  list.add("Third");

  Iterator<String> itr = list.iterator();

  while(itr.hasNext())
  {
    String curStr = itr.next();
    System.out.println(curStr);
    if(curStr.compareTo("Third")==0)
    	itr.remove();
  }

  itr = list.iterator();
  while(itr.hasNext())
  {
   	System.out.println(itr.next());
  }
  ```

  - For-each문의 등장으로 while문은 다음처럼 구성할 수도 있다.

    ```
    for(String str : list)
    	System.out.println(str);
    ```

  - 특장점: 컬렉션 클래스의 종류에 상관없이 동일한 형태의 데이터 참조방식을 유지한다.
