## Extended Kalman Filter(EKF)
<br>

> 선형 시스템에만 적용할 수 있는 칼만필터의 확장 버전  
> 비선형 시스템의 상태를 추정하기 위해 주로 사용됨  
> 비선형 시스템을 선형화 ➡️ 칼만필터 적용

<br>

#### 작동 원리

1.  예측(Prediction)

-   시스템의 현재 상태 + 프로세스 모델 ➡️ 다음 상태 예측
-   상태 + 공분산 행렬 ➡️ 미래 상태 예측
-   비선형인 시스템 모델 ➡️ 테일러 근사로 선형화

2.  업데이트(Update)

-   관측값 ➡️ 예측된 상태 업데이트
-   비선형인 관측값 ➡️ 선형화
-   측정값, 예측값의 차이를 줄이는 방향으로 상태와 공분산 행렬 업데이트

<br>

#### 코드

```
import numpy as np

# 시스템 예측 모델 : 현재상태 x를 기반으로 다음상태를 예측 (위치와 속도를 추적하는 경우, 다음위치와 속도는 시간간격을 고려해 위치 변화량을 더하는 방식)
def f(x):
    dt = 0.1 # 간격
    F = np.array([[1, dt], [0,1]])
    return F @ x

# 관측 모델
def h(x):
    return np.array([x[0]])

# 자코비안 계산 함수(선형화하는 용도)
def jacobian_f(x):
    dt = 0.1
    return np.array([[1,dt], [0, 1]])

def jacobian_h(x):
    return np.array([[1,0]])


# 초기화
x = np.array([0,1])     # 초기 상태(위치, 속도)
P = np.eye(2) * 1000    # 초기 공분산
Q = np.eye(2) * 0.1     # 프로세스 잡음
R = np.eye(1) * 1       # 측정 잡음


# EKF 구현 함수
def extended_kalman_filter(z, x, P): #z:측정값, x:현재상태(위치, 속도), P:공분산 행렬
    #공분산 행렬인 P를 기반으로 예측, 업데이트 단계를 수행하고 업데이트된 공분산을 반환
    
    #예측 단계
    x_pred = f(x)          
    F = jacobian_f(x)      # 시스템 자코비안 계산 : 시스템모델이 비선형일 경우, 선형화한 1차 미분 값. 예측 공분산 행렬 계산할때 사용
    P_pred = F @ P @ F.T + Q #오차 공분산 예측 : (자코비안행렬 F * 공분산 P * 자코비안 행렬의 전치행렬) + 프로세스 잡음
    
    #업데이트 단계 : 새로운 측정값 z를 이용해서 예측된 오차 공분산 상태를 보정
    H = jacobian_h(x_pred) # 관측 자코비안 계산
    K = P_pred @ H.T @ np.linalg.inv(H @ P_pred @ H.T + R)
    x_upd = x_pred + K @ (z - h(x_pred))
    P_upd = (np.eye(len(x)) - K @ H) @ P_pred
    
    return x_upd, P_upd


# 측정값 입력 예시
measurements = [1,2,3]

# 필터 실행
for z in measurements:
    x, P = extended_kalman_filter(z, x, P)
    print(f"Updated state: {x}")

```

