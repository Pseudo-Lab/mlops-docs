# Setup

`Minikube 설치 -> Kubeflow 파드 생성 -> 대시보드 포트 열기` 순서로 진행

[구성 요소에 대한 설명](../Kubeflow/Intro.md)

## version
- kubernetes-version=v1.21.7 
- kustomize v3.10.0
- kubeflow v1.4.0


## 1. minikube

```sh
minikube start --kubernetes-version=v1.21.7 
```

## 2. Kubeflow

### kustomize
```sh
wget https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize%2Fv3.10.0/kustomize_v3.10.0_linux_amd64.tar.gz
tar -zxvf kustomize_v3.10.0_linux_amd64.tar.gz sudo mv kustomize /usr/local/bin/kustomize 
```

### kubeflow manifest

```sh
git clone -b v1.4.0 https://github.com/kubeflow/manifests.git 
cd manifests
while ! kustomize build example | kubectl apply -f -; do echo "Retrying to apply resources"; sleep 10; done
```

## 3. dashboard

`설치 시 모든 파드 생성 완료까지 최소 15분 가량 소요`

```sh 
kubectl get pods -A # 설치확인 (Pod 상태 확인 가능)
kubectl port-forward svc/istio-ingressgateway -n istio-system 8888:80 # Dashboard 포트 열기 (8888 to 80
```

## Troubleshooting