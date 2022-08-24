





# 헤드퍼스트 디자인패턴 개정판





# #1 디자인 패턴 소개와 전략 패턴

## 적용이 필요한 곳

## UML

![1-1](/Users/a10300/Choi/Git/dev-note/etc/images/headfirst-design-pattern-revised-edition/1-1.png)

# #2 옵저버 패턴

## 적용이 필요한 곳

## UML

![2-1](./images/headfirst-design-pattern-revised-edition/2-1.png)

## 구현

```java

public interface Subject {
	public void registerObserver(Observer o);
	public void removeObserver(Observer o);
	public void notifyObservers();
}

// Subject 하위
public class WeatherData implements Subject {
	private List<Observer> observers;
	private float temperature;
	private float humidity;
	private float pressure;
	
	public WeatherData() {
		observers = new ArrayList<>();
	}
	
	public void registerObserver(Observer o) {
		observers.add(o);
	}
	
	public void removeObserver(Observer o) {
		observers.remove(o);
	}
	
	public void notifyObservers() {
		for (Observer observer : observers) {
			observer.update(temperature, humidity, pressure);
		}
	}
	
	public void measurementsChanged() {
		notifyObservers();
	}
	
	public void setMeasurements(float temperature, float humidity, float pressure) {
		this.temperature = temperature;
		this.humidity = humidity;
		this.pressure = pressure;
		measurementsChanged();
	}

}

```

```java
public interface Observer {
	public void update(float temp, float humidity, float pressure);
}

// Observer 하위 
public class ForecastDisplay implements Observer, DisplayElement {
	private float currentPressure = 29.92f;  
	private float lastPressure;
	private WeatherData weatherData;

	public ForecastDisplay(WeatherData weatherData) {
		this.weatherData = weatherData;
		weatherData.registerObserver(this);
	}

	public void update(float temp, float humidity, float pressure) {
        lastPressure = currentPressure;
		currentPressure = pressure;

		display();
	}
}

// Observer 하위 
public class HeatIndexDisplay implements Observer, DisplayElement {
	float heatIndex = 0.0f;
	private WeatherData weatherData;

	public HeatIndexDisplay(WeatherData weatherData) {
		this.weatherData = weatherData;
		weatherData.registerObserver(this);
	}

	public void update(float t, float rh, float pressure) {
		heatIndex = computeHeatIndex(t, rh);
		display();
	}
}

```



```java

// 실제로 사용하기
public class WeatherStation {

	public static void main(String[] args) {
		WeatherData weatherData = new WeatherData();
	
		CurrentConditionsDisplay currentDisplay = 
			new CurrentConditionsDisplay(weatherData);
		StatisticsDisplay statisticsDisplay = new StatisticsDisplay(weatherData);
		ForecastDisplay forecastDisplay = new ForecastDisplay(weatherData);

		weatherData.setMeasurements(80, 65, 30.4f);
		weatherData.setMeasurements(82, 70, 29.2f);
		weatherData.setMeasurements(78, 90, 29.2f);
		
		weatherData.removeObserver(forecastDisplay);
		weatherData.setMeasurements(62, 90, 28.1f);
	}
}

```





## 정리

- 옵저버 패턴은 객체들 사이에 일대다 관계를 정의한다.
- Subject는 동일한 인터페이스를 써서 옵저버에게 연락을 한다.
- Observer인터페이스를 구현하기만 하면 어떤 구상 클래스의 옵저버라도 패턴에 참여할 수 있다.
- Subject는 옵저버들이 Observer 인터페이스를 구현한다는 것을 제외하면 옵저버에 관해 전혀 모른다. (느슨한 결합)
- 옵저버 패턴을 사용하면 Subject가 데이터를 보내거나 (push) 옵저버가 데이터를 가져올(pull) 수 있다. 일반적으로 pull 방식이 더 적합한 경우가 많다.
- 여러 라이브러리, 프레임워크에서 옵저버 패턴을 많이 사용하고 있다.
- 옵저버 패턴은 pub/sub 패턴과 매우 유사하다.

# #3 데코레이터 패턴

- 우리의 목표는 기존 코드를 건드리지 않고 확장으로 새로운 행동을 추가하는 것이다. OCP
- 데코레이터 패턴 정리
  - 객체에 추가 요소를 동적으로 더할 수 있다.
  - 데코레이터를 사용하면 서브클래스를 만들 때보다 훨씬 유연하게 기능을 확장할 수 있다.

## UML

![3-1](./images/headfirst-design-pattern-revised-edition/3-1.png)



## 구현

```java
public abstract class Beverage {
   String description = "Unknown Beverage";
  
   public String getDescription() {
      return description;
   }
 
   public abstract double cost();
}

// Beverage 하위
public class Decaf extends Beverage {
	public Decaf() {
		description = "Decaf Coffee";
	}
 
	public double cost() {
		return 1.05;
	}
}

// Beverage 하위

public class Espresso extends Beverage {
  
	public Espresso() {
		description = "Espresso";
	}
  
	public double cost() {
		return 1.99;
	}
}

```



```java
public abstract class CondimentDecorator extends Beverage { // Beverage를 구현하고
   Beverage beverage; //Beverage를 갖는게 데코레이터 특징
   public abstract String getDescription();
}

// CondimentDecorator 하위
public class Milk extends CondimentDecorator {
	public Milk(Beverage beverage) {
		this.beverage = beverage;
	}

	public String getDescription() {
		return beverage.getDescription() + ", Milk";
	}

	public double cost() {
		return .10 + beverage.cost();
	}
}

// CondimentDecorator 하위
public class Mocha extends CondimentDecorator {
	public Mocha(Beverage beverage) {
		this.beverage = beverage;
	}
 
	public String getDescription() {
		return beverage.getDescription() + ", Mocha";
	}
 
	public double cost() {
		return .20 + beverage.cost();
	}
}

```

```java

// 실제로 사용하기
public class StarbuzzCoffee {
 
	public static void main(String args[]) {
		Beverage beverage = new Espresso();
		System.out.println(beverage.getDescription() 
				+ " $" + beverage.cost());
 
		Beverage beverage2 = new DarkRoast();
		beverage2 = new Mocha(beverage2); // Mocha(DarkRoast)
		beverage2 = new Mocha(beverage2); // Mocha(Mocha(DarkRoast))
		beverage2 = new Whip(beverage2); // Whip(Mocha(Mocha(DarkRoast)))
		System.out.println(beverage2.getDescription() 
				+ " $" + beverage2.cost());
 
		Beverage beverage3 = new HouseBlend();
		beverage3 = new Soy(beverage3);
		beverage3 = new Mocha(beverage3);
		beverage3 = new Whip(beverage3);
		System.out.println(beverage3.getDescription() 
				+ " $" + beverage3.cost());
	}
}

```



# #4 팩토리 패턴



# #5 싱글턴 패턴



# #6 커맨트 패턴



# #7 어댑터 패턴과 퍼사드 패턴



# #8 템플릿 메소드 패턴



# #9 반복자 패턴과 컴포지트 패턴



# #10 상태 패턴



# #11 프록시 패턴



# #12 복합 패턴



# #13 실전 디자인 패턴



# #14 기타 패턴