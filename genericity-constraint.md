# 泛型约束

## 定义
> 定义泛型类时，我们会碰到调用方传入的类型不是我们想要的类型，因此就催生出了Where关键字来指定某种类型，这些限制称之为泛型约束。

看了MSDN泛型约束大概有十种类型

| 约束                | 说明                                                   |
| ------------------- | ------------------------------------------------------ |
| T:class             | 必须是引用类型，通常是 Class、Interface 等             |
| T:struct            | 必须是值类型                                           |
| T:new()             | 必须是具有可访问的无参数的公共构造函数                 |
| T:<基类名>          | 必须是指定基类或者派生自指定的基类                     |
| T:<接口名称>        | 类型参数必须是指定的接口或者实现自指定接口             |
| where T : unmanaged | 必须是为“非托管类型”的类型                             |
| T:Enum              | 必须是枚举，C# >= 7.3                                  |
| T:Delegate          | 委托 ，C# >= 7.3                                       |
| T:MulticastDelegate | 多路广播委托 C# >= 7.3                                 |
| T: notnull          | 不可为 null 的类型，编译器只会警告，不会报错 C# >= 8.0 |

## 示例
> 看下Demo代码，了解下大致使用

### T:class  

```
//必须是Class
public class UsingClass<T> where T : class { }

```
### T:struct  

```
public class UsingClass<T> where T : struct { }
```
### T:new()  
> new 约束指定泛型类声明中的类型实参必须有公共的无参数构造函数。 若要使用 new 约束，则该类型不能为抽象类型。   

```
class ItemFactory<T> where T : new()
{
    public T GetNewItem()
    {
        return new T();
    }
}
// 当与其他约束一起使用时，new() 约束必须最后指定：
public class ItemFactory2<T>
    where T : IComparable, new()
{  }

```
### T:<基类名>   

```
//必须继承自MyClass
public class UsingMyClass<T> where T : MyClass { }
```
### T:<接口名称>  

```
//必须实现 IComparable<T> 接口 接口
public class AGenericClass<T> where T : IComparable<T> { }
```
### T:unmanaged  
>  此约束支持跨所有非托管类型的可重用例程。 unmanaged 约束不能与 class 或 struct 约束结合使用。 unmanaged 约束强制该类型必须为 struct：
```
class UnManagedWrapper<T>
    where T : unmanaged
{ }
```
### T:Enum  

```
public class UsingEnum<T> where T : System.Enum { }
```
### T:Delegate  

```
public class UsingDelegate<T> where T : System.Delegate { }
```
### T:MulticastDelegate  

```
public class Multicaster<T> where T : System.MulticastDelegate { }
```
### T:notnull  
> C# 8.0 及更高版本中的可为 null 上下文中，class 约束要求类型是不可为 null 的引用类型。 若要允许可为 null 的引用类型，请使用 class? 约束，该约束允许可为 null 和不可为 null 的引用类型。
```
class NotNullContainer<T>
    where T : notnull
{
}
```

## 参考链接
where（泛型类型约束）：https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/where-generic-type-constraint
类型参数的约束 ： https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters