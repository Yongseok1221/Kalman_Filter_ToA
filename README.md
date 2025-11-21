# Kalman_Filter_ToA
'''mermaid
flowchart TD
    Start([시작]) --> Init[4개 앵커 배치<br/>정사각형 꼭짓점]
    Init --> SimLoop{10,000회<br/>시뮬레이션}
    
    SimLoop --> TimeLoop{시간 k=0→10<br/>물체 이동}
    
    TimeLoop --> CalcDist[실제 거리 계산<br/>물체↔각 앵커]
    CalcDist --> AddNoise[측정 노이즈 추가<br/>Z = 실제거리 + σ·randn]
    AddNoise --> ToA[ToA 변환<br/>거리차의 제곱으로 선형화]
    
    ToA --> CheckK{k ≥ 2?}
    
    CheckK -->|No| LSE[최소자승법<br/>초기 위치 추정]
    CheckK -->|Yes| KF[칼만 필터]
    
    KF --> Predict[예측 단계<br/>x_pred = Ax + 속도 + Ewk<br/>P_pred = APA' + Q]
    Predict --> Update[추정 단계<br/>K = 칼만 게인 계산<br/>X_est = x_pred + K·잔차<br/>P = P_pred - KHP_pred]
    
    LSE --> CalcError[오차 계산<br/>√((x_est-x_real)² + (y_est-y_real)²)]
    Update --> CalcError
    
    CalcError --> TimeLoop
    
    TimeLoop -->|k=10 완료| SimLoop
    SimLoop -->|10,000회 완료| AvgError[평균 오차 계산<br/>총 오차 / 110,000]
    
    AvgError --> End([종료])
    
    style Start fill:#e1f5e1
    style End fill:#ffe1e1
    style KF fill:#e1e5ff
    style Predict fill:#fff4e1
    style Update fill:#fff4e1
'''
