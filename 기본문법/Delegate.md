# Protocol 과 Delegate 패턴이란? ✏

Delegate 패턴을 이해하기 위해 우선 프로토콜을 알아야 해요!

### Protocol
기본적으로 **프로토콜**을 채택하여 해당 **프로토콜**에서 선언된 함수 혹은 변수를 override 해서 사용한다고 생각 하시면 되요 :)   
예를 들어 볼게요!   
Person 이라는 protocol 이 있다고 가정을 해볼게요!   
  ```
  protocol Person{
	var name:String{ get set }
	var age:Int{ get }
	var address:String { get }
	var car:String?{ get set }
	
	func eat()
	func sleep()
	func work()
  }
  ```   
  
다음과 같은 **Person protocol**이 있을 때 **name,age,address,car** 라는 프로퍼티가 있습니다.   
또한 자동차는 있을수도 있고 없을 수도 있기 때문에 Optional로 지정을 합니다.   

일단 여기서 get 과 set 을 살펴 볼게요

address 같은 경우는 ```{ get }``` 만 있기 때문에   ``` {get} ``` 혹은   ``` {get set} ``` 둘중에 하나만 값을 주셔도 됩니다.   
  ```
	class PersonA:Person{
	
		var mAddress:String = ""
		
		var address:String{
			get:{
				return self.mAddress
			}
		
			set:{
				self.mAddress = newValue
			}
		}
		
	
	}
  ```   

Person이란 프로토콜을 **채택**을 이렇게 했어요!   
그리고 get 과 set 을 지정을 할 수 있어요! 물론 set 을 빼버려도 상관은 없지만 set 만 구현을 할 수는 없습니다!   

두번 째로 name 인데요 name 같은 경우는 get 과 set 이 모두 있기 때문에 둘다 구현을 해주셔야 합니다!   
  ```
	class PersonA:Person{
	
		var mName:String = ""
		
		var name:String{
			get:{
				return self.mName
			}
		
			set:{
				self.mName = newValue
			}
		}
		
	
	}
  ```   

그리고 car 같은 경우에는 프로토콜에서 optional 로 지정이 되어있어서 optional 로 리턴이 됩니다!   

나머지 프로토콜에 선언된 함수를 구현을 해줘야 해요   

  ```
	class PersonA:Person{
		
	func eat(){
		print("eat")
	}
	func sleep(){
		print("sleep")
	}
	func work()
		print("work")
	}
  ```   
  
### 그래서 Delegate 패턴이 뭐냐?
위에서 사용한 Person 의 프로토콜로 계속 예를 들어 볼게요!   

  ```
	class PersonView:UIView{
		@IBOutlet weak private var button: UIButton!
    
		var delegate: Person?
    
		@IBAction private func buttonClick(_ sender: AnyObject){
			delegate?.eat()
			delegate?.sleep()
			delegate?.work()
		}
	}
  ```   
 이런 UIView 가 하나 있다고 가정을 해봅시다. Person 의 delegate를 통해 버튼 이벤트가 발생했을 때 처리 해야할 일의 목록들을 지정합니다!   
 그 다음은 ViewContoller 입니다.   
 
   ```
	class PersonViewController:UIViewController, Person{ //Person delegate를 채택!
		var personView: PersonView?
    
		override func viewDidLoad(){
			super.viewDidLoad()
    
			personView = PersonView()
			personView?.delegate = self
		}
    
		func eat(){
			print("eat")
		}
    
		func sleep(){
			print("sleep")
		}
		
		func work(){
			print("work")
		}
	}
  ```   
PersonViewController 에서는 delegate를 채택하고, 그에 맞는 함수들을 구현 해줍니다.
 