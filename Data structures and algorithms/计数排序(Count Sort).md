

#### 计数排序(Count Sort)
非比较排序。计数排序是**利用哈希原理，记录元素出现的频次。在统计结束后可以直接遍历哈希表，将数据填回数据空间**。由于是空间换时间，所以适合对数据范围集中的数据使用。而且由于用数组下标表示，**只适合只有正整数，0的数据**。

##### 算法描述
1、统计数组中元素出现的频次并找出极值。

2、填充原数组。
```ABAP
计数排序的算法步骤如下：
找出待排序的数组中最大和最小的元素；
统计数组中每个值为 i 的元素出现的次数，存入数组 C 的第 i 项；
对所有的计数累加（从数组 C 中的第一个元素开始，每一项和前一项相加）；
反向填充目标数组：将每个元素 i 放在新数组的第 C[i] 项，每放一个元素就将 C[i] 减去1。
```
##### 算法分析
`T(n)`=`O(n+k)`
注：`k`为范围，即新开辟的数组大小
```C++
void CountingSort(vector<int> &a)
{
    int len = a.size();
    if (len == 0)
        return;
    int Min = a[0], Max = a[0];
    for (int i = 1; i < len; i++)//找范围
    {
        Max = max(Max, a[i]);
        Min = min(Min, a[i]);
    }
    int bias = 0 - Min;
    vector<int> bucket(Max - Min + 1, 0);
    for (int i = 0; i < len; i++)//统计频次
    {
        bucket[a[i] + bias]++;//元素值既是数组下标
    }
    int index = 0, i = 0;
    while (index < len)
    {
        if (bucket[i])//从辅助数组的非0位置开始赋值到原数组
        {
            a[index] = i - bias;
            bucket[i]--;
            index++;
        }
        else
            i++;
    }
}
```