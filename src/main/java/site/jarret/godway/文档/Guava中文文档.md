<h1 align = "center">Guava中文文档</h1>

# 介绍

## 用户手册

```
Guava包含几个谷歌核心库，类似集合、缓存、提供原始类型、并发库、注解、字符窜处理、I/O操作等等，这些都是以java为基础扩展的项目。这里提供的那些工具每天都被谷歌程序员使用并且应用于项目服务中。
但是通过搜索javadoc来使用一个库并不是最有效学习方法。这里，我们将提供关于Guava的一些最常用和最具有特征的一份既可读也愉快的解释。
```

这份wiki是一个寻寻渐进的过程，所以有些地方还处于建设当中。

- 基础工具：使java语言的使用变的更愉快
  1. 这里是列表文本使用和避免null：null的定义模棱两可，会发生一些容易混淆的错误，并且有的时候仅仅是不太愉快。很多guava工具拒绝并且在nulls上快速失败。
  2. 预先处理：对于你的方法的预先处理将更加容易
  3. 共同的object方法：简单实现Object的方法，像hashCode()和toString()
  4. 排序：guava具有强有力的 "fluent Comparator" 类
  5. 异常抛出：简单传递和检查异常和错误
- 集合：guava的集合是JDK集合生态系统的一个扩展，这里有一些最成熟和最受欢迎的一部分guava集合。
  1. 不可变集合，对于防御式编程，持续性数据收集提供更好的性能
  2. 新的集合类型，提供原生JDK没有的提供的，类似multisets, multimaps, tables, bidirectional maps等等。
  3. 更有力的集合工具，提供了很多java.util.Collections中没有的相同的操作。
  4. 扩展工具集：写一个Collections的装饰者模式？实现Iterator？我们可以更容易的实现它。
- 图标：一个图标结构数据模型库，也就是说，实体类以及他们的关联。Key特征包括：
  1. Graph：一种图形，其边是匿名实体，没有身份和信息。
  2. ValueGraph：一种图形，其边与非唯一值相关联。
  3. Network：一种图形，他的边是唯一的实体。
- 缓存：本地缓存，处理正确，并且支持一个范围宽广的（各种各样的）过期行为。
- 函数式编程：谨慎的使用，guava的函数式编程显著的简化代码。
- 并发：强有力的，简单的更加容易的书写并发代码。
  1. ListenableFuture：Futures,当他们完成的时候会有回调。
  2. Service:所有的事情的开始和停止，对于你来说要小心复杂的状态逻辑。
- 字符串：一些少量的非常有用的string工具集：splitting, joining, padding等等。
- 原始类型：原始类型的操作，就像int和char，并不是有JDK提供，包含一些无符号的变体。
- Ranges:guava提供了强有力的API去处理连续的或者是毫不相关的范围内的Comparable类型。
- I/O：简化IO操作，对于java5 和 java6来说比较典型，覆盖整个IO的流和文件。
- Hashing：工具对于大多数老的哈希不仅仅是通过Object.hashCode()提供的哈希，还包括Bloom过滤器。
- 事件总线：组件之间的发布订阅模式并不需要组件之间显式地注册。
- 计算：优化，全覆盖测试JDK并没有提供的math工具
- 发射：guava工具集集成java的发射能力
- 小贴士:用guava让你的应用程序按照你想要的方式工作。
  1. 理念:guava是什么和不是什么，以及我们的目标。
  2. 在您的项目中使用guava，包括Maven、Gradle等构建系统。
  3. 使用ProGuard来避免你不使用的guava部分捆绑在你的库文件中。
  4. Apache Commons等价物，帮助您从使用Apache Commons集合转换代码。
  5. 兼容性，guava版本之间的细节。
  6. Idea墓地，已被最终拒绝的特性请求。
  7. 朋友们，我们喜欢并欣赏开源项目。
  8. 如何贡献，如何贡献guava。



# 基础工具

## 使用-避免 null

```
粗心的使用null会产生一些难以相信的严重的bug。从学习谷歌底层的代码可以看出95%的关于集合的方法并不支持有任何null值在其中，并且如果拥有null值会快速失败，这样做会比沉默的接受null值对于开发者来说更有帮助。

另外，null具有模糊的含义，它很难明确的表明返回null值代表的真正意义，例如：Map.get(key)既可能value在这个map中是null，也可能是这个value不在map中。Null可以理解为失败，也可理解为成功，还能理解为其他任何事情。用什么能让你比null更明确的表达你的意思。

即便如此，在很多时候null也是对的也可以被正确使用。null是低成本的，在内存消耗和速度上，同时在对象数组中也是不可避免的。但是在应用程序的代码中同时在库中是抵制的，它会产生一个困惑魔法值，困难又诡异的bug，而且也具有模糊的含义。当Map.get返回null值，那么他表示这个值是不确定的，这个值也是是缺失也许就是null，从更多的审判角度来说，null给我们带来的困难就是null这个值本身的含义。

基于这些原因，很多guava的工具在null的使用上定义为快速失败而不是接受null并且使用它，只要有一个友好的对空方案可用。相对的，guava提供很多工厂方法当我们必须使用null的时候如何更容易的使用null，同时帮我们极力的避免使用null。
```

### 特殊的案例

```
如果你准备使用一个null作为Set的一个值或者Map的一个key--不要这样干；如果你明确使用特殊情况null在你的操作中，那么他是清楚的（不那么令人惊讶）。

在Map中你如果想将null值作为value--删掉这一项；新建一个分离的Set来保存非null值的key集合（或者是null值的key集合）。以下这些场景非常容易混淆，当一个Map的key序列包含null值，还有是Map中的key序列并没有这个key。这样来说将可以进行分离是非常好的，这样对于你的应用程序来说，你可以将一个key的null值联系起它究竟是哪一种含义。

如果你在一个List中使用null--如果这个list是稀少的，也许使用一个Map<Integer, E>是一个更好的选择?这样也可以带来更高的性能，而且也能潜意识的使你的应用程序更清除。
```

### Optional

```java
很多程序案例中使用null是为了明确表示一些缺少（也即是空）,也许他们也有一个value值，是一个空，或者找不到这个元素。例如，Map.get返回null当一个可以没有找到对应的value值。

Optional<T>是一种替换可以为空的T引用指向一个非null的值，一个Optional不仅可以包含一个非空T引用（就是我们所说的当前引用），也可以包含任何东西（也就是我们所说的那种缺少状态）。但是从来没有说过可以“包含null”。

Optional<Integer> possible = Optional.of(5);
possible.isPresent(); // returns true
possible.get(); // returns 5

Optional不打算模仿其他编程环境的任何现有的“选项”或者“可能”构造，尽管他有一些相似之处。

这里我们列举一些常用的Optional操作：
```

#### 创建一个Optional

```
Optional的任何创建方法都是静态的

Optional.of(T)
    创建一个Optional提供的是一个非null的值，对于null快速失败。
Optional.absent()
    对于一些type创建一个空的（缺席的）Optional
Optional.fromNullable(T)
    提供一个可能为空的引用在Optional中，对待非null就像在场的，对待null的就像缺席的。
```

#### 查询方法

```
这里的方法都是非静态的特指Optional<T>的值。
boolean isPresent()
    如果Optional中包含一个非null的实例则返回true
T get()
    返回必须存在的包含T的实例，否则，抛出异常IllegalStateException
T or(T)
    返回Optional中必须存在的值，如果这里为空，返回一个特别的默认值。
T orNull()
    返回Optional中必须存在的值，如果这里为空，则返回null。和fromNullable的操作是相反的。
Set<T> asSet()
    只要有一个就返回一个不可变的唯一包含Optional中的实例的Set，否则返回一个空的不变的Set。

Optional提供很多实用的工具都是基于这几个方法实现，如果查看细节请看javadoc
```

### 有什么意义呢？

```
除了给null一个名字增加他的可读性，Optional最大的优势操作简易安全可靠。对于你编写程序来说，你强制自己处理缺席的情况，虽然FindBugs对解决这样的问题很有帮助，但是我们认为并不能很好的解决这样的问题。

一种典型的情况是返回的值可能存在也可能不存在，我们知道other.method(a, b)会返回一个空值，但是也有可能忘记a可能是一个空值当我们实现它的时候。 
```

**返回Optional来忘记这种情况对于调用者来说变得重要，因为他们必须自己打开代码来编译他们的代码。**

### 方便的方法

```
无论何时你想用MoreObjects.firstNonNull(T, T)将一个null值替换成其他的一些默认值，关于方法名称的建议，如果所有的输入提交都为null，他将快速失败并抛出一个NPE，如果使用Optional，这里有更好的替代方案--eg.first.or(second).

Strings中提供了一组处理String可能为空的方法。
特别的，我们提供了恰当的命名：

emptyToNull(String)
isNullOrEmpty(String)
nullToEmpty(String)

我们强调这些方法是因为这些方法在一些令人不悦的APIs中将null的字符窜和空的字符窜相等处理，每一次你讲null的字符窜和空的字符窜混淆使用，guava都会感到难过哭泣。（如果null的字符窜和为空的字符窜所表示不同的事情，那就很好啦，如果将它们认为是同一种东西，那就是一种坏的代码味道。）
```

## 先决条件

```
guava提供了一系列的先决条件检查工具类。我们强烈的将这些方法静态化。
```

每一个方法都有三个变量。

- 没有额外的参数，任何异常的抛出都没有错误信息。
- 一个额外的Object参数，任何异常的抛出都会有一个异常信息object.toString()。
- 用一个随意增加的Object参数表示一个额外的String参数。这种行为有时候像printf，但是对于GWT的一致性和高效性，他只允许这样表示%s。
  - 注：checkNotNull, checkArgument和checkState使用原始类型和对象重组而不是像varargs（可变参数）数组一样进行大量的重载--这样避免了原始类型装箱和varargs（可变参数）数组的重新分配问题。

三个变量的例子：

- checkArgument(i >= 0, "Argument was %s but expected nonnegative", i);
- checkArgument(i < j, "Expected i < j, but %s >= %s", i, j);

签名（没有包含额外的参数）

- checkArgument(boolean)
  1. 检查这个boolean是true，用于验证方法的参数。
  2. 如果有异常：IllegalArgumentException
- checkNotNull(T)
  1. 检查该值T是否为null，将该值T直接返回。
  2. 如果有异常：NullPointerException
- checkState(boolean)
  1. 检查object的一些状态，并不依靠于方法的参数。例如：一个Iterator会用这个检查next是否被调用在remove被调用前。
  2. 如果有异常：IllegalStateException
- checkElementIndex(int index, int size)
  1. 检查index在这个列表，字符窜或者是一个指定长度的数组中是否是一个正确的下标，一个元素的下标会在0到size不等。你不能传递这个列表，字符窜或者是定长的数组。你只能通过他们的size返回下标。
  2. 这里是列表文本如果有异常：IndexOutOfBoundsException
- checkPositionIndex(int index, int size)
  1. 检查index在这个列表，字符窜或者是一个指定长度的数组中是否是一个正确的下标，一个位置的下标会在0到size不等。你不能传递这个列表，字符窜或者是定长的数组。你只能通过他们的size返回下标。
  2. 这里是列表文本如果有异常：IndexOutOfBoundsException
- checkPositionIndexes(int start, int end, int size)
  1. 检查 [start, end)这个子数组是否是一个队列，字符串和指定长度的数组的一个正确的子集。返回他们子集的错误信息。
  2. 如果有异常IndexOutOfBoundsException

我们更加喜欢回滚重写Apache Commons的先决条件检查工具集，实现我们自己的工具集是有一些原因的。简要说明：

- 基于静态提供，guava更加简洁也不含糊。checkNotNull清楚的表明在做什么，和什么异常会抛出。
- checkNotNull 在校验后返回他的参数，允许一行程序在构造中：this.field = checkNotNull(field);
- 简单说，可变参数"printf-style"异常信息。（这种优势也是我们为什么推介继续使用checkNotNull而不是Objects.requireNonNull）

我们推介你将先决条件分成不同的行，当我们debug的时候这样就能帮助我们找出什么先决条件失败。此外，我们希望你提供有用的错误信息，这样也能方便的在需要的那一行进行检查。

## 解释排序

### 排序

#### 示例

```java
assertTrue(byLengthOrdering.reverse().isOrdered(list));
```

#### 概述

```
ordering是guava“流利”的Comparator类，可以用来实现复杂的比较器和依赖他们收集对象。

作为他的一个核心，Ordering实例相对于Comparator实例来说并没有什么特别。Ordering仅仅简单的依赖Comparator提供方法（例如：Collections.max）和获取他们作为一个实例方法。针对增强的的地方Ordering提供了通道方法来改进和提高已经存在的比较器。
```

\#创建
相同的排序以静态方法的形式提供：

- natural() 对于比较器类型采用正常的排序
- usingToString() 根据字符窜的表现形式例如toString()返回的字典排序来进行比较

创建一个先于Comparator包含在Ordering中的一个简单实现：

- Ordering.from(Comparator)

但是常用的创建一个定制排序是跳过Comparator，完全喜欢直接继承Ordering的抽象方法：

```java
 Ordering byLengthOrdering = new Ordering() {
   public int compare(String left, String right) {
     return Ints.compare(left.length(), right.length());
   }
 };
```



### 链式编程

```
一个给定的排序可以包装成变种的排序。一些最常用的排序包括：
```

- reverse() 返回反转排序
- nullsFirst() 返回一个将空数据置前非空数据置后的排序，反之nullsLast()。
- compound(Comparator) 使用一个特别的比较器（打破技巧）来返回一个排序。
- lexicographical() 返回一个元素按照词法顺序进行迭代排序的排序
- onResultOf(Function) 通过将函数应用于排序，然后使用原始排序对数据进行比较，返回排序值。

例如：你需要对这个类创建一个比较器

```java
class Foo {
    @Nullable String sortedBy;
	int notSortedBy;
}
```

...这种sortedBy可能为空的情况可以这样处理，这里有一种解决方法通过创建一个链式方法。

```java
 Ordering ordering = Ordering.natural().nullsFirst().onResultOf(new Function<Foo, String>() {
   public String apply(Foo foo) {
     return foo.sortedBy;
   }
 });
```



我们阅读一个链式的Ordering调用， **从右到左向后调用** 。这个例子提供了根据sortedBy这个字段来进行排序的Foo的一个方法实例。首先将所有sortedBy的null值移动到首位然后对后续的字段进行正常的排序。为什么会出现这种向后的调用，因为每一个链式的调用方法都会讲他的前面的方法包装新的Ordering对象中。

这种“向后调用”的异常规则：为了防止链式调用的混乱，我们从左到右读取。来避免困惑，避免在其他的链式调用中混淆困惑的调用。

链式编程的长度要比一般的调用长，所以难以理解。我们期望限制链式编程的数量，最多有三个链式的调用方法就像下面的例子一样。即使那样，你也许期望将分离出来中间的对象进行简化例如Function实例：

```java
Ordering ordering = Ordering.natural().nullsFirst().onResultOf(sortKeyFunction);
```

### 应用

guava提供了一系列的方法去操作和检查值和集合使用排序，我们这里列举一些重要的方法：

- greatestOf(Iterable iterable, int k)
  根据此顺序，从最大到最小的排序中返回k个最大的元素从这个指定的iterabl中。不一定是稳定的。
- isOrdered(Iterable)
  检查这个指定的Iterable是不是按照这个非递减排序实现
- sortedCopy(Iterable)
  作为一个List返回一个已排序的指定元素集合的备份
- min(E, E)
  按照这种排序返回俩个元素中最小的。如果这俩个元素相同，第一个元素会被返回。
- min(E, E, E, E...)
  按照这种排序返回这些元素中最小的。如果最终的元素有多个，则第一个元素会被返回。
- min(Iterable)
  返回Iterable中最小的元素。如果Iterable为空则抛出异常NoSuchElementException。

## 常用类方法

### equals

当你的类的成员变量可能是null的时候，实现Object.equals会产生错误，所以你不得不去检查分离null。使用Object.equal使你在使用相等的方法时对null的产生异常敏感，以确保它没有NPE的风险。

- Objects.equal("a", "a"); // returns true
- Objects.equal(null, "a"); // returns false
- Objects.equal("a", null); // returns false
- Objects.equal(null, null); // returns true

注：JDK7在objects类中提供了最新的相等的判断方法Objects.equals

### hashCode

一个类的所有哈希的成员变量都应该简洁。Guava的Objects.hashCode(Object...)创建了一个对于特殊序列的变量实用的,排序敏感的哈希。使用Objects.hashCode(field1, field2, ..., fieldn)取代手动创建哈希。
注：JDK7在Objects中提供了新的等效的方法Objects.hash(Object...).

### toString

在debug过程中一个好的toString方法的价值是无可估量的，但是书写缺失非常痛苦的。使用MoreObjects.toStringHelper()可以轻松的创建一个有用的toString方法。
一些简单的实例如下：

```java
// Returns "ClassName{x=1}"

 MoreObjects.toStringHelper(this)
   .add("x", 1)
   .toString();

// Returns "MyObject{x=1}"

 MoreObjects.toStringHelper("MyObject")
   .add("x", 1)
   .toString();
```



### compare/compareTo

实现Comparator，或者直接实现Comparable接口，会感到痛苦。

```java
 class Person implements Comparable {
   private String lastName;
   private String firstName;
   private int zipCode;
   public int compareTo(Person other) {
     int cmp = lastName.compareTo(other.lastName);
     if (cmp != 0) {
       return cmp;
     }
     cmp = firstName.compareTo(other.firstName);
     if (cmp != 0) {
       return cmp;
     }
     return Integer.compare(zipCode, other.zipCode);
   }
 }
```



这段代码很容易变得脏乱，查找bug变得困难，并且代码冗长令人不悦。我们可以让这变得更好。
针对这种情况，guava提供ComparisonChain
ComparisonChain是一种“懒式”的比较：他仅仅是比较，直到找到一个非零的结果，然后忽略进一步的输入。

```java
 public int compareTo(Foo that) {
   return ComparisonChain.start()
     .compare(this.aString, that.aString)
     .compare(this.anInt, that.anInt)
     .compare(this.anEnum, that.anEnum, Ordering.natural().nullsLast())
     .result();
 }
```



这种流利的语法更容易阅读，不易出现意外的打字错误，而且简小充足而不需要做其他工作。增加比较的工具可以在guava的"fluent Comparator"中 Ordering类中寻找，不在这里解释。



## Throwables

Guava的Throwables工具经常能简单的处理异常。

### 传播

有时，当你捕获一个异常，你想要将他抛出到下一个try/catch块中。这是一个关于RuntimeException和Error实例的很常见的情况，他们并不需要try/catch块，但是当你并不知道他们什么含义时通过try/catch块会产生异常。
guava提供了一些工具来简化传播异常，例如：

```java
 try {
   someMethodThatCouldThrowAnything();
 } catch (IKnowWhatToDoWithThisException e) {
   handle(e);
 } catch (Throwable t) {
   Throwables.propagateIfInstanceOf(t, IOException.class);
   Throwables.propagateIfInstanceOf(t, SQLException.class);
   throw Throwables.propagate(t);
 }
```



每一个放回都会抛出自己的异常，但是例如这样的抛出结果-- e.g. throw Throwables.propagate(t) -- 当一个异常要抛出的时候提供给收集器会非常有用。

这里有一个Guava提供的关于快速总结的传播方法:

- RuntimeException propagate(Throwable)
  - 按照原样传播如果他是一个RuntimeException或者是一个Error，或者是一个包装起来的RuntimeException或者抛出其他。保证把异常抛出。返回结果的类型是RuntimeException所以你可以类似上面通过写throw Throwables.propagate(t)这样的语句抛出。同时java会意识到这一行为保证会抛出异常。
- void propagateIfInstanceOf(Throwable, Class) throws X
  - 当他仅仅是X的一个实例的时候，按照原样进行传播。
- void propagateIfPossible(Throwable)
  - 如果仅仅是一个RuntimeException或者Error的时候原样抛出throwable。
- void propagateIfPossible(Throwable, Class) throws X
  - 如果仅仅是一个RuntimeException或者Error或者X的时候原样抛出throwable。

### 使用Throwables.propagate

#### 模仿java7的多catch和重复抛出

```
典型的，如果你想让异常在堆栈上传播，你并不需要一个catch块覆盖所有的情况。自从你不想覆盖异常，你可能就不会记录他或者做其他的操作。你可能会执行一些清除的操作，但是当这个操作成功的时候一般清除操作会不顾一切的发生，所以最终结束于finally的块中。然而，一个catch块有时候重新抛出是很有用的：也许你想要更新一个失败的次数在传递异常前，或者你想要在一定的条件下传播该异常。
```

当处理仅仅一种异常的时候捕获并且重新传播一个异常是简单的。当处理多种异常时就会变得复杂：

```java
 @Override public void run() {
   try {
     delegate.run();
   } catch (RuntimeException e) {
     failures.increment();
     throw e;
   } catch (Error e) {
     failures.increment();
     throw e;
   }
 }
```



java 7 通过多捕获来解决该问题：

```java
 } catch (RuntimeException | Error e) {
   failures.increment();
   throw e;
 }
```



非 Java7 的使用者被困住啦，他们喜欢像这样编码，但是编辑器并不允许他们抛出Throwable类型的一个变量：

```java
 } catch (Throwable t) {
   failures.increment();
   throw t;
 }
```



这个解决方案是将throw t 替换成 throw Throwables.propagate(t)。在这种限制的情况下，Throwables.propagate将唯一的行为变成了普通的代码。然而，Throwables.propagate是非常容易书写的，而且在其他方面，隐藏行为。一般情况下，这样的操作仅仅作用域RuntimeException 和 Error这种模式。如果这个catch块会捕获检查异常，像Throwables.propagate并不能直接传递一个检查过的异常。

总体来说，propagate的使用就是如此，在java7以下的模式显得并不是很重要。在其他版本，他保存了一些复制操作，但是这样就需要一个额外的方法进行重构。另外，使用propagate（是她更容易偶然的包装检查异常）

#### 不重要的：将“throws Throwable”到“throws Exception”的转变

很少的APIs，尤其是java相关的API和（作为一种结果）的JUnit，宣称方法抛出Throwable。与这些API进行交互是非常痛苦的，这些API中的大部分普通目的就是throws Exception。
Throwables.propaga被一些回调的人使用，他们会知道这些异常有一个non-Exception, non-Error Throwable。这里有一个JUnit的声明一个Callable的测试例子：

```java
 public Void call() throws Exception {
   try {
     FooTest.super.runTest();
   } catch (Throwable t) {
     Throwables.propagateIfPossible(t, Exception.class);
     Throwables.propagate(t);
   }
  return null;
 }
```



这里已经不需要进行propagate()，第二行就相当于throw new RuntimeException(t)。（题外话：这个例子使我对于propagateIfPossible仍然产生疑惑，因为他传递的不仅仅是参数给与的属性，还包括RuntimeException和Error）

此模式(或类似的变体，如抛出新的RuntimeException(t))在谷歌的代码基中出现大约30次。(搜索“propagateIfPossible[^;]* Exception.class[)];”)。它们中的一小部分采用显式抛出新RuntimeException(t)方法。我们可能想要一个throwWrappingWeirdThrowable方法来进行从抛出到异常的转换，但是考虑到两行转换，除非我们也反对propagateIfPossible，否则可能不太需要它。

#### 关于Throwables.propagate的使用争议

争议：将检查异常转换为不检查异常
理论上，不检查异常暗示bug，检查异常暗示问题超出你的控制。理论上，甚至是JDK有时候获取自己的错误（至少，对于一些方法，对任何人都没有一个正确的答案）

作为一个结果，调用者不得不在异常类型之间进行转换：

```java
 try {
   return Integer.parseInt(userInput);
 } catch (NumberFormatException e) {
   throw new InvalidInputException(e);
 }
 try {
   return publicInterfaceMethod.invoke();
 } catch (IllegalAccessException e) {
   throw new AssertionError(e);
 }
```



有时，这些调用者使用Throwables.propagate，那么他的不利是什么呢？

主要的一个就是没有明确的含义。throw Throwables.propagate(ioException)是什么意思呢？throw new RuntimeException(ioException)是什么意思呢？这俩个是做的相同的事情，但是后者更直接。前者会产生这样的问题：“这是干什么的？他不是仅仅包装进RuntimeException，是这样吗？如果是这样，那么他为什么使用一个方法进行包装。”

这里有一些问题，相对的“propagate”是一个模糊的名称吗。（这是否是一个抛出未声明异常的取渠道）也许“wrapIfChecked”会更好的匹配。如果这个方法用这个命名，然而，对于一个已知的检查过的异常的调用将没有优势。他们会增加一些负面的影响：也许这里有一个比简便的RuntimeException更合适的异常例如IllegalArgumentException

我们有时经常看到当这个异常仅仅是一个检查过的异常时使用propagate。这个结果就是比另一种方法更小更直接。

```java
 } catch (RuntimeException e) {
   throw e;
 } catch (Exception e) {
   throw new RuntimeException(e);
 }
 } catch (Exception e) {
   throw Throwables.propagate(e);
 }
```



然而，转换检查异常和未检查异常在这里就像一个特别典型的将大象放进屋里例子。毫无疑问在某些场景下他是正确的，但是更常见的应用是为了避免处理合法的检查异常。通常来说让我们强制来处理这些检查异常时一个坏主意。我并不希望将所有的都列举到这里。Throwables.propagate足以满足java使用者的目的来避免类似IOException这样的异常。

争议：异常隧道

但是当你实现一个方法并不允许抛出异常时你怎么办？有时你需要将异常包装成一个不检查异常。这当然是好的，但是再说一次，使用propagate进行简单包装是没有必要的。实际上，手动包装更可取：如果你包装所有异常（替代检查异常），另一方面你可以不包装所有的异常，这都是出现在很少的特别的情况下。相对的，你可以使用一个常用的异常类型来进行包装。

争议：重新抛出异常到其他线程

```java
try {
 return future.get();
} catch (ExecutionException e) {
 throw Throwables.propagate(e.getCause());
}
```

这里有一些事情需要注意：

1. 这个例子会是一个检查异常。看以上内容”将检查异常转换为非检查异常“。但是如果这个任务并不知道抛出一个检查异常呢？（也许结果是一个Runnable）结合以上讨论，你可以捕获异常和抛出AssertionError；propagate有最小的提供。典型的再Future中可以定义Futures.get.
2. 这个例子可能是一个 non-Exception, non-Error Throwable。（而且，他也许并不是一个，如果你尝试直接重新抛出异常，编辑器会提前考虑你可能的行为）参考以上的”将检查异常转换为非检查异常“
3. 这个例子可能是一个非检查异常或者是错误。如果是这样，他会直接重新抛出。不幸的是，他的堆栈信息追踪的是最初抛出异常的线程的信息，而不是当前传播异常的线程。显然在当前的传播过程中包含所有线程的堆栈信息链式最好的，就像ExecutionException一样抛出，通过.get获取（这个问题是没有真实反映传播，他是在一个不同的线程中重复抛出一个异常的任何代码）

### 构成链的原因

guava将一个异常的构成链变得简单，提供了三个有用的方法其功能不言而喻：

```java
 Throwable getRootCause(Throwable)
 List getCausalChain(Throwable)
 String getStackTraceAsString(Throwable)
```



# 集合

## 不变集合

### 例子

```java
 public static final ImmutableSet COLOR_NAMES = ImmutableSet.of(
   "red",
   "orange",
   "yellow",
   "green",
   "blue",
   "purple");
 
 class Foo {
   final ImmutableSet bars;
   Foo(Set bars) {
   this.bars = ImmutableSet.copyOf(bars); // defensive copy!
   }
 }
```



### 为什么？

不变的对象有很多有利条件，包括：

- 安全使用不信任的资源库
- 线程安全：可以安全的使用于多线程中而没有竞速风险
- 不支持修改，而且可以按照假设的时间和空间保存，所有的不变集合都要比可变集合具有更高的内存效率。
- 可以被以常量来使用，出乎意料的对于修改后能保持不变。

使用不变的对象进行拷贝是一个非常好的防御式编程技术。Guava对于每一个标准的集合类型都提供简单、使用方便不变版本，也包括Guava自己的集合变体。

JDK提供了Collections.unmodifiableXXX这样的方法，但是在我们看来，他们可以

- 笨重而啰嗦，如果你想在任何地方进行防御式拷贝是非常令人不愉快的。
- 不安全：如果没有人拿到最初集合的引用，那么这个返回的集合就是确定不变的。
- 效率低下：这个数据结构的开销在所有可变集合之上，包括并发修改检查，在哈希表中额外的空间占用。

当你不期望修改一个集合，或者期望一个集合被当做一个常量使用，那么将他使用防御式拷贝进一个不变的集合是一个非常好的主意。

重点：每一个guava的不变集合的实现都拒绝接受null值。我们做了一个额外的统计，在谷歌的内部代码中减少到只有5%的集合是允许使用null值的，其他95%的场景在对待null的值上快速失败。如果你需要使用null值，建议使用Collections.unmodifiableList，他是一个有好的可以允许null值的集合实现。更多的建议可以在这里找到。

### 如何办？

一个ImmutableXXX集合可以通过多种途径创建：

使用copyOf方法，例如：ImmutableSet.copyOf(set)

使用of方法，例如：ImmutableSet.of("a", "b", "c")或者ImmutableMap.of("a", 1, "b", 2)

使用一个Builder，例如：

```java
public static final ImmutableSet GOOGLE_COLORS =
 ImmutableSet.builder()
   .addAll(WEBSAFE_COLORS)
   .add(new Color(0, 191, 255))
   .build();
```



除了存储集合外，排序会在构造时期被保留。例如：

```java
ImmutableSet.of("a", "b", "c", "a", "d", "b")
```

这些元素会被迭代排序"a", "b", "c", "d".

#### copyOf比你想象中更漂亮

当你需要安全使用数据时，使用和记住ImmutableXXX.copyOf来企图避免拷贝数据是非常有用的。--具体确切的细节没有详细说明，但是实现它是一个典型的“明智的”行为。例如：

```java
 ImmutableSet foobar = ImmutableSet.of("foo", "bar", "baz");
 thingamajig(foobar);
 
 void thingamajig(Collection collection) {
   ImmutableList defensiveCopy = ImmutableList.copyOf(collection);
   ...
 }
```



在这样的代码中，当将一个ImmutableSet的常量视图使用ImmutableList.copyOf(foobar)会足够智能的返回foobar.asList()。

作为一个通常的启发，ImmutableXXX.copyOf(ImmutableCollection)试图避免一个线性时间

- **在常量时间内使用底层数据结构是可能的。例如：ImmutableSet.copyOf(ImmutableList)不会在一个常量时间内完成** 。
- 不会发生内存泄露--例如：如果你有ImmutableList hugeList，并且你进行了这样的操作ImmutableList.copyOf(hugeList.subList(0, 10))，执行确认的拷贝，用以避免意外的持有一个 **不需要保留的** hugeList引用
- 他不会改变含义--所以ImmutableSet.copyOf(myImmutableSortedSet)会执行一个显示的拷贝，因为ImmutableSet使用hashCode() 和 equals() 与ImmutableSortedSet的基于比较器的行为具有不同的语义。

这有助于最小化一个好的防御式编程风格的性能开销

### asList

所有的不变集合提供一个ImmutableList视图通过asList()，所以--例如--如果你想使用一个ImmutableSortedSet做数据存储，你会使用sortedSet.asList().get(k)得到k位置的最小元素。

经常返回ImmutableList--不总是，不经常--一个常数时间视图，要比一个解释性的拷贝好。也就是说，他经常比你平常使用的List更智能--例如，关于返回的集合使用更有效率的contains方法。



## 全新的集合类型

```
Guava介绍一些不包括在JDK中的新的集合类型，但是我们发现他们非常的有用。他们非常友好的与JDK集合框架共存，并没有硬性的将一些东西塞进JDk集合抽象概念中。

作为一个通用的总则，Guava集合实现JDK的接口非常清晰。
```

### MultiSet

传统的Java例子中已经不知道多少次这样使用，在一个文档中像这样：

```java
 Map<String, Integer> counts = new HashMap<String, Integer>();
   for (String word : words) {
     Integer count = counts.get(word);
     if (count == null) {
       counts.put(word, 1);
     } else {
     counts.put(word, count + 1);
   }
 }
```



这是使用不便的，有倾向性的错误，并且不能提供一些有用的集合统计数字。就像这个代码中的统计总数，我们可以做的更好。

Guava提供一个新的集合类型，MultiSet，一个提供增加众多元素的类型。定义一个multiSet，在数学中，作为“集合概念的一种推广，其中成员可以出现多次...在多集中，就像在集合中和元组中一样，元素的顺序是无关的：多集{a,a,b}和{a,b,a}是相等的。”

看看这里有俩条主要的途径就像这样：

- 这就像ArrayList没有排序限制：排序并不是很重要的。
- 就像Map<E, Integer>有元素和索引值。

Guava 的 Multiset API将所有的使用途径的组合介绍，就像这样：

- 当我们对待一个通常的集合时，MultiSet表现的更像是一个非强排序的ArrayList：
  1. add(E)的调用对于一个新增的元素只会出现一次
  2. MultiSet的iterator()方法遍历集合中每一个元素都遍历一次
  3. MultiSet的size()方法是每一个元素出现次数的总数
- 这个额外的查询操作，以及性能特征，就像你从Map<E, Integer>期望得到的一样。
  1. count(Object)返回一个将对应元素联系起来的总数。就像HashMultiset，统计的操作是O(1)；TreeMultiset，统计的操作是O(log n)
  2. entrySet()返回一个Set<Multiset.Entry>就像是Map中的类似的entrySet()
  3. elementSet()返回关于MultiSet集合的一个明显元素集合Set。
  4. MultiSet元素的性能消耗的实现是线性的。

尤其，MultiSet与集合接口的约定是完全一致的，除了JDK本身有先例的少数情况--特别地，TreeMultiset就像是TreeSet，使用Object.equals来替代比较器相等。特别，Multiset.addAll(Collection)会把集合中的每一个元素的每一次出现次数加一，这比Map方法所需的for循环要方便的多。

- count(E)
- elementSet()
- entrySet()
- add(E, int)
- remove(E, int)
- setCount(E, int)
- size()

MultiSet并不是一个Map

注意Multiset不是一个Map<E,Integer>，尽管这可能是MultiSet实现的一部分。MultiSet是一种真正的集合类型，他具有集合的所有的契约，其他的一些显著的差异：

- 一个MultiSet的元素拥有自己的相对统计值，没有元素有否定的统计，并且如果并没有被包含到这个MultiSet集合中那么他的值是0.
- multiset.size()返回集合的大小，和所有元素的统计是相同的。对于不同元素的数量，使用elementSet().size()。（例如，add（E）将multiset.size()加一）
- multiset.iterator()迭代每一个元素的每一次出现，所以迭代的长度与multiset.size()的值是相等的
- Multiset支持增加元素，移除元素，或者设置元素的不同索引值。setCount(elem, 0)相当于将这个元素移除。
- multiset.count(elem)对于一个元素不在MultiSet集合中时一般返回0

#### 实现

Guava提供关于MultiSet的很多实现，与JDK的Map实现粗略的对比：

- HashMap HashMultiset
- TreeMap TreeMultiset
- LinkedHashMap LinkedHashMultiset
- ConcurrentHashMap ConcurrentHashMultiset
- ImmutableMap ImmutableMultiset

### SortedMultiset

SortedMultiset是MultiSet接口的一个实现支持高效的将子MultiSet集合放在指定的位置。例如：你可以使用latencies.subMultiset(0, BoundType.CLOSED, 100, BoundType.OPEN).size()在100ms的等待时间里决定给你多少占用字符，并且任然可以通过latencies.size()来决定其他的部分

TreeMultiSet实现SortedMultiset接口，在书写的同时，ImmutableSortedMultiset经测试与GWT很好的兼容。

### MultiMap

每一个有经验的java程序员都知道，一个指针指向另一个地方，实现一个Map<K, List> 或者 Map<K, Set>，并且解决了这种架构的尴尬。例如：Map<K, Set>是一种典型的一种途径来表示存储结构的有向图表。Guava的Multimap框架使得键匹配不同的值变得容易。一个Multimap是一个通常渠道把键和很多值联系到一起。

这里有俩种渠道来理解Multimap的概念：作为一个集合单独的键匹配单独的值

- a -> 1
- a -> 2
- a -> 4
- b -> 3
- c -> 5

或者唯一的key匹配集合：

- a -> [1, 2, 4]
- b -> [3]
- c -> [5]

通常，这个Multimap接口作为第一视图，但是允许你通过另一种方式asMap()来创建，会返回Map<K, Collection>。更重要的是，这里不再有一个key匹配一个空的集合：一个key至少会有一个值，或者他仅仅是不存在与Multimap中。

你可以直接使用MultiMap接口，然而，更多的是使用ListMultimap或者SetMultimap，他们的键会对应的匹配List或者是Set

### 构造

创建一个Multimap的最直接最多的途径是通过使用MultimapBuilder来创建，它可以提前设置你的建和值是什么规则。例如：

```java
// creates a ListMultimap with tree keys and array list values
ListMultimap<String, Integer> treeListMultimap =
    MultimapBuilder.treeKeys().arrayListValues().build();

// creates a SetMultimap with hash keys and enum set values
·SetMultimap<Integer, MyEnum> hashEnumMultimap =
    MultimapBuilder.hashKeys().enumSetValues(MyEnum.class).build();
```

你也可以直接在你实现的类上使用create()方法来创建，但是他并没有吧MultimapBuilder的优势体现出来。

### 修改

Multimap.get(key)返回被执行的key的所有联系的值的一个视图，或者当前什么也没有。对于一个ListMultimap，他返回一个List；对于一个SetMultimap，他返回一个Set。

通过Multimap来修改，例如：

```java
Set<Person> aliceChildren = childrenMultimap.get(alice);
aliceChildren.clear();
aliceChildren.add(bob);
aliceChildren.add(carol);
```

其他一些修改multimap的途径（更多的是直接修改）：

```java
put(K, V)
putAll(K, Iterable<V>)
remove(K, V)
removeAll(K)
replaceValues(K, Iterable<V>)
```

视图
Multimap也支持一些功能强大的视图：

- asMap视图 任何Multimap<K, V>视图作为一个Map<K, Collection>。返回的map支持remove，并且通过写入集合来改变返回的值，但是map并不支持put和putAll。谨慎的，当你想对于一个缺席的值返回一个null或者写入一个新的空的集合（将asMap().get(key)应用到合适的集合类型，SetMultimap替代一个Set，ListMultimap替代一个List--但是集合的系统类型禁止ListMultimap返回Map<K, List>），你可以使用asMap().get(key)。
- entries 在Multimap中关于Collection<Map.Entry<K, V>>的多有的迭代视图（例如：SetMultimap就是一个Set）
- keySet 在Multimap中作为一个Set返回不同keys的视图
- keys 将Multimap的所有key作为一个MultiSet，并且对于所有的值和所对应的key产生多联系。这和Iterables.concat(multimap.asMap().values())很相似，返回一个全集集合来替代。

### Multimap并不是一个Map

一个Multimap<K, V> 并不是一个 Map<K, Collection>，尽管Multimap作为一个map必须实现map，重要的不同点包括：

- Multimap.get(key)经常返回一个非null，可能是空的集合。这并不是multimap花费内存与key建立联系，但是作为替代，返回的是允许你增加与key有联系的集合按照你的要求。
- 如果你更喜欢Map--就行一个key返回一个null的行为，这个并不在multimap中，你需要使用asMap()视图来得到一个Map<K, Collection>（或者，从一个ListMultimap得到一个Map<K,List>,使用静态方法Multimaps.asMap()。相似的方法除了SetMultimap and SortedSetMultimap以外）
- Multimap.containsKey(key)如果而且是任何一个元素与这个特定的key产生联系都会返回true。典型的，如果一个键k明显的与一个或者是多个值有关联，但是已经被从multimap中移除，那么返回的是false。
- Multimap.entries()返回在multimap中迭代多有的key。如果你想要多有的key的集合，那么请使用asMap().entrySet().
- MultiMap.size()返回的迭代multimap时的迭代值，并不是不同key的总数。使用Multimap.keySet().size()可以得到不同的key的总数。

#### 实现

Multimap 提供一个广泛的实现。你可以应用它到很多地方即使你已经使用Map<K, Collection>。

```java
Implementation	Keys behave like...	Values behave like..
ArrayListMultimap	HashMap	ArrayList
HashMultimap	HashMap	HashSet
LinkedListMultimap *	LinkedHashMap``*	LinkedList``*
LinkedHashMultimap**	LinkedHashMap	LinkedHashSet
TreeMultimap	TreeMap	TreeSet
ImmutableListMultimap	ImmutableMap	ImmutableList
ImmutableSetMultimap	ImmutableMap	ImmutableSet
```

每一个实现，除了不变的几个，都支持null键和值。

- LinkedListMultimap.entries()保留条目的插入顺序。具体详情查看对应链接。
  ** LinkedHashMultimap **保留条目的插入顺序** ，就像插入有序的keys，并且根据对应的可以与所属的value建立联系。
  对于已列出的实现我们期望并不是所有的实现都是Map<K, Collection>（特别是，Multimap的一些实现使用Hash表来减少性能开销）

如果你想更多的定制化需求，使用Multimaps.newMultimap(Map, Supplier)或者list和set版本来使用一个通常的集合，list或者set实现来返回你自己的multimap。

### BiMap

通常的方式是将value和key分别存储到不同的map并且让他们保持同步，但是这特别容易造成Bug并且当一个值已经存在于一个map中是非常容易造成迷惑。例如：

```java
Map<String, Integer> nameToId = Maps.newHashMap();
Map<Integer, String> idToName = Maps.newHashMap();

nameToId.put("Bob", 42);
idToName.put(42, "Bob");
// what happens if "Bob" or 42 are already present?
// weird bugs can arise if we forget to keep these in sync...
```

一个BiMap<K,V>就是一个Map<K,V>

- 允许你通过inverse()来获取“相反的”BiMap<V, K>视图
- 确定values是唯一的，设置valus()为一个Set

BiMap.put(key, value)会抛出一个IllegalArgumentException如果你视图将一个key匹配到一个已经存在的value。如果你想将咸鱼这个特定的value值的所有的value值删除，可以使用BiMap.forcePut(key, value)来执行。

```java
BiMap<String, Integer> userId = HashBiMap.create();
...

String userForId = userId.inverse().get(id);
```

#### 实现

```java
Key-Value Map Impl	Value-Key Map Impl	Corresponding BiMap
HashMap	        HashMap	        HashBiMap
ImmutableMap	    ImmutableMap	ImmutableBiMap
EnumMap	        EnumMap	    EnumBiMap
EnumMap	        HashMap	    EnumHashBiMap
```

注意：BiMap工具就像Maps中的synchronizedBiMap

### Table

```java
Table<Vertex, Vertex, Double> weightedGraph = HashBasedTable.create();
weightedGraph.put(v1, v2, 4);
weightedGraph.put(v1, v3, 20);
weightedGraph.put(v2, v3, 5);

weightedGraph.row(v1); // returns a Map mapping v2 to 4, v3 to 20
weightedGraph.column(v3); // returns a Map mapping v1 to 20, v2 to 5
```

典型的，当你同一时间获取超过一个key的索引值，你会得到类似这样的Map<FirstName, Map<LastName, Person>>一些东西，然而这时丑陋的，而且非常难用。Guava提供了一个新的集合类型，Table，提供了类似上面一样具有“row”和“column”类型的集合。Table支持一些你可以使用以下类型数据的视图，包括

- rowMap() Table<R, C, V> 替代 Map<R, Map<C, V>>。相似的，rowKeySet()返回一个Set
- row(r)返回一个非null的Map<C,V> 依赖Table来进行Map的修改
- 类似的column也提供了的方法：columnMap(), columnKeySet(), 和 column(c). （column的有效性要比row的低一些）

提供了一些Table的实现，包括：

- HashBasedTable 本质上返回一个HashMap<R, HashMap<C, V>>
- TreeBasedTable 本质上返回一个TreeMap<R, TreeMap<C, V>>
- ImmutableTable
- ArrayTable 它要求在构建时指定完整的行和列，但是在表比较密集时，他是由一个二维数组支持，以提高速度和内存的效率。ArrayTable的工作方式与其他的实现不同；有关详细信息，请惨遭Javadoc

### ClassToInstanceMap

有时，你的map中的key并不是一样的类型：他们是不同的类型，并且你想要将对应类型的key匹配到对应的value上，Guava提供了ClassToInstanceMap来实现这样的目的。

增加和扩展Map接口，ClassToInstanceMap提供了方法T getInstance(Class) 和 T putInstance(Class, T)，用来排除那些类型安全的隐患。

ClassToInstanceMap有一个独立的类型参数，典型的命名为B，表示映射管理类型的上限。例如：

```
ClassToInstanceMap<Number> numberDefaults = MutableClassToInstanceMap.create();
numberDefaults.putInstance(Integer.class, Integer.valueOf(0));
```

在技术上来说，ClassToInstanceMap **实现了 Map<Class<? extends B>, B>，--或者用其他的言语表达，从B的子类到B的实例的映射。这回在ClassToInstanceMap中普通类型的调用有一点迷惑，但是只要记住B仅仅是映射类型的上限--通常，B仅仅是一个Object**

Guava提供了有用的实现MutableClassToInstanceMap 和 ImmutableClassToInstanceMap。

重要：像任何的Map<Class, Object>，一个ClassToInstanceMap可以包含原始类型的实体，并且一个原始类型和他的包装类型可能映射不同的values。

### RangeSet

一个RangeSet描述的是一个非空的，不连续的set。当在一个可变的RangSet中添加一个Range，任何连续的range合并到一起，并且空的range会忽略。例如：

```java
 RangeSet<Integer> rangeSet = TreeRangeSet.create();
   rangeSet.add(Range.closed(1, 10)); // {[1, 10]}
   rangeSet.add(Range.closedOpen(11, 15)); // disconnected range: {[1, 10], [11, 15)}
   rangeSet.add(Range.closedOpen(15, 20)); // connected range; {[1, 10], [11, 20)}
   rangeSet.add(Range.openClosed(0, 0)); // empty range; {[1, 10], [11, 20)}
   rangeSet.remove(Range.open(5, 10)); // splits [1, 10]; {[1, 5], [10, 10], [11, 20)}
```

记住Range.closed(1, 10) 或者 Range.closedOpen(11, 15)来合并range，你必须首先用Range.canonical(DiscreteDomain)对range进行预处理，或者DiscreteDomain.integers().

笔记：RangSet并不支持GWT，JDK1.5一下也不支持；RangSet请求所有使用的NavigableMap特征是在JDK1.6

#### 视图

RangeSet实现支持一个更加广泛的视图，包括：

- complement()
- subRangeSet(Range)
- asRanges()
- asSet(DiscreteDomain)

#### Queries

增加了在他们视图上的操作，RangSet支持一些直接的查询操作，这是一些比较重要的：

- contains(C)
- rangeContaining(C)
- encloses(Range)
- span()

### RangeMap

RangeMap是描述一个不想交，非空的范围值的集合类型。不像RangeSet，RangeMap不会“联合”相连映射，甚至相交的范围映射相同的值。例如：

```java
RangeMap<Integer, String> rangeMap = TreeRangeMap.create();
rangeMap.put(Range.closed(1, 10), "foo"); // {[1, 10] => "foo"}
rangeMap.put(Range.open(3, 6), "bar"); // {[1, 3] => "foo", (3, 6) => "bar", [6, 10] => "foo"}
rangeMap.put(Range.open(10, 20), "foo"); // {[1, 3] => "foo", (3, 6) => "bar", [6, 10] => "foo", (10, 20) => "foo"}
rangeMap.remove(Range.closed(5, 11)); // {[1, 3] => "foo", (3, 5) => "bar", (11, 20) => "foo"}
```

#### 视图

RangeMap提供了俩个视图：

- asMapOfRanges()
- subRangeMap(Range)



## 工具类

### 集合工具集

任何有经验的开发者在使用JDK集合框架的时候都知道并且喜欢java.util.Collection中的工具集。Guava基于这些线索提供了更多的工具集。这里是Guava最常用和最重要的部分。

对于特定接口的方法以相对直观的方式进行分组：

```java
Interface	JDK or Guava?	Corresponding Guava utility class
Collection	JDK	        Collections2
List	        JDK	        Lists
Set	        JDK	        Sets
SortedSet	JDK	        Sets
Map	        JDK	        Maps
SortedMap	JDK	        Maps
Queue	        JDK	        Queues
Multiset	Guava	        Multisets
Multimap	Guava	        Multimaps
BiMap	        Guava	        Maps
Table	        Guava	        Tables
```

寻找转换、过滤器等等？这些东西都在我们的函数编程文章中，在函数习惯用法下面。

### 静态构造

在JDK 7之前，构造一个新的通常的集合需要复制一段令人不快的代码

```java
List<TypeThatsTooLongForItsOwnGood> list = new ArrayList<TypeThatsTooLongForItsOwnGood>();
```

我认为我们都会认为这是让人不快的。Guava提供了静态的方法就像使用魔法般利用右边的类型进行推测：

```java
List<TypeThatsTooLongForItsOwnGood> list = Lists.newArrayList();
Map<KeyType, LongishValueType> map = Maps.newLinkedHashMap();
```

为了方便使用，在JDK7的时候使用了菱形操作来减少麻烦：

```java
List<TypeThatsTooLongForItsOwnGood> list = new ArrayList<>();
```

但是Guava在这方面做得更好。采用工厂方法模型，我们可以初始化集合时方便的使用开始的元素。

```java
Set<Type> copySet = Sets.newHashSet(elements);
List<String> theseElements = Lists.newArrayList("alpha", "beta", "gamma");
```

另外，有能力命名为工厂方法（Effective Java item 1），我们可以初始化集合的大小来提高他的可读性。

```java
List<Type> exactly100 = Lists.newArrayListWithCapacity(100);
List<Type> approx100 = Lists.newArrayListWithExpectedSize(100);
Set<Type> approx100Set = Sets.newHashSetWithExpectedSize(100);
```

这个清晰的静态工厂方法提供并列出相关的使用程序类。

记录：Guava中新的集合类型不直接公开原始的构造函数，或者再他们的工具类中含有初始化器，他们直接提供静态工厂方法，例如：

```java
Multiset<String> multiset = HashMultiset.create();
```

### Iterables

如果可能，Guava更愿意提供工具集来接受Iterable而不是一个Collection。在谷歌，遇到一个”集合“并不罕见，它实际上不是存储在主内存中，而是从数据库或者其他数据中心获取，并且在不实际获取所有元素的情况下不能支持size()之类的操作。

作为结果，你期望很多collections支持的很多方法和操作可以在Iterables中找到。此外，大多数迭代方法在迭代器中都有一个接受原始迭代器的对应版本。

Iterables中的主要的操作都是懒加载：他们只在相对必要的时候，推进迭代。Iterables很多操作本身返回一个懒加载完成视图，这比在一个内存中建立一个集合更直接。

Guava 12 中，Iterables支持FluentIterable类，一个包装类和对于很多操作提供一个”流畅“句法。

下面列举的是一些很常用的工具类，尽管Iterables中的很多的”函数式“方法会在Guava函数式文档中讨论。

```java
concat(Iterable<Iterable>)	
frequency(Iterable, Object)	
partition(Iterable, int)
getLast(Iterable)
elementsEqual(Iterable, Iterable)
unmodifiableIterable(Iterable)	
limit(Iterable, int)	
getOnlyElement(Iterable)	
Iterable<Integer> concatenated = Iterables.concat(
  Ints.asList(1, 2, 3),
  Ints.asList(4, 5, 6));
// concatenated has elements 1, 2, 3, 4, 5, 6

String lastAdded = Iterables.getLast(myLinkedHashSet);

String theElement = Iterables.getOnlyElement(thisSetIsDefinitelyASingleton);
  // if this set isn't a singleton, something is wrong!
```

#### 收藏夹

通常，集合支持这些原生操作在另一个集合，但不是迭代器。

当一个输入值是一个集合时，他的每一个操作都会被委托给集合接口方法。例如：如果Iterables.size传递给一个集合，他会调用Collection.size方法替换遍历迭代器。

```java
addAll(Collection addTo, Iterable toAdd)
contains(Iterable, Object)
removeAll(Iterable removeFrom, Collection toRemove)
retainAll(Iterable removeFrom, Collection toRetain)
size(Iterable)
toArray(Iterable, Class)
isEmpty(Iterable)	
get(Iterable, int)
toString(Iterable)
```

#### FluentIterable

除了上面提到的函数式方法外，FluentIterable还有一些便利的方法可以拷贝到不变的集合中：

```java
Result Type          Method
ImmutableList        toImmutableList()
ImmutableSet         toImmutableSet()
ImmutableSortedSet   toImmutableSortedSet(Comparator)
```

#### Lists

增加静态构造方法和函数式编程方法，Lists在List类上提供一些工具方法。

```java
partition(List, int)
reverse(List)
List<Integer> countUp = Ints.asList(1, 2, 3, 4, 5);
List<Integer> countDown = Lists.reverse(theList); // {5, 4, 3, 2, 1}

List<List<Integer>> parts = Lists.partition(countUp, 2); // {{1, 2}, {3, 4}, {5}}
```

#### 静态工厂

Lists 提供以下静态工厂方法：

```java
Implementation/Factories
ArrayList	
    basic, with elements, from Iterable, with exact capacity, with expected size, from Iterator
LinkedList	
    basic, from Iterable
```

#### Sets

Sets工具集包含了一些很屌的方法。

\##集论的操作

我们提供一些集论的操作，作为包含参数sets的视图实现。他们返回一个SetView视图，那么有什么用途呢：

- 直接作为一个Set，自从他实现了Set接口。
- 将他拷贝到另外一个可变的集合中使用copyInto(Set)
- 使用immutableCopy()来进行一个不变的拷贝

```java
Method
union(Set, Set)
intersection(Set, Set)
difference(Set, Set)
symmetricDifference(Set, Set)
Set<String> wordsWithPrimeLength = ImmutableSet.of("one", "two", "three", "six", "seven", "eight");
Set<String> primes = ImmutableSet.of("two", "three", "five", "seven");

SetView<String> intersection = Sets.intersection(primes, wordsWithPrimeLength); // contains "two", "three", "seven"
// I can use intersection as a Set directly, but copying it can be more efficient if I use it a lot.
return intersection.immutableCopy();
```

#### 其他Set工具集

```java
cartesianProduct(List<Set>)

powerSet(Set)
Set<String> animals = ImmutableSet.of("gerbil", "hamster");
Set<String> fruits = ImmutableSet.of("apple", "orange", "banana");

Set<List<String>> product = Sets.cartesianProduct(animals, fruits);
// {{"gerbil", "apple"}, {"gerbil", "orange"}, {"gerbil", "banana"},
//  {"hamster", "apple"}, {"hamster", "orange"}, {"hamster", "banana"}}

Set<Set<String>> animalSets = Sets.powerSet(animals);
// {{}, {"gerbil"}, {"hamster"}, {"gerbil", "hamster"}}
```

#### 静态工厂

Sets提供一下静态工厂方法：

```java
HashSet	
    basic, with elements, from Iterable, with expected size, from Iterator
LinkedHashSet	
    basic, from Iterable, with expected size
TreeSet	
    basic, with Comparator, from Iterable
```

#### Maps

Maps有一些非常酷的工具集需要单独的解释。

#### 唯一的索引

Maps.uniqueIndex(Iterable, Function)解决一种常用的情况，即有一组对象，每一个对象都有唯一的属性，并且希望能够基于该属性查找这些对象。

让我们看看我们有对应唯一长度的一组string，并且我们希望通过一些特定的长度查找对应的string。

```java
ImmutableMap<Integer, String> stringsByIndex = Maps.uniqueIndex(strings, new Function<String, Integer> () {
    public Integer apply(String string) {
      return string.length();
    }
  });
```

如果索引不是唯一的，请参照Multimaps.index

#### 不同

Maps.difference(Map, Map)允许你比较这俩个map之间所有的不同。他会返回一个MapDifference的对象，其他方法见一下：

```java
entriesInCommon()
entriesDiffering()
ValueDifference
entriesOnlyOnLeft()
entriesOnlyOnRight()
Map<String, Integer> left = ImmutableMap.of("a", 1, "b", 2, "c", 3);
Map<String, Integer> right = ImmutableMap.of("b", 2, "c", 4, "d", 5);
MapDifference<String, Integer> diff = Maps.difference(left, right);

diff.entriesInCommon(); // {"b" => 2}
diff.entriesDiffering(); // {"c" => (3, 4)}
diff.entriesOnlyOnLeft(); // {"a" => 1}
diff.entriesOnlyOnRight(); // {"d" => 5}
```

#### BiMap 工具集

BiMap的Guava工具集就是一个Maps类，也就是一个BiMap就是一个Map

```java
BiMap utility	                Corresponding Map utility
synchronizedBiMap(BiMap)	Collections.synchronizedMap(Map)
unmodifiableBiMap(BiMap)	Collections.unmodifiableMap(Map)
```

#### Multisets

Collection的操作标准，就像containsAll，忽略multiset中的元素总数，而是仅仅在意这个元素是否在这个multiset中，或者，Multisets提供一系列操作来考虑multiset中的元素的多样性。

```java
Multiset<String> multiset1 = HashMultiset.create();
multiset1.add("a", 2);

Multiset<String> multiset2 = HashMultiset.create();
multiset2.add("a", 5);

multiset1.containsAll(multiset2); // returns true: all unique elements are contained,
  // even though multiset1.count("a") == 2 < multiset2.count("a") == 5
Multisets.containsOccurrences(multiset1, multiset2); // returns false

multiset2.removeOccurrences(multiset1); // multiset2 now contains 3 occurrences of "a"

multiset2.removeAll(multiset1); // removes all occurrences of "a" from multiset2, even though multiset1.count("a") == 2
multiset2.isEmpty(); // returns true
```

Multisets包含的一些其他工具集：

```java
copyHighestCountFirst(Multiset)
unmodifiableMultiset(Multiset)
unmodifiableSortedMultiset(SortedMultiset)
Multiset<String> multiset = HashMultiset.create();
multiset.add("a", 3);
multiset.add("b", 5);
multiset.add("c", 1);

ImmutableMultiset<String> highestCountFirst = Multisets.copyHighestCountFirst(multiset);

// highestCountFirst, like its entrySet and elementSet, iterates over the elements in order {"b", "a", "c"}
```

#### Multimaps

Multimaps提供值得个人解释的一系列普通工具的操作。

index
这个Maps.uniqueIndex的堂兄弟, Multimaps.index(Iterable, Function)回答了当你想要根据具有相同特征的一些特定的条件查找所有的对象的这种场景，这并不一定是唯一的。

让我们看看将string根据长度组合

```java
ImmutableSet<String> digits = ImmutableSet.of(
    "zero", "one", "two", "three", "four",
    "five", "six", "seven", "eight", "nine");
Function<String, Integer> lengthFunction = new Function<String, Integer>() {
  public Integer apply(String string) {
    return string.length();
  }
};
ImmutableListMultimap<Integer, String> digitsByLength = Multimaps.index(digits, lengthFunction);
/*
 * digitsByLength maps:
 *  3 => {"one", "two", "six"}
 *  4 => {"zero", "four", "five", "nine"}
 *  5 => {"three", "seven", "eight"}
 */
```

#### 转换

自从Multimap可以将多个key匹配一个value，和将一个key匹配对个value，将Multimap转换将变得非常有用。Guava提供 invertFrom(Multimap toInvert, Multimap dest) 让你实现这种操作，对于你不用选择一种实现。

注：如果你使用ImmutableMultimap，考虑用 ImmutableMultimap.inverse() 替代。

```java
ArrayListMultimap<String, Integer> multimap = ArrayListMultimap.create();
multimap.putAll("b", Ints.asList(2, 4, 6));
multimap.putAll("a", Ints.asList(4, 2, 1));
multimap.putAll("c", Ints.asList(2, 5, 3));

TreeMultimap<Integer, String> inverse = Multimaps.invertFrom(multimap, TreeMultimap.<String, Integer> create());
// note that we choose the implementation, so if we use a TreeMultimap, we get results in order
/*
 * inverse maps:
 *  1 => {"a"}
 *  2 => {"a", "b", "c"}
 *  3 => {"c"}
 *  4 => {"a", "b"}
 *  5 => {"c"}
 *  6 => {"b"}
 */
```

### forMap

需要在Map上使用一个Multimap的方法？forMap(Map)将一个Map的视图作为一个SetMultimap，这是非常有用的，例如，可以混合使用Multimaps.invertFrom。

```java
Map<String, Integer> map = ImmutableMap.of("a", 1, "b", 1, "c", 2);
SetMultimap<String, Integer> multimap = Multimaps.forMap(map);
// multimap maps ["a" => {1}, "b" => {1}, "c" => {2}]
Multimap<Integer, String> inverse = Multimaps.invertFrom(multimap, HashMultimap.<Integer, String> create());
// inverse maps [1 => {"a", "b"}, 2 => {"c"}]
```

#### 包装

Multimaps提供了典型的包装方法，就像工具一样好，用来获得可选的基于Map或者是Collection实现的Multimap实现。

```java
Multimap type	 Unmodifiable	 Synchronized	 Custom

Multimap	
unmodifiableMultimap	
synchronizedMultimap	
newMultimap

ListMultimap	
unmodifiableListMultimap	
synchronizedListMultimap	
newListMultimap

SetMultimap	
unmodifiableSetMultimap	
synchronizedSetMultimap	
newSetMultimap

SortedSetMultimap
unmodifiableSortedSetMultimap
synchronizedSortedSetMultimap
newSortedSetMultimap
```

Multimap的实现习惯允许你指定一个特定的实现应该返回Multimap。警告包括：

- Multimap假定对map和工厂返回的list具有完全的所有权。这些不应该手动更新，他们在提供时应该是空的，并且他们不应该使用软引用、弱引用和虚引用。
- 在你修改Multimap后将不保证Map中的内容是什么样子的。
- 当任何同时的修改操作发生在Multimap时不保证线程安全，同样map和通过工厂实例化产生的也是。但是并发读操作将工作正确。如果需要将运行的这部分用synchronized包装。
- 如果是map，工厂，工厂产生的列表都是可序列化的，那么multismap的内容也可以序列化。
- 通过Multimap.get(key)返回的集合与你的Supplier返回的集合类型不同，但是Supplier如果返回RandomAccess列表，这个列表通过Multimap.get(key)返回的也会是随机的。

尽管Multimap方法习惯期望一个Supplier的参数来产生一个新的集合。这里有一个例子，写入一个ListMultimap返回一个TreeMap来匹配LinkedList。

```java
ListMultimap<String, Integer> myMultimap = Multimaps.newListMultimap(
  Maps.<String, Collection<Integer>>newTreeMap(),
  new Supplier<LinkedList<Integer>>() {
    public LinkedList<Integer> get() {
      return Lists.newLinkedList();
    }
  });
```

### Tables

Tables类提供了一小部分使用的工具集。

#### customTable

比较Multimaps.newXXXMultimap(Map, Supplier)工具集， Tables.newCustomTable(Map, Supplier) 允许你指定一个Table实现，无论任何的行或者列集合随你所想。

```java
// use LinkedHashMaps instead of HashMaps
Table<String, Character, Integer> table = Tables.newCustomTable(
  Maps.<String, Map<Character, Integer>>newLinkedHashMap(),
  new Supplier<Map<Character, Integer>> () {
    public Map<Character, Integer> get() {
      return Maps.newLinkedHashMap();
    }
  });
```

#### 使。。。调换顺序

这个transpose(Table<R, C, V>)方法允许你将一个Table<R, C, V>视图作为Table<C, R, V>

#### 包装

这是你喜欢和知道的熟悉的不可修改的包装。考虑，在大多数的场景中无论如何使用 ImmutableTable 替换。

- unmodifiableTable
- unmodifiableRowSortedTable



## 额外的工具集

### 介绍

有时候你需要书写你自己的集合扩展。也许你想要已一种特定的行为将元素添加到一个list中，或者你想写一个Iterable来返回数据库查询结果。Guava提供一系列的工具来让这些任务变得容易，并且针对你（毕竟我们自己也在扩展框架）。

### 转发修饰符

对于大多数的集合接口，Guava提供 Forwarding 抽象类来简化修饰符模型。

Forwarding类定义了一个抽象的方法， delegate()，你可以重写返回的修饰符对象。任何其他方法都是直接委托给delegate：所以，例如，ForwardingList.get(int) 就是 delegate().get(int) 的简单实现。

关于子类 ForwardingXXX 并且实现 delegate() 方法，你可以在最终的目的类中选择一个进行重写，添加修饰过的功能，而不必自己委托每个方法。

另外，很多方法有一个standardMethod 的实现你期待的行为，添加一些雷士 AbstractList 或者 其他JDK中的骨架类来提供的好处。

让我们写一个例子，假象你想要修饰一个 List 并且记录他所有元素添加到其中的日志。当然，我们希望记录日志不用关注是使用什么方法（add(int, E), add(E), or addAll(Collection)） -- 所以我们不得不重写所有的这些方法。

```java
class AddLoggingList<E> extends ForwardingList<E> {
  final List<E> delegate; // backing list
  @Override protected List<E> delegate() {
    return delegate;
  }
  @Override public void add(int index, E elem) {
    log(index, elem);
    super.add(index, elem);
  }
  @Override public boolean add(E elem) {
    return standardAdd(elem); // implements in terms of add(int, E)
  }
  @Override public boolean addAll(Collection<? extends E> c) {
    return standardAddAll(c); // implements in terms of add
  }
}
```

记住，默认所有方法直接委托，所有重写 ForwardingMap.put 不会改变 ForwardingMap.putAll 的行为。小心谨慎地重写每一个方法当行为必须改变的时候，并且确信你委托的集合满足合同要求。

通常，抽象集合框架(如AbstractList)提供的大多数方法也在转发装饰器中作为标准实现提供。

接口提供特定的视图有时也提供这些视图的 Standard 实现。例如，ForwardingMap 提供 StandardKeySet, StandardValues, 和 StandardEntrySet 类, 每一个都尽可能地将它们的方法委托给修饰过的映射，否则，它们就会留下不能作为抽象委托的方法。

### PeekingIterator

有时候，普通的 Iterator 接口已经不足够。

Iterators 提供了一个方法 Iterators.peekingIterator(Iterator)，包装了 Iterator 并且返回一个PeekingIterator，一个 Iterator 的子类型让你 peek() 在这个元素上 会返回下一个调用 next().

注：通过 Iterators.peekingIterator 返回的 PeekingIterator 并不支持 remove() 在调用 peek() 后。

让我们写一个例子：拷贝一个 List 同时消除连续的重复元素。

```java
List<E> result = Lists.newArrayList();
PeekingIterator<E> iter = Iterators.peekingIterator(source.iterator());
while (iter.hasNext()) {
  E current = iter.next();
  while (iter.hasNext() && iter.peek().equals(current)) {
    // skip this duplicate element
    iter.next();
  }
  result.add(current);
}
```

普通的解决途径是调用跟踪先前的元素，并且在某些条件下回回落，但是这是一项棘手的而且容易出现bug的业务。PeekingIterator 相对容易理解和使用。

### AbstractIterator

实现你自己的 Iterator？ AbstractIterator让你的操作变得容易。

这是一个容易解释的例子。让我们包装一个 Iterator 来跳过null值。

```java
public static Iterator<String> skipNulls(final Iterator<String> in) {
  return new AbstractIterator<String>() {
    protected String computeNext() {
      while (in.hasNext()) {
        String s = in.next();
        if (s != null) {
          return s;
        }
      }
      return endOfData();
    }
  };
}
```

你实现一个方法，computeNext()，他仅仅完成下一个值。当队列完成的时候，仅仅返回endOfData()来让这个迭代结束。

注：
AbstractIterator 继承 UnmodifiableIterator，来禁止使用 remove() 。如果你需要一个 Iterator 支持 remove()，你可以不继承AbstractIterator。

### AbstractSequentialIterator

有一些 iterators 在其他方面表现的更容易。AbstractSequentialIterator提供了另外一种 iterator的展现形式。

```java
Iterator<Integer> powersOfTwo = new AbstractSequentialIterator<Integer>(1) { // note the initial value!
  protected Integer computeNext(Integer previous) {
    return (previous == 1 << 30) ? null : previous * 2;
  }
};
```

这里，我们实现了方法 computeNext(T) ，接受上一个值作为参数。

注意，你还必须传递一个初始值，或者如果 Iterator 要立即结束 返回null。注意computeNext假设null意味着迭代的结束 -- computeNext不能用于实际可能返回null的迭代器。



# 缓存

```java
LoadingCache<Key, Graph> graphs = CacheBuilder.newBuilder()
       .maximumSize(1000)
       .expireAfterWrite(10, TimeUnit.MINUTES)
       .removalListener(MY_LISTENER)
       .build(
           new CacheLoader<Key, Graph>() {
             public Graph load(Key key) throws AnyException {
               return createExpensiveGraph(key);
             }
           });
```

## 适用性

缓存在很大范围内的使用场景中都是很有用的。例如，当一个值的创建或者是检索都非常昂贵的时候你可以决定使用，并且你确定不止一次的需要他的值。

一个 Cache 类似 ConcurrentMap，但是并不是完全一样。最基本的区别是一个 ConcurrentMap 会提交将所有的元素添加到其中，直到显式的删除。一个 Cache 通常通过另一种方式移除和加入自动化，决定于内存环境。在一些场景中如果不是移除和加入自动化，那么一个LoadingCache将变得非常有用，决定自动的缓存等待。

通常，Guava缓存工具集在任何时候都是很适用的：

- 你想通过花费一些内存来提高运行速度
- 你期望一些key在有时候可以多次查询
- 你的缓存不需要存储超过内存（RAM）容量的数据（Guava缓存作为本地缓存运行在你的应用程序中。他们并不是存储在文件中，也不是其他的外部服务，如果他不能满足你的需求，请考虑像 **Memcached** 这样的一个工具）

如果任何一条满足你的使用场景，那么Guava缓存工具将好适合你。

通过 CacheBuilder 获取一个 Cache， **ConcurrentMap 更加的内存高效，但是使用任何旧的ConcurrentMap 复制大多数的 Cache 是一件非常困难和不可能完成的** 。

## Population

关于你的缓存你问你自己的第一个问题是：是否有一些明智的默认方法来加载或者完成一个值与一个键之间的联系？如果有，你应该使用一个CacheLoader。如果没有，或者你需要重写他的默认方法，但是你依然想要一个明智的原子的“获取如果缺席的计算”，你应该通过 Callable 设置一个 get 的回调。元素可以使用 Cache.put 被直接插入，但是自动加载缓存是首选的，因为它更容易推断所有缓存内容之间的一致性。

### 关于一个CacheLoader

一个 LoadingCache 是一个用固定的 CacheLoader 创建的 Cache。创建一个 CacheLoader 是典型的非常容易的实现这个 **V load(K key) throws Exception** 的方法。所以，例如，你可以像这样创建一个LoadingCache：

```java
LoadingCache<Key, Graph> graphs = CacheBuilder.newBuilder()
       .maximumSize(1000)
       .build(
           new CacheLoader<Key, Graph>() {
             public Graph load(Key key) throws AnyException {
               return createExpensiveGraph(key);
             }
           });

...
try {
  return graphs.get(key);
} catch (ExecutionException e) {
  throw new OtherException(e.getCause());
}
```

用 getAll(Iterable<? extends K>) 方法执行大量的查找。默认 getAll 会单独调用CacheLoader，加载缓存中缺少的每一个key。当批量检索比单独查找更有效时，你可以重写CacheLoader.loadAll来发挥他。getAll(Iterable)的性能将得到相应的提高。

注意加载对应key下面的值时没有特别的要求你可以实现 CacheLoader.loadAll。如果从某个组计算任意键的值可以得到该组中所有键的值，那么loadAll可能同时加载该组的其他键。

if computing the value of any key from some group gives you the value for all keys in the group

### 关于一个 Callable

所有的Guava缓存，加载或者不加载，都支持方法 get(K, Callable)。这个方法返回缓存中一个键与一个值的联系，或者再特别的 Callable 中进行计算和将他添加到缓存。没有显著的联系表示缓存被修改知道加载完成。该方法提供了一个简单传统的“如果缓存，返回。否则创建。缓存和返回。”模式。

```java
Cache<Key, Value> cache = CacheBuilder.newBuilder()
    .maximumSize(1000)
    .build(); // look Ma, no CacheLoader
...
try {
  // If the key wasn't in the "easy to compute" group, we need to
  // do things the hard way.
  cache.get(key, new Callable<Value>() {
    @Override
    public Value call() throws AnyException {
      return doThingsTheHardWay(key);
    }
  });
} catch (ExecutionException e) {
  throw new OtherException(e.getCause());
}
```

#### 直接添加

用cache.put(key, value)可以直接在缓存中添加值。这会重写这个特别key的任何缓存中的值。还可以使用Cache.asMap()视图中关于ConcurrentMap的任何方法来修改缓存。注意没有方法在asMap视图可以自动迭代将数据加载到缓存中。未来，该视图上的原子操作超出了自动缓存加载的范围，所以Cache.get(K, Callable) 应该总是优先于 Cache.asMap()。 putIfAbsent在缓存中使用CacheLoader 或者 Callable加载值。

### 回收

我们大多数情况下是没有足够的内存来缓存我们想缓存的任何数据。你必须决定：当他不值得加入到一个缓存中时？Guava提供了回收的三种基本方式：基于-大小 回收，基于-时间 eviction，和 基于-引用 回收。

#### 基于-大小 回收

如果你的缓存不会增长超过一个确定的值，仅仅使用CacheBuilder.maximumSize(long)。这个缓存会尝试回收那些最近的或者不常用到的。警告：缓存回收条目的前提是超出了限制--典型的是缓存的大小超过了限制。

相对的，如果不同的缓存条目有不同的“体积”--例如，如果缓存值具有完全不同的内存占用--也许你可以使用CacheBuilder.weigher(Weigher) 和拥有最大值的 CacheBuilder.maximumWeight(long)指定一个weight方法。增加了类似 maximumSize 请求的警告，请注意，权重是在条目创建的时候计算的，之后就是静态的。

```java
LoadingCache<Key, Graph> graphs = CacheBuilder.newBuilder()
       .maximumWeight(100000)
       .weigher(new Weigher<Key, Graph>() {
          public int weigh(Key k, Graph g) {
            return g.vertices().size();
          }
        })
       .build(
           new CacheLoader<Key, Graph>() {
             public Graph load(Key key) { // no checked exception
               return createExpensiveGraph(key);
             }
           });
```

### 时间回收

CacheBuilder提供了俩种方法来进行时间回收：

- expireAfterAccess(long, TimeUnit) 只有从最后一次读或者是写开始持续指定的时间之后的过期的条目。注意这种的使用情况要比基于大小的回收要小的条目。
- expireAfterWrite(long, TimeUnit)过期条目的时间是从该条目创建开始持续指定的时间，或者最近替换的值。这描述的是如果缓存数据经过一定的时间增长稳定的情况。

定时过期在写期间执行，偶尔在读期间执行，如下所述。

#### 测试时间回收

测试时间回收并不一定是痛苦的....实际上你不需要花俩秒钟时间来测试俩秒中的呼吸。使用接口Ticker并且
用CacheBuilder.ticker(Ticker)方法指定一个时间源码在你的缓存创建器中，这要比你不得不等系统时间要好的多。

### 基于-引用的回收

Guava允许你通过对键或者值使用弱引用或者对值使用软引用来设置缓存，从而允许对条目进行垃圾回收。

- CacheBuilder.weakKeys() 使用弱引用存储键。如果没有强引用或者软引用在键上允许对条目垃圾回收。因为垃圾回收依据的是身份相同，这就产生缓存使用身份相等（==）来比较键，来替代equals().
- CacheBuilder.weakValues()使用弱引用来存储值。如果没有其他（强或软）对值的引用。由于垃圾回收依赖于标识相等，这也就导致整个缓存使用标识相等，而不是equals().
- CacheBuilder.softValues()将值包装成软引用。软引用对象的垃圾回收机制是全局最近最近使用最少的机制，响应内存需求。因为程序暗示使用弱引用，我们通常推介使用使用具有预测性的 **最大缓存大小** 来替代设置。使用 softValues() 会导致值使用标识相等（=）而不是 equals（）。

### 明确的移除

任何时候，你也许期望明确的使缓存条目失效而不是等待条目被回收。可以这样干：

- individually, using **Cache.invalidate(key)**
- in bulk, using **Cache.invalidateAll(keys)**
- to all entries, using **Cache.invalidateAll()**

### 移除监听器

你也许会指定一个移除的监听器当你的缓存中的一个条目被移除时执行某些操作，通过 CacheBuilder.removalListener(RemovalListener)。这removalListener会通过一个 RemovalNotification，指定
RemovalCause，key和value。

注意通过RemovalListener抛出的任何异常会被记录日志（使用 Logger）并且被吞掉。

```java
CacheLoader<Key, DatabaseConnection> loader = new CacheLoader<Key, DatabaseConnection> () {
  public DatabaseConnection load(Key key) throws Exception {
    return openConnection(key);
  }
};
RemovalListener<Key, DatabaseConnection> removalListener = new RemovalListener<Key, DatabaseConnection>() {
  public void onRemoval(RemovalNotification<Key, DatabaseConnection> removal) {
    DatabaseConnection conn = removal.getValue();
    conn.close(); // tear down properly
  }
};

return CacheBuilder.newBuilder()
  .expireAfterWrite(2, TimeUnit.MINUTES)
  .removalListener(removalListener)
  .build(loader);
```

注意：移除监听器的执行操作默认是同步的，而且缓存维护通常是在正常缓存操作期间执行的，所以昂贵的删除监听器会降低正常缓存功能的速度！如果你有一个昂贵的删除监听器，请使用(RemovalListener, Executor)来修饰RemovalListener，以便进行异步操作。

### 当执行Cleanup时发生了什么？

使用CacheBuilder创建的缓存并不支持cleanup和“自动的”回收值，或者等一个值过期后立即执行，或者任何类似的事情。相反，它在写操作期间执行少量维护，或者在偶尔的读操作期间（如果很少写操作）执行少量维护。

产生这种情况的原因如下：如果想让一个缓存连续的进行维护，我们需要创建一个线程，并且它的操作将会与用户操作争夺共享锁。此外，一些环境限制线程的创建，这将使CacheBuilder在该环境中不可用。

相反，我们将选择权赋予你的手中。如果你的缓存是高吞吐，那么你就不用担心就像这样清除过期条目影响缓存维护。如果你的缓存很少写入并且不希望cleanup操作阻塞读操作，你可以创建自己的维护线程定期执行Cache.cleanUp().

如果你想定期的维护你的很少写入的缓存，可以使用 ScheduledExecutorService 进行维护。

### 刷新

刷新和回收并不相同，在 LoadingCache.refresh(K) 中指定，刷新一个key加载一个新的值，可能是异步的。当这个key开始刷新的时候这个旧的value任然会被返回，与强制回收相反，强制检索要等到重新加载新值时才会执行。

当刷新的时候如果抛出一个异常，会保持旧的值，并且异常会被记录并且吞掉。

一个 CacheLoader 通过重写 CacheLoader.reload(K, V) 可以执行明智的行为来进行刷新，也允许你使用旧的值和新的值进行比较。

```java
// Some keys don't need refreshing, and we want refreshes to be done asynchronously.
LoadingCache<Key, Graph> graphs = CacheBuilder.newBuilder()
       .maximumSize(1000)
       .refreshAfterWrite(1, TimeUnit.MINUTES)
       .build(
           new CacheLoader<Key, Graph>() {
             public Graph load(Key key) { // no checked exception
               return getGraphFromDatabase(key);
             }

             public ListenableFuture<Graph> reload(final Key key, Graph prevGraph) {
               if (neverNeedsRefresh(key)) {
                 return Futures.immediateFuture(prevGraph);
               } else {
                 // asynchronous!
                 ListenableFutureTask<Graph> task = ListenableFutureTask.create(new Callable<Graph>() {
                   public Graph call() {
                     return getGraphFromDatabase(key);
                   }
                 });
                 executor.execute(task);
                 return task;
               }
             }
           });
```

使用CacheBuilder.refreshAfterWrite(long, TimeUnit)自动的按照时间进行刷新。与expireAfterWrite进行比较，refreshAfterWrite将使一个键在指定的持续时间之后有资格刷新，但是只有在查询条目时才会实际启动刷新。(如果CacheLoader.reload的实现是异步的，那么查询就不会因为刷新而降低速度)所以，例如，所以在相同的场景下你可以指定refreshAfterWrite 和 expireAfterWrite，所以一个条目的过期时间不会在它变得有能力刷新后查询，这一点是需要解释的。

### 特征

#### 统计

使用 CacheBuilder.recordStats()，你可以打开Guava缓存的统计集合。Cache.states()方法返回一个CacheStats对象，提供如下的统计：

- hiRate() 返回点击率与请求的比率
- averageLoadPenalty() 加载新值所花费的平均时间（以纳秒为例）
- evictionCount() 缓存回收的次数

还有更多的统计基础。这些统计围绕缓存展开，我们建立在性能关键的应用中，密切关注这些数据。

#### asMap

使用 asMap 你可以将任何Cache视图转换为 ConcurrentMap，但是asMap视图如何与缓存交互需要一些解释。

- cache.asMap()包含当时加载到缓存中的所有条目。所以，例如，cache.asMap().keySet()包含所有的当时加载的key
- asMap().get(key)本质上相等于cache.getIfPresent(key)，并且永远不会被加载，这与Map的契约是一致的。
- 所有缓存读和写操作花费的时间（包含Cache.asMap().get(Object) 和 Cache.asMap().put(K, V)），但是并不是通过containsKey(Object)，也不是Cache.asMap()操作集合视图。所以，例如，遍历cache.asMap(). entryset()不会重置检索条目的访问时间。

### 附言

加载方法（就像 get）永远不会抛出 InterruptedException。我们可以定义这些方法支持 InterruptedException，但是我们的支持是不完善的，这样我们所有人的付出只是对其中一部分人有用。详情请继续阅读。

get请求未缓存的值可以分为俩大类：加载值的调用和等待另一个线程正在加载的调用。这俩者在我们支持中断的能力上是不同的。最简单的情况是等待另一个线程正在进行的加载：这里我们可以输入一个可中断的等待。最困难的是我们自己加载的值。在这里，我们任由用户提供CacheLoader处置。如果它恰好支持中断，我们可以支持中断；如果没有，我们就不能。

那么为什么不让CacheLoader支持中断呢？在某种意义上，我们确实这样做啦（但请参阅下面的内容）：如果
CacheLoader抛出InterruptedException，所有对value的get调用都会被立即返回（就像其他异常一样）。另外，get将恢复加载线程中的中断位。令人惊讶的是，InterruptedException被包装到ExecutionException中。

原则上，我们可以为您打开这个异常。然而，这回强制所有使用 LoadingCache 的用户处理 InterruptedException，即使大多数的CacheLoader并不会抛出该异常。当您考虑到所有的非加载线程的等待任然可能被中断，这也许才是值得的。但是很多缓存只是应用于单一的线程中。他们的用户必须捕获可能的InterruptedException。甚至那些跨线程共享缓存的用户有时也只能中断get调用，这取决于哪个线程首先发出请求。

在这个决策中，我们的指导原则是让缓存的行为就像所有值都加载在同一线程中一样。这一原则使得将缓存引入以前每次调用时重新计算其值的代码变得很容易。如果旧代码不可中断，那么新代码也可以不中断。

我说我们“在某种意义上”支持中断。在另一种意义上，我们没有这样做，这使得加载缓存成为一个泄漏的抽象。如果加载线程被中断，我们会像对待其他异常一样对待它。在很多情况下，这是可以的，但是当多个get调用正在等待该值时，这就不是正确的做法了。虽然计算值的操作被中断了，但是需要该值的其他操作可能没有中断。然而，所有这些调用者都接收InterruptedException(包装在ExecutionException中)，这样加载与其说是“fail”，不如说是“abort”。正确的行为是让剩余的线程之一重试加载。我们有一个bug文件。然而，修复可能是有风险的。我们可以在提议的AsyncLoadingCache中投入额外的精力，而不是修复这个问题，它将返回具有正确中断行为的未来对象。



# 函数式工具

## 警告

Java 8 包含 java.util.function 和 java.util.stream 俩个包，在该语言级别的项目中，它取代了Guava的函数式编程类。

虽然Guava函数式编程在Java8之前是适用的，但是确没有Java8的函数式编程笨拙和冗长的匿名类。

过度的使用Guava的函数式编程会导致代码冗长，混乱，不可读，和效率低下。到目前为止，这些都是Guava中会被滥用的部分，当你的代码变成一行的时候，Guava的团队会哭。

对比一下代码：

```
Function<String, Integer> lengthFunction = new Function<String, Integer>() {
  public Integer apply(String string) {
    return string.length();
  }
};
Predicate<String> allCaps = new Predicate<String>() {
  public boolean apply(String string) {
    return CharMatcher.javaUpperCase().matchesAllOf(string);
  }
};
Multiset<Integer> lengths = HashMultiset.create(
  Iterables.transform(Iterables.filter(strings, allCaps), lengthFunction));
```

或者 FluentIterable版本：

```
Multiset<Integer> lengths = HashMultiset.create(
  FluentIterable.from(strings)
    .filter(new Predicate<String>() {
       public boolean apply(String string) {
         return CharMatcher.javaUpperCase().matchesAllOf(string);
       }
     })
    .transform(new Function<String, Integer>() {
       public Integer apply(String string) {
         return string.length();
       }
     }));
```

with:

```
Multiset<Integer> lengths = HashMultiset.create();
for (String string : strings) {
  if (CharMatcher.javaUpperCase().matchesAllOf(string)) {
    lengths.add(string.length());
  }
}
```

尽管使用的静态导入，如果 Function 和 Predicate 操作被移动到不同的文件中，第一种实现将是非常不简洁，非常不可读，非常不高效。

你的默认书写的是命令行代码，你的首要选择加入是 jdk 1.7。你不能使用函数式编程除非你非常明确一下的规定：

- 使用函数式编程可以显著的降低代码的行数。在上面的例子中，函数式编程用了11行，实现的版本 6.将方法或者是常量移动到另一个文件中是没有必要和帮助的。
- 为了提高效率，您需要转换后的集合的延迟计算视图，而不能满足于显式计算的集合。此外，您已经阅读并重新阅读了Effective Java, item 55，除了遵循这些说明之外，您还实际做了基准测试，以证明这个版本更快，并且可以引用数字来证明它。

请确定，当你使用Guava的函数式编程工具，针对传统的迫切需要做的事情但是没有很好的可读性。尝试将他写出来。它哪里是不好的呢？这不是比您将要尝试的那种荒谬而笨拙的函数方法更易于阅读吗?

## Functions 和 Predicates



# 并发

## ListenableFuture

并发是一个艰难的问题，但是可以使用强大而简单的抽象简化它。为了简化本质，Guava继承JDK的Future接口实现ListenableFuture。

我们强烈建议你经常使用ListenableFuture来替代Future在你所有的代码中，因为：

- 很多Futures方法需要他
- 改变为ListenableFuture后会变得简单
- 提供工具方法而不需要ListenableFuture和Future提供他们方法的变体

### 接口

通常Future代表的是异步计算的结果：一个计算可能或者还没有完成过程产生结果。一个Future是一个正在处理的计算的处理器，只是服务弄诺我们提供结果。

一个ListenableFuture允许你注册一个回调，当计算结果完成是执行。或者这个计算已经完成，立即的。这些简单增加的使得他可能高效的完成很多操作，而这些操作基本的Future是不支持的。

ListenableFuture增加的基础操作是 addListener(Runnable, Executor)，相当于指定Future完成计算，指定的Runnable会在指定的Executor上执行。

### 增加 Callbacks

大部分用户希望可以Futures.addCallback(ListenableFuture, FutureCallback, Executor),或者该版本可以默认使用MoreExecutors.directExecutor()，当回调使用起来的时候又快又轻便。一个 FutureCallback 实现俩个方法：

- onSuccess(V), 这个操作基于他们的结果，如果这个future是成功的。
- onFailure(Throwable), 这个操作基于失败，如果这个future是失败的。

### 创建

JDK相关的 ExecutorService.submit(Callable) 是注册一个异步的计算，Guava提供 ListeningExecutorService 接口，返回一个ListenableFuture而不是像 ExecutorService 返回一个普通的 Future。将ExecutorService装换为ListeningExecutorService，只需要使用 MoreExecutors.listeningDecorator(ExecutorService).

```
ListeningExecutorService service = MoreExecutors.listeningDecorator(Executors.newFixedThreadPool(10));
ListenableFuture<Explosion> explosion = service.submit(new Callable<Explosion>() {
  public Explosion call() {
    return pushBigRedButton();
  }
});
Futures.addCallback(explosion, new FutureCallback<Explosion>() {
  // we want this handler to run immediately after we push the big red button!
  public void onSuccess(Explosion explosion) {
    walkAwayFrom(explosion);
  }
  public void onFailure(Throwable thrown) {
    battleArchNemesis(); // escaped the explosion!
  }
});
```

或者，如果你从一个API基于FutureTask,Guava提供了ListenableFutureTask.create(Callable) 和 ListenableFutureTask.create(Runnable, V)。不像JDK，ListenableFutureTask并不意味着直接扩建。

如果你必须将一个 Future 转换成 ListenableFuture提供给另外一个API使用，你不得不选择使用重量级
的 JdkFutureAdapters.listenInPoolThread(Future) 来将一个Future 转换成 ListenableFuture。如果可能，其外修改原来的代码返回一个ListenableFuture。

### 应用

一个使用ListenableFuture最重要的原因是他可以将完成异步的操作链变得可能。

```
ListenableFuture<RowKey> rowKeyFuture = indexService.lookUp(query);
AsyncFunction<RowKey, QueryResult> queryFunction =
  new AsyncFunction<RowKey, QueryResult>() {
    public ListenableFuture<QueryResult> apply(RowKey rowKey) {
      return dataService.read(rowKey);
    }
  };
ListenableFuture<QueryResult> queryFuture =
    Futures.transformAsync(rowKeyFuture, queryFunction, queryExecutor);
```

ListenableFuture很多其他操作可以高效的支持，然而Future独自不能支持。不同的操作由不同的执行器执行，并且一个单独的ListenableFuture可以有多个操作来支持他。

当另一些操作开始之后开始应该开始几个操作--“fan-out”--ListenableFuture正常工作：他会触发所有的请求回调。稍微加工，我们就可以“fan-in”，或者粗发一个ListenableFuture完成操作是从一些其他所有的furture执行完成：请看一个Futures.allAsList的实现的例子。

| 方法                                                         | 解释                                                         | 同看                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------------------- |
| transformAsync(ListenableFuture, AsyncFunction<A, B>, Executor)* | Returns a new ListenableFuture whose result is the product of applying the given AsyncFunction to the result of the given ListenableFuture. | transformAsync(ListenableFuture, AsyncFunction<A, B>) |
| transform(ListenableFuture, Function<A, B>, Executor)        | Returns a new ListenableFuture whose result is the product of applying the given Function to the result of the given ListenableFuture. | transform(ListenableFuture, Function<A, B>)           |
| allAsList(Iterable<ListenableFuture>)                        | Returns a ListenableFuture whose value is a list containing the values of each of the input futures, in order. If any of the input futures fails or is cancelled, this future fails or is cancelled. | allAsList(ListenableFuture...)                        |
| successfulAsList(Iterable<ListenableFuture>)                 | Returns a ListenableFuture whose value is a list containing the values of each of the successful input futures, in order. The values corresponding to failed or cancelled futures are replaced with null. | successfulAsList(ListenableFuture...)                 |

- 一个 AsyncFunction<A, B> 提供一个方法，ListenableFuture **apply(A input)可以异步改变一个值。**

```
List<ListenableFuture<QueryResult>> queries;
// The queries go to all different data centers, but we want to wait until they're all done or failed.

ListenableFuture<List<QueryResult>> successfulQueries = Futures.successfulAsList(queries);

Futures.addCallback(successfulQueries, callbackOnSuccessfulQueries);
```

### 避免套叠的Future

一个场景的代码是调用一个普通的接口，返回一个Future，它可能会造成套叠的Future。例如：

```
executorService.submit(new Callable<ListenableFuture<Foo>() {
  @Override
  public ListenableFuture<Foo> call() {
    return otherExecutorService.submit(otherCallable);
  }
});
```

这个例子会返回一个ListenableFuture<ListenableFuture>。这段代码是不正确的，因为如果取消对外部Future的争夺与外部Future的完成，取消不会传播到内部的Future。使用get()或监听器检查另外一个未来的失败也是一个常见的错误，但除非特别小心，否则将抑制从其他callable中抛出的异常。为了避免这种情况，Guava的所有Future的处理方法（以及JDK中的一些）都具有* Async版本，可以安全的解开此嵌套- transform(ListenableFuture, Function<A, B>, Executor) 和 transformAsync(ListenableFuture, AsyncFunction<A, B>, Executor), 或者 ExecutorService.submit(Callable) 和 submitAsync(AsyncCallable, Executor), etc.

### CheckedFuture

Guava也提供了 CheckedFuture<V, X extends Exception> 接口。一个CheckedFuture是一个ListenableFuture包含抛出检查异常的get方法的版本。这可以使非常容易的创建一个Future来执行熬出异常时的记录日志。将ListenableFuture转换为CheckedFuture，使用Futures.makeChecked(ListenableFuture, Function<Exception, X>)。

## 服务

Guava的服务接口相当于一个对象中方法启动与关闭的操作状态。例如，webservers、RPC 服务、或者定时器都可以实现service接口。就像这样管理需要合适的开启和停止状态的服务,可以很平凡，特别是多线程或者调度器执行。Guava提供一些架构来管理这些状态并且同步将详情显示给你。

### 使用一个服务

一个Service的通常的生命周期是：

- Service.State.NEW to
- Service.State.STARTING to
- Service.State.RUNNING to
- Service.State.STOPPING to
- Service.State.TERMINATED

一个停止的服务可能不会重新启动。如果服务启动、运行或者停止时失败，他就会进入一种Service.State.FAILED的状态。

一个服务可以使用startAsync()异步启动如果这个服务是NEW。所以你需要设计你的应用程序在每一个服务启动时都有唯一的空间。

停止一个服务也是类似的，使用异步方法stopAsync()。但是并不像startAsync()，多线程环境下调用这个方法是安全的，这样就可能在服务杀死的时候进行一些处理。

Service也提供了一些方法来等待服务完成时的过渡：

- 异步使用 addListener()。addListener()允许你添加一个Service.Listener在服务的过渡期内调用每一个状态。当一个listener添加的时候，如果一个服务不是NEW，那么任何已经发生的过渡期状态将不会在依赖于监听器。
- 同步使用 awaitRunning()。他不会被打断，不会抛出检查异常，并且这个服务已经完成启动，会一次性返回。如果这个服务启动是失败，这回抛出异常IllegalStateException。相似的，awaitTerminated()等待服务达到一种状态（TERMINATED 或者 FAILED） 。这俩种方法都允许重载，可以设置超时时间。

这个Service接口巧妙和复杂。我们并不推介直接实现它。相对而言我们希望使用在guava中使用基础抽象类来实现。每一个基本的类都支持一个指定的线程模型。

### 实现

#### AbstractIdleService

AbstractIdleService的架构实现了一个Service，当处于“running”的状态时，其实是不需要其他操作的。因此当running的时候也不需要一个线程。但是启动或者停止的时候是需要的。实现一个Service可以简单的实现AbstractIdleService，继承他的 startUp() 和 shutDown()方法。

```
protected void startUp() {
  servlets.add(new GcStatsServlet());
}
protected void shutDown() {}
```

注意任何GcStatsServlet查询，已经有一个线程在运行。我们不需要这个服务自己有任何操作，在服务running的时候。

#### AbstractExecutionThreadService

一个AbstractExecutionThreadService将startup、running和shutdown在一个单一的线程。你必须重写他的run()方法，并且他必须回复停止请求。例如，你可能在一个子线程中执行动作。

```
public void run() {
  while (isRunning()) {
    // perform a unit of work
  }
}
```

相对的，当放生run()你可能通过任何渠道实现来返回。

重写startUp() 和 shutDown() 是典型的，但是这个服务的状态需要你自己来管理。

```
protected void startUp() {
  dispatcher.listenForConnections(port, queue);
}
protected void run() {
  Connection connection;
  while ((connection = queue.take() != POISON)) {
    process(connection);
  }
}
protected void triggerShutdown() {
  dispatcher.stopListeningForConnections(queue);
  queue.put(POISON);
}
```

注意 start() 调用你的 startUp()方法，为你创建一个线程。并且调用 run() 在这个线程中。stop()调用triggerShutdown()并且等待线程终结。

#### AbstractScheduledService

一个AbstractScheduledService在运行期间执行一些周期性的任务。子类实现runOneIteration()来指定一个任务的迭代器，就像是普通的startUp 和 shutDown() 方法。

为了描述执行的调度，你必须实现 scheduler() 方法。典型的，你可以使用 AbstractScheduledService.Scheduler提供的调度器中的一种，或者newFixedRateSchedule(initialDelay, delay, TimeUnit) or newFixedDelaySchedule(initialDelay, delay, TimeUnit)。ScheduledExecutorService中的相关的常用方法。习惯的调度器可以实现CustomScheduler；具体查看javadoc详情

#### AbstractService

当你需要自己手动操作的线程管理时，直接重写AbstractService。相对的，你可以实现以上提供的任何一种实现，但是推介实现AbstractService。例如，你可以抽象一些东西，例如就像一个Service提供自己的线程语义，你有你自己指定的线程请求。

为了实现 AbstractService 你必须实现俩个方法：

- doStart(): doStart()直接调用是通过第一个startAsync()调用，你的doStart()方法必须执行所有的初始化并且如果启动成功然后调用 notifyStarted()或者启动失败调用notifyFailed()。
- doStop(): doStop() 是通过第一个调用stopAsync()直接调用，你的doStop()方法需要杀掉你的服务并且如果停止成功执行 notifyStopped()回调，停止失败执行notifyFailed()。

你的 doStart 和 doStop，这俩个方法必须要快。如果你需要做一些期望的初始化，例如读取一些文件、建立网络链接、或者任何可能造成阻塞的方法，你需要考虑将它们移动到另一个线程执行。

#### 使用ServiceManager

为了增加Service架构的实现，Guava提供一个ServiceManager类，让调用多个Service实现的确定操作变得容易。创建一个新的ServiceManager管理一个Service的集合。然后你可以管理它们：

- startAsync()

  will start all the services under management. Much like Service#startAsync() you can only call this method once, if all services are NEW.

- stopAsync()

  will stop all the services under management.

- addListener

  will add a ServiceManager.Listener that will be called on major state transitions.

- awaitHealthy()

  will wait for all services to reach the RUNNING state.

- awaitStopped()

  will wait for all services to reach a terminal state.

或者检查它们：

- isHealthy()

  returns true if all services are RUNNING.

- servicesByState()

  returns a consistent snapshot of all the services indexed by their state.

- startupTimes()

  returns a map from Service under management to how long it took for that service to start in milliseconds. The returned map is guaranteed to be ordered by startup time.

我们推介通过ServiceManager来管理Service的声明周期，通过其他机制启动的状态转换 **不会影响其方法的正确性** 。例如，一些Service通过startAsync()启动，那么这个监听器将会被调用，即使awaitHealthy()依然在执行。ServiceManager强制实施的唯一要求是当ServiceManager构造的时候所有的Service必须是 **NEW** 。



# String工具

## Joiner

将一个序列的字符串通过分隔符拼接到一起是没有理由困难的--但是其实不是。如果你的序列包含null，那么他依然很难。Joiner流利的类型使这件事变得简单。

```java
Joiner joiner = Joiner.on("; ").skipNulls();
return joiner.join("Harry", null, "Ron", "Hermione");
```

返回字符串 "Harry; Ron; Hermione"。相对的，将skipNulls替换，你可以使用 useForNull(String) 指定一个字符串将null替换。

你也许需要使用 toString() 将它们转换并且合并，在项目中也可以使用Joiner。

```java
Joiner.on(",").join(Arrays.asList(1, 5, 7)); // returns "1,5,7"
```

注意：joiner实例一般是不可变的。你必须命名一个明确的语义，因为这joiner构造方法会返回一个新的Joiner。这也就让任何Joiner线程安全，并且可以作为一个static final常量使用。

## Splitter

Java工具提供了一些关于切割字符串的额快速行为的构建方法。例如，String.split 默默地丢弃尾随分隔符，而StringTokenizer正好尊重五个空格字符，而不是其他任何东西。

小测试：`",a,,b,".split(",")`的返回值是什么？

1. "", "a", "", "b", ""
2. null, "a", null, "b", null
3. "a", null, "b"
4. "a", "b"
5. None of the above

正确的答案是None of the above：`"", "a", "", "b"`。仅仅跳过末尾的空格字符，这是什么我不能理解。

Splitter使用一个令人接受的简单的流利的模型完成控制所有的使人迷惑的行为。

```java
Splitter.on(',')
    .trimResults()
    .omitEmptyStrings()
    .split("foo,bar,,   qux");
```

返回一个Iterable 包含 "foo", "bar", "qux". 一个 Splitter 可以设置分割任何的模型, char, String, 或者 CharMatcher.

### 基础工厂

| 方法                                            | 描述                                                         | 举例                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Splitter.on(char)                               | 根据指定的单独字符进行分割                                   | Splitter.on(';')                                             |
| Splitter.on(CharMatcher)                        | 根据指定的字符集进行分割                                     | Splitter.on(CharMatcher.BREAKING_WHITESPACE) Splitter.on(CharMatcher.anyOf(";,.")) |
| Splitter.on(String)                             | 根据明确的字符串进行分割                                     | Splitter.on(", ")                                            |
| Splitter.on(Pattern) Splitter.onPattern(String) | 根据正则表达式进行分割                                       | Splitter.onPattern("\r?\n")                                  |
| Splitter.fixedLength(int)                       | 根据指定的长度将字符串分割为多个子字符串，最后一个子字符串可能长度会更少，但是不会为空 | Splitter.fixedLength(3)                                      |

### 修改

| 方法                     | 描述                                                         | 举例                                                         |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| omitEmptyStrings()       | 从结果中自动的删除空的字符串                                 | Splitter.on(',').omitEmptyStrings().split("a,,c,d") returns "a", "c", "d" |
| trimResults()            | 从结果中自动的跳过空格（trimResults(CharMatcher.WHITESPACE).） | Splitter.on(',').trimResults().split("a,*b*, c, d") returns "a", "b", "c", "d" |
| trimResults(CharMatcher) | 修剪指定的字符                                               | Splitter.on(',').trimResults(CharMatcher.is('_')).split("*a ,\*b* ,c* *") returns "a ", "b* ", "c". |
| limit(int)               | 停止分割并返回指定的字符串数量                               | Splitter.on(',').limit(3).split("a,b,c,d") returns "a", "b", "c,d" |

如果你想要获取一个List，使用splitToList()代替split().

注意：splitter实例一般是不可改变的。splitter的构造方法经常返回一个新的`Splitter`，您必须使用它来获取相应的语义。这也就让Splitter是线程安全的，你可以使用`static final`定义它为一个常量。

### Map Splitter

你也可以使用withKeyValueSeparator()分割一个map。结果`MapSplitter`使用拆分器的分隔符将输入拆分为条目，然后使用给定的键值分隔符将这些条目拆分为键和值，并返回Map <String，String>。

## CharMatcher

随着时间的增长，我们的 StringUtil 类成长为可信类，并且很多方法就像这样：

- allAscii
- collapse
- collapseControlChars
- collapseWhitespace
- lastIndexNotOf
- numSharedChars
- removeChars
- removeCrLf
- retainAllChars
- strip
- stripAndCollapse
- stripNonDigits

它们代表了两个概念的部分交叉积：

- 包含了一个怎样的“匹配”特征？
- 这些“匹配”特征干了什么？

为了简化这些细节，我们开发了CharMatcher。

直观地，您可以将CharMatcher视为表示特定类别的字符，如数字或空格。 实际上，CharMatcher只是字符的布尔谓词 - 事实上，CharMatcher实现了[Predicate ] - 但是因为引用“所有空白字符”或“全部小写字母”是如此常见，Guava提供了这个专用语法和字符API。

但CharMatcher的实用程序在于它允许您在指定类别的字符出现时执行的操作：修剪，折叠，删除，保留等等。 CharMatcher类型的对象代表概念1：什么构成匹配字符？ 然后它提供了许多操作来回答概念2：如何处理那些匹配的字符？ 结果是API复杂度线性增加，以便平方地增加灵活性和功率。 好极了！

```java
String noControl = CharMatcher.javaIsoControl().removeFrom(string); // remove control characters
String theDigits = CharMatcher.digit().retainFrom(string); // only the digits
String spaced = CharMatcher.whitespace().trimAndCollapseFrom(string, ' ');
  // trim whitespace at ends, and replace/collapse whitespace into single spaces
String noDigits = CharMatcher.javaDigit().replaceFrom(string, "*"); // star out all digits
String lowerAndDigit = CharMatcher.javaDigit().or(CharMatcher.javaLowerCase()).retainFrom(string);
  // eliminate all characters that aren't digits or lowercase
```

注：CharMatcher处理char值；它并不能理解额外的Unicode代码指向0x10000到0x10FFFF范围内。这些逻辑字符使用代理项对编码为字符串，CharMatcher将这些字符视为两个单独的字符。

## 获得CharMatchers

通过提供CharMatcher工厂方法来满足很多需求：

- any()
- none()
- whitespace()
- breakingWhitespace()
- invisible()
- digit()
- javaLetter()
- javaDigit()
- javaLetterOrDigit()
- javaIsoControl()
- javaLowerCase()
- javaUpperCase()
- ascii()
- singleWidth()

其他类似的获取CharMatcher的相同途径包括：

| 方法                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| anyOf(CharSequence) | 指定你希望匹配的所有字符。例如：CharMatcher.anyOf("aeiou")匹配小写的英文元音 |
| is(char)            | 指定确切的一个字符去匹配                                     |
| inRange(char, char) | 指定一个范围的字符去匹配，例如：CharMatcher.inRange('a', 'z') |

另外，CharMatcher 提供 negate(), and(CharMatcher), 和 or(CharMatcher). 这些提供了CharMatcher的简单的布尔操作。

## 使用CharMatchers

CharMatchers提供了很广泛的方法用来处理指定的字符在任何CharSequence中存在的数据的操作。他提供了除了我以下列出的非常多的方法，但是一些常用的方法在下面列出：

| 方法                                    | 描述                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| collapseFrom(CharSequence, char)        | 用指定的字符替换每组连续匹配的字符。例如：WHITESPACE.collapseFrom(string, ' ')将空格折叠成单个空格 |
| matchesAllOf(CharSequence)              | 测试matcher是否匹配序列里的所有字符。例如：ASCII.matchesAllOf(string)测试string的字符集是否是ASCII |
| removeFrom(CharSequence)                | 将序列中匹配到的字符移除                                     |
| retainFrom(CharSequence)                | 将序列中没有匹配到的字符移除                                 |
| trimFrom(CharSequence)                  | 删除前导和尾随匹配字符                                       |
| replaceFrom(CharSequence, CharSequence) | 使用给定的序列替换匹配到的字符                               |

注意：除了matchesAllOf返回一个boolean，所有的方法都返回String。

## Charsets

不要这样做：

```java
try {
  bytes = string.getBytes("UTF-8");
} catch (UnsupportedEncodingException e) {
  // how can this possibly happen?
  throw new AssertionError(e);
}
```

要这样做：

```
bytes = string.getBytes(Charsets.UTF_8);
```

Charsets提供对所有Java平台实现都支持的六种标准Charset实现的持续引用。 使用它们而不是按名称引用字符集。

TODO: charsets什么时候使用的一个解释
注意：如果你使用的是JDK1.7，你可以使用StandardCharsets中的常量。

## CaseFormat

CaseFormat是一个方便的小类，用于在ASCII大小写约定之间进行转换 - 例如，编程语言的命名约定。 支持的格式包括：

| Format           | 例子             |
| ---------------- | ---------------- |
| LOWER_CAMEL      | lowerCamel       |
| LOWER_HYPHEN     | lower-hyphen     |
| LOWER_UNDERSCORE | lower_underscore |
| UPPER_CAMEL      | UpperCamel       |
| UPPER_UNDERSCORE | UPPER_UNDERSCORE |

使用它相当的简单：

```java
CaseFormat.UPPER_UNDERSCORE.to(CaseFormat.LOWER_CAMEL, "CONSTANT_NAME"); // returns "constantName"
```

我们发现这是非常有用的，例如：在编写生成其他程序的程序时。

## Strings

关于操作String的普通目的的工具的一个小巧的类。



# 原始类型

## 重写

Java的基础原始类型：

- byte
- short
- int
- long
- float
- double
- char
- boolean

在Guava中查找一个方法的时候，你不妨先在Arrays中检查它是否存在，或者已经在JDK中提供了对应的包装类型。例如：Integer

这些普通类型不能当做一个对象或者类型参数，也就是说很多常用的工具并不依赖它。Guava提供一组普通的工具，在原始类型与集合API之间提供转换。从类型到字节数组表示的转换，以及对某些类型的无符号行为的支持。

| 原始类型 | Guava工具集                         |
| -------- | ----------------------------------- |
| byte     | Bytes, SignedBytes, UnsignedBytes   |
| short    | Shorts                              |
| int      | Ints, UnsignedInteger, UnsignedInts |
| long     | Longs, UnsignedLong, UnsignedLongs  |
| float    | Floats                              |
| double   | Doubles                             |
| char     | Chars                               |
| boolean  | Booleans                            |

对于有符号和无符号字节的行为不同的方法在字节中完全跳过，但仅出现在SignedBytes和UnsignedBytes实用程序中，因为字节的有符号性比其他类型的有符号性更不明确。

UnsignedInts和UnsignedLongs类中提供了int和long方法的无符号变体，但由于这些类型的大多数用法都是有符号的，因此Ints和Longs类将其输入视为有符号。

此外，Guava为unsigned int和long值提供“包装类型”，UnsignedInteger和UnsignedLong，以帮助您使用类型系统强制区分有符号值和无符号值，以换取较小的性能成本。 这些类直接支持BigInteger风格的简单算术运算。

所有方法签名都使用Wrapper来引用相应的JDK包装器类型，而使用prim来引用原始类型。 （Prims，如果适用，指相应的Guava实用程序类。）

## 原始数组工具

用原始数组包装的原始类型总体上是最有效率的方式（无论在内存上还是性能）。Guava提供了非常宽泛的方法来与这些方法共同工作。

| 签名                                           | 描述                                                         | 集合模拟                             |
| ---------------------------------------------- | ------------------------------------------------------------ | ------------------------------------ |
| List asList(prim... backingArray)              | 作为一个List包装一个原始数组来接受包装类型                   | Arrays.asList                        |
| prim[] toArray(Collection collection)          | 将一个集合拷贝到一个新的prim中，这个方法是线程安全的，就像collection.toArray() | Collection.toArray()                 |
| prim[] concat(prim[]... arrays)                | 将多个原始数组联系起来                                       | Iterables.concat                     |
| boolean contains(prim[] array, prim target)    | 查明指定的元素是否在指定的数组中                             | Collection.contains                  |
| int lastIndexOf(prim[] array, prim target)     | 查找值value在array中出现的最后位置，如果不存在，则返回-1     | List.lastIndexOf                     |
| prim min(prim... array)                        | 返回数组的最小值                                             | Collections.min                      |
| prim max(prim... array)                        | 返回数组的最大值                                             | Collections.max                      |
| String join(String separator, prim... array)   | 将array中的元素以separator分割组装成一个字符串               | Joiner.on(separator).join            |
| Comparator<prim[]> lexicographicalComparator() | 一种比较器，用于按字典顺序进行比较                           | Ordering.natural().lexicographical() |

List asList(prim... backingArray)
支持在场的成员：Bytes, Shorts, Ints, Longs, Floats, Doubles, Chars, Booleans. Not UnsignedInts, UnsignedLongs, SignedBytes, or UnsignedBytes

prim min(prim... array)
支持在场的成员：SignedBytes, UnsignedBytes, Shorts, Ints, Longs, Floats, Doubles, Chars, Booleans, UnsignedInts, UnsignedLongs. Not Bytes.

## 普通的工具方法

Guava提供了一系列JDK6中没有的方法。其中一些方法在JDK7中实现。

| 接口方法                       | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| int compare(prim a, prim b)    | 一个普通的Comparator.compare方法，但是是基于原始类型的。在JDK7中的包装类中提供 |
| prim checkedCast(long value)   | 将制定的value **投射** 到prim中，除非制定的值不适合prim，会抛出异常IllegalArgumentException |
| prim saturatedCast(long value) | 将指定值 **转换** 为prim，除非指定的值不适合prim，在这种情况下使用最接近的prim值。 |

## 包装

提供无符号包装类型包括一些方法来让我们使用更加容易：

| 接口                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| UnsignedPrim plus(UnsignedPrim), minus, times, dividedBy, mod） | 简单算术操作                                                 |
| UnsignedPrim valueOf(BigInteger)                             | BigInteger装换为UnsignedPrim                                 |
| UnsignedPrim valueOf(long)                                   | long装换为UnsignedPrim                                       |
| UnsignedPrim fromPrimBits(prim value)                        | 将给定的值创建无符号视图，例如：UnsignedInteger.fromIntBits(1 << 31)有值2的31次方，尽管1 << 31作为一个int是不可能的 |
| BigInteger bigIntegerValue()                                 | 获取这个UnsignedPrim的值，作为一个BigInteger                 |
| toString(), toString(int radix)                              | 返回关于无符号值的一个字符串代表                             |



# Ranges

## 例子

```java
List<Double> scores;
Iterable<Double> belowMedianScores = Iterables.filter(scores, Range.lessThan(median));
...
Range<Integer> validGrades = Range.closed(1, 12);
for(int grade : ContiguousSet.create(validGrades, DiscreteDomain.integers())) {
  ...
}
```

## 介绍

一个Range，有时候我们就是将他理解为是一种间隔，它是convex（非正式，"contiguous" 或者 "unbroken"）指的是特定领域的一部分。当然突出意思是a <= b <= c，range.contains(a) && range.contains(c) 其实暗示 range.contains(b)。

Ranges可以“扩展到无穷大” - 例如，范围x> 3包含任意大的值 - 或者可以是有限约束的，例如2 <= x <5。我们将使用更紧凑的符号，程序员熟悉数学背景：

- (a..b) = {x | a < x < b}
- [a..b] = {x | a <= x <= b}
- [a..b) = {x | a <= x < b}
- (a..b] = {x | a < x <= b}
- (a..+∞) = {x | x > a}
- [a..+∞) = {x | x >= a}
- (-∞..b) = {x | x < b}
- (-∞..b] = {x | x <= b}
- (-∞..+∞) = all values

值a和b在上面被称为节点。为了改善连贯性，Guava提供了Range的概念，上面的结尾节点不能小于下面的结尾节点，这结尾节点可能会相等，当最后一个范围内的元素关闭的时候。

- [a..a]: singleton range
- [a..a); (a..a]: empty, but valid
- (a..a): invalid

Guava中范围有自己的类型Range。所有的范围都是不可变的。

## 创建Ranges

Ranges可以通过Range的静态方法获得：

| 范围类型 | 方法             |
| -------- | ---------------- |
| (a..b)   | open(C, C)       |
| [a..b]   | closed(C, C)     |
| [a..b)   | closedOpen(C, C) |
| (a..b]   | openClosed(C, C) |
| (a..+∞)  | greaterThan(C)   |
| [a..+∞)  | atLeast(C)       |
| (-∞..b)  | lessThan(C)      |
| (-∞..b]  | atMost(C)        |
| (-∞..+∞) | all()            |

```java
Range.closed("left", "right"); // all strings lexographically between "left" and "right" inclusive
Range.lessThan(4.0); // double values strictly less than 4
```

此外，Range实例通过范围类型来创建，解析如下：

| 范围类型                          | 方法                              |
| --------------------------------- | --------------------------------- |
| 俩边的边界                        | range(C, BoundType, C, BoundType) |
| 不在 ((a..+∞) 或者 [a..+∞))的边界 | downTo(C, BoundType)              |
| 不在((-∞..b) 或者 (-∞..b])的边界  | upTo(C, BoundType)                |

这里，BoundType是一个枚举，包含值 CLOSED 和 OPEN。

```java
Range.downTo(4, boundType); // allows you to decide whether or not you want to include 4
Range.range(1, CLOSED, 4, OPEN); // another way of writing Range.closedOpen(1, 4)
```

## 操作

关于Range的一个基本的操作就是contains(C)方法，确定如你想象的表现一样。此外，一个Range可能被作为断言使用，或者在函数式编程中使用。任何Range也支持containsAll(Iterable<? extends C>)。

```java
Range.closed(1, 3).contains(2); // returns true
Range.closed(1, 3).contains(4); // returns false
Range.lessThan(5).contains(5); // returns false
Range.closed(1, 4).containsAll(Ints.asList(1, 2, 3)); // returns true
```

## 查询操作

为了查看一个范围的结尾节点，Range提供了相应的方法：

- hasLowerBound() 和 hasUpperBound()，检查这个范围是不是具有指定的结尾节点，或者返货infinity。
- lowerBoundType() 和 upperBoundType()，返回结尾节点的BoundType，不是CLOSE就是OPEN。如果这个范围没有指定的结尾节点，这个方法会抛出异常IllegalStateException
- lowerEndpoint() 和 upperEndpoint()，在指定的结尾处返回结尾节点，或者如果没有指定的结尾节点就会抛出异常IllegalStateException
- isEmpty() 测试如果这个范围是空的，也就是他们类似这样[a,a) 或者 (a,a]。

```java
Range.closedOpen(4, 4).isEmpty(); // returns true
Range.openClosed(4, 4).isEmpty(); // returns true
Range.closed(4, 4).isEmpty(); // returns false
Range.open(4, 4).isEmpty(); // Range.open throws IllegalArgumentException

Range.closed(3, 10).lowerEndpoint(); // returns 3
Range.open(3, 10).lowerEndpoint(); // returns 3
Range.closed(3, 10).lowerBoundType(); // returns CLOSED
Range.open(3, 10).upperBoundType(); // returns OPEN
```

## 间隔操作

### encloses

范围之间最基础的联系是 encloses(Range)，如果是true，那么里面的范围的边界没有超出外面的范围的边界。这依赖于结尾节点之间的比较。

- [3..6] encloses [4..5]
- (3..6) encloses (3..6)
- [3..6] encloses [4..4) (even though the latter is empty)
- (3..6] does not enclose [3..6]
- [4..5] does not enclose (3..6) 尽管他包含后面范围的每一个值，但是他们是用不相关的地址。
- [3..6] does not enclose (1..1] 尽管他包含后面范围的每一个值

### isConnected

Range.isConnected(Range)测试这些范围是否是连续的。特别是，isConnected不同于数学上的定义，这些范围必须是连续的（特别指出，这里是不包含空范围的）

isConnected是反身的，对称的关系。

```java
Range.closed(3, 5).isConnected(Range.open(5, 10)); // returns true
Range.closed(0, 9).isConnected(Range.closed(3, 4)); // returns true
Range.closed(0, 5).isConnected(Range.closed(3, 9)); // returns true
Range.open(3, 5).isConnected(Range.open(5, 10)); // returns false
Range.closed(1, 5).isConnected(Range.closed(6, 10)); // returns false
```

### intersection（交叉）

Range.intersection(Range) 返回包含这个范围和其他（存在的连续的范围）中都存在的的最大范围。如果没有这样的范围存在，那么会抛出异常IllegalArgumentException

intersection是一个交换，关联[操作] [二元操作]。

```java
Range.closed(3, 5).intersection(Range.open(5, 10)); // returns (5, 5]
Range.closed(0, 9).intersection(Range.closed(3, 4)); // returns [3, 4]
Range.closed(0, 5).intersection(Range.closed(3, 9)); // returns [3, 5]
Range.open(3, 5).intersection(Range.open(5, 10)); // throws IAE
Range.closed(1, 5).intersection(Range.closed(6, 10)); // throws IAE
```

### span

Range.span(Range) 返回这个范围和其他范围的最小范围。如果这些范围是连续的，那么返回的就是他们的合集

```
Range.closed(3, 5).span(Range.open(5, 10)); // returns [3, 10)
Range.closed(0, 9).span(Range.closed(3, 4)); // returns [0, 9]
Range.closed(0, 5).span(Range.closed(3, 9)); // returns [0, 9]
Range.open(3, 5).span(Range.open(5, 10)); // returns (3, 10)
Range.closed(1, 5).span(Range.closed(6, 10)); // returns [1, 10]
```

## 分离的领域（Domains）

一些类型，并不是所有的可比较类型，他们是分离的，也就是说这些范围的所有的边界都是可以列举出来的。

在Guava，一个DiscreteDomain使得C实现了一个分离的操作。一个分离的区域一般表示根据他们的类型遍历所有的值；他不能代表部分区域，例如“prime integers”，或者“半夜的时间”

DiscreteDomain类提供了DiscreteDomain实例：

| 类型    | DiscreteDomain |
| ------- | -------------- |
| Integer | integers()     |
| Long    | longs()        |

如果你有一个DiscreteDomain，你可以使用以下的Range操作：

- ContiguousSet.create(range, domain): view a Range as an ImmutableSortedSet, with a few extra operations thrown in. (Does not work for unbounded ranges, unless the type itself is bounded.)
- canonical(domain): put ranges in a "canonical form." If ContiguousSet.create(a, domain).equals(ContiguousSet.create(b, domain)) and !a.isEmpty(), then a.canonical(domain).equals(b.canonical(domain)). (This does not, however, imply a.equals(b).)

```java
ImmutableSortedSet<Integer> set = ContiguousSet.create(Range.open(1, 5), DiscreteDomain.integers());
// set contains [2, 3, 4]

ContiguousSet.create(Range.greaterThan(0), DiscreteDomain.integers());
// set contains [1, 2, ..., Integer.MAX_VALUE]
```

注意：ContiguousSet.create不会确切的遍历范围，而是返回一个range的set视图

## 你自己的DiscreteDomain

你可以创建自己的DiscreteDomain对象，但是这里有几个非常重要的方面需要你记住：

- A discrete domain always represents the entire set of values of its type; it cannot represent partial domains such as "prime integers" or "strings of length 5." So you cannot, for example, construct a DiscreteDomain to view a set of days in a range, with a JODA DateTime that includes times up to the second: because this would not contain all elements of the type.
- A DiscreteDomain may be infinite -- a BigInteger DiscreteDomain, for example. In this case, you should use the default implementation of minValue() and maxValue(), which throw a NoSuchElementException. This forbids you from using the ContiguousSet.create method on an infinite range, however!

## 什么时候你需要一个Comparator？

我们希望在功率和API复杂性之间达到一个非常具体的平衡，其中一部分涉及不提供基于比较器的接口：我们不需要担心基于不同比较器的范围如何相互作用; API签名都大大简化了; 事情变得更好。

另一方面，如果你想用一个随意的Comparator，你可以做以下其中的一条：

- Use a general Predicate and not Range. (Since Range implements the Predicate interface, you can use Predicates.compose(range, function) to get a Predicate.)
- Use a wrapper class around your objects that defines the desired ordering.



# I\O

## I\O工具

### ByteStreams 和 CharStreams

Guava使用任期内的“流”指向一个I/O数据的有下一个资源的位置状态的Closeable流。“byte stream”指向的是 InputStream 或者 OutputStream，“char stream”指向的是 Reader or Writer （尽管他们父类型 Readable 和 Appendable 经常被作为方法参数类型使用）。对应的工具类分别写入到ByteStreams 和 CharStream类中。

很多的Guava相关流工具同一时间处理一整个流并且/或者高效的缓存处理他们。也不是Guava的方法处理一个流时并不关闭这个流：关闭流是代码的基本责任就像是开启一个流一样。

他们这俩个类中提供的一些方法包括：

| ByteStreams                          | CharStreams                     |
| ------------------------------------ | ------------------------------- |
| byte[] toByteArray(InputStream)      | String toString(Readable)       |
| N/A                                  | List readLines(Readable)        |
| long copy(InputStream, OutputStream) | long copy(Readable, Appendable) |
| void readFully(InputStream, byte[])  | N/A                             |
| void skipFully(InputStream, long)    | void skipFully(Reader, long)    |
| OutputStream nullOutputStream()      | Writer nullWriter()             |

### 汇集来源

他和创建I/O工具方法是相同的，当执行基础操作的时候，帮助你在整个执行流程避免处理流。例如：Guava有Files.toByteArray(File) 和 Files.write(File, byte[])。然而，你结束了处理每一个不同数据来源或者汇集写入的数据时相似的方法分散到各处的情况。例如：Guava有Resources.toByteArray(URL)，就像是Files.toByteArray(File)做同样的事情，但是使用一个URL作为一个数据来源要比一个file更好。

为了解决这个问题，Guava有不同类型数据源和汇集的抽象的集合。一个资源或者汇集为一个资源，你知道如何开启一个新的流，例如一个File或者URL。资源是可读的，然后汇集是可写入的。此外，根据您处理的是 byte或者char来决定是分解汇集还是接受源。

| Operations | Bytes      | Chars      |
| ---------- | ---------- | ---------- |
| Reading    | ByteSource | CharSource |
| Writing    | ByteSink   | CharSink   |

这些API的有利条件是提供了一组相同的操作。也许你将你的数据包装成一个 ByteSource，例如，你可以得到相同的方法而不用管源码发生了什么。

### 创建 sources 和 sinks

Guava提供了一些 sources 和 sinks的实现：

```java
//分别对应Bytes和Chars的操作方法

Files.asByteSource(File)	
Files.asCharSource(File, Charset)

Files.asByteSink(File, FileWriteMode...)	
Files.asCharSink(File, Charset, FileWriteMode...)

MoreFiles.asByteSource(Path, OpenOption...)	
MoreFiles.asCharSource(Path, Charset, OpenOption...)

MoreFiles.asByteSink(Path, OpenOption...)	
MoreFiles.asCharSink(Path, Charset, OpenOption...)

Resources.asByteSource(URL)	
Resources.asCharSource(URL, Charset)

ByteSource.wrap(byte[])	
CharSource.wrap(CharSequence)

ByteSource.concat(ByteSource...)	
CharSource.concat(CharSource...)

ByteSource.slice(long, long)	
N/A

CharSource.asByteSource(Charset)	
ByteSource.asCharSource(Charset)

N/A	
ByteSink.asCharSink(Charset)
```

相对的，你可继承对应的 source 和 sink 类来创建新的实现。

注意：
如果决定创建一个source或者sink来包装一个开启的流（例如是一个InputStream），这是可以避免的。因为你的source或者sink每次创建都会开启一个新的流依靠调用他的openStream()方法。这样就允许source或者sink控制流的整个生命周期，这样也有能力同时处理多次而不是变得没有能力只能处理任何时候的第一次调用。此外，没有将source或者sink放置在第一的位置上，那么很多的操作就可能失败。如果你在创建source或者sink前开启了一个流，如果你的代码中的任何地方抛出异常那么你可能不得不处理，及时你确定正确的将流关闭。

### 使用source和sink

如果你有source或者sink的一个实例，你需要通过一些操作来进行读或者是写。

#### 相同的操作

所有的source或者sink都有能力提供一个新的流来进行读或者是写。默认，其他操作都默认实现调用获取一个新的流之后，做一些事情，然后确认这个流关闭。

这些方法是这么命名的：

- openStream() - 依靠source 或者 sink 返回一个 InputStream, OutputStream, Reader 或者 Write。
- openBufferedStream() - 依靠source 或者 sink 返回一个 InputStream, OutputStream, BufferedReader or Writer 。如果需要返回的流被收集缓存。例如，一个源可以从一个字节数组中读取那么就没有必要在内存中增加缓存。这也就是为什么没有返回BufferedInputStream。etc 特别注意BufferedReader这一项，因为他定义了readLine()方法。

source 操作

```java
//ByteSource	CharSource

byte[] read()	
String read()

N/A	
ImmutableList<String> readLines()

N/A	
String readFirstLine()

long copyTo(ByteSink)	
long copyTo(CharSink)

long copyTo(OutputStream)	
long copyTo(Appendable)

Optional<Long> sizeIfKnown()	
Optional<Long> lengthIfKnown()

long size()	
long length()

boolean isEmpty()	
boolean isEmpty()

boolean contentEquals(ByteSource)	
N/A

HashCode hash(HashFunction)	
N/A
```

Sink操作

```java
//ByteSink	CharSink

void write(byte[])	
void write(CharSequence)

long writeFrom(InputStream)	
long writeFrom(Readable)

N/A	
void writeLines(Iterable<? extends CharSequence>)

N/A	
void writeLines(Iterable<? extends CharSequence>, String)
```

例子：

```java
// Read the lines of a UTF-8 text file
ImmutableList<String> lines = Files.asCharSource(file, Charsets.UTF_8)
    .readLines();

// Count distinct word occurrences in a file
Multiset<String> wordOccurrences = HashMultiset.create(
    Splitter.on(CharMatcher.whitespace())
        .trimResults()
        .omitEmptyStrings()
        .split(Files.asCharSource(file, Charsets.UTF_8).read()));

// SHA-1 a file
HashCode hash = Files.asByteSource(file).hash(Hashing.sha1());

// Copy the data from a URL to a file
Resources.asByteSource(url).copyTo(Files.asByteSink(file));
```

### Files

为了增加创建file sources 和 sinks的方法，Files类包含了一些你感兴趣的便利的方法。

```java
//Method	Description
// 创建必要文件但是不存在父文件夹
createParentDirs(File)	

//获取真实存在的路径的文件
getFileExtension(String)	

//获取已删除其扩展名的文件的名称
getNameWithoutExtension(String)	

//清理路径。 并不总是与您的文件系统一致; 仔细测试！
simplifyPath(String)	

//返回可以遍历文件树的Traverser
fileTraverser()	
```



## 关闭资源

socket链接关闭，文件描述信息不被泄露，确定执行完成后关闭Closeable资源是当他们结束的时候最重要的事情。以及确定正确的编码行为。在JDK1.7之前，完成正确是非常困难的，很多陷井并不是很明显。众所周知，使用JDK自身大部分不能完全正确。

这里有一个典型的开启流的代码，在JDK1.6中使用和关闭一个InputStream：

```java
InputStream in = null;
try {
  in = openInputStream();
  // do something with in
} finally {
  if (in != null) {
    in.close();
  }
}
```

这样的代码，复杂度超出了我们的理解，我们希望得到简单的API。不幸的，他依然有一个问题：如果try中的部分抛出一个异常并且另一个异常是调用再finally块中的in.close()的时候抛出的，这是try中异常会被吞掉，然后把finally中的异常抛出，这样我们就不能得到错误的详情，并且找不到发生时的任何蛛丝马迹。

如果同时打开俩个流，那么情况会变得更糟：

```java
InputStream in = null;
try {
  in = openInputStream();
  OutputStream out = null;
  try {
    out = openOutputStream();
    // do something with in and out
  } finally {
    if (out != null) {
      out.close();
    }
  }
} finally {
  if (in != null) {
    in.close();
  }
}
```

层叠的try/finally代码块非常不利于阅读，并且这样的代码会有和上面一样的问题。

### Input vs. output streams

当资源关闭的时候，这里有关于 Input 或者 output streams的俩个重要的区别。

input流，尝试关闭流时发生的错误对您的程序来说不太可能是重要的：您已经完成了它，您已经阅读了所需的一切。

output流，关闭流时抛出的异常必须被视为与实际使用流时抛出的异常一样重要。 这样做的原因是输出流可以缓冲写入它们的数据，并且在调用close()时需要将缓冲的数据刷新到底层的输出接收器。 换句话说，close()调用可能触发对文件或其他接收器的实际写入，因此从该调用抛出的异常可能表示实际无法完成预期的写入操作。

### 解决

#### try-with-resources

如果你使用的是JDK1.7以上的版本，那么它是非常简单的：使用try-with-resources。这里有一个开启和关闭多资源使用try-with-resources的例子：

```java
try (InputStream in = openInputStream();
            OutputStream out = openOutputStream()) {
  // do stuff with in and out
}
```

当try块执行完的时候，所有的流都会自动的关闭。并且如果抛出多个异常，第一个异常会被压入，其他的也一样，并且所有一叠跟踪的异常都会被写入一个单独的异常在try块中抛出。这是简单的，解决了所有的问题，使用它。

### Sources 和 Sinks

ByteSource 和 CharSource 代表各自字节和字符数据的可读资源，ByteSink 和 CharSink 代表各自的是字节和字符数据的可写汇集。所有的都有能力开启多个可以读或者写的流（例如 InputStream 或者 Writer)。

如果可能，创建这些类型中其中一种（或者使用一个提供的实现例如Files.asByteSource(File)），允许你避免完全的关闭或者开启流的问题，而且允许你使用这些类提供的方法来处理你开启或者关闭后的资源。例如：你可以拷贝一个File中的内容到一些ByteSink，就像这样：

```java
ByteSink sink = ...
Files.asByteSource(file).copyTo(sink);
```

### Closer

Closer是Guava 14.0 增加的一个类。它的目的是作为一个穷人的try-with-resources块，用于必须在JDK6下编译和运行的代码。 以下是使用它的示例：

```java
Closer closer = Closer.create();
try {
  InputStream in = closer.register(openInputStream());
  OutputStream out = closer.register(openOutputStream());
  // do stuff with in and out
} catch (Throwable e) { // must catch Throwable
  throw closer.rethrow(e);
} finally {
  closer.close();
}
```

在Closer上调用close()将安全地关闭所有已向其注册的Closeable对象。 在Java 7下运行时，它甚至使用相同的机制来抑制尝试资源使用的其他异常，从而产生应该相同的行为。 请注意，在使用Closer时，遵循规定的使用模式非常重要：在调用closer.close()之前，必须通过Closer的rethrow方法捕获并重新抛出从使用Closeable资源的块抛出的任何Throwable。 另请注意，如果您希望捕获从整个代码块中抛出的任何异常，则应将整个内容包装在另一个try块中。


# 反射

## TypeToken

由于类型擦除，你不能在运行时传递泛型（ **around** ）类对象 - 你可能能够抛出它们并假装它们是通用的，但它们实际上并非如此。

例如：

```java
ArrayList<String> stringList = Lists.newArrayList();
ArrayList<Integer> intList = Lists.newArrayList();
System.out.println(stringList.getClass().isAssignableFrom(intList.getClass()));
// returns true, even though ArrayList<String> is not assignable from ArrayList<Integer>
```

Guava提供了 TypeToken，可以在运行时使用基于反射的方式允许你操作和查询泛型（generic）的类型。 ** Think of a TypeToken as a way of creating, manipulating, and querying Type (and, implicitly Class) objects in a way that respects generics.** 将TypeToken视为一种以尊重泛型的方式创建，操作和查询Type（和隐式Class）对象的方法。

Guice使用者们请注意：TypeToken和Guice中的TypeLiteral相似，但是有一个实现不同：他支持不是具现化的类型，就像 T ，List 甚至是 List<? extends Number>; 然而 TypeLiteral 不是这样的。TypeToken也是可序列化的，并提供了许多其他实用方法。

## 背景：类型擦除和反射

Java对于对象在运行时期间并不会保留泛型类型的信息。如果你有一个运行时的ArrayList对象，你不能通过反射查明他的反射类型是ArrayList -- 并且你可以，使用不安全的原生类型，匹配他到ArrayList。

然而，反射允许你查明这方法和类的泛型类型。如果你实现了一个方法，返回一个List，并且你使用反射来获取这个方法返回的对象，你会得到一个ParameterizedType代表List。

TypeToken类使用变通的方法使用最小的开销操作泛型类型。

## 介绍

获取基本原始类的TypeToken非常简单

```java
TypeToken<String> stringTok = TypeToken.of(String.class);
TypeToken<Integer> intTok = TypeToken.of(Integer.class);
```

为了获取TypeToken的一个泛型类型--当你在完成的时候知道这个泛型对象的时候--你可以使用一个空的匿名内部类：

```java
TypeToken<List<String>> stringListTok = new TypeToken<List<String>>() {};
```

或者你想要一个百搭的类型：

```java
TypeToken<Map<?, ?>> wildMapTok = new TypeToken<Map<?, ?>>() {};
```

TypeToken提供了动态构造泛型参数的途径，就像这样：

```java
static <K, V> TypeToken<Map<K, V>> mapToken(TypeToken<K> keyToken, TypeToken<V> valueToken) {
  return new TypeToken<Map<K, V>>() {}
    .where(new TypeParameter<K>() {}, keyToken)
    .where(new TypeParameter<V>() {}, valueToken);
}
...
TypeToken<Map<String, BigInteger>> mapToken = mapToken(
   TypeToken.of(String.class),
   TypeToken.of(BigInteger.class));
TypeToken<Map<Integer, Queue<String>>> complexToken = mapToken(
   TypeToken.of(Integer.class),
   new TypeToken<Queue<String>>() {});
```

请注意，如果mapToken只返回了新的TypeToken <Map <K，V >>（），它实际上无法实现分配给K和V的类型，例如：

```java
class Util {
  static <K, V> TypeToken<Map<K, V>> incorrectMapToken() {
    return new TypeToken<Map<K, V>>() {};
  }
}

System.out.println(Util.<String, BigInteger>incorrectMapToken());
// just prints out "java.util.Map<K, V>"
```

相对的，你可以使用一个（通常的匿名函数）子类获取一个泛型类型，并且他紧靠着一个上下文类，这样就㐓知道类型参数是什么。

```java
abstract class IKnowMyType<T> {
  TypeToken<T> type = new TypeToken<T>(getClass()) {};
}
...
new IKnowMyType<String>() {}.type; // returns a correct TypeToken<String>
```

使用这种技术，你就可以。例如，获取知道他们元素类型的类。

## 查询

TypeToken提供了很对关于类的查询，但是适当考虑了通用约束。

支持的查询方法包括：

| 方法                   | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| getType()              | Returns the wrapped java.lang.reflect.Type.                  |
| getRawType()           | Returns the most-known runtime class.                        |
| getSubtype(Class<?>)   | Returns some subtype of this that has the specified raw class. For example, if this is Iterable and the argument is List.class, the result will be List. |
| getSupertype(Class<?>) | Generifies the specified raw class to be a supertype of this type. For example, if this is Set and the argument is Iterable.class, the result will be Iterable. |
| isSupertypeOf(type)    | Returns true if this type is a supertype of the given type. "Supertype" is defined according to the rules for type arguments introduced with Java generics. |
| getTypes()             | Returns the set of all classes and interfaces that this type is or is a subtype of. The returned Set also provides methods classes() and interfaces() to let you view only the superclasses and superinterfaces. |
| isArray()              | Checks if this type is known to be an array, such as int[] or even <? extends A[]>. |
| getComponentType()     | Returns the array component type.                            |

## resolveType

resolveType可以从上下文token中用来“代替”类型参数的使用，他是有力的但是支持复杂查询操作的。例如：

```java
TypeToken<Function<Integer, String>> funToken = new TypeToken<Function<Integer, String>>() {};

TypeToken<?> funResultToken = funToken.resolveType(Function.class.getTypeParameters()[1]));
  // returns a TypeToken<String>
```

TypeToken将Java提供的TypeVariables与“context”标记中的那些类型变量的值统一起来。 这可以用于一般推断类型的方法的返回类型：

```java
TypeToken<Map<String, Integer>> mapToken = new TypeToken<Map<String, Integer>>() {};
TypeToken<?> entrySetToken = mapToken.resolveType(Map.class.getMethod("entrySet").getGenericReturnType());
  // returns a TypeToken<Set<Map.Entry<String, Integer>>>
```

## Invokable

Guava的Invokable是一个java.lang.reflect.Method 和 java.lang.reflect.Constructor的流式包装。它使用任何一种简化了的常见反射代码。一些示例如下：

判断方法是public的？

JDK:

```java
Modifier.isPublic(method.getModifiers())
```

Invokable:

```java
invokable.isPublic();
```

判断方法的包权限是private？

JDK:

```java
!(Modifier.isPrivate(method.getModifiers()) || Modifier.isPublic(method.getModifiers()))
```

Invokable:

```java
invokable.isPackagePrivate()
```

方法可以被子类重写吗？

JDK:

```java
!(Modifier.isFinal(method.getModifiers())
    || Modifiers.isPrivate(method.getModifiers())
    || Modifiers.isStatic(method.getModifiers())
    || Modifiers.isFinal(method.getDeclaringClass().getModifiers()))
```

Invokable:

```java
invokable.isOverridable()
```

判断第一个方法参数是否使用@Nullable？

JDK:

```java
for (Annotation annotation : method.getParameterAnnotations()[0]) {
  if (annotation instanceof Nullable) {
    return true;
  }
}
return false;
```

Invokable:

```java
invokable.getParameters().get(0).isAnnotationPresent(Nullable.class)
```

如何分享对于同一个构造和工厂的方法的类似代码？

您是否想重复自己，因为您的反射代码需要以相同的方式为构造函数和工厂方法工作？

Invokable提供抽象。 以下代码适用于Method或Constructor：

```java
invokable.isPublic();
invokable.getParameters();
invokable.invoke(object, args);
```

对于List.get(int)，如何返回一个List类型？

Invokable提供：

```java
Invokable<List<String>, ?> invokable = new TypeToken<List<String>>() {}.method(getMethod);
invokable.getReturnType(); // String.class
```

## 动态代理

### newProxy()

当只有一个单独接口类型代理，Reflection.newProxy(Class, InvocationHandler)的工具方法是一个类型安全并且创建Java动态代理方便的API。

JDK:

```java
Foo foo = (Foo) Proxy.newProxyInstance(
    Foo.class.getClassLoader(),
    new Class<?>[] {Foo.class},
    invocationHandler);
```

Guava:

```java
Foo foo = Reflection.newProxy(Foo.class, invocationHandler);
```

### AbstractInvocationHandler

有时你想要动态代理简便支持equals(),hashCode() 和 toString()，那就是：* 如果他们有相同的接口类型和相同的调用处理器，一个代理实例相等于另一个代理实例。* A 代理的toString() 委托给调用处理程序的toString()，以便于自定义。

AbstractInvocationHandler实现这样的逻辑。

此外，AbstractInvocationHandler保证参数数组通过handleInvocation(Object, Method, Object[])绝不会是null，因此很少可能NullPointerException。

## ClassPath

直接说，Java没有通过架构途径来浏览类或者类资源路径的方法。然而，有时候我们希望有能力根据一个正确的包或者项目，获取所有的类。例如，检查是否遵循某些项目约定或者约束。

ClassPath 是扫描类路径的一个工具。使用非常简单：

```java
ClassPath classpath = ClassPath.from(classloader); // scans the class path used by classloader
for (ClassPath.ClassInfo classInfo : classpath.getTopLevelClasses("com.mycomp.mypackage")) {
  ...
}
```

在上面的示例中，ClassInfo是要加载的类的句柄。 它允许程序员检查类名或包名，只在必要时加载类。

值得注意的是，ClassPath是一种尽力而为的实用程序。 它只扫描jar文件或文件系统目录下的类。 它也不能扫描由非URLClassLoader的自定义类加载器管理的类。 所以不要将它用于关键任务生产任务。

## 类加载

实用方法Reflection.initialize（Class ...）确保初始化指定的类 - 例如，执行任何静态初始化。

使用这种方法是一种代码气味，因为静态会损害系统的可维护性和可测试性。 如果您在使用遗留框架进行操作时别无选择，则此方法有助于减少代码的难度。

# Math

这个包包含了一系列的数学工具。

## 内容

- 基于原始类型的需求，将数学基础方法独立分隔于IntMath, LongMath, DoubleMath, 和 BigIntegerMath不同的类中。这些类都具有平行的架构，但是每一个也都支持有自己的子集方法。注意，一小部分原生的相似的方法会在类似的 Ints （com.google.common.primitives）中找到。
- 针对单个和成双数据集合，提供了一部分统计的计算（类似：mean，median）。开始读这个 overview 要比浏览Javadoc要容易。
- LinearTransformation 表示 y = mx + b 的双值之间的线性转换；例如，英尺和米之间，或凯尔文和华氏度之间的转换。

## 例子

```java
int logFloor = LongMath.log2(n, FLOOR);

int mustNotOverflow = IntMath.checkedMultiply(x, y);

long quotient = LongMath.divide(knownMultipleOfThree, 3, RoundingMode.UNNECESSARY); // fail fast on non-multiple of 3

BigInteger nearestInteger = DoubleMath.roundToBigInteger(d, RoundingMode.HALF_EVEN);

BigInteger sideLength = BigIntegerMath.sqrt(area, CEILING);
```

## 为什么使用这些？

- 这些工具已经被不寻常的溢出条件全面的测试。溢出语义，如果成立，会被清楚的在特定的文档中被指明。当一个前提条件失败的时候，他会快速失败。
- 他们已经是基准和最优化。然而性能必然会依赖于详细的算法，在某些情况下，与 Apache Commons 的 MathUtils相比他们的花费更少。
- 他们的定义鼓励可读性，正确的编码习惯。IntMath.log2(x, CEILING)的意思是明白的并且明显的要比临时读取文档要好的多。32 - Integer.numberOfLeadingZeros(x - 1)的意思却并不是。

注意：这些工具并不特别满足GWT，并不是在GWT上他们不是最优的，由于不同的溢出逻辑。

## Math在数值类型上

这些工具处理这三种原始的数值类型：int, long, 和 BigInteger。math工具在这些类型上为了方便，命名为IntMath, LongMath, 和 BigIntegerMath。

## 检查算法

我们对于IntMath 和 LongMath提供了检查方法，如果溢出则快速失败，而不是选择默默地将他忽略。

| IntMath                 | LongMath                 |
| ----------------------- | ------------------------ |
| IntMath.checkedAdd      | LongMath.checkedAdd      |
| IntMath.checkedSubtract | LongMath.checkedSubtract |
| IntMath.checkedMultiply | LongMath.checkedMultiply |
| IntMath.checkedPow      | LongMath.checkedPow      |

```
IntMath.checkedAdd(Integer.MAX_VALUE, Integer.MAX_VALUE); // throws ArithmeticException
```

## Real-valued 方法

IntMath, LongMath, 和 BigIntegerMath关于“精确的真实值”提供了一系列的方法，但是他们的结果范围是一个Integer。这些方法接受一个java.math.RoundingMode。这是一个同样的RoundingMode，他已经被JDK是使用。他是一个枚举，具有以下值：

- DOWN: round towards 0. 范围小于零(这是Java除法的一种行为)
- UP: round away from 0.范围大于等于零
- FLOOR: round towards negative infinity.最小负的无穷大
- CEILING: round towards positive infinity.最大正的无穷大
- UNNECESSARY: rounding should not be necessary; 确认是不重要的，如果是，则抛出异常ArithmeticException。
- HALF_UP: round to the nearest half, rounding x.5 away from 0.中间的最近值。
- HALF_DOWN: round to the nearest half, rounding x.5 towards 0.
- HALF_EVEN: round to the nearest half, rounding x.5 to its nearest even neighbor.

这些方法在我们使用的时候变得可读：例如，divide(x, 3, CEILING)是确定计算，甚至我们可以阅读到他的过程。

此外，这里的每一个方法只使用数值计算，特别在 sqrt （平方根）构造初始化接近使用

| Operation | IntMath                        | LongMath                         | BigIntegerMath                               |
| --------- | ------------------------------ | -------------------------------- | -------------------------------------------- |
| Division  | divide(int, int, RoundingMode) | divide(long, long, RoundingMode) | divide(BigInteger, BigInteger, RoundingMode) |

|
| Base-2 logarithm | log2(int, RoundingMode) | log2(long, RoundingMode) | log2(BigInteger, RoundingMode) |
| Base-10 logarithm | log10(int, RoundingMode) | log10(long, RoundingMode) | log10(BigInteger, RoundingMode)|
| Square root | sqrt(int, RoundingMode) | sqrt(long, RoundingMode) | sqrt(BigInteger, RoundingMode) |

```java
BigIntegerMath.sqrt(BigInteger.TEN.pow(99), RoundingMode.HALF_EVEN);
   // returns 31622776601683793319988935444327185337195551393252
```

## 额外的方法

我们提供并支持一些数学方法，我们发现这些方法很有用。

| 操作                                                | IntMath            | LongMath           | BigIntegerMath                     |
| --------------------------------------------------- | ------------------ | ------------------ | ---------------------------------- |
| Greatest common divisor                             | gcd(int, int)      | gcd(long, long)    | In JDK: BigInteger.gcd(BigInteger) |
| Modulus (always nonnegative, -5 mod 3 is 1)         | mod(int, int)      | mod(long, long)    | In JDK: BigInteger.mod(BigInteger) |
| Exponentiation (may overflow)                       | pow(int, int)      | pow(long, int)     | In JDK: BigInteger.pow(int)        |
| Power-of-two testing                                | isPowerOfTwo(int)  | isPowerOfTwo(long) | isPowerOfTwo(BigInteger)           |
| Factorial (returns MAX_VALUE if input too big)      | factorial(int)     | factorial(int)     | factorial(int)                     |
| Binomial coefficient (returns MAX_VALUE if too big) | binomial(int, int) | binomial(int, int) | binomial(int, int)                 |

## Floating-point算法

Floating-point算法完美的包括与JDK，但是我们在 DoubleMath 中增加了一小部分有用的方法。

| 方法                                    | 描述                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| isMathematicalInteger(double)           | Tests if the input is finite and an exact integer.           |
| roundToInt(double, RoundingMode)        | Rounds the specified number and casts it to an int, if it fits into an int, failing fast otherwise. |
| roundToLong(double, RoundingMode)       | Rounds the specified number and casts it to a long, if it fits into a long, failing fast otherwise. |
| roundToBigInteger(double, RoundingMode) | Rounds the specified number to a BigInteger, if it is finite, failing fast otherwise. |
| log2(double, RoundingMode)              | Takes the base-2 logarithm, and rounds to an int using the specified RoundingMode. Faster than Math.log(double). |

