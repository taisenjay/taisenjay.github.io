---
layout: post
title: "必学算法知识习题汇总"
excerpt: "必学算法"
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

