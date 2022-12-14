- Respect and safeguard your user's privacy.
- # Overview
- 건강 정보는 민감할 수 있기에, HealthKit은 사용자에게 앱이 공유할 수 있는 정보에 대한 세밀한 제어를 제공한다.
- # Access Encrypted Data
- 보안상의 이유로 사용자가 기기를 잠그면  HealthKit 스토어는 암호화된다. 이에 따라 백그라운드에서 실행되는 앱은 store의 데이터를 읽지 못할 수 있다.
- 기기가 잠겨도 앱은 여전히 스토어에 기록할 수 있다. HealthKit이 이를 캐시하고 있다가, 기기가 잠금해제되면 암호화된 스토어에 기록한다.
- # Specify How Your App Uses the Health Data
- HealthKit을 통해 얻은 정보를 광고와 그 유사 서비스에 사용하면 안된다.
- HealthKit을 통해 얻은 정보를 밖으로 유출해서는 안된다.
- 정보를 팔아서는 안된다.
- 유저 동의하에서만, 의료 연구에 공유할 수 있다.
- 유저에게 HealthKit 데이터를 어떻게 사용할지 확실하게 밝혀야 한다.
- # Provide a Privacy Policy
- [Model Privacy Notice (MPN) | HealthIT.gov](http://www.healthit.gov/policy-researchers-implementers/personal-health-record-phr-model-privacy-notice)
- [Model Notices of Privacy Practices](http://www.hhs.gov/ocr/privacy/hipaa/modelnotices.html)
- 위의 모델을 참조하여, 웹 기반 서비스에 맞는 정책을 세워야 한다.