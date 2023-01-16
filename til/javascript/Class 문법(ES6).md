> class문법에 대해 정리한다.

## Class 문법
Class는 객체를 생성하기 위한 템플릿으로 클래스는 데이터와 이를 조작하는 코드를 하나로 추상화한다. 

### 선언식과 표현식
```javascript
// 클래스 선언식
class Animal {
	constructor(name, say) {
		this.name;
		this.say;
	}
}

// 클래스 표현식
const Animal = class {
	constructor(name, say) {
		this.name;
		this.say;
	}
}
```
* 호이스팅

	클래스는 호이스팅 시에 초기화를 진행하지 않아 선언 전에 호출하는 것이 불가능하다.

* strict mode
	클래스의 본문은 stric mode에서 실행된다. 즉, 본문 안에는 엄격한 문법이 적용된다.

### constructor와 메서드
* constructor

	constructor는 class의 인스턴스 객체를 생성하고 초기화하기 위한 특별한 메서드이다. <br>
	constructor는 부모 클래스의 constructor를 호출하기 위해 `super`키워드를 사용할 수 있다.
  * constructor는 class문법애서 한번만 사용할 수 있다. 여러 개가 한 클래스안에 존재하면, `SyntaxError` 가 발생한다.
	```javascript
	// 클래스에서 생성자를 정의하지 않으면 기본 생성자를 사용한다. 상속하지 않는 기본 클래스의 경우는 빈 메서드
	constructor() {}
	// 다른 클래스를 상속하는 경우 기본 생성자는 매개변수를 부모 클래스의 constructor로 전달
	constructor(...args) {
		super(...args);
	}
	// 파생 클래스에 직접 생성자를 정의할경우, 부모 클래스의 생성자를 호출 하려면 super()를 사용해야 한다.
	class Square extends Polygon {
		constructor(width, height) {
			super(width, height)
			this.name = 'square'
		}
	}  
	```

* 메서드
	```javascript
	class Animal {
		constructor(name, say) {
			this.name;
			this.say;
		}
		// 메서드
		speak() {
		 return	`${this.name} 가 ${this.say} 하고 운다.`;
		}
	}

	const ramen = new Animal(dog, 'woof woof');

	console.log(ramen.cooking()); // dog 가 woof woof 하고 운다.
	```

* static 키워드
	static 키워드로 메서드나 속성을 정의하면 메서드는 클래스의 인스턴스화 없이 호출되며, 인스턴스에서는 호출할 수 없다. 주로 어플리케이션을 위한 유틸리티 함수를 생성하는 데 사용된다.

	속성은 캐시, 고정 환경설정 또는 인스턴스 간에 복제할 필요가 없는 기타 데이터에 유용하다.
  ```javascript
  class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  static displayName = "Point";
  static distance(a, b) {
    const dx = a.x - b.x;
    const dy = a.y - b.y;

    return Math.hypot(dx, dy);
    }
  }

  const p1 = new Point(5, 5);
  const p2 = new Point(10, 10);
  p1.displayName; // undefined
  p1.distance;    // undefined
  p2.displayName; // undefined
  p2.distance;    // undefined

  console.log(Point.displayName);      // "Point"
  console.log(Point.distance(p1, p2)); // 7.0710678118654755
	```

### extends 클래스 상속
* extends키워드는 클래스 선언식이나 표현식에서 다른 클래스의 자식 클래스를 생성하기 위해 사용된다.	

* 사용자 정의 클래스의 자식 클래스를 만드는 것 뿐만 아니라 내장 객체의 하위 클래스를 만드는 데에도 사용 할 수 있다.
* `super` 키워드는 부모가 가지고 있는 메서드를 호출하기 위해 사용된다.
```javascript
class Animal {
  constructor(name, say) {
    this.name = name;
		this.say = say;
  }

  speak() {
    return	`${this.name} 가 ${this.say} 하고 운다.`;
  }
}

class Dog extends Animal {
  constructor(name, say) {
    return super(name, say); // super class 생성자를 호출하여 name 매개변수 전달
  }

  speak() {
    super.speak();
  }
}

let d = new Dog('Mitzie', 'woof woof');
d.speak(); // Mitzie barks 가 woof woof 하고 운다

```

___

## 참고 사이트

MDN-Classes
* https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes

MDN-super
* https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/super

MDN-extends
* https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes/extends

MDN-constructor
* https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes/constructor