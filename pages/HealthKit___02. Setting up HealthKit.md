- Set up and configure your HealthKit store.
- # Overview
- 1. 앱 내 HealthKit 활성화
- 2. 현재 기기에서 HealthKit이 사용가능한 지 확인
- 3. 앱의 HealthKit store 생성
- 4. 권한 요청 후 데이터 읽기 및 공유
- # Enable HealthKit
- Clinical Health Records는 필요한 경우에만 선택해야한다. 그렇지 않으면 App Review가 거절될 수 있다.
- NSHealthShareUsageDescription
- NSHealthUpdateUsageDescription
- # Ensure HealthKit's availability
- ```swift
  if HKHealthStore.isHealthDataAvailable() {}
  ```
- HealthKit이 사용불가능하면 HealthKit 메소드들은 errorHealthDataUnavailable 에러를 일으킨다.
- HealthKit이 제한되면 HealthKit 메소드들은 errorHealthDataRestricted 에러를 일으킨다.
- # Create the HealthKit Store
- ```swift
  let healthStore = HKHealthStore()
  ```
- 앱에는 하나의 HealthKit store만 있으면 된다. 이것은 생명이 긴 객체로, 한 번만 생성해두고 필요할 때 쓰면 된다.