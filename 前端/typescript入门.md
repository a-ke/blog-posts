---
title: typescript入门
published: 2025-07-04
tags:
  - typescript
category: typescript
draft: false
---

## 认识类型编程

Typescript 的类型编程可以理解为一门有限的函数式编程语言。

既然称作类型编程，那自然和普通编程语言一样，用于类型变量定义语句、类型表达式、类型函数等等，接下来将详细讲述类型编程的一些基础知识。

## 学习途径

1. [type-challenges](https://github.com/type-challenges/type-challenges) 一个类型编程挑战的仓库，ts中的leetcode
2. [ts在线编程](https://www.typescriptlang.org/zh/play) 官方提供的ts在线编程平台
2. [官方手册](https://www.tslang.cn/docs/handbook/basic-types.html) typescript官方提供的中文手册
2. [社区手册](https://bosens-china.github.io/Typescript-manual/) 由社区维护的中文手册

## 基础类型

1. 基础数据类型，比如 string、number、boolean、symbol、undefined 等等。
2. 字面量类型，比如 '123'，5 等
3. 对象类型，比如 { a: string }
4. 函数类型，比如 (a: string) => void
5. 元组类型，比如 [1, 2, 3]
6. 数组类型，比如 string[]

```typescript
// 使用 type 定义类型变量，类型是一个字面亮类型 '123'
type TypeA = '123'

// 使用 interface 定义类型变量
interface TypeB {
  a: string
}

// 将对象类型
// {
//  b: number
//  c: TypeA
// } 
// 赋值给 TypeC
// TypeA 是上面定义的类型变量，可以直接使用
type TypeC = {
  b: number
  c: TypeA
}

// 类型变量可以直接赋值给另一个类型变量
type D = TypeB
// 将函数类型赋值给 E
type E = (a: string) => void;
```



## 类型操作

TypeScript类型系统的强大之处主要体现在它允许我们通过类型来表达类型，也就是说我们可以通过现有的类型经过一系列的操作得到另一个类型（从类型创建类型），我们将通过下面表格所列举的顺序来讲解如何表达一个新的类型：

| Types                  | 类型              | 描述                                      |
| ---------------------- | ----------------- | ----------------------------------------- |
| Intersection Types     | 交叉类型          | 将多个类型合并为一个类型                  |
| Union Types            | 联合类型          | 取几种类型之一                            |
| Generics Types         | 泛型类型          | 带参数的类型                              |
| Keyof Type Operator    | Keyof 类型运算符  | 使用keyof运算符创建新类型                 |
| Typeof Type Operator   | Typeof 类型运算符 | 使用typeof运算符创建新类型                |
| Indexed Access Types   | 索引访问类型      | 使用Type['a']语法访问类型的子集           |
| Conditional Types      | 条件类型          | 行为类似于类型系统中的 if 语句的类型      |
| Infer Types            | 推断类型          | 可以在extends的条件语句中推断待推断的类型 |
| Mapped Types           | 映射类型          | 通过映射现有类型中的每个属性来创建类型    |
| Template Literal Types | 模板字符串类型    | 通过模板字符串更改属性的映射类型          |

### 交叉类型

 ` & ` 合并多个类型对象的键到一个类型对象中。

```typescript
type A = { a: number }
type B = { b: string }
type C = A & B;
// C 包含 A 和 B 定义的所有键
/**
* C = {
    a: number;
    b: string;
  }
*/
const c: C = {
  a: 1,
  b: '1'
}
```



### 联合类型

`|` 将多个类型组成联合类型：

```typescript
type A = string | number;
type B = string;
```

此时类型 A 既可以是 string 又可以是 number，类型 B 是类型 A 的子集，所有能赋值给类型 B 的值都可以赋值给类型 A。

### 泛型类型

在TypeScript开发过程中我们可以显示的来标记传入参数和返回数据的类型，当需要支持传入和返回数据类型的限制相对宽泛我们可以使用any来表示，但这样也就丢失了TypeScript的强大之处（静态类型推断）。这时候就可以用到泛型，动态设置参数和返回数据的类型。

1. 定义泛型

   ```typescript
   // 接口泛型
   interface Obj<T> {
     a: T
   }
   // 使用 type 也能定义泛型
   type Type<T> = { b: T }
   // 函数泛型
   type Fn = <T>(...args: any[]) => any;
   
   // 泛型也可以有默认值，这样如果没有指定泛型参数，默认是 string
   interface Obj<T = string> {
     a: T
   }
   ```

2. 使用extends约束泛型

   ```typescript
   // extends 后可以接类型表达式
   type Fn = <T extends string | number>(...args: any[]) => any;
   
   // 泛型可以和函数泛型结合
   type Fn<I> = <T extends string | number>(...args: T[]) => I;
   ```

3. 基于泛型创建新类型

   通过 泛型名<类型表达式> 即可使用泛型生成新类型，如下：

   ```typescript
   type Fn<I> = <T extends string | number>(...args: T[]) => I;
   
   type MyFn = Fn<boolean>;
   /** 可以看到 Fn 中的类型已经被替换成了 boolean，也就是我们指定的参数类型
   type MyFn = <T extends string | number>(...args: T[]) => boolean
   */
   
   // 使用新类型
   const myfn: MyFn = (a: any) => true;
   ```

4. 泛型递归调用

   泛型调用支持递归：

   ```typescript
   type Fn<T> = T extends string ? T : Fn<T>;
   ```

   在上个例子中，我们定义了一个泛型 Fn<T>，当 T 是 string 的时候，Fn<T> 返回 T，否则返回一个递归的结果！

   例如递归我们就可以做很多有意思的事情了，比如类型对象的深度优先遍历、实现循环等等。下面我们给斐波那契数列计算的例子：

   ```typescript
   // 辅助函数，暂时不用关心
   type NumberToArray<T, I extends any[] = []> = T extends T ? I['length'] extends T ? I : NumberToArray<T, [any, ...I]> : never;
   type Add<A, B> = [...NumberToArray<A>, ...NumberToArray<B>]['length']
   type Sub1<T extends number> = NumberToArray<T> extends [infer _, ...infer R] ? R['length'] : never;
   type Sub2<T extends number> = NumberToArray<T> extends [infer _, infer __, ...infer R] ? R['length'] : never;
   
   // 计算斐波那契数列
   type Fibonacci<T extends number> = 
     T extends 1 ? 1 :
     T extends 2 ? 1 :
     Add<Fibonacci<Sub1<T>>, Fibonacci<Sub2<T>>>;
     
     type Fibonacci9 = Fibonacci<9>;
     /** 得到结果
     type Fibonacci9 = 34
     */
   ```

### keyof 类型运算符

keyof 可以获取某些对象类型的键：

```typescript
interface People {
  a: string;
  b: string;
}

// 返回 'a' | 'b'
type KeyofPeople = keyof People;
// type KeyofPeople = 'a' | 'b';
```

### typeof 类型运算符

使用typeof来获取已有值的类型

```typescript
// 获取对象的类型
const obj = { a: '123', b: 123 }
type Obj = typeof obj;
/**
type Obj = {
    a: string;
    b: number;
}
*/

// 获取函数的类型
function fn(a: Obj, b: number) {
  return true;
}
type Fn = typeof fn;
/**
type Fn = (a: Obj, b: number) => boolean
*/

// ...获取各种值的类型
```

注意对于 enum 需要先进行 typeof 操作获取类型，才能通过 keyof 等类型操作完成正确的类型计算（因为 enum 可以是类型也可以是值，如果不使用 typeof 会当值计算）：

```typescript
enum E1 {
  A,
  B,
  C
}

type TE1 = keyof E1;
/**
拿到的是错误的类型
type TE1 = "toString" | "toFixed" | "toExponential" | "toPrecision" | "valueOf" | "toLocaleString"
*/

type TE2 = keyof typeof E1;
/**
拿到的是正确的类型
type TE2 = "A" | "B" | "C"
*/
```

### 索引访问类型

 我们可以使用索引访问类型来查找另一种类型的特定属性

```typescript
type Person = { age: number; name: string; alive: boolean };
type Age = Person["age"]; // 输出类型 number
```

### 条件类型

类似于js中的三元表达式，用来进行条件的判断，并返回一个新类型

```typescript
type C = 'a' extends 'a' | 'b' ? true : false
/**
type C = true
*/
```

### 推断类型

infer总是配合extends关键字一起使用的，我们将infer的行为称为模式匹配。简而言之是通过 extends 对类型参数做匹配，如果匹配成功，就会将匹配结果保存到通过 infer 声明的局部类型变量里。

```typescript
// 数组 infer
type Infer1<T> = T extends (infer S)[] ? S : never;
type a1 = Infer1<string>; // never
type a2 = Infer1<string[]>; // string

// 单元素元组 infer
type Infer2<T> = T extends [infer S] ? S : never;
type b1 = Infer2<[string, number]>; // never
type b2 = Infer2<[string]>; // string

// 多元素元组 infer
// R 同样是一个元组类型，数量最少为 0 个
type Infer3<T> = T extends [infer S, ...infer R] ? [S, R] : never;
type c1 = Infer3<[]>; // never
type c2 = Infer3<[string]>; // [string, []]
type c3 = Infer3<[string, number]>; // [string, [number]]

// 字符串字面量 infer
type Infer4<T> = T extends `${infer S}` ? S : never;
type d1 = Infer4<"str">; // str
type d2 = Infer4<1>; // never

// 字符串字面量 infer
type Infer5<T> = T extends `${infer S}${infer R}` ? [S, R] : never;
type e1 = Infer5<"">; // never
type e2 = Infer5<"s">; // ["s", ""]
type e3 = Infer5<"st">; // ["s", "t"]

// 字符串字面量 infer
// 获取分隔符前后的字面量
type Infer6<T> = T extends `${infer S}__${infer R}` ? [S, R] : never;
type f1 = Infer6<"">; // never
type f2 = Infer6<"str1__str2">; // ["str1", "str2"]
type f3 = Infer6<"str1__str2__str3">; // ["str1", "str2__str3"]

// 其他类型 infer 
type Infer7<T> = T extends Promise<infer R> ? R : never;
type g1 = Infer7<"">; // never
type g2 = Infer7<Promise<number>>; // number

```

### 映射类型

TypeScript 中的映射类型和数学中的映射类似，能够将一个集合的元素转换为新集合的元素，只是 TypeScript 映射类型是将一个类型映射成另一个类型。

在我们实际开发中，经常会需要一个类型的所有属性转换为可选类型，这时候你可以直接使用 TypeScript 中的 Partial工具类型：

```typescript
type User = {
  name: string;
  location: string;
  age: number;
}

type User2 = Partial<User>;
/*
  User2 的类型：
  
  type User2 = {
      name?: string | undefined;
      location?: string | undefined;
      age?: number | undefined;
  }
*/
```

1. 实现Partial

   ```typescript
   type Partial<T> = {
     [K in keyof T]?: T[K]
   }
   ```

   拆解下其中的每一部分：

   - type Partial<T>：定义一个类型别名 Partial和泛型 T；
   - keyof T：通过 keyof操作符获取泛型 T中所有 key，返回一个联合类型；
   - in：类似 JS 中 for...in中的 in，用来遍历目标类型的公开属性名；
   - T[P]：是个索引访问类型（也称查找类型），获取泛型 T中 P类型，类似 JS 中的访问对象的方式；
   - ?:将类型值设置为可选类型；
   - { [K in keyof T] ?: T[P] | undefined}：遍历 keyof T返回的联合类型，并定义用 K变量接收，其每次遍历返回的值为可选类型的 T[K]。

2. 实现Required

   用来将类型的所有属性设置为必选属性。

   ```typescript
   type Required<T> = {
     [K in keyof T]-?: T[K]
   }
   ```

   这边的 -?符号可以暂时理解为“将可选属性转换为必选属性”

3. 实现Readonly

   用来将所有属性的类型设置为只读类型，即不能重新分配类型。

   ```typescript
   type Readonly<T> = {
     readonly [K in keyof T]: T[K]
   }
   ```

4. 实现Pick

   用来从指定类型中选择指定属性并返回

   ```typescript
   type Pick<T, K extends keyof T> = {
     [P in K]: T[P]
   }
   ```

   使用如下：

   ```typescript
   type User = {
     name?: string;
     location?: string;
     age?: number;
   }
   
   type User2 = Pick<User, 'name' | 'age'>;
   /*
     type User2 = {
         name?: string | undefined;
         age?: number | undefined;
     }
   */
   
   ```

5. 实现Omit

   作用类似与 Pick工具类型相反，可以从指定类型中忽略指定的属性并返回。

   ```typescript
   type Omit<T, K extends keyof T> = {
     [P in Exclude<keyof T, K>]: T[P]
   }
   ```

   使用如下：

   ```typescript
   type User = {
     name?: string;
     location?: string;
     age?: number;
   }
   
   type User2 = Omit<User, 'name' | 'age'>;
   /*
     type User2 = {
         location?: string | undefined;
     }
   */
   ```

6. 实现Exclude

   用来从指定的联合类型中排除指定类型。

   ```typescript
   type Exclude<T, U> = T extends U ? never : T;
   ```

   使用如下：

   ```typescript
   type User = {
     name?: string;
     location?: string;
     age?: number;
   }
   
   type User2 = Exclude<keyof User, 'name'>;
   /*
     type User2 = "location" | "age"
   */
   
   ```

**映射修饰符**

在自定义映射类型的时候，我们可以使用两个映射类型的修饰符来实现我们的需求：

- readonly修饰符：将指定属性设置为只读类型；
- ?修饰符：将指定属性设置为可选类型；

当然，也可以对修饰符进行操作：

- +添加修饰符（默认使用）；
- -删除修饰符；

### 模板字符串类型

TS 字符串模板类型的写法跟 JS 模板字符串非常类似，比如：

```typescript
type World = 'world';

type Greeting = `hello ${World}`;
// type Greeting = "hello world"
```

我们可以使用模板字符串来组合联合类型

```typescript
type EmailLocaleIDs = "welcome_email" | "email_heading";
type FooterLocaleIDs = "footer_title" | "footer_sendoff";
 
type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
// type AllLocaleIDs = "welcome_email_id" | "email_heading_id" | "footer_title_id" | "footer_sendoff_id"
```

结合extends和infer可以实现更多功能，比如：

```typescript
// 实现Trim，去除字符串两端的空格
type TrimLeft<Str extends string> = Str extends ` ${ infer Rest }` ? TrimLeft<Rest> : Str;
type TrimRight<Str extends string> = Str extends `${ infer Rest } ` ? TrimRight<Rest> : Str;
type Trim<Str extends string> = TrimLeft<TrimRight<Str>>;

type T2 = Trim<'  Hello World   '>;
```



## 挑战

1. 实现两个数的加法

   类型系统并不能直接实现加法，所以显然不能type Add<T1 extends number,T2 extends number> = T1 + T2。

   这里需要使用元组的特性：元组是包含了固定长度的数组，所以 TS 能确切的知道元组的长度。

   ```typescript
   type Tuple1 = [1,2,3];
   type LengthOfTuple1 = Tuple1["length"]; // 3
   ```

   那么加法运算的思路就是：

   - 分别构建长度为T1和T2的元组
   - 将两个元组合并，合并后元组的长度就是加法运算的结果

   ```typescript
   // 构建长度为 T 的元组
   type GetTuple<T extends number, R extends any[] = []> = R["length"] extends T
     ? R
     : GetTuple<T, [...R, any]>;
   
   type Add<T1 extends number, T2 extends number> = [
     ...GetTuple<T1>,
     ...GetTuple<T2>
   ]["length"];
   
   type Five = Add<3, 2>; // 5
   
   ```

2. 下划线字符串转小驼峰

   ```typescript
   type CamelCase<
     S extends string
   > = S extends `${infer left}_${infer char}${infer right}`
         ? `${Lowercase<left>}${Uppercase<char>}${CamelCase<right>}`
         : Lowercase<S>
   ```
