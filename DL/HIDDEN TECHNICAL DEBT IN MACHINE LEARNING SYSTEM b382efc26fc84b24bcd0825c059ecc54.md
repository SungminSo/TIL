# HIDDEN TECHNICAL DEBT IN MACHINE LEARNING SYSTEM

- 기존 코드의 유지 관리 문제와 추가적인 ML 관련 문제가 있지만, 이것은 발견하기가 꽤나 어려운데, 그 이유는 코드 상의 문제가 아닌 시스템상의 문제이기 때문.
- 이 논문에서는 그 해결책을 제시해주는것은 아님, 단지 어떤 문제들이 있는지 의식하도록 하는것



### Complex Models Erode Boundaries

- the desired behavior cannot be effectively expressed in software logic without dependency on external data
- **Entanglement** : CACE principle: Changing Anything Changes Everything.
    - ex) input signals, hyper-parameters
    - 완화 방법 : ensembles / 예측 동작의 변화 발생 감지
- **Correction Cascades** : `A` 케이스에 대한 모델 Ma가 `A'`케이스의 해결책이 될 수 있을 때, `A'`에 Ma를 사용할 경우(이때 Ma를 Correction model 이라함) 문제가 될 수 있음
    - 이유 :
        1. Ma에 대한 의존성이 생긴다.	
        2. 해당 모델의 학습성과 분석이 더 어려워진다.	
        3. 이러한 correction model이 cascade(이후의 케이스, 모델에 대해 같은 양상이 반복)된다면 improvement deadlock 발생
    - 완화 방법 : Ma augmentation / `A``에 대한 별도의 모델 학습
- **Undeclared Consumers** : 하나의 모델 Ma에서의 예측값이 이곳저곳에서 많이 사용될 경우 에러 발생 잠재율이 올라간댜.
    - expensive at best and dangerous at worst
    - 전체적인 성능향상이 있을지라도 모델 Ma를 변화 또는 수정하기가 어려워지고, hidden feeback loops를 만들수도 있다.
    - 완화 방법 : access restrictions or strict-level argreements(SLAs)



### Data Dependencies Cost More than Code Dependencies

- **Unstable Data Dependencies** : 다른 머신의 output을 모델의 학습 input으로 둘 때, 이 데이터들이 질적으로, 양적으로 안정적이지 않는 경우 문제가 될 수 있음
    - 이유 : 이런 불안정한 input에 대한 improvement가 있기 때문. 진단 및 해결에 비용이 많이 들 수 있음
    - 완화 방법 : 이 데이터 input들의 versioned copy 생성
- **Underutilized Data Dependencies** : 딱히 유의미하지 않은 데이터들이 계속 사용되어 의존도가 발생하는 문제
    - 이 문제가 발생하게 되는 몇가지 원인들
        - Legacy Features : 현재는 그다지 유효하지 않지만 초기때부터 사용되어 시간이 자남에 따라 새로운 기능에 의해 불필요하게 되지만 감지가 어렵다.
        - Bundled Features : feature 덩어리가 한번에 모델에 추가되어 의미가 없을 수도 있는 feature까지 포함됨.
        - ϵ-Features : 아주 조금의 정확도 향상을 위했지만 복잡성 오버헤드가 있을 경우.
        - Correlated Features : 두가지의 features가 강한 연관이 있을 경우. 많은 ML 방법론이 이 차이를 구분 및 발견하기는 어렵고 같은 feature로 취급하기 쉽지만, 추후에 조금의 변화로 모델이 깨지기 쉽다.
    - 완화 방법 : leave-one-feature-out evaluations로 발견 가능
- **Static Analysis of Data Dependencies** : static tool을 사용하지 않아서 문제가 발생할 수도 있음.
    - 코드가 컴파일러나 빌드 시스템에 의해 의존도 체크를 하듯이, ML에서도 tool을 이용해서 에러 체크 등을 할 수 있음.
    - 이런 체크가 모든 의존성이 적절한지를 확인하기 때문에 automated feature management system으로 볼 수 있다.



### Feedback Loops

- ML 시스템은 지속적으로 업데이트된다면 해당 시스템의 결과의 영향을 받는데, 이것은 analysis debt를 초래한다.
- **Direct Feedback Loops**
    - 지도학습 모델의 경우에서 나타날 수 있는 문제
    - 실제 알고리즘 또는 모델이 real-world problems만큼의 스케일을 반드시 가져야 할 필요는 없다 => 피드백에 의한 데이터의 범주가 너무 클 수 있다.
    - 완화방법 : 어느정도의 난수화(randomization) / isolating certain parts of data
- **Hidden Feedback Loops**
    - 직접적인 feedback loop도 분석하기에 어렵고, 통계에 있어서 도전적인 과제다.
    - 간접적으로 서로 영향을 주는 시스템들처럼 직접적이지 않은 feedback loop는 더욱 더 어렵다.
        - 예시 : 웹 페이지의 양상을 분별하는 서로 다른 2개의 시스템 (하나는 나열할 상품을 선택하고, 다른 하나는 이와 관련된 리뷰를 선택)
    - 이런 간접적인 loop가 완전히 분리된 시스템 사이에서도 발생할 수 있다.
        - 예시 : 서로 다른 회사에서 각자 개발한 주식시장 예측 모델 (하나의 모델에서의 호가, 매수가 다른 모델에 영향을 줄 수 있다.)



### ML-System Anti-Patterns

- 학습과 예측에 굉장히 조금의 코드가 사용되지만, 통합적인 기계학습 방법은 대부분 high-dept design pattern으로 귀결된다.
- **Glue Code**
    - 널리 쓰일 수 있는 패키지를 쓰다보면 장기적으로 봤을때 해당 패키지에 의해 시스템에 제한이 생기거나, 도메인 특색에 맞게 향상되기 어려운 문제가 있다. (대안으로 쓰일 수 있는 패키지를 테스트하는게 엄청나게 어려울 수 있다.)
    - 완화 방법 : general-package 사용 비율을 줄이고 본 모델에서의 native 코드 사용 / glue-code가 될법한 부분을 공통 api로 wrap
- **Pipeline Jungles**
    - Glue Code의 일종으로, 데이터 전처리(preparation) 시 발생할 수 있는 문제
    - 관리를 하지 않으면 파악하기 어렵게(작업이 너무 많거나 구조가 복잡) 될 수 있음
    - pipeline은 문제를 파악하기 어렵지만, 문제가 있을 경우 end-to-end 통합 테스트를 진행하기 어렵게 만들 수 있어 주의가 필요하다.
    - 완화 방법 : 데이터 수집 및 feature 추출 시 전체적인 설계 및 생각 필요 (pipeline jungle 문제가 있는 인프라 폐기 및 재구축은 어렵긴 하지만 가장 확실한 방법이 될 수 있다.)
    - Glue-Code와 Pipeline-Jungle 문제는 연구와 엔지니어링이 전반적으로 분리되어 있을때 나타나는 증상
        - 엔지니어와 연구자가 함께 작업하는 방법으로 어느정도 해소 가능
- **Dead Experimental codepaths**
    - glue-code와 pipeline-jungle의 결과로 production에 대해 각 조건 브랜치를 만들어서 여러 실험적인 codepaths를 추가하는 방법이 단기적으로 더 좋아보일 수 있다.
    - 각 변경점에 대해 이 방식의 비용은 매우 적긴 하다 (리워크 등이 필요하지 않기 때문에)
    - 하지만 시간이 지날수록 codepaths가 축적되면서 기존 코드와의 호환성을 유지하기 어렵고, 복잡성이 급격히 증가한다.
    - 예시 : Knight Capital 시스템(예측하지 문제 하나로 엄청난 결과를 맞이..)
    - 완화 방법 : 주기적으로 지워질 브랜치 확인(테스트)
- **Abstraction Debt**
    - 위의 이슈들은 ML 시스템의 명확한 추상화가 부족해서 생기는 일이다.
    - Map-Reduce의 사용이 불충분한 학습 추상화를 만들었다고 생각한다.
- **Common Smells**
    - Plain-Old-Data Type Smell
        - ML 시스템에 의해 사용되고 생성되는 데이터가 float이나 integer같은 기본 타입
        - 모델 파라미터로 들어가는 값의 용도에 맞는 타입 사용 필요
    - Multiple-Language Smell
        - 여러언어를 사용하는 방법은 테스트하기 어렵다는 점과 관리 용이성이 떨어진다.
    - Prototype Smell
        - 프로토타입을 사용하는건 좋으나 그 환경을 계속해서 사용하는건 실제 프로덕트로의 전환을 어렵게하는 문제가 될 수 있다.
        - 프로토타입 환경을 유지하는것도 쉽지않다



### Configuration Debt

- configuration을 확인하고 테스트하는게 별거아닐 수 있지만, 실제 연구자와 엔지니어 모두 configuration에 대해 숙고하고 있음.
- configuration이 지속적으로 축적될 경우 코드보다 길어질 수 있고 그로 인한 실수 등이 발생할 수 있다.
- **good configuration principles**
    - 이전 config로부터 작은 변경으로 새로운 config 생성이 간단해야 한다.
    - 수동 작업으로 인한 에러 발생이 어려워야 한다.
    - 서로 다른 모델의 config 차이가 보기 쉬워야 한다.
    - config의 기본적인 사항이 자동적으로 확인되어야 한다. : 사용되는 feature 갯수, transitive closure of data dependencies 등
    - 사용하지 않는 세팅 발견이 가능해야 한다.
    - config는 전체 리뷰가 진행되어야 한다.



### Dealing with Changes in the External World

- ML 시스템의 매력적인 점은 외부와의 직접적인 상호작용인데, 외부는 거의 고정적이지 않기 때문에 이를 위한 유지보수 비용이 발생
- **Fixed Thresholds in Dynamic Systems**
    - 모델의 decision threshold가 필요한 경우가 자주 있는데, 이때 threshold를 수동으로 설정해줘야 하기 때문에 새로운 데이터를 학습할 시 이 threshold가 유효하지 않을 수 있음.
    - 완화 방법 : threshold를 검증된 데이터로 간단히 계산하거나 학습시키는 방법
- **Monitoring and Testing**
    - unit 테스트와 end-to-end 테스트는 충분히 가치가 있지만 그것만으로 시스템이 문제없이 작동하고 있다고 확신할 수는 없다.
    - 어떤걸 모니터링 하느냐, 어떤걸 테스트 하느냐도 굉장히 중요한 문제
    - Prediction Bias
        - 예측 편향 = 예측 평균 - 데이터 라벨의 평균
        - 이 값이 0이 아니라면(예측값과 데이터 라벨의 분포가 다른 경우) 모델에 문제가 있을 수도 있음을 암시하기 때문에 모니터링할 유용할 지표가 된다.
        - 예측 편향의 원인 예시는 아래와 같다(참고: [https://developers.google.com/machine-learning/crash-course/classification/prediction-bias?hl=ko](https://developers.google.com/machine-learning/crash-course/classification/prediction-bias?hl=ko))
            - 불완전한 특성 세트
            - 노이즈 데이터 세트
            - 결함이 있는 파이프라인
            - 편향된 학습 샘플
            - 지나치게 강한 정규화
    - Action Limits
        - 실제 어떤 액션을 행하는 모델의 경우(물건 구매나 스팸메세지 구분 등) action limits이 있어야 한다.
        - action이 임계점을 넘었을 때 알림을 해 즉각적인 대응이 가능하도록
    - Up-Stream Producers
        - 다른 모델을 통해 생성 및 전달되는 데이터가 있을 경우 이 모델들을 up-stream producer라고 한다.
        - down-stream 모델에 영향을 줄 수 있기 때문에 모니터링 및 테스트가 필요하다.



### Conclusions : Measuring Debt and Paying it Off

- few useful questions to consider
    - How easily can an entirely new algorithmic approach be tested at full scale?
    - What is the transitive closure of all data dependencies?
    - How precisely can the impact of a new change to the system be measured?
    - Does improving one model or signal degrade others?
    - How quickly can new members of the team be brought up to speed?