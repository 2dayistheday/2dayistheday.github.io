---
layout: post
title:  "옵저버 패턴"
date:   2017-08-20
excerpt: "[Java Design Patterns] Observer Pattern"
tag:
- JAVA
- 디자인패턴
- HeadFirst
comments: true
---

### [옵저버 패턴(Observer Pattern)](http://hyeonstorage.tistory.com/165)

: 뭔가 중요한 일이 일어났을 때 객체들한테 새소식을 알려줄 수 있는 패턴

***

-  WeatherData 객체는 현재 기상 조건(기온, 습도, 기압)을 추적하는 객체. 최신 측정치를 수집할 때마다 실시간으로 갱신됩니다.

  ```
  public class WeatherData {
  	float getTemperature(){
  		//최근에 측정된 온도 반환
  	}
  	float getHumidity(){
  		//최근에 측정된 습도 반환
  	}
  	float getPressure(){
  		//최근에 측정된 기압 반환
  	}
  	public void measurementsChanged(){
  		//기압관측값이 갱신될 때마다 알려주기 위한 메소드
  		float temp = getTemperature();
  		float humidity = getHumidity();
  		float pressure = getPressure();
  		
  		currentConditionsDisplay.update(temp, humidity, pressure);
  		statisticsDisplay.update(temp, humidity, pressure);
  		forecastDisplay.update(temp, humidity, pressure);
  	}
  }
  ```

  위의 코드로 설명 중 옳은 것은

  1. **인터페이스가 아닌 구체적인 구현을 바탕으로 코딩을 하고 있습니다.**
  2. **새로운 디스플레이 항목이 추가될 때마다 코드를 변경해야 합니다.**
  3. **실행중에 디스플레이 항목을 추가/제거할 수가 없습니다.**
  4. 디스플레이 항목들이 공통적인 인터페이스를 구현하지 않습니다.
  5. **바뀌는 부분을 캡슐화하지 않았습니다.**
  6. WeatherData 클래스를 캡슐화하지 않고 있습니다.

- 신문이나 잡지를 어떤 식으로 구독하는지는 아시죠?

  1. 신문사가 사업을 시작하고 신문을 찍어내기 시작합니다.
  2. 독자가 특정 신문사/잡지사에 구독 신청을 하면 매번 새로운 신문/잡지가 나올 대마다 배달을 받을 수 있습니다. 계속 구독자로 남아있는 이상 계속해서 신문/잡지를 받을 수 있습니다.
  3. 신문을 더 이상 보고 싶지 않으면 구독 해지 신청을 합니다. 그러면 더 이상 신문이 오지 않습니다.
  4. 신문사가 계속 영업을 하는 이상 여러 개인 독자, 호텔, 항공사 및 기타 회사 등에서 꾸준히 구독 및 해지를 하게 됩니다.

  출판사+구독자 = 옵저버 패턴
  : 신문 구독 메커니즘만 제대로 이해할 수 있다면 옵저버 패턴을 쉽게 이해할 수 있습니다. 출판사를 주제(subject), 구독자를 옵저버(observer)라고 부른다는 것만 외워두세요.

  **옵저버 패턴(Observer Pattern)**에서는 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다(one-to-many) 의존성을 정의합니다.

-  옵저버 패턴의 클래스 다이어그램

   ![2_observer_pattern](/Users/todayistheday/Documents/스터디\mdFile_pic\2_observer_pattern.PNG)


> 디자인 원칙 : 서로 상호작용을 하는 객체 사이에서는 가능하면 느슨하게 결합하는 디자인을 사용해야 한다.

- 느슨한 결합(Loose Coupling)의 위력

  : 두 객체가 느슨하게 결합되어 있다는 것은, 그 둘이 상호작용을 하긴 하지만 서로에 대해 서로 잘 모른다는 것을 의미합니다.

- 옵저버 패턴에서는 주제와 옵저버가 느슨하게 결합되어 있는 객체 디자인을 제공합니다.
  : 주제가 옵저버에 대해 아는 것은 옵저버가 특정 인터페이스(Observer 인터페이스)를 구현한다는 것 뿐입니다. 옵저버는 언제든지 새로 추가할 수 있습니다. 새로운 형식의 옵저버를 추가하려고 할 때도 주제를 전혀 변경할 필요가 없습니다. 주제아 옵저버는 서로 독립적으로 재사용할 수 있습니다. 주제나 옵저버가 바뀌더라도 서로한테 영향을 미치지는 않습니다.

- 기상 측정 시스템을 만들어 볼까요?
  ![2_observer_pattern_2](/Users/todayistheday/Documents/스터디\mdFile_pic\2_observer_pattern_2.PNG)

- 기존의 java.util.observable를 사용하여 weatherData프로젝트를 완성해봅시다.

- JDK에서 옵저버 패턴을 쓰는 부분
  : 옵저버 패턴을 쓰는 부분은 java.util에 들어있는 Observable/Observer 뿐이 아닙니다. JavaBeans와 Swing에서도 이 패턴을 구현한 것을 제공합니다.
  listener가 옵저버라고 생각하면 됩니다. 스윙의 이벤트를 감시하는 이스너가 있습니다.  버튼을 누르는 것을 감시...

  ```
  package mk.pattern.useobserver.ex;

  import java.awt.BorderLayout;
  import java.awt.event.ActionEvent;
  import java.awt.event.ActionListener;

  import javax.swing.JButton;
  import javax.swing.JFrame;

  public class SwigObserverExample {
  	JFrame frame;
  	public static void main(String[] args){
  		SwigObserverExample ex = new SwigObserverExample();
  		ex.go();
  	}
  	
  	public void go(){
  		frame = new JFrame();
  		JButton button = new JButton("정말 해도 될까?");
  		button.addActionListener(new AngelListener());
  		button.addActionListener(new DevilListener());
  		frame.getContentPane().add(BorderLayout.CENTER, button);
  		//프레임 속성 설정
  		frame.setSize(200,200);
  		frame.setVisible(true);
  	}
  	
  	class AngelListener implements ActionListener{
  		public void actionPerformed(ActionEvent event){
  			System.out.println("안돼. 분명 나중에 후회할꺼야.");
  		}
  	}
  	class DevilListener implements ActionListener{
  		public void actionPerformed(ActionEvent event){
  			System.out.println("당연하지. 그냥 저질러 버려!");
  		}
  	}
  }
  ```

  ​


