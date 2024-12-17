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






MATLAB을 이용한 칼만필터 적용으로 SOC 추정하는 코드(C > Python)


2. 다양한 입력값의 특수 케이스를 처리하면서 정확하고 안정적인 승수 연산을 수행하는 코드
```
def rt_powd_snf(u0, u1):
    if math.isnan(u0) or math.isnan(u1):
        return float('nan')  # NaN 처리

    tmp = abs(u0)   # base의 절대값
    tmp_0 = abs(u1) # exponent의 절대값

    # 무한대 exponent 처리
    if math.isinf(u1):
        if tmp == 1.0:
            return 1.0  # 1^Inf = 1
        elif tmp > 1.0:
            return float('inf') if u1 > 0.0 else 0.0  # 큰 base: Inf 또는 0
        else:
            return 0.0 if u1 > 0.0 else float('inf')  # 작은 base: 0 또는 Inf

    if tmp_0 == 0.0:        # exponent가 0인 경우
        return 1.0          # x^0 = 1

    if tmp_0 == 1.0:        # exponent가 1인 경우
        return u0 if u1 > 0.0 else 1.0 / u0  # x^1 또는 1/x
    
    if u1 == 2.0:               # exponent가 2인 경우
        return u0 * u0  # x^2
    
    if u1 == 0.5 and u0 >= 0.0:     # exponent가 0.5인 경우
        return math.sqrt(u0)  # x^(1/2)
    
    if u0 < 0.0 and u1 > math.floor(u1):        # base가 음수이고 exponent가 정수가 아닌 경우
        return float('nan')  # 음수 base의 비정수 승
    
    return math.pow(u0, u1)     # 기본적으로 math.pow 사용



def extended_kalman_filter(z, x, P):
    # 예측 단계
    x_pred = f(x)
    F = jacobian_f(x)
    P_pred = F @ P @ F.T + Q

    # 업데이트 단계
    H = jacobian_h(x_pred)
    K = P_pred @ H.T @ np.linalg.inv(H @ P_pred @ H.T + R)
    x_upd = x_pred + K @ (z - h(x_pred))
    P_upd = (np.eye(len(x)) - K @ H) @ P_pred


def look1_binlxpw(u0, bp0, table, maxIndex):
    if u0 <= bp0[0]:
        iLeft = 0
        frac = (u0 - bp0[0]) / (bp0[1] - bp0[0])
    elif u0 < bp0[maxIndex]:
        # Binary search
        iLeft, iRght = 0, maxIndex
        while iRght - iLeft > 1:
            bpIdx = (iRght + iLeft) // 2
            if u0 < bp0[bpIdx]:
                iRght = bpIdx
            else:
                iLeft = bpIdx
        frac = (u0 - bp0[iLeft]) / (bp0[iLeft + 1] - bp0[iLeft])
    else:
        iLeft = maxIndex - 1
        frac = (u0 - bp0[maxIndex - 1]) / (bp0[maxIndex] - bp0[maxIndex - 1])

    return (table[iLeft + 1] - table[iLeft]) * frac + table[iLeft]

    return x_upd, P_upd

```

