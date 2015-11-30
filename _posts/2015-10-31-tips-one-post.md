---
layout: post
title: "Android Studio导入ViewpagerIndicator"
excerpt: "tips编程中遇到的小问题（1）"
tags: [android studio,stackoverflow]
comments: true
---


在开发中用到了[JakeWharton](https://github.com/JakeWharton)大神的ViewPagerIndicator类，但是看了之后发现这个项目年代已经很久远了，还是用的Eclipse开发工具，想要导入到Android Studio第三方依赖类库中应该怎样做呢？下面就来看看stackoverflow上热心用户提供的解决办法。

在Android Studio中使用Android-ViewPagerIndicator，你不能download it from gradle（这是没有gradle编译版本的意思吧）。想要实现的话，必须将这个类库以“Existing Project”的形式导入到当下的项目中。

下面是详细步骤：

- 1、从github下载[源代码](https://github.com/JakeWharton/ViewPagerIndicator)
- 2、在Android Studio中依次打开File->Project Structure->add(就是左上角的“+”号)->Import Eclipse ADT Project(会看到是一个提供了多个选项的窗体，有gradle project,jar/arr等等你可以添加的模块，PS：我用的是Android Studio1.4)->只选中“library”这个文件夹而不是整个项目（下面默认的一步步就可以了）
- 3、下面就是需要注意的问题了，你项目的build.gradle中的compileSdkVersion很大可能和ViewPagerIndicator中的compileSdkVersion不一样，所以将后者的修改成与你项目的一样。此外buildToolVersion也会不一样，按照一样的解决办法修改就行了
- 4、在你的根build.gradle中添加ViewPagerIndicator项目依赖
	dependencies {
    	compile project(':library')
	}
- 5、最后就可以同步项目了，Sync……搞定


开发中碰到的问题大多都被前人遇到并解决了，所以一定要善用搜索引擎和stackoverflow.

