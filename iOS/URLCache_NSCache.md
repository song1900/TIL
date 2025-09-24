# URLCache & NSCache

## URLCache
### 개념
- 네트워크 요청에 대한 response를 캐싱하는 객체.
- URLSession과 함께 동작하면서, 동일한 요청이 다시 발생할 때 네트워크를 타지 않고 캐시된 데이터를 반환할 수 있음.
- HTTP 헤더(Cache-Control, ETag, Expires) 기반으로 동작.

### 특징
- Disk / Memory 캐시 모두 지원
    - memoryCapacity, diskCapacity 로 설정 가능.
- 자동 관리
    - HTTP 표준 캐시 규칙을 따름
    - 직접 캐시에 저장하거나 가져올 수도 있음 (storeCachedResponse, cachedResponse(for:))
- 적합한 경우
    - 이미지, JSON 응답 등 네트워크 자원을 재사용해야 할 때.

### 기본 사용 예시
```swift
// URLCache 설정
let urlCache = URLCache(
    memoryCapacity: 20 * 1024 * 1024, // 20MB
    diskCapacity: 100 * 1024 * 1024, // 100MB
    directory: nil
)
URLCache.shared = urlCache

// URLSessionConfig 연결
let config = URLSessionConfiguration.default
config.urlCache = URLCache.shared
config.requestCachePolicy = .returnCacheDataElseLoad
let session = URLSession(configuration: config)

// 요청 시 캐시 사용
let task = session.dataTask(with: URL(string: "https://example.com/data.json")!) { data, response, error in
    if let data = data {
        print("데이터 크기: \(data.count)")
    }
}
task.resume()
```

<br>


## NSCache
### 개념
- 메모리 기반 임지 저장소
- Dictionary 와 유사하지만, 메모리 압박 시 시스템이 자동으로 캐시를 비움 ➡️ Out of Memory 방지.
- 키-값 쌍(AnyObject ➡️ AnyObject) 형태로 저장

### 특징
- Disk 캐시는 없음 ➡️ 앱 종료 시 데이터 소멸
- Thread-safe
- 캐시 정책 제공
    - countLimit: 최대 개수 제한.
    - totalCostLimit: 비용(메모리) 제한.
- 적합한 경우
    - 이미지 캐싱, 비싼 연산 결과 재사용 등 메모리에 올려두면 성능 이점이 있는 경우.

### 기본 사용 예시
```swift
let imageCache = NSCache<NSString, UIImage>()
imageCache.countLimit = 100   // 최대 100개까지
imageCache.totalCostLimit = 50 * 1024 * 1024 // 50MB

// 저장
imageCache.setObject(myImage, forKey: "profile_image")

// 조회
if let cached = imageCache.object(forKey: "profile_image") {
    print("캐시에서 가져옴")
} else {
    print("네트워크로 받아오기")
}
```

<br>

## 사용 전략
- 네트워크 응답 캐싱 : URLCache
    - ex: REST API 호출, 이미지 다운로드 응답
- 메모리 객체 캐싱 : NSCache
    - ex: UIImage, Custom Model 객체, 파싱된 JSON 객체
- 같이 사용하는 경우
    - URLCache -> 원본 Data 저장
    - NSCache -> 디코딩된 UIImage 객체 저장