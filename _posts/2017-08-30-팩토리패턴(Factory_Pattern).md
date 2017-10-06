---
layout: post
title:  "팩토리패턴(Factory Pattern)"
date:   2017-08-30
excerpt: "[Java Design Patterns] Factory Pattern"
tag:
- JAVA
- 디자인패턴
- HeadFirst
comments: true
---

### 팩토리패턴(Factory Pattern)

: 느슨한 결합을 이용하는 객체지향 디자인을 만들어 봅시다. 인스턴스를 만드는 작업이 항상 공개되어 있어야 하는 것은 아닙니다. 팩토리 패턴을 이용하면 불필요한 의존성을 없앨 수 있습니다.

- "new"는 "구상 객체"를 뜻합니다.
  : 인터페이스에 맞춰서 코딩을 하면 시스템에 일어나는 여러 변화를 이겨낼 수 있습니다. 다형성 덕분에 어떤 클래스든 특정 인터페이스만 구현하면 사용할 수 있기 때문이죠. 반대로 코드에 구상 클래스를 많이 사용하면 새로운 구상 클래스가 추가될 때마다 코드를 고쳐야 하기 때문에 수많은 문제가 생길 수 있습니다.

- 여러분이 피자 가게를 운영하고 있고 주문된 피자를 처리하는 메소드를 만들었다고 가정합시다.

  ```
  Pizza orderPizza(){
    Pizza pizza = new Pizza();
    
    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();
    
    return pizza;
  }
  ```

- 피자 종류를 고를 수 있다면?

  ```
  Pizza orderPizza(String type){
    Pizza pizza;
    
    if(type.equals("cheese")){
      pizza = new CheesePizza();
    }
    else if(type.equals("greek")){
      pizza = new GreekPizza();
    } else if(type.equals("pepperoni")){
      pizza = new PepperoniPizza();
    }
    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();
    
    return pizza;
  }
  ```

- 만약 피자 종류를 추가해야 한다면?
  if문을 수정해서 추가한 피자를 만들고, 제거할 피자는 삭제합니다.
  --> 코드 변경에 닫혀 있지 않습니다.

- 구상 클래스를 선택하는 부분이 문제입니다. 캡슐화해봅시다.

  ```
  public class SimplePizzaFactory {
  	public Pizza createPizza(String type){
  		Pizza pizza = null;
  		
  		if(type.equals("cheese")){
  			pizza = new CheesePizza();
  		}else if(type.equals("pepperoni")){
  			pizza = new PepperoniPizza();
  		}else if(type.equals("veggie")){
  			pizza = new VeggiePizza();
  		}
  		
  		return pizza;
  	}
  }
  ```

  - 이렇게 하면 무슨 장점이 있는 건가요? PizzaStore와 같은 것 같은데,..
    -> 다른 클래스에서도 피자를 선택할 때 가져다 쓸 수 있습니다.
  - 비슷한 식으로 팩토리를 쓰는데, 메소드를 정적 메소드로 선언한 디자인과 어떻게 다른가요?
    -> 정적 팩토리(static factory)는 객체의 인스턴스를 만들지 않아도 메소드를 이용할 수 있습니다. 하지만 서브클래스를 만들어 객체 생성 메소드의 행동을 변경시킬 수 없다는 단점이 있다는 것도 기억해두세요.


  ```
  public class PizzaStore {
  	SimplePizzaFactory factory;
  	
  	public PizzaStore(SimplePizzaFactory factory) {
  		this.factory = factory;
  	}

  	Pizza orderPizza(String type) {
  		Pizza pizza = null;

  		pizza = factory.createPizza(type);
  		
  		pizza.prepare();
  		pizza.bake();
  		pizza.cut();
  		pizza.box();

  		return pizza;
  	}
  }
  ```

- 위의 경우는 정확하게 말하면 패턴이라 할 수 없습니다. 프로그래밍을 하는 데 있어서 자주 쓰이는 관용구에 가깝다고 할 수 있죠.

- 피자 프랜차이즈 사업
  : PizzaStore 피자 가게가 큰 성공을 거둬서 인기를 끌고 나니 분점을 내기로 결정했습니다. 하지만 지역별로 조금씩 다른 차이점을 어떤 식으로 적용해야 할까요?

- SImplePizzaFactory를 빼고 세 가지 서로 다른 팩토리(NYPizzaFactory, ChiagoPizzaFactory, CalliforniaPizzaFactory)를 만든 다음, PizzaStore에서 적당한 팩토리를 사용하도록 하면 분점에서도 문제 없이 쓸 수 있을 것입니다.
  !4_pizzaland](C:\Users\2dayi\Desktop\mdFile_pic\4_pizzaland.PNG)

  ```
  public class main {
  	static void main(String[] args){
  		NYPizzaFactory nyFactory = new NYPizzaFactory();
  		
  		PizzaStore nyStore = new PizzaStore(nyFactory);
  		nyStore.orderPizza("veggie");
  	}
  }
  ```

- 그런데 문제가 생겼습니다. 분점들이 우리가 만든 팩토리를 써서 피자를 만들긴 하는데, 독자적인 방법으로 피자를 굽네요? 다시 생각하니 피자 가게와 제작과정이 하나로 묶어있지 않아서 그런 것 같습니다. 한 프레임워크로 만들고 싶습니다. 물론 유연성을 읽어버리지 않으면서요.

  ```
  public abstract class PizzaStore {

  	public Pizza orderPizza(String type) {
  		Pizza pizza;

  		pizza = createPizza(type);
  		
  		pizza.prepare();
  		pizza.bake();
  		pizza.cut();
  		pizza.box();

  		return pizza;
  	}
  	
  	abstract Pizza createPizza(String type);
  }
  ```

  피자가게를 추상클래스로 만들고 나머지 분점들이 서브클래스가 되어 지역별로 피자 스타일을 결정합니다.

  ```
  public class NYPizzaStrore extends PizzaStore {
  	
  	@Override
  	Pizza createPizza(String type) {
  		Pizza pizza = null;

  		if (type.equals("cheese")) {
  			pizza = new NYStyleCheesePizza();
  		} else if (type.equals("pepperoni")) {
  			pizza = new NYStylePepperoniPizza();
  		} else if (type.equals("veggie")) {
  			pizza = new NYStyleVeggiePizza();
  		}

  		return pizza;
  	}
  }
  ```

- 서브클래스에서 결정을 하는 과정
  : 피자를 주문하고 어떤 피자를 만들지를 서브클래스에서 결정을 한다? 그게 무슨 말이죠? NYPizzaStore에서 어떤 결정을 내리나요?

  ​

  PizzaStore의 orderPizza()메소드 입장에서 생각해봅시다. 그 메소드는 추상 클래스인 PizzaStore에 정의되어 있습니다. 그 클래스의 서브클래스를 만들기 전까지 구상클래스가 만들어지지 않죠(createPizza가 추상이기 때문에)

  좀 더 생각해보면 orderPizza()에서 Pizza 객체를 가지고 여러작업(피자를 굽고, 자르고, 포장하고)을 하지만 Pizza는 추상 클래스기 때문에 orderPizza()에서 실제로 어떤 구상 클래스에서 작업이 처리되고 있는지 전혀 알 수 없습니다. 다시 말새 PizzaStore와 Pizza는 서로 분리되어 있습니다.

  orderPizza()에서 createPizza()를 호출하면 Pizza의 서브클래스가 그 호출을 받아 피자를 만듭니다. 어떤 종류의 피자가 만들어질까요? 그건 피자를 주문하는 피자 가게에 따라 다릅니다. NYPizzaStore에서 주문을 하면 뉴욕풍 피자가 만들어질테고, ChicagoPizzaStore에서 주문하면 시카고풍 피자가 만들어지겠죠.

  그렇다면 서브클래스에서 뭔가를 실시간으로 결정하는 건가요? 그렇진 않습니다. 하지만 NYPizzaStore을 선택했다면, orderPizza()입장에서 볼 때는 그 서브클래스에서 피자 종류를 결정한다고 할 수 있을 것입니다.

- 팩토리 메소드 패턴의 정의
  : 팩토리 메소드 패턴에서는 객체를 생성하기 위한 인터페이스를 정의하는데, 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정하게 만듭니다. 팩토리 메소드 패턴을 이용하면 클래스의 인스턴스를 만드는 일을 서브클래스에게 맡기는 것이죠.

- 원칙을 지키는 데 도움이 될만한 가이드라인

  - 어떤 변수에도 구상 클래스에 대한 레퍼런스를 저장하지 맙시다.
  - 구상 클래스에서 유도된 클래스를 만들지 맙시다.
  - 베이스 클래스에 이미 구현되어 있던 매소드를 오버라이드하지 맙시다.

