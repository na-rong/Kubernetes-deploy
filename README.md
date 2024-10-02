# Kubernetes 프로젝트 (2024-10-02) 🚀

## 📝 개요
이 프로젝트는 **Kubernetes 환경에서 Spring app**를 배포하고, 3개의 Pod와 1개의 외부 통신이 가능한 Service를 설정하는 실습 과정입니다. 또한, 간단한 Spring 애플리케이션을 데이터베이스 없이 배포하는 것도 목표로 합니다. 이 과정은 Kubernetes에서 컨테이너 기반 애플리케이션을 어떻게 배포하고 관리하는지에 대한 깊이 있는 이해를 목표로 합니다.


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
## 📈 결론


