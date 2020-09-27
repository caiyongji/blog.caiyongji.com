---
title: 微信红包的随机算法是怎样实现的？
tags:
- 微信红包
- 算法设计
---



我在知乎上看到这样一个问题[微信红包的随机算法是怎样实现的？](https://www.zhihu.com/question/22625187/answer/307424621)

有人说腾讯大致是这样实现的：

```java
public static double getRandomMoney(LeftMoneyPackage _leftMoneyPackage) {
    // remainSize 剩余的红包数量
    // remainMoney 剩余的钱
    if (_leftMoneyPackage.remainSize == 1) {
        _leftMoneyPackage.remainSize--;
        return (double) Math.round(_leftMoneyPackage.remainMoney * 100) / 100;
    }
    Random r     = new Random();
    double min   = 0.01; //
    double max   = _leftMoneyPackage.remainMoney / _leftMoneyPackage.remainSize * 2;
    double money = r.nextDouble() * max;
    money = money <= min ? 0.01: money;
    money = Math.floor(money * 100) / 100;
    _leftMoneyPackage.remainSize--;
    _leftMoneyPackage.remainMoney -= money;
    return money;
}
```
也有人做了正太分布、方差分析、回归分析、统计模拟等，图太长我就不贴了。

**然而**

1. 所有答案都是“取时随机”，即设计“红包池”的概念，然后在抽取时随机取数。
2. 所有答案都是“钱的随机”，即随机金额，然后return。

下面我们换个思路，现在我们把所有的钱换成1分的**硬币**，把红包想象成**罐子**，然后**撒币**。

```java
/**
 * @param count 红包数
 * @param money 总金额
 * @return
 */
public static Integer[] ranRedPac(Integer count, Integer money) {
	Integer[] result = new Integer[count];
	for (int i = 1; i <= money; i++) {
		int n = new Random().nextInt(count);
		result[n] = result[n] == null ? 1 : result[n] + 1;
	}
	return result;
}

//测试
public static void main(String[] args) {
	Arrays.asList(ranRedPac(10, 5000000)).forEach(i -> System.out.println(i));
	System.out.println("sum: " + Arrays.asList(ranRedPac(10, 50)).stream().mapToInt(i -> i).sum());
}
```


**每分钱随机选择红包。**

至于什么回归分析，统计模拟**统统用不上**。

**本例中我们摒弃“抽取”、“随机金额”这样的传统概念，使钱拥有选择意识，执行“随机”行为，自然而然红包就有了随机金额的属性。**

改变一下思路，别把简单问题复杂化。

我们在编码设计时，通常会考虑现实生活中的逻辑，并把对象抽象成类，行为抽象成方法。但是，我们偶尔也要考虑思维**反转**。

当然，我的代码有一定的弊端。

思维是最重要的。


