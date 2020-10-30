# Class VS Stuct✏


### Class 와 Struct 의 공통점

1. 메서드와 각각의 프로퍼티가 존재한다.
2. 사용자의 타입을 만든다.
3. Protocol 사용가능하다.
4. 이니셜라이저(init) 으로 초기값을 할당 할 수 있다.

### Class 와 Struct 의 차이점

차이점을 설명하기에 앞서 Refrence Type 과 Value Type에 대한 이해가 필요해요~   
   
 * Refrence?
  Refrence는 객체를 가르키고있는 메모리 주소값이라고 하면 설명이 쉬울듯 하네요.   
  예를 들어 A 라는 객체가 있을 때 A 객체의 메모리 주소값을 참조한다! 라고 할 떄 Refrence 라는 표현이 적절하겠네요.   
  
  * Value?
  우리가 흔히들 생각하는 실제 값 타입 이라고 생각하시면 됩니다.   
  Refrence는 메모리 주소값을 참조하지만 Value 같은 경우에는 실제 값을 가지고 있어요.   
  ```
	let a = 1 //1
  ```
  위의 코드 처럼 a 에는 1 이라고 하는 값이 있으니, 이 때는 value 라고 쓰는게 적절합니다.   
  이 부분에서 Class 와 Struct가 어떤 차이점이 있는지 진 면목이 드립니다.   
  
  Class 는 Refrence Type 이고 Struct 는 Value Type 입니다.   
  
  ```
	struct SampleStruct{
		var name:String
		init(name:String){
			self.name = name
		}
	}
	
	var imStruct1 = SampleStruct(name:"test_user_1")
`	var imStruct2 = imStruct1
	imStruct2.name = "test_user_2" // imStruct2 에다가 test_user_3 이라는 값을 대입
	
	print(imStruct1) // test_user_1
	print(imStruct2) // test_user_3
  ```
  
  위와 같이 코드를 수정 작성하였을 떄 imStruct1 의 결과 값은 다들 생각하셨듯이 test_user_1 이 나옵니다.   
  그리고 imStruct2의 결과 값은 test_user_2 이 나오겠지요?   
  struct를 생성하고 값을 copy 후 바꾸니 각각의 데이터는 독립적으로 존재합니다.   
  하지만 다음의 코드는 이야기가 조금 다릅니다.
  
  ```
	class SampleClass{
		var name:String
		init(name:String){
			self.name = name
		}
	}
	
	var imClass1 = SampleClass(name:"test_user_1")
	var imClass2 = imClass2
	imClass2.name = "test_user_2"
	
	print(imClass1) //test_user_2
	print(imClass2) //test_user_2
  ```
  
  희안하게 imClass1과 imClass2의 값이 같습니다. 왜냐하면 Class 는 Refrence Type이기 때문입니다.   
  그래서 imClass2가 변경이 되면 imClass1도 같은 메모리에 있는 객체를 참조하고 있기때문에 imClass1의 값도 바뀌는 것 입니다.   

### 어떤경우에 Struct Class 를 사용할까?
  [애플공식 문서](https://github.com/CKANYWHERE/noteswift/blob/main/%EA%B8%B0%ED%83%80/AlamoFire%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC.md) 에따른 내용으로는,
  
  * Struct
	1. object 두개의 값을 비교해야하는 경우
	2. 각 객체의 값을 독립적으로 가져야 할 때
	3. 오브젝트의 데이터를 여러 스레드가 접근하여 사용하는 경우
	
  * Class
	1. 하나의 객체를 필요로하고, 인스턴스 자체가 같은지 확인해야 할 떄
	2. 두 객체의 인스턴스 자체가 같음을 확인할 때
  

  