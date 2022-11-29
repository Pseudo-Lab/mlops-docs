# Katib

Kubeflow Components로 동작되며 Hyperparameter tunning을 제공

## 구성요소
Experiment, Suggestion, Trial, Job으로 구성됨
### Experiment
`하나의 실행단위로 Job 개념과 같음. k8s 내 커스텀 리소스로 Trial 를 실행하는 역할`
- Katib는 HPO 한 세트를 Experiment라는 custom resource로 정의하고 관리
Experimen는 목표로 하는 대상 값의 Hyperparameters 탐색 작업, Hyperparameters를 찾기 위해서는 Experiment 리소스를 생성해야함

- Trial Count : 실행 횟수 (병렬)
- Trial Template : Trial 파드 템플릿
- Objective: Hyperparameters 튜닝의 목표 정의, Hyperparameters 튜닝 시 metric의 최대화/최소화 여부 지정.
- Search Parameter: Hyperparameters 튜닝 작업시 사용해야 할 모든 Hyperparameters 값과 Hyperparameters의 제약 조건을 정의. 예를 들어, Learning rate 값을 결정하고 optimizer로 sgd와 adam을 사용한다고 정의.
- Search Algorithm: Hyperparameters 튜닝 작업시 사용할 알고리즘을 정의함 Random Search, Grid Search, Bayesian Optimization 등 다용한 알고리즘을 사용함. 최적의 Hyperparameters 값을 검색 할 때 사용할 알고리즘.

### Trial
`최적화 과정의 반복 단위. Experiment의 Trial Count 값 만큼 Trial을 생성하고 순차적으로 실행. 하나의 Trial 에서 하나의 worker job을 실행`
- Trial은 제안된 Hyperparameters를 평가하기 위한 하나의 작업(실행 단위)을 의미하는 사용자 리소스. 
- 제안된 매개 변수 값들을 Woker Job 인스턴스 할당해서 실행함.
- Experiment는 목표나 설정한 최대 시도 횟수에 도달 할 때까지 Trial을 계속 실행함.

### Suggestion
`Search Algorithm 으로 생성한 하이퍼파라미터 값의 모음`
- 하나의 Experiment 에서 하나의 Suggestion 을 생성
- Katib는 각 Experiment 사용자 리소스 마다 하나의 Suggestion 사용자 리소스를 생성함
- Suggestion은 Search Algorithm이 제안한 Hyperparameters 값들의 세트를 가지고 있음 
- Katib는 제안된 Hyperparameters 값들을 세트별로 평가하기 위한 Trial을 작성함

### Worker job
`파라미터와 Suggestion 값을 이용해 Trial, 각각의 값을 평가하고 계사하는 프로세스. 실제로 학습을 수행하며 K8s Job 과 TFJob, PyTorch 을 사용`
 

## Workflow
[자세한 동작과정](https://github.com/kubeflow/katib/blob/master/docs/workflow-design.md)