---
layout: post
title: "HBase"
description: "HBase 정리."
categories: blog
tags: [hbase]
image:
feature:
date: 2019-06-02T21:31:50-04:00
---

> `HBase in action`으로 학습한 것을 정리한 내용입니다.

# HBase란?
## HBase의 특징
`HBase`는 `Hadoop`을 기반으로 구현한 컬럼 기반의 분산 데이터 베이스입니다. `HBase`에 대한 여러 가지 설명이 있을 수 있지만, 기본적으로 `random access data를 저장하고 검색하는 플랫폼`입니다. 이 때 저장되는 데이터는 타입에 관계 없으며 너무 크지만 않다면 크기적인 제한도 없는 동적이고 유연한 데이터 모델을 제공합니다.  
`HBase`를 대표하는 특징 들은 아래와 같습니다.
- Sparse
- Distributed
- Persistent
- Multi-dimensional
- Sorted-map

### Sparse

### Distributed

### Persistent

### Multi-dimensional

### Sorted-map


## RDB와의 차이점
- SQL을 사용할 수 없습니다.
- 데이터 내의 관계를 정의할 수 없습니다.
- Row 간 트랜잭션을 할 수 없습니다.
- 동일 컬럼에 속한 로우일 지라도 각각 다른 타입의 데이터를 저장할 수 있습니다.
- Row가 아닌 `Column`이 중심입니다.
- 하나의 Row에 대해서만 인덱스를 설정할 수 있습니다.
- 수 천 TPS인 RDB에 비해 많은 데이터를 처리할 수 있습니다. (수 백만 TPS)

`HBase`는 단일 노드 뿐만이 아니라 분산 클러스터에서 동작하도록 설계 된 데이터 베이스입니다. 유일한 노드가 존재하지 않기 때문에 유연하고 확장 가능한 환경을 제공합니다. 대부분의 RDBMS 솔루션이 부가 기능으로서 분산 처리 기능을 제공하는 반면, `HBase`는 노드 추가만으로 선형 확장이 가능한 토대 위에서 시작 하였습니다.