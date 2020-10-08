
#### 十大排序算法
![sort](https://github.com/hgsw/Sort/blob/6f1e2c39cb85fa270478a0335f99b8eee9ae50a6/sort.png)
因为排序算法十分常用，所以本人用一周的时间学习总结最常用的排序算法，首先说明
- 本部分的排序都是用java实现的
- 所有的排序都是针对整型数据进行升序学习的
- 并记录部分排序算法的优化

稳定性说明：当对原始数据进行排好序后，原数据同一元素的相对位置发生改变，则称其实不稳定的排序，否则就是稳定的排序。

**1 、冒泡排序（bubble sort）**

**从头开始比较每一对相邻的元素，如果第一个比第二大就交换他们的位置。**

```java
public bubbleSort(int[] arr){
    for(int i=0;i<arr.length;i++)
        for(int j=1;j<arr.length-i;j++)
            if(arr[j]<arr[j-1]){
                int tem=arr[j];
                arr[j]=arr[j-1];
                arr[j-1]=tem;
            }
}
```

每一轮扫描都要记录最后一次交换的位置，后面就不用交换了。下一次扫描到上一次记录的位置即可。

```java
public bubbleSort(int[] arr){
    for(int i=arr.length-1;i>0;i--) {
        int last = 1;//选择1是因为如果arr有序，则直接结束
        for (int j = 1; j <= i; j++) {
            if (arr[j] < arr[j - 1]) {
                int tem = arr[j];
                arr[j] = arr[j - 1];
                arr[j - 1] = tem;
                last=j;
            }
        }
        i=last;
    }
}
```

冒泡排序是稳定的，最好O(n)，平均时间复杂度是O(n^2)。

**2、选择排序（selection sort）**

**从序列中找到最大的那个元素，然后和最末尾的元素进行交换**

```java
public  void selectionSort(int[] arr){
    for(int end=arr.length-1;end>0;end--) {
        int maxIndex = 0;
        for (int j = 1; j <= end; j++) {
            if (arr[maxIndex] <= arr[j])//等号是增加其稳定性
                maxIndex=j;
        }
        int tem=arr[maxIndex];
        arr[maxIndex]=arr[end];
        arr[end]=tem;
    }
}
```

选择排序不一定是稳定的，最好，最坏，平均时间复杂度是O(n^2)。

比如：7 5 10 10 2 4 2   //两个10的相对位置可能会发生变化

**3、堆排序（heap sort）**

**堆排序可以看成是选择排序的优化。**

步骤：

1、对数组进行原地建堆（heapify）

2、重复以下操作，直到堆的大小为1

交换堆顶元素与尾元素，堆的大小减一，对0位置进行siftDown操作

```java
public static void heapSort(int []arr){
    for(int i=parent(arr.length-1);i>=0;i--) //构建大顶堆
        shifDown(arr,i,arr.length);
    for(int j=arr.length-1;j>0;j--){
        swap(arr,0,j);       //将堆顶元素与末尾元素进行交换
        shifDown(arr,0,j);  //对0位置到j进行siftDown操作
    }
}
public static int parent(int i){
    if(i==0)
        throw new IllegalArgumentException("error");
    return (i-1)/2;
}
public static int leftChild(int i){return i*2+1;}
public static int rightChild(int i){return i*2+2;}

public static void shifDown(int []arr,int start,int end){
    while(leftChild(start)<end){//保证左子树节点还在有效的数组范围内
        int j=leftChild(start);	//右孩子在数组中对应的索引
        if(j+1<end&&arr[j+1]>arr[j])
            j++;
        if(arr[start]>=arr[j])//当前节点已经大于左右孩子的节点
            break;
        swap(arr,start,j);
        start=j;
    }
}
public static void swap(int []arr,int k ,int j){
    int temp=arr[k];
    arr[k] = arr[j];
    arr[j] = temp;
}
```

堆排序不是稳定的，最好，最坏，平均时间复杂度是O(nlogn)。

**4、插入排序（insertion sort）**

**从第二个元素开始向后扫描，然后比较每一个元素，将该元素插到前面合适的位置。**

```java
public static void insertionSort(int[] arr){
    for(int i=1;i<arr.length;i++){
        int cur=i;
        while(cur>0&&arr[cur]<arr[cur-1]){
            int tem=arr[cur];
            arr[cur]=arr[cur-1];
            arr[cur-1]=tem;
            cur--;
        }
    }
}
```

插入排序是稳定的，插入排序的时间复杂度和逆序对成正比。最坏的时间复杂度O(n^2),最好的时间复杂度O(n)，平均时间复杂度(n^2)

**优化1（针对逆序对较多的情况）**

思路将交换转变成挪动

1）先将待插入的元素备份，2）将待插入元素前面所有比待插入元素大的值整体向后挪一位，3）将待插入元素插到适合的位置。

```java
public static void insertionSort2 ( int[] arr){
    for (int i = 1; i < arr.length; i++) {
        int cur = i;
        int data = arr[cur];
        //优化语句，因为这里要挪位置，想到用二分查找来优化比较次数
        while (cur > 0 && data < arr[cur - 1]) {
            arr[cur] = arr[cur - 1];
            cur--;
        }
        arr[cur] = data;
    }
}
```

**优化2（大量减少交换的次数）**

利用二分查找，将返回第一个大于待插入元素的位置(保持稳定性)

```java
public static void insertionSort3 ( int[] arr){
    for (int i = 1; i < arr.length; i++) {
        int data = arr[i];//备份数据
        int insertIndex=search(i);//利用二分查找寻找索引
        for(int k=i;k>insertIndex;k--)//挪动位置
            arr[k]=arr[k-1];
        arr[insertIndex]=data;//插入数据
    }
}
```

```java
public static int search(int index) {
    int left=0;
    int right=index;
    while (left < right) {
        int mid = (left + right)>>1;
        if (arr[index] < arr[mid])
            right = mid;
        else
            left = mid + 1;
    }
    return left;
}
```

**5、归并排序(merge sort)**

1945年冯诺依曼提出，思路是先将数组分割到两个数，排好序后再合并。

```java
public void merge(int[] arr, int start, int end) {
    if (start == end) return;
    int mid = (start + end)/2;
    merge(arr, start, mid);
    merge(arr, mid + 1, end);

    int[] temp = new int[end - start + 1];
    int i = start, j = mid + 1, k = 0;
    while(i <= mid && j <= end)
        temp[k++] = arr[i] < arr[j] ? arr[i++] : arr[j++];
    while(i <= mid)
        temp[k++] = arr[i++];
    while(j <= end)
        temp[k++] = arr[j++];
    System.arraycopy(temp, 0, arr, start, end-start+1);
}
```

归并排序是稳定的，归并排序的最好，最坏，平均时间复杂度均为O(nlogn)，空间复杂度O(n)+O(logn)创建临时数组保存数据的空间复杂度为O(n)，递归调用所需要的空间复杂度为递归调用次数O(logn)，总的空间复杂度为O(n)

时间复杂度分析：

![image-20200928174111108](C:\Users\hgsw9\AppData\Roaming\Typora\typora-user-images\image-20200928174111108.png)

常见地推公式和复杂度：

![image-20200928174205272](C:\Users\hgsw9\AppData\Roaming\Typora\typora-user-images\image-20200928174205272.png)

**6、快速排序（quick sort）**

1960年东尼·霍尔提出的，其本质将每一个元素转化为轴点元素。

1) 从序列中选出一个轴点元素(pivot)

2) 利用轴点元素将小于轴点的元素放在轴点的左侧，大于的元素放右侧，等于的两边都可以

2) 对子序列继续1)，2)的操作

```java
public static void quickSort(int[] arr,int begin,int end){//[begin,end]
    if(begin>=end)
        return;
    int mid=pivotIndex(arr,begin,end);
    quickSort(arr,begin,mid-1);
    quickSort(arr,mid+1,end);
}
private static int pivotIndex(int[] arr,int left,int right){//[left,right]
    //备份begin元素
    //可以降低最坏的情况
    //swap(left,left+(int)Math.random()*(right-left))//如果元素都是一样的还是O(n^2)
    int pivot=arr[left];
    int j=left;
    for(int i=left+1;i<=right;i++){
        if(arr[i]<pivot){
            j++;
          swap(i,j);
        }
    }
 	swap(arr,left,j);
    return j;
}
public static void swap(int[]arr, int i,int j){
    int tem=arr[i];
    arr[i]=arr[j];
    arr[j]=tem;
}
```

另外一种写法

```java
public static void quickSort(int[] arr,int begin,int end){
    if(begin>=end)
        return;
    //确定轴点
    int mid=pivotIndex(arr,begin,end);
    quickSort(arr,begin,mid-1);
    quickSort(arr,mid+1,end);
}
//构造轴点
private static int pivotIndex(int[] arr,int left,int right){//[left,right]
    //备份begin元素
    int pivot=arr[left];
    while(left<right){
        while(left<right){
            if(arr[right]>pivot)
                right--;
            else {//右边元素<= 轴点元素，用right的元素覆盖left的元素,left++
                arr[left]=arr[right];
                left++;
                break;
            }
        }
        while (left<right){
            if(arr[left]<pivot)
                left++;
            else {
                arr[right]=arr[left];
                right--;
                break;
            }
        }
    }
    arr[left]=pivot;//将轴点元素归位
    return left;
}
```

快速排序是不稳定的，快速排序最坏的时间复杂度为O(n^2),平均和最好都是O(nlogn)

**优化双路快排**

```java
public static void qiuckSort2Ways(int[] arr,int left,int right){
    if(left>=right)
        return;
    int mid=pivotIndex2Ways(arr,left,right);
    qiuckSort2Ways(arr,left,mid-1);
    qiuckSort2Ways(arr,mid+1,right);
}
```

```java
private static int pivotIndex2Ways(int[] arr,int left,int right){
    swap(arr,left,left+(int)Math.random()*(right-left));
    int pivot=arr[left];
    //arr[left+1...i]<=pivot,arr[j...right]>=pivot
    int i=left+1,j=right;
    while(true){
        while(i<=right&&arr[i]<pivot)
            i++;
        while(j>left+1&&arr[j]>pivot)
            j--;
        if(i>j)
            break;
        swap(arr,i,j);
        i++;
        j--;
    }
    swap(arr,left,j);
    return j;
}
```

**7、希尔排序(shell sort)**

1959年唐纳德·希尔提出，希尔排序把序列看作是一个矩阵，分成m列，逐列进行排序

m从某个整数逐渐减为1，当m为1时整个数列将完全有序,本质就是在每一次排序中减少逆序对

希尔本人给出的步长序列是n/2^k,当n=16时，步长序列是{1,2,4,8}

```java
public static void main(String[] args){
    int[] arr=new int[]{3,5,1,0,9};
    List<Integer> list=shellStep(arr.length);
    for(int i:list)
    shellSort(arr,i);
    }
    //实现每一列的排序
    public static void shellSort(int[] arr,int step){
        for(int col=0;col<step;col++){
            for (int i =col+step; i <arr.length; i+=step) {
                int cur = i;
                while (cur > col && arr[cur] < arr[cur - step]) {
                    int tem = arr[cur];
                    arr[cur] = arr[cur - step];
                    arr[cur - step] = tem;
                    cur-=step;
                }
            }
        }
    }
    //获得步长
    private static List<Integer> shellStep(int length){
        List<Integer> list=new ArrayList<>();
        while ((length/=2)>0){
            list.add(length);
        }
        return list;
    }
```

希尔排序是不稳定的，最坏时间复杂度是O(n^2),目前最好的是O(n^4/3),最好是O(n),平均时间复杂度取决于使用的步长，空间复杂度是O(1)。

以下不是三中排序不是基于比较排序的方法，他们是经典的空间换时间。

**9、计数排序（counting sort）**

1954年提出的，适合对**一定范围内的整数**进行排序，其核心思想是统计每个整数在序列中出现的次数，进而推导出每个整数在有序序列中的索引

基本代码，十分浪费空间，对负数无法排序且不稳定的。

```java
public static void countSort(int[] arr){
    int max=arr[0];
    for(int i=0;i<arr.length;i++){
        if(max<arr[i])
            max=arr[i];
    }
    int[] counts=new int[max+1];//这里浪费空间
    int index=0;
    for(int i=0;i<arr.length;i++)
        counts[arr[i]]++;
    for(int i=0;i<counts.length;i++){
        while(counts[i]>0){
            arr[index++]=i;
            counts[i]--;
        }
    }
}
```

改进计数排序，可以解决稳定性，而且对负数也可以排序。

先对arr数组进行遍历记录最大值和最小值，开辟新的数组空间counts[max-min+1],记录每个元素出现的次数，

counts[i]=counts[min]+counts[min-1]+···+counts[i-1]+1，在开一个新的数组ret[arr.length],遍历arr数组，其元素就是counts数组的索引，该元素k在**有序数组**的位置就是index=counts[k-min]-p对应的值。

```Java
public static void countSort(int[] arr){
    int max=arr[0];
    int min=arr[0];
    for(int i=0;i<arr.length;i++){
       max=Math.max(max,arr[i]);
       min=Math.min(min,arr[i]);
    }
    int[] counts=new int[max-min+1];
    for(int i=0;i<arr.length;i++)		//统计元素出现的次数
        counts[arr[i]-min]++;
    for(int i=1;i<counts.length;i++)	//累加
        counts[i]+=counts[i-1];
    int[] ret=new int[arr.length];
    for(int i=arr.length-1;i>=0;i--)    //从后向前遍历数组
        ret[--counts[arr[i]-min]]=arr[i];
    for(int i=0;i<arr.length;i++)
        arr[i]=ret[i];
}
```

计数排序是稳定的，时间复杂度和空间复杂度都是O(n+k)，其中k=max-min+1。

计数排数也可以对自定义类型进行排序，前提是自定义类型必须提供排序的整数类型。

**9、基数排序(radix sort)**

基数排序十分适合用于非负整数的排序，思想是一次对个位、百位···进行排序(从低位到高位)

基数排序可以对每一位用计数排序，因为基数只能从0~9中取得，计数排序就是对一定范围的整数进行排序。

```Java
public static void radixSort(int[] arr){
    int max=arr[0];
    for(int i:arr)
        max=Math.max(max,i);
    for(int k=1;k<=max;k*=10)
        countSort(arr,k);
}
public static void countSort(int[] arr,int k){
    int[] counts=new int[10];

    for(int i=0;i<arr.length;i++)
        counts[arr[i]/k%10]++;

    for(int i=1;i<counts.length;i++)
        counts[i]+=counts[i-1];

    int[] ret=new int[arr.length];
    for(int i=arr.length-1;i>=0;i--)     //从后向前遍历数组
        ret[--counts[arr[i]/k%10]]=arr[i];

    for(int i=0;i<arr.length;i++)
        arr[i]=ret[i];
}
```

基数排序是稳定的，时间复杂度是O(d*(n+k)),空间复杂度是O(n+k)。

**10、桶排序（bucket sort）**

桶排序从 1956 年就开始被使用，思路是将待排序集合中**处于同一个值域的元素存入同一个桶中**，也就是根据元素值特性将集合拆分为多个区域，则拆分后形成的多个桶，从值域上看是处于有序状态的。对每个桶中元素进行排序，则所有桶中元素构成的集合是有序的。

```java
public static void bucketSort(double[] arr){
    List<Double>[] buckets=new List[arr.length];
    for(int i=0;i<arr.length;i++){
        int bucketIndex=(int)(arr[i]*arr.length);
        List<Double> bucket=buckets[bucketIndex];

        if(bucket==null){
            bucket=new LinkedList<>();
            buckets[bucketIndex]=bucket;
        }
        bucket.add(arr[i]);
    }
    int index=0;
    for(int i=0;i<buckets.length;i++){
        if(buckets[i]==null)
            continue;
        buckets[i].sort(null); 

        for(double d:buckets[i])
            arr[index++]=d;
    }

}
```

```java
//20个数分三个桶来排，结果10 15 25 28 43 56 66 67 68 70 76 77 77 77 82 85 86 86 95 95 
public static void bucketSort(int[] arr){
    List<Integer>[] buckets=new List[3];
    for(int i=0;i<arr.length;i++){
        int bucketIndex;
        if(arr[i]<49)
            bucketIndex=0;
        else if(arr[i]>=50&&arr[i]<89)
            bucketIndex=1;
        else
            bucketIndex=2;
        
        List<Integer> bucket=buckets[bucketIndex];
        if(bucket==null){
            bucket=new LinkedList<>();
            buckets[bucketIndex]=bucket;
        }
        bucket.add(arr[i]);
    }
    int index=0;
    for(int i=0;i<buckets.length;i++){
        if(buckets[i]==null)
            continue;
        buckets[i].sort(null);

        for(int d:buckets[i])
            arr[index++]=d;
    }
}
```

桶排序是稳定的，时间复杂度O(n)+m*O(n/m\*logn/m)=O(n+n\*logn/m)=O(n+n(logn-logm)).

最终平均时间复杂度为(n+k),k=n(logn-logm),空间复杂度O(n+m),m=桶的个数。
