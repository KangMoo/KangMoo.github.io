---
layout: post
title: Multi Line to Single Line
date: 2023-11-13
categories: [Code Snippet]
tags: [Code Snippet]
---

이 스크립트의 주 목적은 로그 파일을 처리하여 각 로그 메시지를 단일 줄로 변환하는 것입니다. 이를 통해 로그 파일의 가독성을 향상시키고, 분석이나 검색을 용이하게 합니다.

- 변수 설정: 스크립트는 PATTERN이라는 변수를 사용하여 로그 메시지의 시작 패턴을 정의합니다. 기본값은 날짜와 시간을 나타내는 정규 표현식입니다.
- 입력 파일 처리: 스크립트는 첫 번째 인자로 입력 파일을 받습니다. 인자가 제공되지 않으면 스크립트는 사용 방법을 출력하고 종료됩니다.
- 로그 메시지 처리: awk 명령어를 사용하여 로그 파일을 처리합니다. 이 스크립트는 정의된 패턴을 사용하여 새로운 로그 메시지의 시작을 감지하고, 연속된 줄을 하나의 줄로 결합합니다. 이렇게 하여 각 로그 메시지가 단일 줄로 출력됩니다.

``` bash
# !/bin/bash

# 로그 메시지 시작 패턴을 변수로 저장. 기본값은 날짜와 시간 패턴 (YYYY-MM-DD HH:MM:SS)
# 필요에 따라 이 값을 원하는 정규 표현식으로 변경하여 사용

PATTERN='^.[0-9]{4}-[0-9]{2}-[0-9]{2}'

INPUT_FILE="$1"


# 인자가 제공되지 않았을 경우 스크립트 종료

if [ -z "$INPUT_FILE" ]; then
    echo "Usage: $0 <INPUT_FILE>"
    exit 1
fi

# 각 로그 메시지를 한 줄로 변환하여 출력
# 이는 로그 시작 패턴에 따라 새로운 메시지를 감지하고, 연속된 줄을 이어 붙여 처리
awk -v pattern="$PATTERN" \
    '{
        if ($0 ~ pattern) {
            if (NR != 1) print line;
            line=$0;
        } else {
            line=line" "$0;
        }
    }
    END {
        print line;
    }' "$INPUT_FILE"

```
