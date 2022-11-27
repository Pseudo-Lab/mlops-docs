# Pipeline

## Pipeline 
Kubeflow Pipeline은 docker 컨테이너를 기반으로 확장 가능한 ML Workflow를 구축하고 개발하기 위한 플랫폼, Argo CD를 기반으로 만들어졌음

## Argo CD
- 할당된 리소스에 어떤 컨테이너를 실행할지 컨트롤 하는 역할
1. 사용자가 YAML DAG 명세를 작성하여 쿠버네티스 마스터에 요청을 합니다.
2. 쿠버네티스 API 서버가 명세를 받아 etcd DB에 workflow 정보를 저장합니다.
3. Argo controller가 reconcilation loop에서 etcd DB의 새로운 정보를 확인하고 kube-scheduler에 필요한 Pod를 요청합니다.
4. kube-scheduler는 Pod를 적절한 노드에 스케줄링합니다.
5. Argo controller가 다음번 reconcilation loop에서 다음 dependency가 걸려 있는 Job을 요청합니다.

## Kubeflow Pipeline 구성요소
### Kubeflow UI 웹 서버
Pipelines 웹 서버는 다양한 서비스에서 데이터를 수집하여 현재 실행중인 Pipelines 목록, Pipelines 실행 기록, data artifact 목록, 개별 Pipelines 실행에 대한 디버깅 정보, 개별 Pipelines 실행에 대한 실행 상태를 표시함

### Python SDK
Kubeflow Pipelines 도메인 별 언어 (*DSL)를 사용하여 component를 생성하거나 Pipelines을 지정 (python code to .yaml)

### Pipelines Service (중앙 제어)
Pipelines 서비스를 호출하여 정적 구성에서 실행되는 Pipelines 생성 요청, 실행 요청(쿠버네티스 리소스 준비 및 실행), 등록, Workflow 저장 기능 수행

### Kubernetes 리소스 (리소스 생성)
 Pipelines 서비스는 *Kubernetes API 서버를 호출하여 Pipelines을 실행하는 데 필요한 Kubernetes 리소스 (CRD)를 만듬 

### 오케스트레이션 컨트롤러 (컨테이너(Job) 실행)
일련의 오케스트레이션 컨트롤러는 Pipelines을 완료하는데 필요한 컨테이너를 실행하며, 컨테이너는 가상 머신의 Kubernetes pod 내에서 실행됨 

### Pipeline Persistence Agent 
 파이프라인 서비스가 생성한 리소스를 추적하고 메타데이터 서비스의 리소스 상태를 기록 / 컨테이너 실행 추적
 
### Kube API Server
API 서버는 최종 사용자, 클러스터의 다른 부분 그리고 외부 컴포넌트가 서로 통신할 수 있도록 HTTP API를 제공한다. 쿠버네티스 API를 사용하면 쿠버네티스의 API 오브젝트(예: 파드(Pod), 네임스페이스(Namespace), 컨피그맵(ConfigMap) 그리고 이벤트(Event))를 질의(query)하고 조작

### Metadata
Experimentation, Job, Pipelines 실행 및 모델 Evaluation.

### Artifact
Kubeflow Pipelines는 Minio 서버 또는 Cloud Storage와 같은 Artifact 저장소에 Artifact를 저장

  
## 설치 방법
[참고](https://www.manceps.com/articles/tutorial/how-to-install-kubeflow-on-various-operating-systems
)
## KFP 용어
### Component
특정 프로그램, entrypoint를 컨테이너 안에서 표현한 것(함수와 같이 input/output 존재, yaml 포맷으로 구성)
- Metadata : name, description
- Interface : Input / Output 정의
- Data passing : Input / Output의 Args(Command-line 또는 파일 경로)

### Step
Pipeline의 Component 내 실행 단위 (컴포넌트 연결 표시)

### Experiment
Pipeline이 실행되는 workspace (파이프라인은 Workspace 안에서 실행)

### Run and Recurring run (Experiment 내 포함)
- Run : Pipeline의 Single execution (runtime graph, output artifact, log 포함)
- Recurring run : pipeline의 반복 실행 단위

### Artifact
모델 Train과 test 결과 (log, model)

### DSL
Python to yaml format

## KFP SDK
KFP SDK : ML Workflow를 지정하고 실행하는데 사용할 수 있는 Python Package

|Type|목적|설명|
|-----|---------|---------|
|kfp.compiler|파이프라인을 컴파일|(YMAL 형식으로, Argo workflow 형태)|파이프 라인 구성 요소에 대한 Docker 컨테이너 이미지를 빌드하기 위한 클래스 및 메소드를 포함|
|kfp.components|구성된 파이프라인 Load / 파이프라인 내 함수 호출|파이프 라인과 interacting하기 위한 Class 및 Method를 포함|
|kfp.containers|컴포넌트 컨테이너 이미지를 빌드||
|kfp.dsl|파이프 라인 및 컴포넌트를 정의하고 상호작용|파이프 라인 및 구성 요소를 정의하고 상호 작용하는 데 사용할 수 있는 DSL이 포함|
|kfp.client|Pipeline 실행|Kubeflow Pipelines를 위한 client libraries|

## KFP CLI Tool
|Type|목적|설명|
|----|---------|---------|
|kfp diagnose_me|지정된 파라미터로 환경 진단|`json :` 명령 실행 결과를 JSON으로 반환하도록 합니다. 별도로 설정하지 않으면, 결과는 사람이 읽을 수 있는 형식으로 반환됩니다. `namespace TEXT :` 대상 쿠버네티스 네임스페이스를 지정합니다. 별도로 설정하지 않으면, 모든 네임스페이스를 대상으로 합니다.|
|kfp pipeline <COMMAND>|파이프 라인을 관리|`get :` Kubeflow Pipelines 클러스터의 Kubeflow 파이프 라인에 대한 상세한 정보를 조회합니다. `list :` Kubeflow Pipelines 클러스터에 업로드 된 파이프 라인 목록을 조회 합니다. `upload :` Kubeflow Pipelines 클러스터에 파이프 라인을 업로드합니다.|
|kfp run <COMMAND>|파이프 라인 실행|`get :` 파이프 라인 실행의 상세한 정보를 조회합니다. `list :` 최근 실행한 파이프 라인 실행 목록을 조회 합니다 submit – 파이프 라인을 실행 시킵니다.|

## 컴포넌트 생성 실습
### 순서
#### 1. 컴포넌트 작성 
컴포넌트에서 사용할 소스코드 작성, 다른 컴포넌트로부터 데이터를 받기 위해서, 파일이나 명령행 인수를 사용해야 함
#### 2. 컴포넌트 도커이미지 생성

#### 3. 컴포넌트 만들기 - 컴포넌트 빌드
컴포넌트의 데이터 모델을 정의하기 위해서 YAML 형식의 파일을 작성
```
1. Kubeflow Pipelines DSL을 사용하여 컴포넌트 함수 (ContainerOp)를 작성한다.

2. image 파라미터에 컴포넌트에서 사용하는/생성한 컨테이너 이미지를 입력한다.

3. 컴포넌트 함수는 kfp.dsl.ContainerOp를 리턴한다(선택적으로 kfp.dsl.component 라는 데코레이터를 사용하여 DSL 컴파일러에서 정적 타입 검사가 가능) : 파이프라인 스탭 생성

* ContainerOp : 컨테이너 이미지로 구현된 파이프 라인 작업 (op)을 나타냄.
* dsl.component : 파이프 라인 구성 요소를 반환하는 DSL 함수의 데코레이터독립적으로 수행가능한 Component(container 형태)로 패키징
```

#### 4. 파이프라인 빌드
```
1. Kubeflow Pipelines DSL을 사용하여 파이프 라인 함수를 작성한다.

2. 파이프 라인을 정의하는 함수에 사용할 컴포넌트들을 추가한다.

3. 파이프 라인 함수에서 파이프 라인을 빌드하기 위해서 kfp.dsl.pipeline 데코레이터를 사용합니다. 
(데코레이터를 사용하려면 @kfp.dsl.pipeline 어노테이션을 파이프 라인 함수에 추가 하면 됩니다.)


* kfp.dsl.pipeline: 파이프 라인을 빌드하여 반환하는 Python 함수의 데코레이터
```
#### 5. 파이프라인 컴파일 후 업로드
```
1. 파이프 라인을 컴파일하여 압축 된 YAML 파일을 생성한다. 
2. YAML 파일에는 파이프 라인 실행을 위한 쿠버네티스 리소스들이 정의되어 있습니다. kfp.compiler.Compiler.compile 메소드를 사용하여 컴파일 
3. Kubeflow Pipelines SDK 를 사용하여 파이프라인을 업로드하고 실행한다.

* kfp.compiler.Compiler.compile  : k8s Pipelines service가 가능하게 Python DSL Code를 YAML형식으로 변환
```
### 파이프라인 패턴
1. Creating components within your application code
2. Creating components from existing application code
3. Using prebuilt, reusable components in your pipeline
