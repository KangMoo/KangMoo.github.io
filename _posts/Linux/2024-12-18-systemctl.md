---
layout: post
title: systemctl을 사용하여 커스텀 서비스 등록방법
date: 2024-12-18
categories: [Linux]
tags: [Linux, OS]
author: KangMoo
summary: 리눅스의 systemctl 명령어를 사용하여 서비스를 관리하는 방법을 알아보자.
---

## systemctl 이란?

시스템의 서비스를 관리하고 제어하는 프로세스는 `systemd`이다.

이러한 **`systemd`를 제어하기 위해 사용하는 명령어 도구가 `systemctl`**이다.

---

## `systemctl`의 기본 명령어

### 서비스 관리 명령어

`systemctl`은 서비스의 시작, 중지, 상태 확인 등을 처리한다.

| 명령어                       | 설명                       |
| ---------------------------- | -------------------------- |
| `systemctl start <서비스>`   | 서비스를 시작한다.         |
| `systemctl stop <서비스>`    | 서비스를 중지한다.         |
| `systemctl restart <서비스>` | 서비스를 재시작한다.       |
| `systemctl reload <서비스>`  | 설정 파일을 다시 로드한다. |
| `systemctl status <서비스>`  | 서비스 상태를 확인한다.    |

### 부팅 시 서비스 활성화/비활성화

Linux에서는 특정 서비스를 부팅 시 자동으로 실행하거나 비활성화할 수 있다.

| 명령어                          | 설명                               |
| ------------------------------- | ---------------------------------- |
| `systemctl enable <서비스>`     | 부팅 시 서비스를 활성화한다.       |
| `systemctl disable <서비스>`    | 부팅 시 서비스를 비활성화한다.     |
| `systemctl is-enabled <서비스>` | 서비스가 활성화 상태인지 확인한다. |

---

## 커스텀 서비스 등록

`systemctl`을 사용하여 커스텀 서비스를 등록할 수 있다.

커스텀 서비스를 등록하려면 다음과 같은 단계를 따르면 된다.

1. 서비스 파일 생성
2. 서비스 파일 리로드
3. 서비스 시작 및 자동 시작 설정
4. 상태 확인 및 로그 보기

### 서비스 파일 생성

서비스 파일은 `/etc/systemd/system/` 디렉토리에 위치해야 하며, `.service` 확장자를 가진다. (예시: `myprocess.service`)

서비스 파일 내용은 다음과 같이 작성한다.

```ini
[Unit]
Description=My Custom Process
After=network.target

[Service]
ExecStart=/path/to/your/program --your-arguments
WorkingDirectory=/path/to/working/directory
Restart=always
User=your-username
Group=your-groupname

[Install]
WantedBy=multi-user.target
```

각 필드에 대한 설명은 다음과 같다.

- `[Unit]`
  - `Description`: 서비스 설명.
  - `After`: 서비스 시작 순서를 지정 (예: `network.target` 이후 시작).
- `[Service]`
  - `ExecStart`: 실행할 명령어 경로와 인자.
  - `WorkingDirectory`: 프로세스가 실행되는 작업 디렉토리.
  - `Restart`: 프로세스 실패 시 재시작 정책 (`always`, `on-failure` 등).
  - `User`/`Group`: 프로세스를 실행할 사용자와 그룹.
- `[Install]`
  - `WantedBy`: 서비스가 어떤 target에서 활성화될지 지정 (`multi-user.target`은 일반적인 시스템 레벨).

> 더 많은 내용을 알고 싶다면 다음을 참고한다 : [https://www.freedesktop.org/software/systemd/man/systemd.service.html](https://www.freedesktop.org/software/systemd/man/systemd.service.html)

서비스 파일을 정상적으로 생성했다면, 거의 다 완료된 것으로 볼 수 있다.

### 서비스 파일 리로드

서비스 파일을 생성하거나 수정한 경우, `systemctl`에게 변경 사항을 알려야 한다.

```bash
sudo systemctl daemon-reload
```

### 서비스 시작 및 자동 시작 설정

서비스를 시작하고 부팅 시 자동으로 시작하도록 설정한다.

```bash
# 서비스 시작
sudo systemctl start myprocess

# 부팅 시 자동 시작
sudo systemctl enable myproces
```

### 상태 확인 및 로그 보기

서비스 상태 확인

```bash
sudo systemctl status myprocess
```

서비스 로그 확인

```bash
sudo journalctl -u myprocess
```

> `journalctl` 명령어는 `systemd`가 생성하는 로그를 볼 수 있는 명령어이다.
