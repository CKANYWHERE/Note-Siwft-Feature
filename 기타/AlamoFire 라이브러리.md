# Alamofire 란? ✏
Alamofire는 기본적으로 서버와 Http 통신을 할때 많이 쓰여요 :)   
ios를 개발할때 백엔드와 연동은 정말 중요하기 때문에 많이 쓰이는 라이브러리중 하나지요 ㅎㅎ  
일단 설치 부터 해봅시다!   
  ```
  # Uncomment the next line to define a global platform for your project
  platform :ios, '12.0'

  target 'GamePartner' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  pod 'Alamofire'

  end


  ```

pod 파일에 vi 에디터로 수정해주세요! 그리고 pod install 하면 설치가 됩니다   
기본 적으로 Alamofire는 비동기로 네트워크 응답을 처리해요!   
그러면 호출 방법부터 살펴 볼게요   

### 호출 방식
  ```
	AF.request(URL)
	AF.request(URL, method: .post)
	AF.request(URL, method: .put)
	AF.request(URL, method: .delete)

  ```
   이 외에도 HTTPMethod 가 enum 형식으로 Head, Patch 등등 여러가지로 정의가 되어 있습니다.   
   기본 호출 방식은 다음과 같아요!  
  ```
  AF.request(URL).validate()
				 .responseJSON{ response in
                switch response.result {
					case .success(let json):
						let data = JSON(json)
						let parseModel = data["body"].stringValue
					case .failure(let error):
						print(error)
						return
                }
            }
    }
  ```
  다음과 같은 방법으로 API를 호출 합니다! 그리고, JSON()이라는 객체를 사용하였는데 저 아이는 [SwiftyJson](https://github.com/SwiftyJSON/SwiftyJSON) 이라는 라이브러리로 JSON 파싱을 간결하게 시켜주는 객체라고 생각하시면 됩니다!   
  사용법도 어렵지 않고 Alamofire 와 찰떡이니 한번 보시는걸 추천 드려요!   
  그러면 파라메터와 HTTP 헤더를 사용하여 다른 방식으로도 호출을 해야하 할 경우가 종종 있습니다 그럴땐 이런식으로 하면 됩니다.   
   ```
    let param: Parameters = [
    "userId": userId,
    "userPw": userPw
	]
	
	let headers: HTTPHeaders = [
	"Authorization": "Auth",
	"ResponseType": "application/json"
	]
	
	AF.request(URL, method: .post, parameters: params, headers: headers)
    .validate()
    .responseJSON{ response in
		switch response.result{
			case .success(let json):
				let data = JSON(json)
				let parseModel = data["body"].stringValue
			case .failure(let error):
				print(error)
				return
        }
    }
	
   ```
   
   Alamofire 는 사용법이 그렇게 어렵지 않아서 러닝커브도 높지 않다고 생각이 되요! 더 공부하시다가 모르시는게 생기신다면 [Alamofire Gitgub](https://github.com/Alamofire/Alamofire)를 보면 될듯 합니다.
  