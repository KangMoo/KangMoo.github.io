---
layout: post
title: Python venv란?
date: 2023-11-19
category: [Python]
tags: [Python, venv]
---

## Python venv 란?

![Python venv](/assets/img/2023-11-19-python-venv/2023-11-19-23-40-18.png)

- Python은 기본적으로 라이브러리와 패키지들이 시스템의 전역 환경에 설치된다.
  - 각 프로젝트별로 다른 버전의 라이브러리가 필요한 경우 충돌 문제가 발생했다.
- `venv` 는 '가상 환경'을 만들어 Python 프로젝트에 대해 독립적인 개발 환경을 제공한다.
  - 서로 다른 프로젝트에서 같은 라이브러리의 다른 버전이 필요한 경우, `venv` 는 이러한 요구 사항을 충돌 없이 관리할 수 있게 한다
  - 각 가상 환경은 완전히 독립적이므로, 하나의 환경에서 이루어진 변경이 다른 환경에 영향을 미치지 않는다.
- `venv` 는 Python 3.3 버전부터 표준 라이브러리의 일부로 포함되었다. 그러니 별도의 설치 과정 없이 사용할 수 있다.
  - 이전 버전에서는 `virtualenv` 라는 별도의 패키지를 사용했다.

## venv 설정하기

가상 환경을 생성하고 활성화하는 단계별 과정은 다음과 같다.

### 1. 가상 환경 생성

- 가상 환경을 생성하려면 먼저 프로젝트의 루트 디렉토리로 이동한 뒤, 터미널에서 다음 명령어를 입력하여 새로운 가상 환경을 생성한다.

```bash
python -m venv myenv
```

- 여기서 `myenv` 는 생성될 가상 환경의 이름이며 원하는 다른 이름을 설정하면 된다.
- `myenv` 디렉토리가 생성되면, 이 안에는 Python 인터프리터, 표준 라이브러리, 스크립트들이 포함된 bin (또는 Windows에서는 Scripts) 디렉토리 및 가상 환경을 관리하는 데 필요한 모든 것이 포함되어 있다.

### 2. 가상 환경 활성화

- 생성된 가상 환경을 사용하기 위해 활성화해야 한다.

- Linux/macOS에서

  ```bash
  source myenv/bin/activate
  ```

- Windows에서

  ```cmd
  myenv\Scripts\activate
  ```

### 3. 가상 환경 비활성화

- `deactivate` 명령어로 활성화하고 원래의 시스템 환경으로 돌아갈 수 있다.

  ```bash
  deactivate
  ```

## 가상 환경 활용

### 패키지 관리

가상 환경을 활성화한 후, `pip` 를 사용하여 필요한 Python 패키지들을 설치 및 관리를 할 수 있다.

- 패키지 설치

  ```bash
  pip install package_name
  ```

  > 프로젝트에 필요한 패키지를 설치하는 명령어. `package_name`은 설치하려는 패키지의 이름이다

- 패키지 목록 확인

  ```bash
  pip list
  ```

  > 설치된 패키지와 그 버전을 확인한다

### 의존성 관리

프로젝트의 의존성을 효과적으로 관리하려면, `requirements.txt` 파일을 사용하는 것이 일반적이며, 가상 환경에서도 역시 `requirements.txt` 를 사용이 가능하다.

- 의존성 목록 생성

  ```bash
  pip freeze > requirements.txt
  ```

  > 현재 환경에 설치된 모든 패키지와 그 버전을 requirements.txt 파일에 기록한다.

- 의존성 설치

  ```bash
  pip install -r requirements.txt
  ```

  > 프로젝트를 다른 환경으로 이동시킬 때, `requirements.txt` 파일을 사용하여 동일한 의존성을 쉽게 설치할 수 있다.

## 팁

### git 사용 시

- `.gitignore` 파일에 가상 환경 폴더 이름을 추가하여, 가상 환경 폴더를 버전 관리 대상에서 제외함으로써 불필요한 파일들이 저장소에 포함되는 것을 방지할 수 있다.
- 프로젝트의 `requirements.txt` 파일을 저장소에 포함시켜, 다른 개발자들이 필요한 모든 의존성을 쉽게 설치할 수 있도록 한다.

### 여러 버전 Python 사용

- 서로 다른 Python 버전을 사용하는 여러 프로젝트를 관리할 때는, 각 프로젝트에 맞는 Python 버전으로 가상 환경을 설정하는 것이 좋다. 예를 들어, Python 3.7을 사용하는 프로젝트와 Python 3.8을 사용하는 프로젝트는 각각 다른 가상 환경을 필요로 한다.
- 특정 Python 버전으로 가상 환경을 생성하려면, 해당 Python 인터프리터를 직접 지정할 수 있다. 예를 들어, Python 3.8을 사용하여 가상 환경을 생성하려면 다음과 같이 명령어를 사용한다.

  ```bash
  python3.8 -m venv myenv
  ```
