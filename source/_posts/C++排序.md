---
title: C++排序
categories: 学习
tags:
  - C++
  - 排序
description: 整理了C++的十大排序算法
top_img: 'https://cdn.jsdelivr.net/gh/sirosaqqara/picHosting/img/2022/04/11/135619.jpeg'
abbrlink: 2188
date: 2022-04-08 21:22:37
---

# 常用排序

> <center style="color:#C0C0C0">所有算法均为C++，实现了各类排序的基本功能</center>

## 算法概述

### 算法分类

常见的排序算法可以分为一下两大类：

* **非线性时间比较类**：通过比较来决定元素间的相对次序，由于其时间复杂度不能突破O(nlogn)，因此称为非线性时间比较类排序。
* **线性时间非比较类**：不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此称为线性时间非比较类排序。

![](https://cdn.jsdelivr.net/gh/sirosaqqara/picHosting/img/2022/03/29/142345.png)

### 时间复杂度比较

![](https://cdn.jsdelivr.net/gh/sirosaqqara/picHosting/img/2022/03/29/142535.png)

<center style="color:#C0C0C0">仅供参考</center>

### 相关概念

<span style="background:#FFDBBB;">稳定</span>：如果a原本在b前面，而a=b，排序之后a仍然在b的前面。

<span style="background:#FFDBBB;">不稳定</span>：如果a原本在b的前面，而a=b，排序之后 a 可能会出现在 b 的后面。

<span style="background:#FF9999;">时间复杂度</span>：对排序数据的总的操作次数。反映当n变化时，操作次数呈现什么规律。

<span style="background:#FF9999;">空间复杂度</span>：是指算法在计算机内执行时所需存储空间的度量，它也是数据规模n的函数。

## 快速排序 quickSort()

### 算法描述

快速排序使用分治法来把一个串（list）分为两个子串（sub-lists）。具体算法描述如下：

* 从数列中挑出一个元素，称为 “基准”（pivot）。
* 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作。
* 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。

### 具体实现

```C++
void quickSort(vector<int>* vec, int left, int right){
    //返回条件，如果vec为空或者左右指针位置相同或相反则返回
    if(vec->empty()) return;
    if(left >= right) return;

    //取随机位置首先与作比较值的元素交换，防止有序序列引起的效率低下
    srand(unsigned(time(NULL)));
    int temp = rand()%(right-left+1)+left;
    swap((*vec)[left], (*vec)[temp]);

    //初始化，存储vec[left]作为比较值以及left和right
    int key = (*vec)[left],  i = left, j = right;

    //主循环
    while(i < j){
        //首先右指针向左找到第一个小于比较值的元素vec[j]
        //找到后将i指向的元素改为vec[j]
        //接着左指针向右找到第一个大于比较值的元素vec[i]
        //找到后将j指向的元素改为vec[i]
        //直到i和j相遇
        while((*vec)[j] > key && i < j) j--;
        (*vec)[i] = (*vec)[j];
        while((*vec)[i] <= key && i < j) i++;
        (*vec)[j] = (*vec)[i];
    }

    //退出时i和j应该是同一个位置，此时将key放在此位置
    (*vec)[i] = key;

    //递归对左右两部分进行quickSort()
    quickSort(vec, left, i-1);
    quickSort(vec, j+1, right);
}
```

## 冒泡排序 bubbleSort()

### 算法描述

冒泡排序是稳定排序

* 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
* 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；
* 针对所有的元素重复以上的步骤，除了最后一个；
* 重复步骤1~3，直到排序完成。

### 具体实现

```C++
void bubbleSort(vector<int>* vec){
    if(vec->empty()) return;
    
    //循环遍历，比较相邻元素
    for(int i = 0; i < vec->size(); i++){
        for(int j = 0; j < vec->size()-1; j++){
            if((*vec)[j] >= (*vec)[j+1]) swap((*vec)[j], (*vec)[j+1]);
        }
    }
}
```

## 希尔排序 shellSort()

### 算法描述

<span style="background:#FF9999;">希尔排序是[插入排序](#insertionSort)的优化版，但不是稳定排序</span>

先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，具体算法描述：

* 选择一个增量序列$t1，t2，…，tk$，其中$ti>tj，tk=1$；
* 按增量序列个数$k$，对序列进行$k$趟排序；
* 每趟排序，根据对应的增量$ti$，将待排序列分割成若干长度为$m$的子序列，分别对各子表进行直接插入排序。仅增量因子为$1$时，整个序列作为一个表来处理，表长度即为整个序列的长度。

### 具体实现

```c++
void shellSort(vector<int>* vec, int step){
    if(step == 0) return;
    
    //与插入排序相比多了步长的条件
    for(int k = 0; k < step; k++){
        for(int i = step + k; i < vec->size(); i += step){
            for(int j = i; j > k; j -= step){
                if((*vec)[j] <= (*vec)[j - step]) swap((*vec)[j], (*vec)[j - step]);
            }
        }
    }
    
    //步长减半，递归执行
    shellSort(vec, step/2);
}
```

<a id="insertionSort"></a>
## 插入排序 insertSort()

### 算法描述

直接插入排序（straight insertion sort），有时也简称为插入排序（insertion sort），是减治法的一种典型应用。其基本思想如下：

* 对于一个数组$A[0,n]$的排序问题，假设认为数组在$A[0,n-1]$排序的问题已经解决了。
* 考虑$A[n]$的值，从右向左扫描有序数组$A[0,n-1]$，直到第一个小于等于$A[n]$的元素，将$A[n]$插在这个元素的后面。

很显然，基于增量法的思想在解决这个问题上拥有更高的效率。

直接插入排序对于最坏情况（严格递减的数组），需要比较和移位的次数为$\frac{n(n-1)}{2}$；对于最好的情况（严格递增的数组），需要比较的次数是$n-1$，需要移位的次数是0。当然，对于最好和最坏的研究其实没有太大的意义，因为实际情况下，一般不会出现如此极端的情况。然而，直接插入排序对于基本有序的数组，会体现出良好的性能，这一特性，也给了它进一步优化的可能性。（希尔排序）。直接插入排序的时间复杂度是$O(n^2)$，空间复杂度是$O(1)$，同时也是稳定排序。

### 具体实现

```c++
void insertSort(vector<int>* vec){
    if(vec->empty()) return;
    
    //循环遍历，满足条件就前插
    for(int i = 0; i < vec->size(); i++){
        for(int j = i; j > 0; j--){
            if((*vec)[j] < (*vec)[j-1]) swap((*vec)[j], (*vec)[j-1]);
            else break;
        }
    }
}
```

## 堆排序 heapSort()

### 算法描述

堆排序实际上是利用堆的性质来进行排序的，要知道堆排序的原理首先一定要知道什么是堆
堆的定义：
堆实际上是一棵完全二叉树。
堆满足两个性质:

1. 堆的每一个父节点都大于（或小于）其子节点。
2. 堆的每个左子树和右子树也是一个堆。

堆分为两类：

1. 最大堆（大根堆）：堆的每个父节点都大于其孩子节点。

2. 最小堆（小根堆）：堆的每个父节点都小于其孩子节点。

堆的存储：
一般都用数组来表示堆，i结点的父结点下标就为$(i – 1) / 2$，它的左右子结点下标分别为$2 *i + 1和2* i + 2$。如下图所示：

![](https://cdn.jsdelivr.net/gh/sirosaqqara/picHosting/img/2022/03/29/185022.png)

根据堆的性质可知，堆顶的元素要么是最大值，要么是最小值。则在排序的时候可以利用这个性质，将第一个元素与最后一个元素交换，接着调整堆的前$n-1$个元素（假设原来有$n$个元素），之后重复这个步骤即可。

### 具体实现

```c++
//调整堆（大根堆），从上往下调整，传入堆指针vec, 调整位置adj， 调整长度len
void adjustHeap(vector<int>* vec, int adj, int len){
    //存储左孩子
    int child = adj * 2 + 1;

    //自上而下
    while(child < len){
        
        //当存在右孩子且右孩子比左孩子大时，child改为右孩子
        if(child + 1 < len && (*vec)[child] < (*vec)[child + 1]) child++;
        
        //如果调整结点更大则说明不需要调整，直接退出
        if((*vec)[adj] >= (*vec)[child]) break;

        //进行调整，交换调整结点和孩子结点
        swap((*vec)[adj], (*vec)[child]);

        //将调整结点赋为孩子结点，孩子结点赋为修改后的调整结点的左孩子
        adj = child;
        child = adj * 2 + 1;
    }
}

//建堆，传入待建堆（用vector表示堆）
void makeHeap(vector<int>* vec){
    
    //从最后一个非叶子结点开始，循环向上执行adjustHeap()
    for(int i = vec->size()/2; i >= 0; --i){
        adjustHeap(vec, i, vec->size());
    }
}

//堆排序，传入待排序vector
void heapSort(vector<int>* vec){
    if(vec->empty()) return;

    //先建堆
    makeHeap(vec);
    int len = vec->size();

    //从最后一个结点开始，每次先将堆顶与其交换，接着调整除最后一个结点外的堆
    for (int i = len-1; i >= 0; --i) {
        swap((*vec)[0], (*vec)[i]);
        adjustHeap(vec, 0, i);
    }
}
```

## 选择排序 selectSort()

### 算法描述

与堆排序一样，选择排序也可以一次将一个元素送到最终位置，其工作原理很容易理解：

1. 在序列中找到最小元素，放到序列的起始位置，作为已排序部分。
2. 在未排序部分找到最小元素，放到已排序部分的末尾，重复第二步，直到所有元素均处于已排序部分。

### 具体实现

```c++
void selectSort(vector<int>* vec){

    for(int i = 0; i < vec->size()-1; i++){
        int min = i;
        for(int j = i + 1; j < vec->size(); j++){
            min = (*vec)[j] < (*vec)[min] ? j : min;
        }
        swap((*vec)[min], (*vec)[i]);
    }
}
```

## 归并排序 mergeSort()

### 算法描述

归并排序是利用分治的思想将问题分成一些小的子问题递归求解，其算法步骤可分为：

1. 申请空间，大小为两个已排序序列之和，用于存放合并后的序列
2. 设定两个指针，最初位置分别是两个已排序序列的起始位置
3. 比较元素，选择小值放入合并序列，并移动指针到下一个位置，重复该步骤直到某一指针到序列尾
4. 另一序列剩下的元素直接复制到合并序列尾。

### 具体实现

```c++
void merge(vector<int>* vec, int left, int mid, int right){

    vector<int> leftSubVector(vec->begin() + left, vec->begin() + mid + 1);
    vector<int> rightSubVector(vec->begin() + mid + 1, vec->begin() + right + 1);

    int idLeft = 0, idRight = 0;

    leftSubVector.insert(leftSubVector.end(), INT_MAX);
    rightSubVector.insert(rightSubVector.end(), INT_MAX);

    for (int i = left; i <= right; i++) {
        if(leftSubVector[idLeft] < rightSubVector[idRight]){
            (*vec)[i] = leftSubVector[idLeft];
            idLeft++;
        }else {
            (*vec)[i] = rightSubVector[idRight];
            idRight++;
        }
    }
}

void mergeSort(vector<int>* vec, int left, int right){
    if(left >= right) return;

    int mid = (left + right) / 2;
    mergeSort(vec, left, mid);
    mergeSort(vec, mid + 1, right);
    merge(vec, left, mid, right);
}
```

## 基数排序 radixSort()

### 算法描述

基数排序是一种非比较型整数排序算法，其原理是将整数按位数切割成不同的数字，然后按每个位数分别比较。由于整数也可以表达字符串（比如名字或日期）和特定格式的浮点数，所以基数排序也不是只能使用于整数。

基数排序借助额外的空间（存储0~9各自出现的次数以及临时排序的`vector`）进行排序，使得效率可以达到线性时间复杂度。

### 具体实现

```c++
void radixSort(vector<int>* vec){

    if(vec->size() < 2) return;

    //获取最大位数
    int bit = maxBit(vec);
    
    //count用于存储基数出现的次数，temp用于临时存储当次基数排序结果
    vector<int> temp(vec->size());
    vector<int> count(10);

    int radix = 1, j = 0, k = 0;

    //迭代bit次
    for(int i = 0; i <= bit; i++){
        
        //count清零
        for(j = 0; j < 10; j++) count[j] = 0;
        
        //count计数
        for(j = 0; j < vec->size(); j++){
            k = ((*vec)[j] / radix) % 10;
            count[k]++;
        }

        //对所有计数累加，方便后面temp存储，count[j]此时表示前面一共多少个位置
        for(j = 1; j < 10; j++) count[j] += count[j-1];

        //倒序遍历vec，并将结果保存在temp中，此时即为按基数排序状态
        for(j = vec->size() - 1; j >= 0; j--){
            k = ((*vec)[j] / radix) % 10;
            
            //这里存放的位置就是当位的最后一个位置
            //因为count是表示0~9，所以temp中的结果应该是按照基数从大到小的
            //这样比完一轮后，后面赋回vec，进行下一轮时上次的基数大值应该同样在基数小值前面
            //即插入当位的最后一个位置
            temp[count[k] - 1] = (*vec)[j];
            count[k]--;
        }

        //temp赋回vec
        for(j = 0; j < vec->size(); j++) (*vec)[j] = temp[j];
        
        //提一位
        radix *= 10;
    }
}
```

## 计数排序 countSort()

### 算法描述

计数排序有点类似基数排序~~（甚至名字都差不多）~~，都是基于额外的空间完成，达到线性的时间复杂度。

计数排序直接存储唯一键值，通过统计键值出现的次数，最后排序。这也要求了数据范围尽量小且重复度较高，否则会浪费空间。

算法步骤如下：

1. 找出待排序的数组中最大和最小的元素
2. 统计数组中每个值为$i$的元素出现的次数，存入`vector C`的第$i$项
3. 对所有的计数累加（从`vector C`中的第一个元素开始，每一项和前一项相加）
4. 反向填充目标数组：将每个元素$i$放在新数组的第$C[i]$项，每放一个元素就将$C[i]$减去$1$

### 具体实现

```c++
void countSort(vector<int>* vec){
    if(vec->size() < 2) return;

    int maxData = (*vec)[0], i = 0;
    while(i < vec->size()){
        if(maxData < (*vec)[i]) maxData = (*vec)[i];
        i++;
    }

    vector<int> count(maxData+1, 0);
    vector<int> temp(*vec);

    for(auto x: *vec){
        count[x]++;
    }

    for(i = 1; i <= maxData; i++) count[i] += count[i-1];
    for(i = vec->size()-1; i >= 0; i--){
        (*vec)[count[temp[i]]] = temp[i];
        count[temp[i]]--;
    }
}
```

------

## 桶排序 bucketSort()

### 算法描述

桶排序是计数排序的升级版。它利用了函数的映射关系，高效与否的关键就在于这个映射函数的确定。桶排序的工作的原理：假设输入数据服从均匀分布，将数据分到有限数量的桶里，每个桶再分别排序。（有可能再使用别的排序算法或是以递归方式继续使用桶排序）

假设有$n$个数字，$m$个桶，如果数字是平均分布的，则每个桶里面平均有$\frac{n}{m}$个数字。如果对每个桶中的数字采用快速排序，那么整个算法的复杂度是：
$$
O(n + m \times \frac{n}{m} \times log_2(\frac{n}{m})) = O(n + nlog_2n – nlog_2m)
$$
当$m$趋近于$n$时，最佳的时间复杂度可以达到$O(n)$

### 具体实现

```C++
void insert(list<int>& bucket, int val){
    auto iter = bucket.begin();
    while(iter != bucket.end() && val >= *iter) ++iter;
    bucket.insert(iter, val);
}

void bucketSort(vector<int>* vec){
    
    if(vec->size() < 2) return;
    
    int len = vec->size();
    int minData = (*vec)[0], maxData = minData, i = 0, index = 0;

    for(i = 0; i < len; i++){
        if(minData > (*vec)[i]) minData = (*vec)[i];
        if(maxData < (*vec)[i]) maxData = (*vec)[i];
    }

    int k = 10;
    int bucketNum = (maxData - minData) / k + 1;
    vector<list<int>> buckets(bucketNum);

    for(i = 0; i < len; i++) insert(buckets[((*vec)[i] - minData) / k], (*vec)[i]);

    for(i = 0; i < bucketNum; i++){
        if(buckets[i].size()){
            for(auto& val: buckets[i]) (*vec)[index++] = val;
        }
    }
}
```

### 关于桶排序

<span style="background:#FF9999;">基数排序</span>，<span style="background:#FF9999;">计数排序</span>和<span style="background:#FF9999;">桶排序</span>都是通过利用<span style="background:#bbffff;">额外的空间</span>辅助来达到线性时间复杂度的，这种额外的空间称为桶，也就是说，这三种排序方法都用到了桶，只不过桶的功能有明显差异：

* 基数排序：根据键值的每位数字来分配桶；
* 计数排序：每个桶只存储单一键值；
* 桶排序：每个桶存储一定范围的数值。
