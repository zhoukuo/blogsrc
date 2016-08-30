---
title: 单元测试规范-Java版
date: 2016-08-26 10:22:46
tags: [测试,java]
---
实施单元测试的时候, 如果没有一份经过实践证明的详细规范, 很难掌握测试的 “度”, 范围太小施展不开, 太大又侵犯 “别人的” 地盘。上帝的归上帝, 凯撒的归凯撒, 给单元测试念念紧箍咒不见得是件坏事, 反而更有利于发挥单元测试的威力, 为代码重构和提高代码质量提供保障。
<!--more-->
## 单元测试框架和工具
Java单元测试框架主要包括 JUnit 和 TestNG，相比 JUnit，TestNG 支持分组，参数化等更多的特性，因此选择 TestNG 作为我们的单元测试框架。
目前市面上有很多 Java 的 Mock 工具，但仔细分析后发现支持私有方法、构造方法、静态方法的并不多，PowerMockito 可以很好的支持对这些方法进行 Mock，因此我们采用PowerMockito作为我们的Mock工具。

## 哪些模块需要进行单元测试
理论上来讲，所有模块都要进行单元测试，但考虑到测试成本的因素，我们可以先针对核心业务模块实施单元测试。
具体来说，目前我们的Java项目主要采用 Spring 框架，代码主要分为三个层次：
* Controller
* Service
* DAO

考虑到核心的业务模块集中在Service层，因此单元测试要求覆盖所有的Service模块。
每个 Service 模块中，所有方法都要覆盖，包括：公有方法、私有方法、保护方法，静态方法。

## 安排测试优先次序
单元测试是典型的自底向上过程, 如果没有足够的资源测试一个系统的所有模块, 就应该先把重点放在较底层的模块。

## 确保你的测试代码独立于功能代码
把测试代码从功能代码目录中独立出来, 通常是创建一个和 src 目录同级的 test 目录，例如：产品代码所在路径为 css/src/main/，则测试代码所在路径为 css/src/test/。
这么做保证功能代码和测试代码隔离, 目录结构清晰, 并且发布源码的时候更容易排除测试用例。

## 合理的命名测试
### 包名
测试类所在的包名在被测产品包名基础上增加.test后缀，如：被测类包名：cn.org.bjca.css.domain.cert.impl，测试类包名：cn.org.bjca.css.domain.cert.impl.test。

### 测试类名
测试类名在被测试类基础上增加 Test 后缀，如：被测试类为 UpdateGetCertImpl，那么测试类名称则为 UpdateGetCertImplTest。

### 测试方法名
每个被测试类中，每个成员方法包含一个或多个测试方法，如果只有一个测试方法，那么方法名为 Test + 被测方法名，如：被测方法名为 JudgeStatus，那么测试方法名则为 TestJudgeStatus。

### 被测类对象命名
* 正常情况下被测类对象统一命名 为sut(system under test)
```java
@InjectMocks
private UpdateGetNewEnvsnImpl sut;
```

* 被 spy 的对象统一命名为 spySut
```java
UpdateGetNewEnvsnImpl spySut = PowerMockito.spy(sut);
```

* 被 mock 的对象统一命名为 mockedXXX
```java
@Mock
private IRAConfigService mockedRAConfigService;
```

### 变量命名
* 实际返回值无论什么类型，统一命名为：actually
* 期望值无论什么类型，统一命名为：expected

示例如下：
```java
@Test
public void TestJudgeStatus(CertStatus certStatus, boolean expected) {
    when(bu.getCertStatus()).thenReturn(certStatus);
    boolean actually = (Boolean) Whitebox.invokeMethod(sut, "judgeStatus", bu);
    assertEquals(actually, expected);
}
```

## 使用显式断言
应该总是优先使用 assertEquals(a, b) 而不是 assertTrue(a == b) , 因为前者会给出更有意义的测试失败信息。

## 注释
* 为每个测试方法、每组测试用例添加注释，注释统一使用中文
* 对特殊情况注释：如测试方法中期望抛出异常，无验证逻辑
```java
@Test(expectedExceptions = CertServiceException.class)
public void TestCertApplyWithResIsFailure() {
    // 方法模拟
    when(resp.getResponseinfo()).thenReturn(responseinfo);
    when(resp.isSuccess()).thenReturn(false);
    // 方法调用
    Whitebox.invokeMethod(updateGetNewEnvsnImpl, "certApply", bu);
    // 期望异常抛出，因此无需显式验证结果
}
```

## 保证测试的覆盖率
* 保证一个模块中的所有独立路径至少被执行一次
* 对所有逻辑值均需测试 true 和 false
* 使所有判定中各条件判断结果的所有组合至少出现一次

## 为测试分组

为你的测试分组可以更加灵活的选择所要执行的测试用例，在更短的时间内完成测试，根据实际应用情况，我们通常把测试用例分成以下几组：
* 正向测试(Positive Test)
* 逆向测试(Negative Test)
* 缺陷修复测试(Bugfix Test)

正向测试就是测试模块在正常情况下是否完成了它应该完成的工作；逆向测试就是测试模块在异常情况下是否进行正确的处理；缺陷修复测试就是验证之前提交的缺陷是否已经被修复。

## 确保测试代码和测试数据分离
使用@DataProvider注解，分离测试数据和测试代码，只包含一条测试数据的测试方法可以在测试代码中准备数据，其它情况必须使用单独的方法构造测试数据对象。

```java
@DataProvider(name = "certStatus")
public Object[][] NewCertStatus() {
    return new Object[][] {
        {CertStatus.ISSUE_ERROR, false},
        {CertStatus.INSTALL_SUCCESS, false},
        {CertStatus.DOWNLOAD_SUCCESS, true},
        {CertStatus.DOWNLOAD_ERROR, true},
    };
}

@Test(groups = {"Positive"}, dataProvider = "certStatus")
public void TestJudgeStatus(CertStatus certStatus, boolean expected) throws Exception{
    // Mock对象
    when(bu.getCertStatus()).thenReturn(certStatus);
    // 方法调用
    boolean actually = (Boolean) Whitebox.invokeMethod(updateGetCertImpl, "judgeStatus", bu);
    // 结果验证
    assertEquals(actually, expected);
}
```

## 保持测试的独立性
单元测试即类 (Class) 的测试。一个 “测试类” 应该只对应于一个 “被测类”, 并且 “被测类” 的行为应该被隔离测试。必须谨慎避免使用单元测试框架来测试整个程序的工作流, 这样的测试即低效又难维护。工作流测试有它自己的地盘, 但它绝不是单元测试, 必须单独建立和执行。

为了保证测试稳定可靠且便于维护, 测试用例之间决不能有相互依赖, 也不能依赖执行的先后次序。

## 保持测试代码结构清晰
每个case其实都可以分为三步走，
1. mock对象，准备测试数据和设定期望值
2. 调用目标API
3. 验证输出和行为

所以我们可以用如下方式将3步分别放入三个分段中，这样我们一眼扫过去就可以清晰的看出一个case大体上都在干什么。

```java
@Test(groups = {"Postive"}, dataProvider = "certStatus")
public void TestJudgeStatus(CertStatus certStatus, boolean expected) throws Exception {
    // Mock对象
    when(bu.getCertStatus()).thenReturn(certStatus);
    // 方法调用
    boolean actually = (Boolean) Whitebox.invokeMethod(updateGetCertImpl, "judgeStatus", bu);
    // 结果验证
    assertEquals(actually, expected);
}
```

## 保持测试代码和功能代码同步更新
遇到以下情况，请增加或修改你的单元测试代码：
* 上报一个新的bug
每上报一个 bug, 都要写一个测试用例来重现这个bug (即无法通过测试), 并用它作为成功修正代码的检验标准.
* 增加新功能
每增加一个新的功能，在开始编写功能代码前，都要增加测试用例，并用它作为检验新特性的标准。
* 修改原有功能
原有功能修改，必须及时更新对应的单元测试代码，以免出现测试代码与功能代码不一致的情况。

## 经常执行你的测试代码
遇到以下情况，请执行你的单元测试代码：
* 一个单元测试方法编写完成
* 修复了一个bug
* 增加了新的功能
* 修改了原有功能
* 产品代码提交SVN前
