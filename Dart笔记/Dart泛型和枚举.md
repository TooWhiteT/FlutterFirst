# Dart泛型和枚举

如果您查看基本数组类型List的API文档，您将看到该类型实际上是List<E>。<...>符号标记列表为泛型(或参数化)类型——具有形式类型参数的类型。根据约定，类型变量具有单字母名称，如E、T、S、K和V。

## 为什么使用泛型

泛型通常是类型安全所必需的，他们对于写出严谨高质量的代码是很有用的：

- 适当地指定泛型类型可以生成更好的代码。
- 您可以使用泛型来减少代码重复。

如果您想要一个列表只包含字符串，您可以将它声明为list (读作“String of String”)。这样，您和其他程序员，以及您的工具就可以检测到将一个非字符串分配到列表中可能是一个错误。这里有一个例子:

```dart
var names = List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
names.add(42); // Error
```

使用泛型的另一个原因是减少代码重复。泛型允许您在许多类型之间共享一个接口和实现，同时仍然利用静态分析。例如，假设您创建了一个用于缓存对象的接口:

```dart
abstract class ObjectCache {
  Object getByKey(String key);
  void setByKey(String key, Object value);
}
```

您发现您想要这个接口的特定字符串版本，所以您创建了另一个接口:

```dart
abstract class StringCache {
  String getByKey(String key);
  void setByKey(String key, String value);
}
```

如果稍后你想要获取这个接口的一个数字特征的版本。
泛型类型可以省去创建所有这些接口的麻烦。相反，您可以创建一个具有类型参数的接口:

```dart
abstract class Cache<T> {
  T getByKey(String key);
  void setByKey(String key, T value);
}
```

在这段代码中，T是替代类型。它是一个占位符，您可以将其视为开发人员稍后将定义的类型。

## 使用集合字面量

List和map字面量可以被参数化。参数化字面量和你已经认识的所有字面量一样，仅仅是在字面量的开始括号之前添加<type>(对于list类型来说)或者添加<keyType, valueType>（对于map类型来说）。

```dart
var names = <String>['Seth', 'Kathy', 'Lars'];
var pages = <String, String>{
  'index.html': 'Homepage',
  'robots.txt': 'Hints for web robots',
  'humans.txt': 'We are people, not machines'
};
```

## 构造函数的参数化类型

要在使用构造函数时指定一个或多个类型，请将类型放在类名后面的尖括号(<…>)中。例如:

```dart
var names = List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
var nameSet = Set<String>.from(names);
```

下面的代码创建了一个具有整数键和视图类型值的map映射:

```dart
var views = Map<int, View>();
```

## 泛型集合及其包含的类型

Dart通用类型被具体化，这意味着它们在运行时携带它们的类型信息。例如，您可以测试集合的类型:

```dart
var names = List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true
```

> 注意:相反，Java中的泛型使用擦除，这意味着泛型类型参数在运行时被删除。在Java中，您可以测试一个对象是否是一个列表，但不能测试它是否是List。

## 限制参数化类型

在实现泛型类型时，您可能希望限制其参数的类型。你可以使用extends。

```dart
class Foo<T extends SomeBaseClass> {
  // Implementation goes here...
  String toString() => "Instance of 'Foo<$T>'";
}

class Extender extends SomeBaseClass {...}
```

可以使用SomeBaseClass 或它的任何子类作为泛型参数:

```dart
var someBaseClassFoo = Foo<SomeBaseClass>();
var extenderFoo = Foo<Extender>();
```

也可以不指定泛型参数:

```dart
var foo = Foo();
print(foo); // Instance of 'Foo<SomeBaseClass>'
```

指定任何非somebaseclass类型都会导致错误:

```dart
var foo = Foo<Object>();
```

## 使用泛型方法

最初，Dart仅仅在类中支持泛型。后来一种称为泛型方法的新语法允许在方法和函数中使用类型参数。

```dart
T first<T>(List<T> ts) {
  // Do some initial work or error checking, then...
  T tmp = ts[0];
  // Do some additional checking or processing...
  return tmp;
}
```

在这里，first上的泛型参数(<T>)允许你在很多地方使用类型参数T：

- 在函数的返回中返回类型(T)
- 在参数的类型中使用(List<T>)
- 在局部变量的类型中(T tmp)

## 枚举

枚举类型，通常称为枚举或枚举类型，是一种特殊类型的类，用于表示固定数量的常量值。

## 使用枚举

使用enum关键字声明一个枚举类型：

```dart
enum Color { red, green, blue }
```

枚举中的每个值都有一个索引getter，它返回enum声明中值的从0开始的位置。例如，第一个值有索引0，第二个值有索引1。

```dart
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
```

要获取枚举中所有值的列表，请使用enum的values 常量。

```dart
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```

您可以在switch语句中使用enum，如果switch的case不处理enum的所有值，将会报一个警告消息:

```dart
var aColor = Color.blue;

switch (aColor) {
  case Color.red:
    print('Red as roses!');
    break;
  case Color.green:
    print('Green as grass!');
    break;
  default: // Without this, you see a WARNING.
    print(aColor); // 'Color.blue'
}
```

枚举类型有以下限制:

- 您不能子类化、混合或实现枚举。
- 不能显式实例化一个枚举