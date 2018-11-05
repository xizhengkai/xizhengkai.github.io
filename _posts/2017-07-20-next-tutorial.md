---
title: struct 与 union 区别
description: struct 可以存储多个成员信息，而union每个成员会用同一个存储空间，只能存储最后一个成员的信息
categories:
 - c++语法
tags:
---

<!-- more -->

## Struct 和Union区别

1.在存储多个成员信息时，编译器会自动给struct成员分配空间，`struct可以存储多个成员信息`，而Union`每个成员会用一个存储空间，只能存储最后一个成员的信息`。
2.都是由多个不同的数据类型成员组成，但在任何同一时刻，`Union只存放了一个被先选中的成员`，而`结构体的所有成员都存在`。
3.对于`Union的不同成员赋值，将会对其他成员重写`，原来成员的值就不存在了，而对于struct的不同成员赋值，是互不影响的。

注：在很多地方需要对结构体的成员变量进行修改。只是部分成员变量，那么就不能用联合体Union，因为Union的所有成员变量占一个内存。eg：在链表中个别数值域进行赋值就必须用struct。

## 实例说明

* struct
  struct简单来说就是一些相互关联的元素的集合，说是集合，其实它们在内存中的存放是有先后顺序的，并且每个元素都有自己的内存空间。看个例子：
  
```	cpp
struct sTest
{
	int a; //sizeof(int) = 4
	char b; //sizeof(char) = 1
	short c; //sizeof(short) = 2
}x;
```
所以在内存中至少占用4+1+2 = 7 byte。然而实际中占用的内存并不是7 byte，这就涉及到字节对齐方式。

* union
  union的不同之处就在于，它所有的元素共享同一内存单元，且__分配给union的内存size由类型最大的元素size来确定__，如下的内存就为一个double类型size

  ```cpp
  union uTest
  {
      int a; //sizeof(int) = 4
      double b; //sizeof(double) = 8
      char c; //sizeof(char) = 1
  }x;
  ```

  所以分配的内存size就是8 byte。

  既然是内存共享，理所当然地，它不能同时存放多个成员的值，而只能存放其中的一个值，就是最后赋予它的值，如：

  ```cpp
  x.a = 3;
  x.b = 4.5;
  x.c = 'A';
  ```

  __这样你只看到了x.c = 'A'，而其它已经被覆盖掉，失去了意义。__

  __eg:__Sample联合只包含其中某一个成员，要么是index，要么是price。

  ```cpp
  union Sample{
      int index;
      double price;
  };
  ```

  若 Sample ss; ss.index = 10; // 从今往后只能使用ss.index

  若 Sample ss; ss.price = 14.25; // 从今往后只能使用ss.price

  __在union的使用中，如果给其中某个成员赋值，然后使用另一个成员，是未定义行为，后果自负__

  struct成员是互相独立的，一个struct包含所有成员。

  ```cpp
  struct Example{
      int indext;
      double price;
  };
  ```

  Example 结构包含两个成员，修改index不会对price产生影响，反之亦然。

  *union的成员共享内存空间，一个union只包含其中某一个成员。*

  两者的区别无非就**在于内存单元的分配与使用**。然而要灵活的使用struct和union还是存在很多小技巧的，比如：**元素的相关性不强时，完全是可以使用union，从而节省内存size；**struct和union还可以嵌套。

## 内存对齐方式

  ```cpp
  union u1{
      double a;
      int b;
  };
  union u2{
      char a[13];
      int b;
  };
  union u3{
      char a[13];
      char b;
  };
  cout<<sizeof(u1)<<endl; //8
  cout<<sizeof(u2)<<endl; //16
  cout<<sizeof(u3)<<endl; //13
  ```

  都知道**union的大小取决于它所有的成员中，占用空间最大的一个成员的大小**。所以对于u1来说，大小就是最大的double类型成员a了，所以sizeof(u1) = sizeof(double) = 8。但是对于u2和u3，最大的空间都是char[13]类型的数组，为什么u3的大小是13，而u2是16呢？**关键在于u2中的成员int b。由于int类型成员的存在，使u2的对齐方式变成4**，也就是说，u2的大小必须在4的对界上，所以占用的空间变成了16（最接近13的对界）。

  结论：**复合数据类型，如union，struct，class的对齐方式为成员中对齐方式最大的成员的对齐方式。**

  顺便提一下CPU对界问题，**32的c++采用8位对界来提高运行速度**，所以编译器会尽量把数据放在它的对界上以提高内存命中率。**对界是可以更改的，使用\#pagma pack(x)宏**可以改变编译器的对界方式，**默认是8**。**c++固有类型的对界取编译器对界方式与自身大小中较小的一个**。例如，指定编译器按2对界，int类型的大小是4，则int的对界为2和4中较小的2。在默认的对界方式下，因为几乎**所有的数据类型都不大于默认的对界方式8（除了long double），**所以**所有的固有类型的对界方式可以认为就是类型自身的大小**。更改一下上面的程序：

  ```cpp
  #pragma pack(2)
  union u2{
      char a[13];
      int b;
  };
  union u3{
      char a[13];
      char b;
  };
  #pragma pack(8)
  cout<<sizeof(u2)<<endl; //14  由于手动更改对界方式为2，所以int的对界也变成了2，u2的对界取成员中最大的对界，也是2了，所以此时sizeof（u2）= 14。
  cout<<sizeof(u3)<<endl; //13 char的对界为1。
  ```


![Desktop Sidebar Preview](http://iissnan.com/nexus/next/desktop-sidebar-toc.png)

* Mobile

![Mobile Preview](http://iissnan.com/nexus/next/mobile.png)


## Installation

Check whether you have `Ruby 2.1.0` or higher installed:

```sh
ruby --version
```

Install `Bundler`:

```sh
gem install bundler
```

Clone Jacman theme:

```sh
git clone https://github.com/Simpleyyt/jekyll-theme-next.git
cd jekyll-theme-next
```

Install Jekyll and other dependencies from the GitHub Pages gem:

```sh
bundle install
```

Run your Jekyll site locally:

```sh
bundle exec jekyll server
```

More Details：[Setting up your GitHub Pages site locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)


## Features

### Multiple languages support, including: English / Russian / French / German / Simplified Chinese / Traditional Chinese.

Default language is English.

```yml
language: en
# language: zh-Hans
# language: fr-FR
# language: zh-hk
# language: zh-tw
# language: ru
# language: de
```

Set `language` field as following in site `_config.yml` to change to Chinese.

```yml
language: zh-Hans
```

### Comment support.

NexT has native support for `DuoShuo` and `Disqus` comment systems.

Add the following snippets to your `_config.yml`:

```yml
duoshuo:
  enable: true
  shortname: your-duoshuo-shortname
```

OR

```yml
disqus_shortname: your-disqus-shortname
```

### Social Media

NexT can automatically add links to your Social Media accounts:

```yml
social:
  GitHub: your-github-url
  Twitter: your-twitter-url
  Weibo: your-weibo-url
  DouBan: your-douban-url
  ZhiHu: your-zhihu-url
```

### Feed link.

> Show a feed link.

Set `rss` field in theme's `_config.yml`, as the following value:

1. `rss: false` will totally disable feed link.
2. `rss:  ` use sites' feed link. This is the default option.

    Follow the installation instruction in the plugin's README. After the configuration is done for this plugin, the feed link is ready too.

3. `rss: http://your-feed-url` set specific feed link.

### Up to 5 code highlight themes built-in.

NexT uses [Tomorrow Theme](https://github.com/chriskempson/tomorrow-theme) with 5 themes for you to choose from.
Next use `normal` by default. Have a preview about `normal` and `night`:

![Tomorrow Normal Preview](http://iissnan.com/nexus/next/tomorrow-normal.png)
![Tomorrow Night Preview](http://iissnan.com/nexus/next/tomorrow-night.png)

Head over to [Tomorrow Theme](https://github.com/chriskempson/tomorrow-theme) for more details.

## Configuration

NexT comes with few configurations.

```yml

# Menu configuration.
menu:
  home: /
  archives: /archives

# Favicon
favicon: /favicon.ico

# Avatar (put the image into next/source/images/)
# can be any image format supported by web browsers (JPEG,PNG,GIF,SVG,..)
avatar: /default_avatar.png

# Code highlight theme
# available: normal | night | night eighties | night blue | night bright
highlight_theme: normal

# Fancybox for image gallery
fancybox: true

# Specify the date when the site was setup
since: 2013

```

## Browser support

![Browser support](http://iissnan.com/nexus/next/browser-support.png)
