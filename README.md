# Snowplow Data Pipeline on Kubernetes

## 1. k8s & kafka

### 쿠버네티스 클러스터 설치

```bash
kind create cluster --config 1-k8s-kafka/1-kind-config.yaml
```

### Strimzi Operator 설치(Operator, CRDs)

```bash
kubectl create namespace kafka
kubectl create -f 1-k8s-kafka/2-strimzi-operator.yaml -n kafka
```

### 카프카 클러스터 및 커넥트 배포(CR)

```bash
kubectl apply -f 1-k8s-kafka/3-kafka-persistent.yaml
kubectl apply -f 1-k8s-kafka/4-kafka-connect.yaml

# 메시지 전송 테스트(토픽 자동 생성)
kubectl -n kafka run kafka-producer -ti --image=quay.io/strimzi/kafka:0.30.0-kafka-3.2.0 --rm=true --restart=Never -- bin/kafka-console-producer.sh --bootstrap-server snowplow-kafka-bootstrap:9092 --topic my-topic
kubectl -n kafka run kafka-consumer -ti --image=quay.io/strimzi/kafka:0.30.0-kafka-3.2.0 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server snowplow-kafka-bootstrap:9092 --topic my-topic --from-beginning

# Kafka UI (Kafdrop)
kubectl apply -f 1-k8s-kafka/5-kafdrop.yaml
kubectl port-forward -n kafka svc/kafdrop 9000:9000
```

### 파이프라인에서 사용할 카프카 토픽 생성

```bash
kubectl apply -f 1-k8s-kafka/6-kafka-topics.yaml
```

## 2. Collector

```bash
kubectl apply -f 2-collector/
```

## 3. Tracker

README 참고. (JavaScript Tracker Example)

## 4. Enricher

```bash
kubectl apply -f 4-enricher/
```

## 5. Loader

```bash
kubectl apply -f 5-loader/
```