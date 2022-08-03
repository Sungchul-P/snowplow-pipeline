# 쿠버네티스 및 카프카 클러스터 설치

## 전제 조건

```bash
# Validate docker installation
docker ps
docker version

# Validate kind
kind version

# Validate kubectl
kubectl version
```

## 쿠버네티스 클러스터 설치

```bash
kind create cluster --config 1-kind-config.yaml
```

## 카프카 클러스터 설치 (feat. Strimzi Operator)

### Strimzi Operator 설치(Operator, CRDs)

```bash
kubectl create namespace kafka
kubectl create -f 2-strimzi-operator.yaml -n kafka
```

### 카프카 클러스터 및 커넥트 배포(CR)

```bash
kubectl apply -f 3-kafka-persistent.yaml
kubectl apply -f 4-kafka-connect.yaml

# 메시지 전송 테스트(토픽 자동 생성)
kubectl -n kafka run kafka-producer -ti --image=quay.io/strimzi/kafka:0.30.0-kafka-3.2.0 --rm=true --restart=Never -- bin/kafka-console-producer.sh --bootstrap-server snowplow-kafka-bootstrap:9092 --topic my-topic
kubectl -n kafka run kafka-consumer -ti --image=quay.io/strimzi/kafka:0.30.0-kafka-3.2.0 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server snowplow-kafka-bootstrap:9092 --topic my-topic --from-beginning

# Kafka UI (Kafdrop)
kubectl apply -f 5-kafdrop.yaml
```

### 파이프라인에서 사용할 카프카 토픽 생성

```bash
kubectl apply -f 6-kafka-topics.yaml
```
