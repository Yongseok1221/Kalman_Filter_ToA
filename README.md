# Kalman_Filter_ToA
'''mermaid
flowchart TD
    %% 스타일 정의
    classDef init fill:#f9f,stroke:#333,stroke-width:2px,color:black;
    classDef loop fill:#e1f5fe,stroke:#0277bd,stroke-width:2px,color:black;
    classDef process fill:#fff9c4,stroke:#fbc02d,stroke-width:2px,color:black;
    classDef decision fill:#e0f2f1,stroke:#00695c,stroke-width:2px,color:black;
    classDef result fill:#ffebee,stroke:#c62828,stroke-width:2px,color:black;

    Start((시작)) --> Init[변수 및 H 행렬 초기화]:::init
    Init --> OuterLoop{시뮬레이션\n10^4회 반복}:::loop
    
    subgraph Simulation_Cycle [1회 시뮬레이션 과정]
        OuterLoop --> InitTensor[위치 저장 텐서 Xk 초기화]
        InitTensor --> InnerLoop{시간 k = 0 ~ 10}:::loop
        
        InnerLoop --> GenData[실제 위치 이동 & 노이즈 추가된 거리 측정값 Z 생성]:::process
        GenData --> CalcZk[선형화된 관측값 zk 계산\n제곱 차 이용]:::process
        
        CalcZk --> CheckK{데이터 충분?\nk >= 2}:::decision
        
        CheckK -- No (초기 단계) --> LS_Mode[ToA 최소자승법\nLeast Squares]:::process
        LS_Mode --> SavePos[위치 추정값 저장 & 에러 계산]
        
        CheckK -- Yes (추적 단계) --> KF_Mode[[칼만 필터 호출\nkalmanFilter]]:::process
        KF_Mode --> KF_Logic
            subgraph KF_Internal [칼만 필터 내부 로직]
                KF_Logic(매개변수 로드) --> Pred[Time Update: 예측\n등속 모델 가정]
                Pred --> DynamicR[R 행렬 동적 계산\n거리 오차 반영]
                DynamicR --> Correct[Measurement Update: 보정\nKalman Gain 계산]
            end
        Correct --> SavePos
    end
    
    SavePos --> InnerLoop
    InnerLoop -- k 종료 --> AccError[에러 누적]:::process
    AccError --> OuterLoop
    
    OuterLoop -- 반복 완료 --> FinalCalc[평균 에러 계산]:::result
    FinalCalc --> End((종료))

    %% 링크 스타일
    linkStyle default stroke-width:2px,fill:none,stroke:#333;
    '''
