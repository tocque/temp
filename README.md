# 排序

排序是生活和软件开发中能遇到的最复杂的算法之一, 计算机中的算法

## 朴素排序

冒泡排序, 插入排序和选择排序是比较直观朴素的排序方法, 网上的资料很多, 而且其时间复杂度不尽如人意(均为$O(n^2)$)故这里就按下不表, 有兴趣可以点下面链接查看三种算法的动画, 或者到网上搜索其他教程

https://www.cs.usfca.edu/~galles/visualization/ComparisonSort.html

## 快速排序

快速排序是**基于比较**的排序方法中常数最小的一种, 它是一种不稳定的排序方法, 平均的时间复杂度为$O(n\log(n))$, 最坏情况下退化为$O(n^2)$

下面用伪代码简述一下其运行过程

```js
快速排序(序列) {
    if (序列中只有一个元素) return;
    支点 = 选取一个元素();
    [左半边, 右半边] = 按照是否大于支点将序列划分为两部分()
    快速排序(左半边);
    快速排序(右半边);
}
```

可以看出, 快排的关键在于将序列划分两部分, 再对两部分分别进行处理(实际上, 这就是分治思想), 由于左边任何元素都小于支点, 右边任何元素都大于支点, 故完成的序列有序

### 时间复杂度证明

用n表示序列大小, T()表示快速排序的时间复杂度
我们假设划分元素可以在$O(序列大小)$内完成, 且每次划分的两部分大小相等, 则整个算法的时间复杂度可以表示为

$T(n) = O(n) + 2T(n/2)$

容易发现这个递归只会进行$\log_2(n)$次, 每一层的时间复杂度都是$O(n)$, 故总的时间复杂度为$O(n\log_2(n))$, 推广来看, 若划分两部分的比值为a:b, 则时间复杂度为$O(n\log_{\frac{a+b}{\max(a,b)}}(n))$, 若a:b控制在一定范围内, 仍然是$O(n\log(n))$级别的, 而最极端的情况发生在每次划分均为1, n-1时, 此时算法需进行n次, 时间复杂度显然退化为$O(n^2)$

那么, 如何才能在$O(n)$内划分序列呢, 这个的实现比较巧妙, 下面给出划分操作的伪代码

```js
划分序列(序列, 支点) {
    while(true) {
        a = 从左侧开始找到一个比支点大的元素();
        b = 从右侧开始找到一个比支点小的元素();
        if (a在b的左边) {
            交换a, b;
        } else break;
    }
}
```

相对而言, 找到一个能均匀划分序列的支点非常困难, 因而涌现了一系列寻找支点的算法, 此处不表, 有兴趣可以在网上查找相关资料

### 代码实现

```c++
void quicksort(int a[], int l, int r) {
    if (l >= r) return;
    int mid = (a[l]+a[r]) >> 1; // 此处取左右端点的平均数为支点
    int i = l, j = r;
    while(true) {
        while(a[i] < mid && i <= r) i++;
        while(a[j] > mid && l <= j) j--;
        if(i <= j) {
            swap(a[i],a[j]);
            i++; j--;
        } else break;
    }
    quicksort(a, l, j);
    quicksort(a, i, r);
}
```

实际上, 在ACM比赛中, 并不会有手写快排的场景, 因为STL里的sort函数相当方便, 这个sort的实现非常复杂, 在快排的基础上使用了大量优化和检查防止复杂度退化

## 归并排序

与快速排序相比, 归并排序是一种稳定的算法, 总能拥有$O(n\log(n))$的优秀性能

```js
// 归并排序的伪代码实现
归并排序(序列) {
    if (序列中只有一个元素) return;
    [左半边, 右半边] = 分割为大小相等的两部分()
    归并排序(左半边);
    归并排序(右半边);
    合并(左半边, 右半边);
}
```
### 时间复杂度证明

由于归并排序划分的两部分始终是相等规模的, 因此总能拥有$O(n\log_2(n))$的排序性能

### 实现

```c++
void mergesort(int a[], int l, int r) {
    if (l >= r) return;
    int mid = (a[l]+a[r]) >> 1; // 此处取左右端点的平均数为支点
    int i = l, j = r;
    while(true) {
        while(a[i] < mid && i <= r) i++;
        while(a[j] > mid && l <= j) j--;
        if(i <= j) {
            swap(a[i],a[j]);
            i++; j--;
        } else break;
    }
    quicksort(a, l, j);
    quicksort(a, i, r);
}
```

与快速排序不同的是, 归并排序在算法竞赛中是有运用场景的/fad, 在分治一节中将会提到几个相关的运用场景

## 计数排序

计数排序是算法竞赛中常见的排序方法, 即用数组统计每个元素的出现次数, 从小到大遍历数组, ,这种排序方法相当朴素, 因此不做赘述, 直接展示其实现

```c++
int W = 1e6; // 待排序元素的最大值
void counting_sort(vector<int> &a, vector<int> rank) {
    vector<int> cnt(W);
    for (auto i: a) cnt[i]++;
    for (int i = 1; i < W; ++i) cnt[i] += cnt[i - 1];
    for (int i = n; i >= 1; --i) {
        rank[cnt[a[i]]--] = a[i];
    }
}
```

可以容易的看出, 这种排序方法的时间复杂度是$O(n)$, 同时, 它的使用限制非常大, 只能解决特定的排序问题

## 桶排序

桶排序可以被理解为计数排序的高级版, 或者一种分治型排序, 在桶排序中, 先将每个元素放入对应的桶中, 对桶进行排序, 再按顺序把桶中的元素取出进行合并, 由于划分桶的规则可以自己定义, 即使对于浮点数等情形也可以完成划分

## 基数排序

在WC2017中曾经出现过一道毒瘤卡常题https://www.luogu.com.cn/problem/P4604, 要求在6s内对2e8个整数进行排序, 这样的恐怖要求, $O(n\log(n))$的快速排序根本是难以完成, 此题的正解是使用基数排序, 配合足够的常数优化

基数排序的前提是两个元素可以通过以下方法进行比较

```js
比较(元素A, 元素B) {
    for(关键字 of 关键字列表) {
        if (元素A.关键字 != 元素B.关键字) {
            return 元素A.关键字 < 元素B.关键字;
        }
    }
}
```
一个例子是字典序的比较, 比较十进制数也可以用这种方法, 譬如比较1453和1926时, 我们可以先比较千位(1==1), 然后比较百位(4<9), 得出1453 < 1926的结论

基数排序的流程是

```js
基数排序(序列) {
    forEach(关键字) {
        按照关键字对序列进行排序()
    }
}
```

基数排序的关键在于按照关键字的排序, 在常见的基数排序场景中, 这个排序是使用计数排序/桶排序实现的, 在上面那道题中, 整数被分解为四个关键字, 每个关键字为8位

### 时间复杂度证明

显然, 基数排序的复杂度是$O(nk)$ k为常数, 即关键字的数量

### 代码实现

```c++
// 来自OIWIKI
const int N = 100010;
const int W = 100010;
const int K = 100;

int n, w[K], k, cnt[W];

struct Element {
  int key[K];
  bool operator<(const Element& y) const  // shows how two elements are compared
  {
    for (int i = 1; i <= k; ++i) {
      if (key[i] == y.key[i]) continue;
      return key[i] < y.key[i];
    }
    return false;
  }
} a[N], b[N];

void counting_sort(int p) {
  memset(cnt, 0, sizeof(cnt));
  for (int i = 1; i <= n; ++i) ++cnt[a[i].key[p]];
  for (int i = 1; i <= w[p]; ++i) cnt[i] += cnt[i - 1];
  for (int i = 1; i <= n; ++i) b[cnt[a[i].key[p]]--] = a[i];
  memcpy(a, b, sizeof(a));
}

void radix_sort() {
  for (int i = k; i >= 1; --i) {
    counting_sort(i);
  }
}
```

# 分治

分治是一种算法思想, 即将大的数据规模分解成小的部分进行计算, 然后再合并各个部分的计算结果
```js
分治(数据) {
    if (可以直接得出结果) return 结果;
    结果1 = 分治(一部分数据);
    结果2 = 分治(一部分数据);
    ...
    return 合并结果(结果1, 结果2...);
}
```
,在排序一节中业已证明过这种拆分的优异时间复杂度

## 场景: 统计逆序数对

逆序数对是指, 在序列$a_1, a_2 ...a_n$中, 满足$a_i < a_j(i > j)$的$(i, j)$

一种朴素的想法是遍历所有的数对, 以$O(n^2)$的代价进行统计, 不过如果我们利用分治思想, $O(n\log(n))$足以完成这一任务

实际上, 使用的方法正是归并排序
回想一下归并排序的流程, 在合并两个序列时, 对于右侧序列的每一个元素而言, 左侧序列中比它大的元素, 就是在它放入时, 左侧序列中剩余的元素, 因此, 只要依次累加

在去年的校赛中无敌mps就出过一道逆序数对的题目http://47.106.114.75/problem/498, 有兴趣的同学可以尝试一下

## 场景: 求最大子段和

求最大子段和, 即在一个序列中找到连续的, 和最长的子段, 与求逆序数对类似, 一种朴素的想法可以是枚举每个序列进行统计$O(n^3)$(前缀和优化$O(n^2)$)

现在使用分治的思想来设计算法, 首先可以试着拆分序列, 分别统计两部分的最大子段和, 但是我们会发现, 答案可能是在横跨两部分的, 因此, 还需要额外统计两个部分与边界相连的子段的最值

## CDQ分治(简单提一下)

CDQ分治是一种较为特别的分治方法, 由我国算法选手陈丹琦提出, 它可以将线段树/树状数组等区间操作转变为离线的分治操作, 拥有更优秀的常数和更小的内存消耗

### 敌兵布阵

题面:

敌人有N个工兵营地，第$i$个工兵营地里开始时有$a_i$个人($1\le a_i\le50$), 给出3种操作:

(1) Add $i j$, 第$i$个营地增加$j$个人($j\le30$)）

(2) Sub $i j$, 第$i$个营地减少$j$个人($j\le30$);

(3) Query $i j$ , $i \le j$, 询问第$i$到第$j$个营地的总人数;

这道题在上学期新生培训中出现过, 当时是一道分块题, 现在以CDQ分治的方式重新考虑一下

我们可以将每条修改操作看成一个二元组(时间, 位置), 因此查询操作, 相当于统计出现在其之前出现, 位置在$i, j$之间的修改操作的影响, 加上原始数组中$[i, j]$的区间和, 这与之前提到的统计逆序对非常相似

### 陌上花开

题面:

有 $n$ 个元素，第 $i$ 个元素有$a_i,b_i,c_i$
  三个属性，设$f(i)$表示满足 $a_j \leq a_i, b_j \leq b_i, c_j \leq c_i, j \ne i$ 的 $j$ 的数量。

对于 $d \in [0, n)$，求 $f(i) = d$ 的数量。


这是一道经典的CDQ分治题目, 可以在https://www.luogu.com.cn/problem/P3810进行尝试

可以看出此题即是上一题的加强版, 元素拥有三个关键字, 那么, 此时应该如何处理呢, 我们可以尝试使用其他数据结构对一维进行处理, 譬如我们对上一题使用过分块, 也可以使用线段树/树状数组进行处理

思路
```js
排序by关键字a(序列);
new 线段树(序列);
solve(序列) {
    左序列 = solve(左半部分);
    右序列 = solve(右半部分);
    return 合并by关键字b(左序列, 右序列, (元素) => {
        if (元素 in 左序列) {
            线段树.增加(元素.c, 1);
        } else {
            f(元素) += 线段树.查询(0, 元素.c);
        }
    });
}

```

由于尚未讲过线段树/树状数组, 这一部分看看即可, 若有兴趣可以尝试实现代码
