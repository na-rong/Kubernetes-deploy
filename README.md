# Kubernetes 프로젝트 🚀

## 📝 개요
이 프로젝트는 **Kubernetes 환경에서 Spring app**를 배포하고, 3개의 Pod와 1개의 외부 통신이 가능한 Service를 설정하는 실습 과정입니다.
<br>
또한, 간단한 Spring 애플리케이션을 데이터베이스 없이 배포하는 것도 목표로 합니다.
<br>
이 과정은 Kubernetes에서 컨테이너 기반 애플리케이션을 어떻게 배포하고 관리하는지에 대한 깊이 있는 이해를 목표로 합니다.


## 📚 학습 및 진행 과정

### 1. JAR 파일 생성
![image](https://github.com/user-attachments/assets/7e2e5dde-313d-4d24-83a5-f186930c950e)

### 2. Dockerfile 생성

```bash
# 베이스 이미지로 OpenJDK 17을 사용
FROM openjdk:17-jdk-slim

# 컨테이너 내 작업 디렉터리 설정
WORKDIR /app

# JAR 파일을 컨테이너로 복사
COPY SpringApp-0.0.1-SNAPSHOT.jar /app/app.jar

# 애플리케이션이 사용할 포트 지정 (8999로 properties에 설정해놓음)
EXPOSE 8999

# JAR 파일 실행
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

### 3. 도커 이미지 생성 및 실행 
- 이미지 이름은 my-spring-app
```bash
 docker build -t my-spring-app .
```
- 도커 이미지 생성 완료
```bash
username@servername:~/springtest$ docker images
REPOSITORY                    TAG         IMAGE ID       CREATED         SIZE
my-spring-app                 latest      121585a3a3bb   4 seconds ago   428MB
```
- 도커 컨테이너 실행
```bash
docker run -p 8999:8999 my-spring-app
```
### 4. 도커 허브에 이미지 푸시
```bash
# Docker hub 로그인
docker login
# 이미지에 태그 추가
docker tag my-spring-app your-dockerhub-username/my-spring-app
# 이미지 푸시
docker push your-dockerhub-username/my-spring-app
```
![image](https://github.com/user-attachments/assets/84c18a41-7757-429f-b816-1859f29d54be)
![image](https://github.com/user-attachments/assets/29365ae0-4d3e-424f-b985-4262072ba6d7)

---
### 5. Spring APP Kubernets에 배포
- 3개의 컨테이너(pods) 생성
```bash
# Minikube 클러스터 시작
minikube start

# Docker Hub에 푸시한 Spring 이미지로 Deployment를 생성한 것을 3개로 구성해서 생성 및 배포
kubectl create deployment spring-app --image=your-dockerhub-username/my-spring-app --replicas=3
![image](https://github.com/user-attachments/assets/5c8ec4fc-8e9f-4517-b976-bab86de2a31d)

# 서비스 확인
kubectl get services
```

- 서비스 생성
```bash
- # Pod 및 서비스 상태 확인
kubectl get pods
kubectl get services

# 배포를 외부 로드밸런서를 통해 포트 80으로 노출하는 설정(로컬 test용)
kubectl expose deployment spring-app --type=LoadBalancer --port=80 --target-port=8999

# Minikube에서 외부 IP 제공받기 위한 터널링
# 전후 확인(대기상태) -> 외부 접속 가능한 활성화 상태로 변경
# ✨ minikube tunnel 명령어 입력후 출력 확인후 ctrl+z로 종료가 아닌 명령 프롬프트로 이동해야 
# 참고 : ctrl+c 는 프로세스 완전 종료, ctrl+z 는 프로세스 일시 정지를 의미 
minikube tunnel  
minikube ip
kubectl get services
```
![image](https://github.com/user-attachments/assets/7f79c53f-4cab-45ad-83d9-c211068f420b)

- 포트 추가해주기
![image](https://github.com/user-attachments/assets/eb0c79fd-53cd-4148-a356-6955ab3c20ec)

### 6. 결과 화면
![image](https://github.com/user-attachments/assets/b27da826-30e3-4593-bff1-27dd706ebe7b)
![image](https://github.com/user-attachments/assets/a3a65341-3c3f-4694-a8c5-924122f9e744)

--- 

## 🚀 트러블 슈팅 
- 이미 실행 중인 터널 프로세스가 있다는 오류
 ![image](https://github.com/user-attachments/assets/433029ed-7b6d-4684-891a-9d671f1216aa)
- 현재 실행 중인 터널 프로세스 확인
```bash
ps aux | grep minikube
```
![image](https://github.com/user-attachments/assets/76dd3841-3ed3-44bd-9c75-c5f261bba022)
위 출력에서 **minikube tunnel**이 이미 실행 중인 프로세스가 있음을 확인할 수 있습니다. 
해당 프로세스의 **PID는 757640**입니다.

- 실행 중인 터널 프로세스 종료 (-9 로 강제 종료)
```bash
sudo kill -9 757640
```
--- 

## 📈 결론
이번 프로젝트를 통해 **Kubernetes**를 이용한 Spring 애플리케이션 배포와 **로드 밸런싱** 적용을 성공적으로 구현했습니다. 특히 **Minikube 환경**에서 Docker 이미지를 생성하고 이를 컨테이너로 배포하는 실습을 통해 실전 경험을 쌓을 수 있었습니다. 또한, 서비스가 외부와 통신할 수 있도록 LoadBalancer를 설정하고 Minikube 터널을 활용하여 로컬에서 테스트할 수 있었습니다.

