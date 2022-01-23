---
layout: post
toc: true
title:  "리눅스 데몬(Daemon)이란?"
date:   2022-01-23 16:18:00
description: Linux, Unix, Daemon, OS
categories: Linux
tags: [Linux, OS]
---

# 리눅스 데몬

리눅스 데몬에 대해 이해하기 전 프로세스에 대해 이해하고 있어야한다.

## 프로세스?

프로세스의 개념은 잘 정리된 많은 자료들이 있다. 여기서는 간략하게 예시로 설명하고 넘어가면 **프로세스**는  보조기억장치(HDD, SDD)에 저장된 프로그램이 메모리에 로딩되어 활성화 된 것이라고 생각할 수 있다. 예를 들어 크롬을 실행하여 화면에 나타난 상태를 **프로세스**라고 할 수 있다. 반면 화면에 나타나지 않고 백그라운드로 실행되고 있는 것을 백그라운드 프로세스라고 한다. 작업관리자를 열어 프로세스 탭을 눌러 확인해보면 많은 백그라운드 프로세스들을 확인 할 수 있다. 

## 데몬 (Daemon) ?

리눅스 시스템이 처음 가동 될 때 실행되는 백그라운드 프로세스의 일종으로 메모리에 상주하며 특정 요청이 오면 즉시 대응할 수 있도록 대기중인 프로세스이다. 

예를 들어 웹 서버 프로세스, 네임 서버 프로세스, telnet, DB 서버 프로세스 등이 있다. 백그라운드에서 실행되는 프로세스이며 오랫동안 유지되는 프로세스이다. 

데몬과 일반 백그라운드 프로세스와 차이점은 데몬은 제어 터미널이 없고, 표준 입출력이 없다. 즉, 사용자로부터 직접적인 입출력을 받지 않는다. 

데몬은 관례상 프로세스 이름이 d로 끝난다. 

### 데몬의 실행방식

데몬의 실행 방식은 크게 2가지이다. 

- **standalone 방식**
    
    독자적으로 구동되는 방식으로 혼자서 요청을 받아 처리하며 메모리 상에 항상 구동되고 있으므로 응답속도가 빠르나 메모리에 부하를 줄 수 있다. 요청이 빈번하게 일어나는 프로세스에서 많이 쓰인다.
    
- **xinetd 방식**
    
    슈퍼데몬이라고도 하는데 다른 데몬들의 상위에 존재한다. 요청이 오면 xinetd에 종속되어 있는 하위 데몬을 실행시키는 방식이지만 xinetd 자체는 standalone 방식으로 작동한다. 응답 처리 속도가 standalone 방식에 비해 느리지만 요청이 들어오지 않을 때는 **휴면 상태**로 메모리를 사용하지 않으며 요청이 빈번하지 않은 서비스에서 사용된다. 
    

CentOS 6에서는 service 명령어이며, CentOS 7에서는 systemctl 명령어를 사용한다. 

CentOS 6  이전 버전은 /etc/rc.d/init.d/ 디렉토리에 있는 서비스를 관리 하고 service 명령어에 의해 제어 할 수 있다. 

그러나, CentOS 7 부터는 몇몇 관리 서비스를 제외하고 각 서비스 유닛으로 변경되었으며, 서비스 유닛은 .service으로 끝나는 파일이다. systemctl 명령어에 의해 제어할 수 있다. 

### 서비스 시작, 종료, 재시작

CentOS 7 이후 기준

- 서비스 시작 : systemctl start [서비스 이름]
    
    ```bash
    [root@server ~]# systemctl start sshd
    ```
    
- 서비스 재시작 : systemctl restart [서비스 이름]
    
    ```bash
    [root@server ~]# systemctl restart sshd
    ```
    
- 서비스 종료 : systemctl stop [서비스 이름]
    
    ```bash
    [root@server ~]# systemctl stop sshd
    ```
    

### 설정 정보 변경, 서비스 상태 확인

```bash
[root@server ~]# systemctl reload sshd

[root@server ~]# systemctl status sshd
```

### CentOS 부팅시 데몬 자동 시작 명령어 사용법

자동 시작 여부를 설정하기 위해 chkconfig와 systemctl의 명령어를 사용한다. 

- 자동시작 활성화/비활성화
    
    ```bash
    [root@server ~]# systemctl enable sshd
    [root@server ~]# systemctl disable sshd
    
    CentOS 6 이하에서는 
    [root@server ~]# chkconfig sshd on
    [root@server ~]# chkconfig sshd off
    ```
    
- 자동시작 활성화 여부 확인
    
    ```bash
    [root@server ~]# systemctl is-enabled sshd
    ```