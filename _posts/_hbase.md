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
사전적으로 `밀집도가 낮은` 이라는 의미인데 보통 데이터 베이스의 특성에서 `sparse` 하다는 것은 하나의 row에 대한 모든 column이 값을 가지지 않아도(`null` or `empty`) 된다는 것을 의미합니다. 단순히 값을 가지지 않게 해주는 것이 아니라, 이러한 상태를 비용 없이 처리할 수 있게 해줍니다. 만약 수 백만, 수 천만개의 row에서 비어 있는 column을 저장할 때 그것을 나타내는 어떠한 값을 가진다면 엄청난 낭비가 될 것입니다. 이러한 `sparse`한 특성은 동작 중 column을 추가하거나 변경 하는 등의 `schema`를 변경한다고 하더라도 이러한 특성으로 인해 부담 없이 변경 가능하게 해줍니다. 그리고 이를 통해 NOSQL로 분류 되는 데이터 베이스의 특징인 `schmaless`함을 지향할 수 있게 됩니다.

### Distributed
`HBase`는 `Apache Hadoop` 기반으로 동작합니다. `Hadoop`의 `HDFS(Hadoop Distributed File System)`은 그 이름처럼 분산 환경 기반의 신뢰성있는 스토리지를 제공하고 있습니다. 또한 `HBase`의 테이블은 작은 chunk단위로 분할 되어 여러 대의 서버에 나누어 저정하는 방식을 따르고 있습니다. 

### Persistent


### Multi-dimensional
`HBase`는 데이터를 구분하기 위해 크게 아래의 구분자(`coordinator`)를 사용하고 있습니다.
- Table
- Row key
- Column Family
- Column Qualifier
- Timestamp

위와 같은 구분자의 조합을 통해 데이터가 구분 되고 이러한 조합이 다차원의 데이터 모델로 표현 됩니다. 많이 친숙한 `MySQL`의 경우 데이터를 `row`와 `column`의 조합으로 구분하는 `2D coordinate` 시스템이라고 표현할 수 있습니다.

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