## LinkedList
### 属性

```java
public class LinkedList<E> extends AbstractSequentialList<E> implements List<E>, Deque<E>, Cloneable, Serializable {
    transient int size;
    transient LinkedList.Node<E> first;
    transient LinkedList.Node<E> last;
    private static final long serialVersionUID = 876323262645176354L;
}    
```



### 常用方法

### 添加

```java
public boolean add(E var1) {
        this.linkLast(var1);
        return true;
    }

void linkLast(E var1) {
        LinkedList.Node var2 = this.last;
    	// 创建节点 参数(prev,value,next)
        LinkedList.Node var3 = new LinkedList.Node(var2, var1, (LinkedList.Node)null);
        this.last = var3;
    	// 首次添加为空
        if (var2 == null) {
            this.first = var3;
        } else {
            var2.next = var3;
        }

        ++this.size;
        ++this.modCount;
    }

 private static class Node<E> {
        E item;
        LinkedList.Node<E> next;
        LinkedList.Node<E> prev;

        Node(LinkedList.Node<E> var1, E var2, LinkedList.Node<E> var3) {
            this.item = var2;
            this.next = var3;
            this.prev = var1;
        }
    }
```

