---
layout: post
title: PyInstaller - Python 프로그램을 실행파일로 만들기
date: 2025-1-16
categories: [Python]
tags: [Python, PyInstaller]
summary: 
---


## PyInstaller란?

PyInstaller는 파이썬 스크립트를 손쉽게 바이너리 실행 파일로 만들어주는 패키지다.

## PyInstaller 설치 및 사용

PyInstaller를 설치하려면 다음과 같이 실행한다.

```bash
pip install pyinstaller
```

PyInstaller를 사용하려면 다음과 같이 실행한다.

```bash
pyinstaller <options> ${파일명}.py
```

## PyInstaller 사용 예시

실행 파일로 만드려는 파이썬 스크립트가 `hello.py`라고 한다면, 다음과 같이 실행 파일을 만들 수 있다.

```bash
pyinstaller --onefile hello.py
```

> `--onefile` 옵션을 사용하면 단일 파일로 실행 파일을 생성한다.

성공적으로 명령이 수행되면 다음과 같은 파일 및 디렉터리가 생성된다.

- `dist` 디렉터리: 실행 파일이 생성되는 디렉터리
- `build` 디렉터리: 빌드 관련 파일이 생성되는 디렉터리
- `hello.spec` 파일: 실행 파일을 생성하는 데 필요한 정보를 담고 있는 파일

최종적으로 생성되는 바이너리 파일은 `dist` 디렉터리에 `hello`라는 이름으로 생성된다.
> 이름을 바꾸고 싶다면 `--name` 옵션을 사용하면 된다.

PyInstaller는 기본적으로 파이썬 스크립트에서 사용하는 모듈을 분석하여 실행 파일에 시키지만, 참조를 못하는 경우가 있다.

만약 외부 참조가 필요한 경우 다음 옵션을 사용해서 명시적으로 추가할 수 있다.

- `--paths`: 모듈 검색 경로를 추가한다.
- `--hidden-import`: PyInstaller가 자동으로 찾지 못하는 숨겨진 import를 추가한다.
- `--collect-all`: 지정된 모듈의 모든 리소스(서브 모듈, 데이터, 바이너리)를 수집한다.

---

이 밖에도 많은 옵션들이 있으니 필요한 경우 공식 문서를 참고한다.

[https://pyinstaller.org/en/stable/man/pyinstaller.html](https://pyinstaller.org/en/stable/man/pyinstaller.html)
