# 🙈 PAM을 사용하여 비밀번호를 8자리 이상으로 규제하는 법

## 1. 👀작업 환경
> Virtual Box 새로운 서버 만들고 고정 ip 할당 10.0.2.21 -> PAM 사용하여 비밀번호 8자리 이상으로 규제
### 1.1 📌고정 ip 만들기 10.0.2.21
#### 1.1.1 💻 Virtual 머신에서 NAT 설정을 통해 모바엑스텀이나 VSC에서 오픈
![](https://velog.velcdn.com/images/yuwankang/post/f3d494fe-136e-41cc-881c-998faed4d8e4/image.png)
#### 1.1.2 🔗 모바엑스텀 연결 완료
![](https://velog.velcdn.com/images/yuwankang/post/6307e721-3a76-4b58-b99a-50432c77a331/image.png)
#### 1.1.3 🔍 00-installer-config.yaml 파일 위치 찾고 오픈하기
![](https://velog.velcdn.com/images/yuwankang/post/ee6acf5e-664b-4da1-a1f6-b6b69f85dfe0/image.png)

#### 1.1.4 ✍️ 00-installer-config.yaml 코드 수정
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      addresses:
        - 10.0.2.21/24  # 변경된 고정 IP 주소
      routes:
        - to: default
          via: 10.0.2.1  # 게이트웨이
      nameservers:
        addresses:
          - 8.8.8.8
      dhcp4: false
```
  
![](https://velog.velcdn.com/images/yuwankang/post/e5b63979-adf0-4d9f-8ba2-ef9b3fd98875/image.png)

#### 1.1.5 🌐 고정 IP 할당 후 NAT 네트워크 방식으로 변경
  
![](https://velog.velcdn.com/images/yuwankang/post/012a3c1d-ba05-483a-95d7-3291ffeaa20e/image.png)

#### 1.1.6 🛠 VSC 연결을 위한 config 파일 수정
```
Host myserver02
    HostName 127.0.0.1
    User username
    Port 8022

Host myserver03
    HostName 127.0.0.1
    User username
    Port 7022

Host myserver01
    HostName 127.0.0.1
    User username
    Port 22
```
![](https://velog.velcdn.com/images/yuwankang/post/a26b6600-81a9-4373-8fd0-46f648698a55/image.png)

#### 1.1.7 ✅ 바뀐 IP 확인
  
![](https://velog.velcdn.com/images/yuwankang/post/ad4353cb-8613-4b51-bbc7-7a4f417ec2af/image.png)
## 2. 🔐 PAM을 사용하여 비밀번호를 8자리 이상으로 규제하기
#### 2.1 🛠 libpam-pwquality 패키지 설치
  
```
sudo apt update
sudo apt install libpam-pwquality
```
#### 2.2 📝 PAM 설정 파일 수정
- 비밀번호 정책은 /etc/pam.d/common-password 파일에서 설정
```
sudo vi /etc/pam.d/common-password
```
#### 2.3 🖋 아래 줄을 찾고 minlen=8 추가
```
password requisite pam_pwquality.so retry=3
```
![](https://velog.velcdn.com/images/yuwankang/post/96d1395b-8460-45e8-814b-874c3a8a450e/image.png)
#### 2.4 ✨ 추가 가능한 설정
```
etry=3: 사용자가 3번 비밀번호 입력을 시도할 수 있도록 설정합니다.
minlen=8: 최소 비밀번호 길이를 8자로 설정합니다.
dcredit=-1: 적어도 1개의 숫자가 포함되어야 함.
ucredit=-1: 적어도 1개의 대문자가 포함되어야 함.
lcredit=-1: 적어도 1개의 소문자가 포함되어야 함.
ocredit=-1: 적어도 1개의 특수 문자가 포함되어야 함.
```
#### 2.5 🚫 8자리 미만의 비밀번호로 변경 시 오류 메시지

![](https://velog.velcdn.com/images/yuwankang/post/e84d2b9e-0515-4c68-9622-8a117fd19f26/image.png)

#### 2.6 🔐 비밀번호 변경 완료
  
![](https://velog.velcdn.com/images/yuwankang/post/11034c9a-aac4-4b5e-bfc2-c9274fab6059/image.png)
