# Observer 와 Observable의 차이 ✏ 
RX를 처음 사용할 때 헷갈릴 수도 있고, Type도 생각보다 꽤 많아서 어떤것을 사용해야 할지 헷갈릴 수도 있어요.   
쉽게 설명을 드리자면 **Observer는 관찰을 하는 객체**, **Observable은 관찰을 가능하게 해주는 객체**라고 생각하시면 쉬우실 수도 있겠네요.   
예를 들어봅시다.   
예를 들어 카카오 택시를 사용해 택시를 기다리고 있을 때 **Observable**은 택시가 잡히는지 안잡히는지 기다리고 있는 사람 입니다.   
**Observer**는 카카오 택시를 실행 시키고 있는 나의 핸드폰이라고 가정을 하겠습니다.   
여기서 택시가 잡혔을 때 **Observer**인 내 핸드폰은 택시가 잡혔다고 **Observable**인 나에게 알림을 줍니다.   

1. 핸드폰은 택시가 잡힐 때 까지 대기 (이벤트 대기)
2. 택시가 잡힘(이벤트 발생)
3. 택시의 거리가 너무 멀면 택시를 다시 잡음(이벤트 발생에 따른 처리)

이 부분을 RxSwift에 그대로 적용 시켜봅시다.   

## Observable

* 관찰할 수 있는 객체, Observer를 통해 이벤트를 구독시키는 객체
* subscribe를 사용하여 이벤트를 구독

 ```
	let myObservable:Observable<Void>
 ```
 
## Observer
 * 관찰하는 객체
 * 보통 Observable과 Subject를 묶어서 사용한다.
  ```
	let myObserver:AnyObserver<Void>
 ```
 
## Subject는 또 뭐야?
Subject는 Observable과 Observer의 두가지 기능을 다가지고 있는 특별한 객체라고 생각 하시면 됩니다.   
Observable을 subscribe 할 수 있고 Observable의 형태로 관찰 할 수 있는 객체로도 사용 할 수 있습니다.   
크게 3가지 타입이 있어요.   
* PublishSubject
* BehaviorSubject
* ReplaySubject

**PublishSubject**   
그래서 이걸 어디다가 어떻게 사용을 할까요?   
코드를 한번 볼게요.   
  ```
	var publishSubject = PublishSubject<String>()
	
	Observer로 사용
	publishSubject.subscribe { (event) in
		print(event)
	}
	//onNext 로 이벤트를 생성
	publishSubject.onNext("1")
	
	// output:1
	
 ```
 
 두번째 값을 한번 줘볼까요 ?   
   ```
	publishSubject.onNext("2")
	//output:1
	//output:2
	
 ```
 네 당연한 결과 입니다 ㅋㅋ   
 그러면 이렇게 해보면 어떨까요?
 ```
	publishSubject.onNext("before subscribe")
	let firstSubscribe = publishSubject.subscribe(onNext:{(event) in
		print("fisrt: \"($0)" ")
	})
	
	publishSubject.onNext("1")
	publishSubject.onNext("2")
	
	let secondSubscribe = publishSubject.subscribe(onNext:{(event) in
		print("second: \"($0)" ")
	})
	
	publishSubject.onNext("3")
	
	firstSubscribe.dispose()
	
	secondSubscribe.onNext("4")
	secondSubscribe.dispose()
	
	//output -> first:1
	//output -> first:2
	//output -> first:3
	//output -> second:3
	//output -> second:4
 ```
 
 이렇게 출력이 되요! 그래서 PublishSubject 는 구독한 뒤에 Observalbe이 보낸 이벤트를 전달 받아요.   
 조금 특이한 부분이 있다면 PublishSubject가 한번 completed 되면 새로운 구독이 생겨도 다시 completed 이벤트를 방출 합니다.   
 ```
 	publishSubject.onNext("before subscribe")
	let firstSubscribe = publishSubject.subscribe(onNext:{(event) in
		print("fisrt: \"($0)" ")
	})
	
	publishSubject.onNext("1")
	publishSubject.onNext("2")
	
	let secondSubscribe = publishSubject.subscribe(onNext:{(event) in
		print("second: \"($0)" ")
	})
	
	publishSubject.onNext("3")
	
	firstSubscribe.dispose()
	
	
	secondSubscribe.onNext("4")
	publishSubject.onCompleted()
	publishSubject.onNext("5")
	
	let disposeBag = DisposeBag()
	publishSubject.subscribe{
		print("third: \"($0)" ")
	}.disposed(by:disposeBag)
	
	publishSubject.onNext("6")
	
	//output -> first:1
	//output -> first:2
	//output -> first:3
	//output -> second:3
	//output -> second:4
	//output -> second: completed
	//output -> third: completed
 ```
**BehaviorSubject**   
BehaviorSubject는 구독후에 가장 최근에 있는 데이터를 전달해주는 역활을 해요!   
 ```
	let disposeBag = DisposeBag()
	let subject = BehaviorSubject(value: "1")

	subject.subscribe { print("First : \($0)") }
		.addDisposableTo(disposeBag)

	subject.onNext("2")
	subject.onNext("3")

	subject.subscribe { print("Second : \($0)") }
		.addDisposableTo(disposeBag)

	subject.onNext("4")
	subject.onNext("5")
	subject.subscribe { print("Third : \($0)") }
	.addDisposableTo(disposeBag)
	subject.onCompleted()

	// Output
	First : 1
	First : 2
	First : 3
	Second : 3
	First : 4
	Second : 4
	First : 5
	Second : 5
	Third : 5
 ```
 제일 최신의 데이터를 구독하자마자 방출을 해줘서 최신의 데이터로 뷰를 구성해야할 때 사용하면 좋을듯 하네요!   
 근데 최신의 값이 아니라 여러개의 값이 있어야 한다면 ReplaySubject를 사용하시면 됩니다!   
 
**ReplaySubject**   
ReplaySubject는 계속 방출해온 이벤트 중 최근의 이벤트를 내가 직접 버퍼를 지정해서 가져올 수 있어요!   
예를 들어보면 버퍼사이즈가 2라면?   
 ```
	let subject = ReplaySubject<String>.create(bufferSize: 2)
        subject.onNext("1")
        subject.onNext("2")
        subject.onNext("3")
            
        subject.subscribe{
			print("First",$0)
        }.disposed(by: disposeBag)
            
        subject.subscribe{
			print("Second",$0)
        }.disposed(by: disposeBag)
	// Output
	First : 2
	Second : 2
	First : 3
	Second : 3
 ```
 다음과 같이 나옵니다 버퍼사이즈가 2 가 되니까 1은 무시가 되어버리는 경우 이죠   
 그러면 여기서 더 추가가 된다 면 어떨까요?
  ```
	subject.onNext("4")
  ```
  그러면 당연히 3 과 4 가 ReplaySubject 에 남아있게 됩니다!