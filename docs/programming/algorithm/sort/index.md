# 排序

## 1.稳定性

如果在排序过程中，两个相等的元素在排序前后的相对位置不变，则称该排序算法是稳定的（Stable Sort）。否则，该排序算法是不稳定的（Unstable Sort）。

比如数组：`[5, 3₁, 2, 3₂, 1]`（`3₁`和`3₂`是两个值相同但不同的元素），在排序后：

- 如果变为：`[1, 2, 3₁, 3₂, 5]`，则说明排序算法是稳定的。
- 如果变为：`[1, 2, 3₂, 3₁, 5]`，则说明排序算法是不稳定的。

对自定义对象进行排序时，如果排序的关键字相同，且希望保持它们在原始数组中的相对位置不变，则应选择稳定的排序算法。

## 2.原地算法

原地算法（In-place Algorithm）是指在排序过程中只使用常数级别的额外空间（即空间复杂度为 O(1)），而不依赖于额外的数据结构来存储数据。换句话说，原地算法通过在原始数据结构上进行修改来实现排序，而不是创建一个新的数据结构来存储排序后的结果。

## 3.封装公共父类

在所有排序算法中，均有一些公共的逻辑，比如：交换、比较大小等操作，可以将这些公共逻辑封装到一个抽象类中，还可以进行一些指标统计，所有排序算法均继承自该抽象类，并实现`sort()`方法，如下：

```java
package sort;

import java.text.DecimalFormat;

/**
 * 所有排序算法的基类
 * 实现 `Comparable<Sort>` 接口，可以用于比较不同排序算法的性能
 *
 * @param <E> 元素类型，要求实现 Comparable 接口
 * @author yolk
 * @since 2025/10/6 15:47
 */
public abstract class Sort<E extends Comparable<E>> implements Comparable<Sort<E>> {

    // 待排序的数组
    protected E[] array;

    // 统计比较次数
    protected int cmpCount;

    // 统计交换次数
    protected int swapCount;

    // 当比较次数和交换次数过大时，缩短显示
    private static final DecimalFormat fmt = new DecimalFormat("#.00");

    // 耗时（毫秒数）
    private long time;

    /**
     * 对外提供的排序接口
     */
    public void sort(E[] arr) {
        if (arr == null || arr.length < 2) {
            return;
        }

        // 赋值给成员变量，子类可以直接使用
        this.array = arr;

        long begin = System.currentTimeMillis();
        sort();
        long end = System.currentTimeMillis();

        // 记录排序耗时
        this.time = end - begin;
    }

    /**
     * 具体的排序算法由子类实现
     */
    protected abstract void sort();

    /**
     * 基于索引比较 array[i1] 和 array[i2] 的大小
     *
     * @param i1
     * @param i2
     * @return =0: array[i1] == array[i2]
     * <0: array[i1] < array[i2]
     * >0: array[i1] > array[i2]
     */
    protected int compare(int i1, int i2) {
        // 比较次数加 1
        cmpCount++;

        return array[i1].compareTo(array[i2]);
    }

    /**
     * 直接比较元素 v1 和 v2 的大小
     *
     * @param v1
     * @param v2
     * @return =0: v1 == v2
     * <0: v1 < v2
     * >0: v1 > v2
     */
    protected int compare(E v1, E v2) {
        // 比较次数加 1
        cmpCount++;

        return v1.compareTo(v2);
    }

    /**
     * 交换 array[i1] 和 array[i2] 的值
     *
     * @param i1
     * @param i2
     */
    protected void swap(int i1, int i2) {
        // 如果索引 i1 和 i2 相等，则不交换
        if (i1 == i2) {
            return;
        }
        // 交换次数加 1
        swapCount++;

        // 交换 array[i1] 和 array[i2]
        E tmp = array[i1];
        array[i1] = array[i2];
        array[i2] = tmp;
    }

    /**
     * 重写 toString 方法，打印排序效率统计信息
     */
    @Override
    public String toString() {
        String countStr = "数据规模：" + numberString(array.length);
        String timeStr = "耗时：" + (time / 1000.0) + "s(" + time + "ms)";
        String compareCountStr = "比较：" + numberString(cmpCount);
        String swapCountStr = "交换：" + numberString(swapCount);
        return "【" + getClass().getSimpleName() + "】\n"
                + countStr + "\t "
                + timeStr + " \t"
                + compareCountStr + "\t "
                + swapCountStr + "\n"
                + "------------------------------------------------------------------";

    }

    /**
     * 当比较次数和交换次数过大时，缩短显示
     * 当数字小于 1w 时，直接显示数字
     * 当数字小于 1亿 时，显示为 xx.xx 万
     * 否则显示为 xx.xx 亿
     */
    private String numberString(int number) {
        if (number < 10000) return "" + number;

        if (number < 100000000) return fmt.format(number / 10000.0) + "万";
        return fmt.format(number / 100000000.0) + "亿";
    }

    /**
     * 重写 compareTo 方法，比较两个排序算法的效率
     * 先比较耗时，耗时少的排在前面
     * 如果耗时相同，则比较比较次数，比较次数少的排在前面
     * 如果比较次数也相同，则比较交换次数，交换次数少的排在前面
     *
     * @param o 另一个排序算法对象
     * @return <0：当前算法效率更高
     * >0：另一个算法效率更高
     * =0： 效率相同
     */
    @Override
    public int compareTo(Sort o) {
        int result = Math.toIntExact(this.time - o.time);
        if (result != 0) {
            return result;
        }
        result = this.cmpCount - o.cmpCount;
        if (result != 0) {
            return result;
        }

        return this.swapCount - o.swapCount;
    }
}
```

## 3.种类

- [冒泡排序（Bubble Sort）](./bubble)
- [选择排序（Selection Sort）](./selection)
- [插入排序（Insertion Sort）](./insertion)
- [希尔排序（Shell Sort）](./shell)
- [归并排序（Merge Sort）](./merge)
- [快速排序（Quick Sort）](./quick)
- [堆排序（Heap Sort）](./heap)
- [计数排序（Counting Sort）](./counting)
- [桶排序（Bucket Sort）](./bucket)
- [基数排序（Radix Sort）](./radix)

## 4.对比

<table cellspacing="0" cellpadding="6">
  <tr>
    <th>名称</th>
    <th>最好</th>
    <th>最坏</th>
    <th>平均</th>
    <th>额外空间复杂度</th>
    <th>In-place</th>
    <th>稳定性</th>
  </tr>
  <tr>
    <td>冒泡排序（Bubble Sort）</td>
    <td><span style="color:#00BFFF">O(n)</span></td>
    <td><span style="color:#FF1493">O(n²)</span></td>
    <td><span style="color:#FF1493">O(n²)</span></td>
    <td><span style="color:#00CED1">O(1)</span></td>
    <td>✅</td>
    <td>✅</td>
  </tr>
  <tr>
    <td>选择排序（Selection Sort）</td>
    <td><span style="color:#FF1493">O(n²)</span></td>
    <td><span style="color:#FF1493">O(n²)</span></td>
    <td><span style="color:#FF1493">O(n²)</span></td>
    <td><span style="color:#00CED1">O(1)</span></td>
    <td>✅</td>
    <td>❌</td>
  </tr>
  <tr>
    <td>插入排序（Insertion Sort）</td>
    <td><span style="color:#00BFFF">O(n)</span></td>
    <td><span style="color:#FF1493">O(n²)</span></td>
    <td><span style="color:#FF1493">O(n²)</span></td>
    <td><span style="color:#00CED1">O(1)</span></td>
    <td>✅</td>
    <td>✅</td>
  </tr>
  <tr>
    <td>归并排序（Merge Sort）</td>
    <td><span style="color:#9370DB">O(nlogn)</span></td>
    <td><span style="color:#9370DB">O(nlogn)</span></td>
    <td><span style="color:#9370DB">O(nlogn)</span></td>
    <td><span style="color:#00BFFF">O(n)</span></td>
    <td>❌</td>
    <td>✅</td>
  </tr>
  <tr>
    <td>快速排序（Quick Sort）</td>
    <td><span style="color:#9370DB">O(nlogn)</span></td>
    <td><span style="color:#FF1493">O(n²)</span></td>
    <td><span style="color:#9370DB">O(nlogn)</span></td>
    <td><span style="color:#FFD700">O(logn)</span></td>
    <td>✅</td>
    <td>❌</td>
  </tr>
  <tr>
    <td>希尔排序（Shell Sort）</td>
    <td><span style="color:#00BFFF">O(nlogn)</span></td>
    <td><span style="color:#FF1493">O(n<sup>4/3</sup>) ~ O(n²)</span></td>
    <td><span style="color:#A9A9A9">取决于步长序列</span></td>
    <td><span style="color:#00CED1">O(1)</span></td>
    <td>✅</td>
    <td>❌</td>
  </tr>
  <tr>
    <td>堆排序（Heap Sort）</td>
    <td><span style="color:#9370DB">O(nlogn)</span></td>
    <td><span style="color:#9370DB">O(nlogn)</span></td>
    <td><span style="color:#9370DB">O(nlogn)</span></td>
    <td><span style="color:#00CED1">O(1)</span></td>
    <td>✅</td>
    <td>❌</td>
  </tr>
  <tr>
    <td>基数排序（Radix Sort）</td>
    <td><span style="color:#00BFFF">O(d * (n+k))</span></td>
    <td><span style="color:#00BFFF">O(d * (n+k))</span></td>
    <td><span style="color:#00BFFF">O(d * (n+k))</span></td>
    <td><span style="color:#00BFFF">O(n+k)</span></td>
    <td>❌</td>
    <td>✅</td>
  </tr>
  <tr>
    <td>桶排序（Bucket Sort）</td>
    <td><span style="color:#00BFFF">O(n+k)</span></td>
    <td><span style="color:#00BFFF">O(n+k)</span></td>
    <td><span style="color:#00BFFF">O(n+m)</span></td>
    <td><span style="color:#00BFFF">O(n+m)</span></td>
    <td>❌</td>
    <td>✅</td>
  </tr>
  <tr>
    <td>计数排序（Counting Sort）</td>
    <td><span style="color:#00BFFF">O(n+k)</span></td>
    <td><span style="color:#00BFFF">O(n+k)</span></td>
    <td><span style="color:#00BFFF">O(n+k)</span></td>
    <td><span style="color:#00BFFF">O(n+k)</span></td>
    <td>❌</td>
    <td>✅</td>
  </tr>
</table>

注意

- 以上表格是基于数组类型的数据结构进行排序的一般性总结，并且每种算法实现方式也有所不同，所以此分析仅供参考
- 冒泡、选择、插入、归并、快速、希尔、堆排序是<font color=#FF000><b>比较排序</b></font>（Comparison Sorting）

### 4.1.性能对比测试代码

```java
package sort;

import sort.compare.bubble.BubbleSort1;
import sort.compare.bubble.BubbleSort2;
import sort.compare.bubble.BubbleSort3;
import sort.compare.heap.HeapSort;
import sort.compare.selection.SelectionSort;
import utils.Integers;

import java.util.Arrays;
import java.util.List;

/**
 * 比较每种排序的性能
 *
 * @author yolk
 * @since 2025/10/6 14:30
 */
public class Test {

    public static void main(String[] args) {
        Integer[] arr = Integers.random(10000, 1, 100000);
        List<Sort<Integer>> sorts = Arrays.asList(
                new BubbleSort1<>(),
                new BubbleSort2<>(),
                new BubbleSort3<>(),
                new SelectionSort<>(),
                new HeapSort<>(),
                new InsertionSort1<>(),
                new InsertionSort2<>(),
                new InsertionSort3<>()
        );
        testSorts(arr, sorts);
    }

    public static void testSorts(Integer[] array, List<Sort<Integer>> sorts) {
        for (Sort<Integer> sort : sorts) {
            // 复制一份数组，避免排序算法之间相互影响
            Integer[] arr = Integers.copy(array);

            sort.sort(arr);
        }

        // 使用 Sort 类实现的 Comparable 接口，进行排序，以比较效率
        sorts.sort(null);
        System.out.println("各种排序效率从高到低：");
        for (Sort<Integer> sort : sorts) {
            System.out.println(sort);
        }
    }
}
```

## 5.工具类

### 5.1.Integers.java

用于生成各种类型的整数数组，如：随机数组、升序数组、降序数组、部分有序数组等

```java
package utils;

import java.util.Arrays;
import java.util.Random;

/**
 * 整数数组工具类
 * 提供各种生成、操作和检查整数数组的静态方法
 */
public class Integers {

    private static final Random RANDOM = new Random();

    /** 随机生成 count 个 [min, max] 的整数 */
    public static Integer[] random(int count, int min, int max) {
        Integer[] array = new Integer[count];
        int delta = max - min + 1;
        for (int i = 0; i < count; i++) {
            array[i] = min + RANDOM.nextInt(delta);
        }
        return array;
    }

    /** 合并两个数组 */
    public static Integer[] combine(Integer[] array1, Integer[] array2) {
        Integer[] array = new Integer[array1.length + array2.length];
        System.arraycopy(array1, 0, array, 0, array1.length);
        System.arraycopy(array2, 0, array, array1.length, array2.length);
        return array;
    }

    /** 生成前 unsameCount 个不同，剩余相同的数组 */
    public static Integer[] same(int count, int unsameCount) {
        Integer[] array = new Integer[count];
        for (int i = 0; i < unsameCount; i++) {
            array[i] = unsameCount - i;
        }
        for (int i = unsameCount; i < count; i++) {
            array[i] = unsameCount + 1;
        }
        return array;
    }

    /** 头尾升序，中间 disorderCount 个反序 */
    public static Integer[] headTailAscOrder(int min, int max, int disorderCount) {
        Integer[] array = ascOrder(min, max);
        if (disorderCount > array.length) return array;

        int begin = (array.length - disorderCount) >> 1;
        reverse(array, begin, begin + disorderCount);
        return array;
    }

    /** 中心升序，头尾 disorderCount 个反序 */
    public static Integer[] centerAscOrder(int min, int max, int disorderCount) {
        Integer[] array = ascOrder(min, max);
        if (disorderCount > array.length) return array;

        int left = disorderCount >> 1;
        reverse(array, 0, left);

        int right = disorderCount - left;
        reverse(array, array.length - right, array.length);
        return array;
    }

    /** 头升序，尾 disorderCount 个反序 */
    public static Integer[] headAscOrder(int min, int max, int disorderCount) {
        Integer[] array = ascOrder(min, max);
        if (disorderCount > array.length) return array;
        reverse(array, array.length - disorderCount, array.length);
        return array;
    }

    /** 尾升序，头 disorderCount 个反序 */
    public static Integer[] tailAscOrder(int min, int max, int disorderCount) {
        Integer[] array = ascOrder(min, max);
        if (disorderCount > array.length) return array;
        reverse(array, 0, disorderCount);
        return array;
    }

    /** 升序数组 [min, max] */
    public static Integer[] ascOrder(int min, int max) {
        Integer[] array = new Integer[max - min + 1];
        for (int i = 0; i < array.length; i++) {
            array[i] = min++;
        }
        return array;
    }

    /** 降序数组 [min, max] */
    public static Integer[] descOrder(int min, int max) {
        Integer[] array = new Integer[max - min + 1];
        for (int i = 0; i < array.length; i++) {
            array[i] = max--;
        }
        return array;
    }

    /** 反转数组，索引范围 [begin, end) */
    private static void reverse(Integer[] array, int begin, int end) {
        int count = (end - begin) >> 1;
        int sum = begin + end - 1;
        for (int i = begin; i < begin + count; i++) {
            int j = sum - i;
            Integer tmp = array[i];
            array[i] = array[j];
            array[j] = tmp;
        }
    }

    /** 复制数组 */
    public static Integer[] copy(Integer[] array) {
        return Arrays.copyOf(array, array.length);
    }

    /** 判断是否升序 */
    public static boolean isAscOrder(Integer[] array) {
        if (array == null || array.length == 0) return false;
        for (int i = 1; i < array.length; i++) {
            if (array[i - 1] > array[i]) return false;
        }
        return true;
    }

    /** 打印数组 */
    public static void println(Integer[] array) {
        System.out.println("-------------------------------------");
        System.out.println(Arrays.toString(array));
    }
}
```

#### 使用示例

```java
package utils;

import java.util.Arrays;
import java.util.Random;

/**
 * 整数数组工具类
 * 提供各种生成、操作和检查整数数组的静态方法
 */
public class Integers {

    private static final Random RANDOM = new Random();

    /**
     * 随机生成 count 个 [min, max] 的整数
     */
    public static Integer[] random(int count, int min, int max) {
        Integer[] array = new Integer[count];
        int delta = max - min + 1;
        for (int i = 0; i < count; i++) {
            array[i] = min + RANDOM.nextInt(delta);
        }
        return array;
    }

    /**
     * 合并两个数组
     */
    public static Integer[] combine(Integer[] array1, Integer[] array2) {
        Integer[] array = new Integer[array1.length + array2.length];
        System.arraycopy(array1, 0, array, 0, array1.length);
        System.arraycopy(array2, 0, array, array1.length, array2.length);
        return array;
    }

    /**
     * 生成前 unsameCount 个不同，剩余相同的数组
     */
    public static Integer[] same(int count, int unsameCount) {
        Integer[] array = new Integer[count];
        for (int i = 0; i < unsameCount; i++) {
            array[i] = unsameCount - i;
        }
        for (int i = unsameCount; i < count; i++) {
            array[i] = unsameCount + 1;
        }
        return array;
    }

    /**
     * 头尾升序，中间 disorderCount 个反序
     */
    public static Integer[] headTailAscOrder(int min, int max, int disorderCount) {
        Integer[] array = ascOrder(min, max);
        if (disorderCount > array.length) return array;

        int begin = (array.length - disorderCount) >> 1;
        reverse(array, begin, begin + disorderCount);
        return array;
    }

    /**
     * 中心升序，头尾 disorderCount 个反序
     */
    public static Integer[] centerAscOrder(int min, int max, int disorderCount) {
        Integer[] array = ascOrder(min, max);
        if (disorderCount > array.length) return array;

        int left = disorderCount >> 1;
        reverse(array, 0, left);

        int right = disorderCount - left;
        reverse(array, array.length - right, array.length);
        return array;
    }

    /**
     * 头升序，尾 disorderCount 个反序
     */
    public static Integer[] headAscOrder(int min, int max, int disorderCount) {
        Integer[] array = ascOrder(min, max);
        if (disorderCount > array.length) return array;
        reverse(array, array.length - disorderCount, array.length);
        return array;
    }

    /**
     * 尾升序，头 disorderCount 个反序
     */
    public static Integer[] tailAscOrder(int min, int max, int disorderCount) {
        Integer[] array = ascOrder(min, max);
        if (disorderCount > array.length) return array;
        reverse(array, 0, disorderCount);
        return array;
    }

    /**
     * 升序数组 [min, max]
     */
    public static Integer[] ascOrder(int min, int max) {
        Integer[] array = new Integer[max - min + 1];
        for (int i = 0; i < array.length; i++) {
            array[i] = min++;
        }
        return array;
    }

    /**
     * 降序数组 [min, max]
     */
    public static Integer[] descOrder(int min, int max) {
        Integer[] array = new Integer[max - min + 1];
        for (int i = 0; i < array.length; i++) {
            array[i] = max--;
        }
        return array;
    }

    /**
     * 反转数组，索引范围 [begin, end)
     */
    private static void reverse(Integer[] array, int begin, int end) {
        int count = (end - begin) >> 1;
        int sum = begin + end - 1;
        for (int i = begin; i < begin + count; i++) {
            int j = sum - i;
            Integer tmp = array[i];
            array[i] = array[j];
            array[j] = tmp;
        }
    }

    /**
     * 复制数组
     */
    public static Integer[] copy(Integer[] array) {
        return Arrays.copyOf(array, array.length);
    }

    /**
     * 判断是否升序
     */
    public static boolean isAscOrder(Integer[] array) {
        if (array == null || array.length == 0) return false;
        for (int i = 1; i < array.length; i++) {
            if (array[i - 1] > array[i]) return false;
        }
        return true;
    }

    /**
     * 打印数组
     */
    public static void println(Integer[] array) {
        System.out.println("-------------------------------------");
        System.out.println(Arrays.toString(array));
    }
}
```

<!-- ### 5.2.Times.java

用于测试某段代码的执行时间。

```java
package utils;

import java.text.SimpleDateFormat;
import java.util.Date;

public class Times {
	private static final SimpleDateFormat fmt = new SimpleDateFormat("HH:mm:ss.SSS");

	public interface Task {
		void execute();
	}

    public static void test(String title, Task task) {
        if (task == null) return;

        title = (title == null) ? "" : ("【" + title + "】");
        System.out.println(title);

        // 打印可读开始时间
        Date startDate = new Date();
        System.out.println("开始：" + fmt.format(startDate));

        // 高精度计时开始
        long beginNano = startDate.getTime();
        task.execute();
        Date endDate = new Date();
        long endNano = endDate.getTime();

        // 打印可读结束时间
        System.out.println("结束：" + fmt.format(endDate));

        // 计算耗时，单位为秒，保留3位小数
        double deltaSeconds = (endNano - beginNano) / 1000.0;
        System.out.println("耗时：" + String.format("%.3f", deltaSeconds) + "秒");

        System.out.println("-------------------------------------");
    }

}
```

#### 使用方式

```java
Times.test("测试任务", new Times.Task() {
    @Override
    public void execute() {
        // 需要测试的代码
    }
});
``` -->