---
layout: post
title: 대수적 성질 (Algebraic Properties)
date: 2023-11-26
categories: [Basic Algebra]
tags: [Basic Algebra, Algebraic Properties]
---

{% include katex.html %}

대수학은 수학의 한 분야로, 수와 수의 조합으로 이루어진 식들을 연구한다.

대수학에서 중요한 역할을 하는 주요 대수적 성질들에 대해서 알아보자

## 결합 법칙 (Associative Property)

- 연산을 수행하는 순서가 결과에 영향을 주지 않는 성질
  - 세 개 이상의 수를 더하거나 곱할 때, 괄호를 어떻게 설정하든 결과가 동일하다

- 덧셈의 결합 법칙 : $$(a + b) + c = a + (b + c)$$
- 곱셈의 결합 법칙 : $$(a \times b) \times c = a \times (b \times c)$$

## 교환 법칙 (Commutative Property)

- 숫자들의 순서를 바꿔도 연산 결과가 바뀌지 않는 성질
- 덧셈의 교환 법칙 : $$a + b = b + a$$
- 곱셈의 교환 법칙 : $$a \times b = b \times a$$

## 분배 법칙 (Distributive Property)

- 두 가지 이상의 연산이 복합적으로 이루어질 때 적용되는 법칙
  - ex) $$a \times (b + c) = a \times b + a \times c$$

## 항등 법칙 (Identity Property)

- 특정 값을 연산에 적용했을 때 원래의 수가 그대로 유지되는 성질
  - 덧셈과 곱셈에서 각각 0과 1이 이러한 역할을 한다
- 어떤 집합에서 다른 원소와 이항연산을 했을 때 그 결과가 항상 다시 그 원소로 나타나는 원소를 **항등원** 이라고 한다
- 덧셈의 항등원 : `0`. 어떤 수에 `0`을 더해도 그 수는 변하지 않는다
  - ex) $$a + 0 = a$$
- 곱셈의 항등원 : `1`. 어떤 수에 `1`을 곱해도 그 수는 변하지 않는다
  - ex) $$a \times 1 = a$$

## 역원 법칙 (Inverse Property)

- 어떤 수에 대해 덧셈 혹은 곱셈을 통해 항등원(덧셈의 경우 0, 곱셈의 경우 1)을 얻을 수 있는 수를 찾는 법칙
  - 덧셈과 곱셈에서 각각의 역원을 더하거나 곱하면 항등원이 되는 원리
- 변수 a에 대한 덧세의 역원 : `-a`
  - ex) $$a + (-a) = 0$$
- 변수 a에 대한 곱셈의 역원 : `1/a`
  - ex) $$a \times \frac{1}{a} = 1$$
