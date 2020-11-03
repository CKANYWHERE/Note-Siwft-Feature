# 내겐 넘나어려운 ARC ✏

## ARC(Auto Reference Counting)
iOS는 메모리 관리를 위하여 ARC 라는 기능을 사용합니다.   
ARC는 말그대로 **참조 메모리를 자동으로 관리 해주는 기능**을 말합니다!   
이떼 새로운 인스턴스가 참조되거나 참조가 끝날 때 Counting을 변경 시키는데 Counting이 0이 되는 순간 인스턴스를 메모리에서 해제 시킵니다.   

GC(Garbage Collector)와 한번 비교를 해보겠습니다.   

* GC
 1. 인스턴스 해제가 될 확률이 ARC 보다 비교적 높음.
 2. 규칙을 딱히 신경쓰지 않아도 됨.
 3. 개발자가 메모리 시점을 예측하기 어려움(garbage collector 가 자동으로 돌기 때문)
 4. Run time 중에 계속 추적해야해서 성능이 나빠질 수 있음.
 
* ARC
 1. 컴파일시 메모리 해제 시점이 결정되어서 대략적으로 언제 해제되는지 알 수 있음.
 2. run time에 메모리를 해제하기 위해 추가 리소스를 사용안해도 됨.
 3. 개발자의 실수로 인해 인스턴스가 메모리에서 영원히 해제되지 않을 수 있음.
 4. 3번의 이유로 ARC의 동작을 알고 코드를 작성하는데 신중 해야함.
 

## Counting

코드를 통해서 이해를 한번 해볼게요!   
  ```
 	class SampleClass{
		var name:String
		init(name:String){
			self.name = name
		}
		deinit{
			print("\(name) 이 헤제되었습니다.")
		}
	}
  ```
 
 다음과 같이 SampleClass를 선언하고 메모리에 올라올때 name에 값을 셋팅해주고, 메모리에 해제 될때 출력해주는 코드를 적어봤습니다.   
   ```
	var a:SampleClass?
	var b:SampleClass?
	var c:SampleClass?
	
	a = SampleClass("test_user_1") //ARC : 1
	b = a //ARC:2
	c = a //ARC:3
  ```
  
 참조 카운팅은 class 와 같은 참조 타입 값을 변수에 할당할 때 증가합니다.   
 class 를 변수에 할당 할때는 heap 메모리의 주소값을 변수에 할당하는 작업이 일어나요(참고로 이 때 a 변수에는 SampleClass의 인스턴스가 강한참조로 연결됩니다).   
 현재 SampleClass를 강한참조로 연결된 부분이 3개 이기 때문에 카운팅은 3이 됩니다.   
 만약에 여기서   
 
   ```
    a = nil
	b = nil
   ```
 
 다음과 같이 코드를 작성하면, 참조는 1개를 하고 있습니다. 따라서 메모리가 해제 되지도 않죠  
 만약에 참조를 해제 시켜버리려면
   ```
	c = nil // test_user_1 이 헤제되었습니다.
   ```
  다음과 같이 출력이 될 겁니다.   
  또한 그 순간 같이 SampleClass의 인스턴스가 메모리에서 할당이 해제 됩니다.   
  
## Strong Reference Cycle
  아마 눈치가 빠르신 분들이라면 이런 질문을 할 수 있어요.   
  객체가 또다른 객체를 참조하면 어떻게 하나요?   
  또한번 코드로 예를 한번 들어 볼게요!   
    ```
 	class SampleClass{
		var name:String?
		init(name:String){
			self.name = name
		}
		deinit{
			print("\(name) 이 헤제되었습니다.")
		}
	}
	class ReferenceClass{
		var unit: String
		var refer: SampleClass?
		init(unit: String) { self.unit = unit }
		deinit{
			print("메모리에서 해제 되었습니다.")
		}
	}
	var a: SampleClass?
	var b: ReferenceClass?
	
	a = SampleClass("test_user_1")
	b = ReferenceClass(unit: "test_user_2")
	b.refer = a
    ```
이 때 각각의 SampleClass와 ReferenceClass에 인스턴스를 할당 해줍니다!   

그러면 결국에 SampleClass가 생성이 되고 SampleClass를 참조하는 ReferenceClass가 생성이 되겠죠?   

이럴 때 바로 Strong Reference Cycle로 연결이 되는건데, 
    ```
	a = nil
	b = nil
    ```
 다음과 같이 참조를 해제 시켜도 인스턴스 할당을 해제 시키지 않습니다.   
 이를 해제 하려면 직접 b를 참조헤서 메모리를 해제를 시켜야 하는데 이미 b 는 해제가 된 상태여서 메모리가 해제 되지 않습니다.
 이러한 strong reference cycle을 해결하기 위한 방법으로는 weak, unowned 참조가 있는데 이건 나중에 설명 드릴게요 :)
 