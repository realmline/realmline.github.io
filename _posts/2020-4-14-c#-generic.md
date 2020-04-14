---
layout: post
title: 'python3基础笔记(一)'
date: 2018-11-22
author: realmline
color: rgb(100,149,237)
cover: '../assets/posts/2018-11/1.png'
tags: python3
---

# 泛型

泛型出现于`.net framework 2.0`

### 设计思想

- 推迟一切可以推迟的，具体到编译的时候的才确定类型
- 优点
  - 类型安全
  - 可扩展性
  - 高性能
  - 代码复用

> `object`类型转换时可能会出错
>
> 泛型的扩展只有方法、类、接口、委托
>
> 不需要进行装箱和拆箱操作提高了性能
>
> 因为泛型的缘故，一个方法，可以让不同类型的数据进行调用

#### 泛型方法

```c#
public M Show<N,M>(N t){
    ...
}
```

#### 泛型类

```c#
public class GeneircClass<T>{
    public T TField;
    public T TProperty{get;set;}
    ...
}
```

> 泛型属性以及泛型字段并不算泛型的扩展，因为它们是出自于泛型类`GeneircClass的泛型类型T`

#### 泛型接口

```c#
public interface IGeneirc<T>{
    ...
}
```

#### 泛型委托

````c#
Action<T> GeneircAction = (t) => {
    ...
}

Func<N,M> GeneircFunc = (n) =>
{
    //初始化一个默认值
    M m = default(M);
    ...
    return m;
}
````

> `default()`可以给泛型类型初始化一个默认值，如`int`的默认值就是`0`，`string`的默认值就是`""`

### 自动推断

泛型有自动推断的特点，如果参数中包含了泛型类型，那么则可以简写

```c#
public void Show<T>(T t){
    Console.WriteLine(t);
}
show<int>(ivalue) == show(ivalue)
```

> 因为泛型类型`T`已经在参数中出现了，所以可以自动推算出类型
>
> 如果`T`是返回类型，且不在参数中出现，那么就无法简写

### 延迟声明

声明的时候并没有指定类型，具体是在调用的时候指定类型

> 性能和普通方法差不多，**但是可以避免装箱和拆箱操作从而提升性能**

### 泛型约束

泛型约束通过`where`关键字来实现

#### 接口约束

```c#
public class GeneircClass<T> where T:IGeneirc {
    ...
}
```

#### 引用类型约束

```c#
public class GeneircClass<T> where T:BaseClass{
    ...
}
```

#### 值类型约束

```c#
public class GeneircClass<T> where T:BaseStruct{
    ...
}
```

#### 无参构造函数约束

```c#
public class GeneircClass<T> where T:new(){
    ...
}
```

> 无参构造函数约束，没法约束带参数的构造函数

#### 混合约束

```c#
public class GeneircClass<N,M> 
where N:IGeneirc,new()
where M:BaseClass,new()
{
    ...
}
//无参构造函数约束可以在泛型类型里面进行new的操作，而且传进来的对象没有无参构造函数也会产生报错
N n = new N();
```

> 约束之后可以使用基类中的属性和方法
>
> 类型也必须满足约束，可以是基类和子类
>
> 约束可以叠加，但不能重复约束，如果进行了`peopel`类的约束，就不能重复添加`class`约束
>
> 值类型和引用类型的约束也不能同时出现

### 子类继承泛型的两种情况

``` c#
public class GenericClass<T>{
    public T Property
}

//将子类的泛型类型同时用作到父类上
public class chlidClassGeneric<T>:GenricClass<T>
{

}

//指定父类的泛型类型
public class chlidClass:GenricClass<string>   
{

}

```

> 当泛型被指定类型后，可以认定为不再是泛型

### 协变和逆变

***只能在接口或委托上面实现***

> 协变和逆变实际上只是用于通过编译器的检查

#### 协变(covariant)

- `out`，`<out T>`这个泛型类型只能用作返回值类型

  ```c#
  //父类是可以用子类来实现的
  parent = new child;
  //但是使用子类集合来实现父类集合时，因为两者都是list，所以无法识别从而报错
  IList<parent> list != new List<child>();
  //IEnumerable<out T>是.net内置的协变接口
  IEnumerable<parent> enumerable = new List<child>(); 
  ```
  
  > 因为`out`修饰的返回值的，所以可以通过编译器检查

#### 逆变(contravariant)

- `in`，`<in T>`这个泛型类型只能用作参数类型

  ```c#
  //没有内置的逆变接口，此处用IEnumerable假设
  IEnumerable<child> list = new List<parent>(); 
  ```
  
  > `child`并不是实际返回值，只是传入的参数，所以就算是父类可以通过检查

### 泛型类缓存

- 泛型在`jit`编译的时候，不同的参数的泛型，会产生对应类型的全新的类，也就是通过副本产生真正的具体类型

- ***静态字段和静态构造函数只会初始化一次，而且会常驻在内容中***，当泛型和这个组合的时候就可以灵活的产生泛型缓存，不同的类型都会执行一次各自的静态字段和静态构造函数

#### 计时器

``` c#
//初始化一个计时器对象
Stopwatch watch = new Stopwatch();
//启动计时器
watch.start;
... //进行编译操作
//停止计时器
watch.stop;
//获取期间消耗的时间（毫秒为单位）
watch.ElapsedMilliseconds
```

## 要点

```c#
//在编译、反射获取泛型类的时候泛型的类型显示为genricClass`1，它表示genricClass这个泛型类有一个泛型参数，`2则表示有两个泛型参数
```

> 当泛型方法返回泛型的返回值时，可以使用`return default(T)`语法糖，这个语法糖会根据泛型的类型产生默认值

