## EnumSet ##

    public abstract class EnumSet<E extends Enum<E>> extends AbstractSet<E>

### java doc ###

用于枚举类型的专用Set实现。枚举集中的所有元素必须来自单个枚举类型，该类型在创建集时显式或隐式指定。枚举集在内部表示为位向量。这种表现非常紧凑和高效。这个类的空间和时间性能应该足够好，以允许它作为传统的基于int的“位标志”的高质量，类型安全的替代品。即使批量操作（例如containsAll和retainAll）如果它们的参数也是枚举集，也应该非常快速地运行。
迭代器方法返回的迭代器以其自然顺序（枚举枚举常量的顺序）遍历元素。返回的迭代器是弱一致的：它永远不会抛出ConcurrentModificationException，它可能会也可能不会显示迭代进行过程中对集合所做的任何修改的影响。

不允许使用空元素。尝试插入null元素将抛出NullPointerException。但是，尝试测试是否存在null元素或删除一个元素将正常运行。

与大多数集合实现一样，EnumSet不同步。如果多个线程同时访问枚举集，并且至少有一个线程修改了该集，则应该在外部进行同步。这通常通过在自然封装枚举集的某个对象上同步来完成。如果不存在此类对象，则应使用Collections.synchronizedSet（java.util.Set <T>）方法“包装”该集合。这最好在创建时完成，以防止意外的不同步访问：
设置

    <MyEnum> s = Collections.synchronizedSet（EnumSet.noneOf（MyEnum.class））;
 
实施说明：所有基本操作都在恒定时间内执行。它们很可能（虽然不能保证）比它们的HashSet对应物快得多。如果它们的参数也是枚举集，即使批量操作也会在恒定时间内执行。

### 字段 ###

    /**
     * 该集合中所有元素的类。
     */
    final transient Class<E> elementType;

    /**
     * 所有包含E.的值.（缓存性能。）
     */
    final transient Enum<?>[] universe;

### 分析 ###

EnumSet是一个特殊的抽象类，它里面只有静态方法，通过静态方法创建枚举set。返回的是它的子类，RegularEnumSet或者JumboEnumSet。


## RegularEnumSet ##

    class RegularEnumSet<E extends Enum<E>> extends EnumSet<E>

### java doc ###

EnumSet的私有实现类，用于“常规大小”的枚举类型,具有64或更少枚举常数。


### 字段 ###
   /**
     * 该集的位向量表示。 2^k 位表示该集合中存在宇宙[k].
     */
    private long elements = 0L;


### 构造方法 ###

    RegularEnumSet(Class<E>elementType, Enum<?>[] universe) {
        super(elementType, universe);
    }

    void addRange(E from, E to) {
        elements = (-1L >>>  (from.ordinal() - to.ordinal() - 1)) << from.ordinal();
    }

