---
layout: post
title: ArrayList 扩容
categories: Java
tags:
- ArrayList
- Java
description: ArrayList 扩容
---

#### ArrayList 初始化

{% highlight Java %}
/**
 * Shared empty array instance used for default sized empty instances. We
 * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
 * first element is added.
 */
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};


/**
 * Constructs an empty list with an initial capacity of ten.
 */
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
{% endhighlight %}

默认构造器，初始化时，将elementData设置为DEFAULTCAPACITY_EMPTY_ELEMENTDATA，一个空的数组。

#### add操作

{% highlight Java %}
    /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
{% endhighlight %}

add操作时，先调用ensureCapacityInternal确认是否需要扩容。然后将数据存入elementData。

#### 扩容

{% highlight Java %}
    /**
     * Increases the capacity of this <tt>ArrayList</tt> instance, if
     * necessary, to ensure that it can hold at least the number of elements
     * specified by the minimum capacity argument.
     *
     * @param   minCapacity   the desired minimum capacity
     */
    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }
    
    
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
    
    
    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
{% endhighlight %}

在初始化后，插入第一个数据的时候，调用ensureCapacityInternal，该方法判断是插入第一个数据，即elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA时候，会将minCapacity设置为DEFAULT_CAPACITY，即10，因为elementData.length是0，所以必然进行grow扩容。grow扩容的时候，会将扩容后的容量，即newCapacity设置为DEFAULT_CAPACITY。

如果不是插入的第一个数据，ensureExplicitCapacity会判断插入后的至少所需的容量minCapacity是否大于elementData当前的容量，如果是，则进行grow扩容。扩容时，newCapacity = oldCapacity + (oldCapacity >> 1)，容量扩容为原来的1.5倍。