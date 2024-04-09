---
layout: post
title: 집합의 기수 (Cardinality of Sets)
date: 2023-12-02
categories: [Basic Algebra]
tags: [Basic Algebra, Sets]
---

{% include mathjax.html %}

## 집합의 기수(Cardinality of Sets)

$$ |A| = (\# elements) $$

- 집합의 기수(Cardinality of Sets)란 해당 **집합을 구성하는 원소의 수**를 말한다. 이는 집합의 크기를 나타내는 중요한 척도로 사용된다.

### 예시

1. $$ A = \{ 0,1 \} \to |A| = 2 $$
2. $$ B = \{ a,b,c \} \to |B| = 3 $$
3. $$ C = \{ x | \text{x 는 한 자리 정수} \} \to |C| = 10 $$
4. $$ D = \{ x | \text{x 는 알파벳} \} \to |D| = 26 $$
5. $$ |\phi| = 0 $$

> **한원소 집합 (Singleton Set)** : 단 하나의 원소만을 포함하는 집합
> `|A| = 1`

## 집합의 분류

- 집합은 크게 가산 집합과 비가산 집합으로 분류할 수 있다. 여기서 '가산'이란 어떤 집합의 원소를 자연수의 집합과 일대일로 대응시킬 수 있다는 의미이다.

![Classification of Sets](</assets/img/2023-12-02-집합의%20기수%20(Cardinality%20of%20Sets)/2023-12-02-16-01-56.png>)

### 가산 집합 (Countable Sets)

- **유한 집합 (Finite Sets)**: 원소의 개수가 한정된 집합. ex) 처음 100개의 자연수 집합
- **가산 무한 집합 (Countably Infinite Sets)**: 원소의 개수가 무한하지만 자연수와 일대일 대응이 가능한 집합. ex) 유리수 집합.

<details>
  <summary>양의 유리수의 가산성</summary>
  <div markdown="1">

- 양의 유리수는 분모와 분자가 자연수인 수의 집합으로, 이들은 가산 무한 집합이다.
- 이는 양의 유리수를 자연수와 일대일 대응으로 나열할 수 있다는 것을 의미한다.
- 이러한 대응의 한 예로, 분모와 분자의 합이 같은 유리수를 하나의 그룹으로 묶어 나열하는 방식이 있다.
- ![positive rational numbers are countable](</assets/img/2023-12-02-집합의%20기수%20(Cardinality%20of%20Sets)/2023-12-02-15-56-43.png>)
  - 첫 번째로, 분모와 분자의 합 $$ ( p + q ) $$ 가 2가 되는 유리수 $$( \frac{p}{q} )$$ 를 나열한다. 여기서는 단 하나의 수, $$( \frac{1}{1} )$$ 이 있다.
  - 다음으로 합이 3이 되는 유리수를 나열하고, 이 과정을 반복한다.
  - 이때, 이전에 나열된 수를 반복해서 리스트에 포함시키지 않도록 주의한다.
  - 위 방법을 통해 양의 유리수는 다음과 같이 나열될 수 있다. $$ [ 1, \frac{1}{2}, 2, \frac{1}{3}, \frac{3}{1}, \frac{1}{4}, \frac{4}{1}, \frac{2}{3}, \frac{3}{2}, ... ] $$

<!---->
  </div>
</details>

### 비가산 집합 (Uncountable Sets)

- **비가산 무한 집합 (Uncountably Infinite Sets)**: 원소의 개수가 무한하며 자연수와 일대일 대응이 불가능한 집합, 예를 들어 실수 집합.
