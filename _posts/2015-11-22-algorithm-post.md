---
layout: post
title: "归并排序和快速排序"
excerpt: "简单介绍"
tags: [算法,排序]
comments: true
image:
  feature: sample-image-5.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

#归并排序

归并排序是将数组分成两半，将两半分别排序后，再归并到一起。排序其中的一半时，再继续沿用归并排序，也就是再将其分成两半，这样最终你将归并两个只含一个元素的数组。所以归并排序算法的实现的关键是`归并`这一过程。

归并的过程可以通过一个形象生动的比喻来形容：我们将两堆已排好序（按从小到大）的扑克牌背面朝上，即我们每次只能翻开第一张扑克牌。我们通过对比两堆牌上的第一张牌的大小来排序，比如A堆上的第一张是2，B堆上的第一张是4，通过对比我们将2放在结果数组的第一位。然后我们就翻开A堆的下一张，如果比4小，我们就继续将其放入结果数组，如果比4大，那我们就将B堆上的4放入数组，接着再翻开B堆的下一张……这样一直进行下去，如果到最后时发现A堆已经没有牌了，而B堆还有好几张呢，我们就可以直接将其放入结果数组当中了。所以我们发现归并的过程是个很简单而且自然而然的过程。

下面进行代码实现。在下面的代码中，merge方法会将目标数组的所有元素拷贝到临时数组helper中，并记下数组左右两半的起始位置（helperLeft和helperRight）。然后迭代访问helper数组,将左右两半中较小的元素，复制到目标数组中。最后，再将余下所有元素复制回目标数组。

{% highlight java %}
	
	void mergeSort(int[] array,int low,int high){
		if(low<high){
			int middle=(low+high)/2;
			mergeSort(array,low,middle);//递归排序左半部分
			mergeSort(array,middle+1,high);//递归排序有半部分
			merge(array,low,middle,high);//归并
		}
	}
	
	void merge(int[] array,int low,int middle,int high){
		int[] helper=new int[array.length];
		//将数组左右两半拷贝到helper数组中
		for(int i=low;i<=high;i++){
			helper[i]=array[i];
		}
		int helperLeft=low;
		int helperRight=middle+1;
		int current=low;
		//迭代访问helper数组，比较左右两半的元素，将较小元素复制到原先的数组中
		while(helperLeft<=middle&&helperRight<=high){
			if(helper[helperLeft]<=helper[helperRight]){
				array[current]=helper[helperLeft];
				helperLeft++;
			}else{
				array[current]=helper[helperRight];
				helperRight++;
			}
			current++;
		}
		//将数组左半部分剩余元素复制到目标数组中
		//不复制右半部分剩余元素的原因是这部分元素已经在目标数组中，无需复制
		int remaining=middle-helperLeft;
		for(int i=0;i<=remaining;i++){
			array[current+i]=helper[helperLeft+1];
		}
	}

{% endhighlight %}

#快速排序

快速排序的基本思想是：首先随机挑选一个轴值，将待排序的数组进行分割，将所有比轴值小的元素排在前面，比它大的元素排在后面。然后再对已分割的两部分重复进行上述分割过程，直到整个序列有序。

快速排序是对冒泡排序的一种改进。在冒泡排序中，元素的比较和移动只能在相邻位置进行，因而总的比较次数和移动次数较多。而在快速排序中，元素的比较和移动是从两端向中间进行的，较大的元素一次就能从前面移动到后面，较小的元素一次就能从后面移动到前面，元素移动的距离较远，从而减少了总的比较次数和移动次数。然而，因为我们无法确保选取的分割轴值就是数组的中位数（或接近中位数），快排的效率可能会很低下，这也是为什么最差情况时间复杂度为O(n^2)。

{% highlight java %}

	void quickSort(int[] arr,int left,int right){
		int index=partition(arr,left,right);
		if(left<index-1){
			quickSort(arr,left,index-1);//排序左半部分
		}
		if(index<right){
			quickSort(arr,index,right);//排序右半部分
		}
	}
	int partition(int[] arr,int left,int right){
		int pivot=arr[(left+right)/2];//挑出基准点
		while(left<=right){
			//找出左边中应被放到右边的元素
			while(arr[left]<pivot)left++;
			//找出右边中应被放到左边的元素
			while(arr[right]>pivot)right--;
			//交换元素，同时调整左右索引值
			if(left<=right){
				swap(arr,left,right);
				left++;
				right--;
			}
		}
		return left;
	}
{% endhighlight %}

