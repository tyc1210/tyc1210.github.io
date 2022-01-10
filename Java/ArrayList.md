## ArrayList

> 分析源码基于JDK1.8

属性

```java
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, Serializable {
    private static final long serialVersionUID = 8683452581122892189L;
    private static final int DEFAULT_CAPACITY = 10;
    private static final Object[] EMPTY_ELEMENTDATA = new Object[0];
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = new Object[0];
    // transient防止将空元素序列化，序列化时调用提供的writeObject方法
    transient Object[] elementData;
    private int size;
    private static final int MAX_ARRAY_SIZE = 2147483639;
	...
}
```



### 构造函数

注意

- 不指定初始容量为0

```java
public ArrayList(int var1) {
        if (var1 > 0) {
            this.elementData = new Object[var1];
        } else {
            if (var1 != 0) {
                throw new IllegalArgumentException("Illegal Capacity: " + var1);
            }

            this.elementData = EMPTY_ELEMENTDATA;
        }

    }

    public ArrayList() {
        // 若不指定初始化容量大小则创建一个容量为0的数组
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    public ArrayList(Collection<? extends E> var1) {
        this.elementData = var1.toArray();
        if ((this.size = this.elementData.length) != 0) {
            // 若不是Object[].class 转型
            if (this.elementData.getClass() != Object[].class) {
                // Arrays.copyOf 返回新数组
                this.elementData = Arrays.copyOf(this.elementData, this.size, Object[].class);
            }
        } else {
            this.elementData = EMPTY_ELEMENTDATA;
        }

    }

```



## 常用方法

###  添加

注意

- 首次添加才扩容为10
- 扩容大小并不一定是1.5倍

```java
    public boolean add(E var1) {
        this.ensureCapacityInternal(this.size + 1);
        this.elementData[this.size++] = var1;
        return true;
    }

 private void ensureCapacityInternal(int var1) {
        this.ensureExplicitCapacity(calculateCapacity(this.elementData, var1));
    }

// 计算所需容量： 初始化为空数组，首次添加容量为10
private static int calculateCapacity(Object[] var0, int var1) {
        return var0 == DEFAULTCAPACITY_EMPTY_ELEMENTDATA ? Math.max(10, var1) : var1;
    }

private void ensureExplicitCapacity(int var1) {
    	// 快速失败使用
        ++this.modCount;
    	// 若所需容量大于存储数据的数组大小进行扩容
        if (var1 - this.elementData.length > 0) {
            this.grow(var1);
        }

    }


    // 扩容大小为：数组长度的1.5倍与计算出的容量比较 谁大取谁
    private void grow(int var1) {
        int var2 = this.elementData.length;
        int var3 = var2 + (var2 >> 1);
        if (var3 - var1 < 0) {
            var3 = var1;
        }

        if (var3 - 2147483639 > 0) {
            var3 = hugeCapacity(var1);
        }

        this.elementData = Arrays.copyOf(this.elementData, var3);
    }

private static int hugeCapacity(int var0) {
        if (var0 < 0) {
            throw new OutOfMemoryError();
        } else {
            return var0 > 2147483639 ? 2147483647 : 2147483639;
        }
    }
```

数组拷贝

```java
// 参数为数组和要拷贝的长度  底层调用System.arraycopy
Arrays.copyOf(var1, var2);
// System.arraycopy 原数组 拷贝起始位置 目标数组 拷贝起始位置 拷贝长度 返回目标数组
// 这是一种极为消耗资源的操作，在频繁的插入或者是删除元素的情况下，LinkedList的性能会更加好
arraycopy(Object var0, int var1, Object var2, int var3, int var4);
```



### 删除

```java
public boolean remove(Object var1) {
        int var2;
        if (var1 == null) {
            for(var2 = 0; var2 < this.size; ++var2) {
                // 知道对应索引
                if (this.elementData[var2] == null) {
                    this.fastRemove(var2);
                    return true;
                }
            }
        } else {
            for(var2 = 0; var2 < this.size; ++var2) {
                if (var1.equals(this.elementData[var2])) {
                    this.fastRemove(var2);
                    return true;
                }
            }
        }

        return false;
    }

private void fastRemove(int var1) {
    	// 记录修改
        ++this.modCount;
    	// 需要拷贝的长度
        int var2 = this.size - var1 - 1;
        if (var2 > 0) {
            System.arraycopy(this.elementData, var1 + 1, this.elementData, var1, var2);
        }
        this.elementData[--this.size] = null;
    }
```



添加和删除会带来元素的移动效率较低

修改查询直接根据首地址+下标速度较快