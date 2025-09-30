# 冒泡排序

## 1.流程

1. 从头开始比较每一对相邻元素，如果第 1 个比第 2 个大，就交换它们；
   
   执行完一轮后，最末尾那个元素就是最大的元素

2. 忽略第 ① 步中找到的最大元素，重复执行步骤 ①，直到全部元素有序

<video src="./imgs/1.mp4" controls></video>

## 2.实现

1. 先实现找出数组中最大的元素移动到最右边

    ```java
    // 直接从第 2 个元素开始比较
    for (int i = 1; i < arr.length; i++) {
        // 左边比右边大，则交换
        if (arr[i - 1] > arr[i]) {
            int tmp = arr[i];
            arr[i] = arr[i - 1];
            arr[i - 1] = tmp;
        }
    }
    ```

2. 思考两个问题
   
   - 一共要执行多少轮?（`arr.length` or `arr.length-1` ?）
   - 每轮比较都是从第一个元素到最后一个元素吗?

    ```java
    for (int i = 0; i < arr.length - 1; i++) { // 比较的轮数（每一轮都找出最大值）
        for (int j = 1; j < arr.length - i; j++) { // 比较次数
            if (arr[j] < arr[j - 1]) {
                int tmp = arr[j - 1];
                arr[j - 1] = arr[j];
                arr[j] = tmp;
            }
        }
    }
    ```

3. 换种理解
   
   `end`: 每轮比较应该把最大的元素放到哪个索引处
   
   `begin`: 最大值即为 end

   ```java
    for (int end = arr.length - 1; end > 0; end--) {
        // end>0，而不是 end>=0，是因为比较到最后时，第 1 个元素无需再比较了  
        for (int begin = 1; begin <= end; begin++) {
            if (arr[begin] < arr[begin - 1]) {
                int tmp = arr[begin - 1];
                arr[begin - 1] = arr[begin];
                arr[begin] = tmp;
            }
        }
    }
    ```

## 3.优化

### 3.1.**`优化①`**: 如果数组已经是有序的，可以提前终止排序

如何判断是否有序? `某一趟比较中如果没有发生交换，说明数组已经有序`

```java
for (int end = arr.length - 1; end > 0; end--) {
    // 标记本轮比较是否发生交换
    boolean isExchange = false;
    for (int begin = 1; begin <= end; begin++) {
        if (arr[begin] < arr[begin - 1]) {
            // 发生交换
            isExchange = true;
            int tmp = arr[begin - 1];
            arr[begin - 1] = arr[begin];
            arr[begin] = tmp;
        }
    }
    if (!isExchange) {
        break;
    }
}
```

### 3.2.**`优化②`**: 如果数组尾部已经有序，可以缩小比较范围

在`优化①`中，只有全部排好序时才能提前退出，这种概率是非常低的

如：[55, 64, 64, 25, 41, 2, 3, 70, 84, 98]，经过一轮比较后，`70, 84, 98` 已经是有序的了，
下一轮比较时是只需要再比较到元素`3`即可，如果是原本的算法，则依次比较到 `98、84、70`，这部分比较是没有意义的

如何判断尾部是否有序? `某一趟比较中最后一次发生交换的位置，其后面的元素都是有序的`

```java
for (int end = arr.length - 1; end > 0; end--) {
    int lastExchangeIndex = 0;
    for (int begin = 1; begin <= end; begin++) {
        if (arr[begin - 1] > arr[begin]) {
            lastExchangeIndex = begin;
            int tmp = arr[begin - 1];
            arr[begin - 1] = arr[begin];
            arr[begin] = tmp;
        }
    }
    /*
    等于 lastExchange 而不是 lastExchangeIndex-1，是因为 end 即将要自减 1
    lastExchangeIndex-1 是下一轮 begin 的最大值
    */
    end = lastExchangeIndex == 0 ? end : lastExchangeIndex;
}
```

