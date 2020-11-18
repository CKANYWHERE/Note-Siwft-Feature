# 메모리 참조? ✏

## 메모리를 참조하는 여러 방법
저번에 제가 ARC 관련해서 설명을 적었던 적이 있었어요.. 그 때 제가 Strong과 Weak 에 대햐여 설명을 해주리라 했는데 이제 설명을 적네요 :)   
Strong Reference Cycle에 관련하여서는 저번에 설명을 드렸지만 이번에 한번더 설명을 드릴게요!

## Strong
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
저번에 사용했던 코드입니다. ~~코드를 새로 짜기가 너무 귀찮았습니다 죄송합니다 ㅋㅋㅋㅋ~~   
자세히 보시면 b 라는 객체는 ReferenceClass의 클래스로 가지고 있는데 
```
	b.refer = a
```
이렇게 b.refer 에 a 를 바로 값을 할당 해버렸다고 가정을 하고, 
```
	b = nil
```
b 에 nil 값을 넣어 버리면 a 라는 객체가 메모리에 계속 남아있게 되고 해당 메모리를 해제하려 해도 이미 b 는 nil 이 되어버려 메모리에 없는 상태라 memory leak 이 발생 하게 됩니다.   
이걸 쉽게 해결하는 방법이 weak 참조 입니다.   

## Weak
Weak 신기한게 retain count를 증가 시키지 않아요! 하지만 객체는 참조를 해요.   
Strong 과 비교를 해보면 Strong 은 객체를 참조하고 retain count를 증가 시킵니다!   
앞에서 본 코드에서 ReferenceClass 만 수정을 해볼게요!   
```
  	class ReferenceClass{
		var unit: String
		weak var refer: SampleClass?
		init(unit: String) { self.unit = unit }
		deinit{
			print("메모리에서 해제 되었습니다.")
		}
	}
```
Strong Retain Count 를 설명할때는 refer의 값을 그냥 var 로 줘 버렸고, 여기서는 weak var로 줘 버렸어요!   
```
  	var a: SampleClass?
	var b: ReferenceClass?
	
	a = SampleClass("test_user_1")
	b = ReferenceClass(unit: "test_user_2")
	
	b.refer = a
	b = nil //객체가 생성되지만 weak 이기 때문에 바로 객체가 해제 되어 nil 이됨.
```
마찬가지로 다음과 같이 코드를 작성 했어요!   
아까 말씀 드렸듯이 Weak 같은 경우에는 retain count를 증가시키지 않습니다.   
그래서 b 가 nil 이 되면 b.refer 에 a 값을 넣어줘도 깔끔하게 메모리에서 해제가 됩니다.   
그렇다면 unowned 는 뭘까요?   

## Unowned
사실은 weak 이랑 비슷해요, retain count를 증가 시키지 않고, 미소유 참조로써 참조를 하게 됩니다.   
```
  	class ReferenceClass{
		var unit: String
		unowned var refer: SampleClass
		init(unit: String) { self.unit = unit }
		deinit{
			print("메모리에서 해제 되었습니다.")
		}
	}
```
그렇다고 weak 과 unowned는 같은게 아닙니다.   
일반적으로 weak 은 optional 일때 사용하고 unowned는 optional 이 아닐때 사용한다고 합니다. 왜그러냐?   
**Weak 같은 경우에는 객체를 추적하면서 객체가 사라지면 nil 로 되지만 Unowned 같은 경우에는 객체가 사라지면 더미 값이 남습니다.**   
**이 더미 값이 남을 때 재 참조를 하게 되면 크러시가 발생 합니다. 그래서 unowned는 객체가 사라지지 않는것이 보장 될때만 사용을 해야 합니다.**   

그러면 더 안전하게 사용하려면 weak 만쓰면 되는거 아닌가요?   
굳이 상관은 없지만 weak 같은 경우에는 객체를 계속 추적해서 런타임때 오버헤드라 확실한 객체에는 Unowned를 사용합니다.
