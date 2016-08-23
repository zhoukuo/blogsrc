---
title: 单元测试规范-Java版
date: 2016-08-23 10:22:46
tags: [测试,java]
---
实施单元测试的时候, 如果没有一份经过实践证明的详细规范, 很难掌握测试的 “度”, 范围太小施展不开, 太大又侵犯 “别人的” 地盘. 上帝的归上帝, 凯撒的归凯撒, 给单元测试念念紧箍咒不见得是件坏事, 反而更有利于发挥单元测试的威力, 为代码重构和提高代码质量提供动力。
<!--more-->
## 测试范围

### 模块
覆盖Spring项目中所有的Service模块

### 方法
每个模块中，公有方法、私有方法、保护方法，静态方法，都要覆盖

## 测试工具
单元测试工具采用TestNG + PowerMockito

## 目录结构
单元测试代码与产品代码分离，放在一个单独的目录中，并且保持与产品代码相同层级创建测试目录，例如：产品代码所在路径为css/src/main/，则测试代码所在路径为css/src/test/

## 命名

### 包名
测试类所在的包名在被测产品包名基础上增加.test后缀，如：被测类包名：cn.org.bjca.css.domain.cert.impl，测试类包名：cn.org.bjca.css.domain.cert.impl.test

### 测试类名
测试类名在被测试类基础上增加Test后缀，如：被测试类为UpdateGetCertImpl，那么测试类名称则为UpdateGetCertImplTest

### 测试方法名
每个被测试类中，每个成员方法包含一个或多个测试方法，如果只有一个测试方法，那么方法名为Test+被测方法名，如：被测方法名为JudgeStatus，那么测试方法名则为TestJudgeStatus

### 被测类对象命名
* 正常情况下被测类对象统一命名为sut(system under test)
* 被spy的对象统一命名为spySut
* 被mock的对象统一命名为mockSut

### 自定义参数匹配器命名
自定义参数匹配器命名规则为Any+类名，如：需要定义匹配器的类为CertUpdateBU，则匹配器的名称为AnyCertUpdateBU

### 变量命名
* 实际返回值无论什么类型，统一命名为：actually
* 期望值无论什么类型，统一命名为：expected

## 断言的使用
任何时候都不要使用assertTrue或assertFalse，优先使用assertEquals/assertNotEquals，assertNull/assertNotNull，后者比前者输出更丰富的调试信息。

## 注释
* 注释统一使用中文，并说明此测试方法包含哪些用例

## 覆盖率
分支覆盖要求达到100%

## 参数匹配器
Mockito内置了一些通用的参数匹配器，如anyObject, anyString, anyInt等，同时用户可以自定义参数匹配器，例如：

```java
package css.matcher;

import org.mockito.ArgumentMatcher;
import cn.org.bjca.css.domain.sys.raconfig.business.RAChannelRule;

public class AnyRAChannelRule extends ArgumentMatcher<RAChannelRule> {
    public boolean matches(Object list) {
        return true;
    }
}
```

**注意：**仅在mock或verify时无法得到确切的参数时使用

## 测试分组

* BVT(版本验证测试)
* 正向测试
* 逆向测试

## 参数化

## 依赖隔离


## 测试方法
