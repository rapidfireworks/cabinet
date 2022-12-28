- Learn about the architecture and design of the HealthKit framework.
- # Overview
- 앱들 간에 유의미한 방식으로 데이터를 공유하기 위해 설계되었다.
- 데이터 타입과 단위를 미리 정의된 목록에 따라 제한한다.
- 개발자는 데이터 타입과 단위를 커스텀할 수 없다.
- # HealthKit Data
- Characteristic data
	- 대개는 변하지 않는 항목
	- 생일, 혈액형, 생물학적 성, 피부타입
	- 앱은 이러한 정보를 저장할 수 없다. 사용자는 Health 앱을 이용해 이러한 정보를 입력한다.
- Sample data
	- 대부분의 사용자 건강 데이터는 특정 시점의 데이터 샘플 형태로 저장되나.
	- HKObject > HKSample
- Workout data
	- Information about fitness and exercise activities
	- HKWorkout
- Source data
	- HKSourceRevision
		- 샘플을 저장한 앱 또는 디바이스 정보
	- HKDevice
		- 데이터를 생성한 하드웨어 기기 정보
- Deleted objects
	- HKDeletedObject
	- 지워진 아이템의 UUID를 일시적으로 저장
- # Properties of Objects and Sample
- HKObject
	- 모든 HealthKit 샘플 타입의 superclass로, 불변이다.
	- Properties
		- UUID
		- Metadata
		- Source Revision
		- Device
- HKSample
	- HKObject의 subclass이자 모든 샘플의 superclass로, 특정 시점의 데이터를 의미한다.
	- Properties
		- Type
		- Start date
		- End date
	- Subclasses
		- HKCategorySample
		- HKQuantitySample
		- HKCorrelation
		- HKWorkout
- # Threading
- HealthKit store는 thread-safe하고, 대부분의 HealthKit 객체들은 불변이므로, 일반적으로 HealthKit을 멀티쓰레드 환경에서 사용할 수 있다.
- 모든 HealthKit API의 completion handler는 private background queue들에서 실행된다.
- # Syncing Data between Devices
- 아이폰과 애플워치에는 각각의 HealthKit store가 있고, 데이터는 자동으로 동기화된다.
- 저장공간을 아끼기 위해 오래된 데이터는 애플워치에서 삭제한다.
- HealthKit은 아이패드에서 사용할 수 없다.