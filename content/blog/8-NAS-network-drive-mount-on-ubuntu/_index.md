---
title: "우분투에 NAS 마운트"
subtitle: "NAS에 백업된 다수의 대용량 파일에 쉽게 접근하기"
draft : false
weight: 5
---

## 들어가면서

우리는 [NAS](https://namu.wiki/w/NAS(저장장치))를 사용하면서 데스크탑에 저장할 수 없는 대용량의 자료들을 저장하고는 합니다. 보통 백업의 용도로 사용되는데, 가끔은 외장하드처럼 마운트해서 사용해야 할 때가 있죠. 그래서 여러가지 접속 방법을 사용하여 자신의 PC에 있는 드라이브 처럼 사용할 수 있습니다. 윈도우는 검색하면 많이 나오는데 우분투의 경우에는 많이 없는 것 같아서 기록을 하게 되었습니다.

## 환경

알아야 할 정보들: 

1. NAS IP 
2. NAS access ID
3. NAS access PW
4. 마운트할 nas 경로
5. 마운트할 local 경로

## Methods

### 1. 패키지 설치

마운트를 하기 위한 패키지 설치 단계로 cifs-utils를 설치해주어야 합니다.

```bash
sudo apt-get install cifs-utils
```

### 2. 마운트 하기 위한 폴더 생성

NAS를 현재 우분투의 경로에 마운트를 해 주어야 하는데, 그러기 위해 폴더를 생성해줍니다.

```bash
mkdir /mnt/nas-drive
# /mnt/nas-drive 루트 경로에 추가하기 위해서는 sudo 권한이 필요할 것이며,
# 자신의 디렉토리에 폴더를 생성해서 마운트도 가능합니다.
```

### 3. 네트워크 드라이브 연결 (마운트)

아래와 같은 형식으로 입력을 해주어야 합니다. 마지막에 위치하는 `vers=1.0` 을 입력해주어야 저는 에러가 나지 않더라구요.

```bash
sudo mount -t cifs //{NAS drive IP}/{NAS directory path} {local path} -o user='NAS ID',password='NAS PW',rw,vers=1.0
```

예를 들어:

1. NAS IP : 111.11.11.111
2. NAS access ID : admin
3. NAS access PW : admin
4. 마운트할 nas 경로 : /home
5. 마운트할 local 경로 : /mnt/nas-drive

라는 환경이라면

```bash
sudo mount -t cifs //111.11.11.111/home /mnt/nas-drive -o user='admin',password='admin',rw,vers=1.0
```

가 되겠죠?

### 4. 자동 마운트 등록

4번을 진행하지 않는다면 재부팅 후에는 마운트가 끊어집니다. 계속 연결이 되어야 한다면 `fstab` 에 등록을 해주어서 부팅할 때마다 마운트 하도록 하면 됩니다.

``` bash
sudo vim /etc/fstab

//111.11.11.111/home /mnt/nas-drive cifs user='admin',password='admin',rw,vers=1.0	0	0
```

---

