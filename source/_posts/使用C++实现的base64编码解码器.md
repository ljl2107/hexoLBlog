---
abbrlink: 使用C++实现的base64编码解码器
categories:
- - 技术
cover: /img/R-C.jfif
date: '2023-06-04T22:32:21.300648+08:00'
excerpt: Base64 在线编码解码 | Base64 加密解密 - Base64.us 所有结果可以使用上述网站检验。  什么是base64编码？ base64编码是一种编码方式  用64 + 1 个字符表示字符  本质是将三位8比特字符扩增为四位8比特字符，但是这么说开始可能很懵。 下面给个图示，该编码是以3字节为一个单位进行操作。  原来的:   每个双向箭头为一个单元(6个比特)，每一个单元单独拿出...
tags:
- C++
- 博客
- 编码
- 实例
title: 使用C++实现的base64编码解码器
top_img: /img/R-C.jfif
updated: 2023-6-4T23:10:17.463+8:0
---
[Base64 在线编码解码 | Base64 加密解密 - Base64.us](https://base64.us/)

所有结果可以使用上述网站检验。

---

# 什么是base64编码？

```
base64编码是一种编码方式

用64 + 1 个字符表示字符
```

本质是将三位8比特字符扩增为四位8比特字符，但是这么说开始可能很懵。

下面给个图示，该编码是以3字节为一个单位进行操作。

* 原来的:

![base64.jpg](https://fastly.jsdelivr.net/gh/ljl2107/imageshack/note/base64.jpg)

每个双向箭头为一个单元(6个比特)，每一个单元单独拿出来，在之前（高位）补两位0，重组为8比特。

这样原来的字节就被以一种特殊的方式编码了。

而且高2位是0，所以它的每一字节范围就是 2^6 = 64 ，这也符合了这个编码的名字 ：‘64’编码。

---

新产生的字符串根据特定的规则进行显示，具体如下：（网上找的）

![base64suoyin.jpg](https://fastly.jsdelivr.net/gh/ljl2107/imageshack/note/base64suoyin.jpg)

好，那为什么我前面说是用 64 + 1 个字符表示呢？

先问个问题，如果说原字符不够3字节怎么办？

想想看...

```
   ___     ___     ___     ___      __    _ _  
  | _ )   /   \   / __|   | __|    / /   | | |   
  | _ \   | - |   \__ \   | _|    / _ \  |_  _|  
  |___/   |_|_|   |___/   |___|   \___/   _|_|_  
_|"""""|_|"""""|_|"""""|_|"""""|_|"""""|_|"""""| 
"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-' 
   ___     ___     ___     ___      __    _ _  
  | _ )   /   \   / __|   | __|    / /   | | |   
  | _ \   | - |   \__ \   | _|    / _ \  |_  _|  
  |___/   |_|_|   |___/   |___|   \___/   _|_|_  
_|"""""|_|"""""|_|"""""|_|"""""|_|"""""|_|"""""| 
"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-' 
```

如果原字符只有1个字节或者2个字节（最后剩下也可能），不能像之前的3个字节一样进行操作。

这里的解决方法是在原字符上全部补零，补齐3字节。再按照前面的法则进行加密，对于多出来的字符则全部以“=”补充。

---

# 下面放代码：

```cpp
//文件名 base64.h

#include<iostream>
#include<cstring>
using namespace std;
////对应的字符列表
const char base64List[67] = "AABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";
char b[200] = "";
char a[100] = "";

void menu();//菜单 
char* enCoder(char* a);//编码器 
char* deCoder(char* bq);//解码器
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

```cpp
//文件名自起
#include"base64.h"

//JL
int main()
{
	char en[100] = "";
	char de[100] = "";
	int c = 0;
	char f;
	int chose = 0;
	char pos[100] = "";
	while (1)
	{
		menu();
		cin >> chose;
		switch (chose)
		{
		case 1:
			cout << "请输入请输入要进行 Base64 编码的字符";
			cin >> en;
			enCoder(en);
			system("pause");
			break;
		case 2:
			cout << "请输入请输入要进行 Base64 解码的字符";
			cin >> de;
			for (int i = 0; i < strlen(de); i++)
			{
				c = strchr(base64List, de[i]) - base64List;//这里可以得到常字符在列表中的位置
				if (c == 0) {
					c = 1;
				}
				pos[i] = c;
			}
			deCoder(pos);
			system("pause");
			break;
		case 3:
			cout << "成功退出";
			exit(-1);
			break;
		default:
			while((f=getchar())!='\n'){};
			break;
		}
		memset(a,0,sizeof(a));
		memset(b,0,sizeof(b));
		fflush(stdin);
		system("cls");
	}
	return 0;
}

void menu()
{
	cout << "--------------base64编解码器--------------" << endl;
	cout << "1.编码（Encode）" << endl;
	cout << "2.解码（Decode）" << endl;
	cout << "3.退出（Exit）" << endl;
	cout << "请输入选项：" << endl;
}
char* enCoder(char* a) {//编码器
	int i = 0;
	for (i = 0; i < strlen(a); i = i + 3)
	{
		int indexb = i / 3;
		char b1 = (a[i] >> 2) + 1;
		b[indexb * 4] = b1;
		char b2 = ((a[i] & 0x03) << 4) | (a[i + 1] >> 4) + 1;
		b[indexb * 4 + 1] = b2;
		if (strlen(a) - i == 1) {
			b[strlen(b)] = 65;
			b[strlen(b)] = 65;
			break;
		}
		char b3 = ((a[i + 1] & 0x0f) << 2) | (a[i + 2] >> 6) + 1;
		b[indexb * 4 + 2] = b3;
		if (strlen(a) - i == 2) {
			b[strlen(b)] = 65;
			break;
		}
		char b4 = (a[i + 2] & 0x3f) + 1;
		b[indexb * 4 + 3] = b4;
	}
	cout << "Base64 编码的结果：" ;
	for (int i = 0; i < strlen(b); i++) {
		cout<< base64List[b[i]];
	}
	cout << endl;
	return b;
}
char* deCoder(char* bq)//解码器
{

	for (int i = 0; i < strlen(bq); i = i + 4)
	{
		int indexa = i / 4;
		char a1 = (bq[i] - 1 << 2) | (bq[i + 1] - 1 >> 4);
		a[indexa * 3] = a1;
		if (strlen(bq) - i <= 4 && base64List[bq[strlen(bq) - 2]] == '=') {
			break;
		}
		char a2 = (bq[i + 1] - 1 << 4) | (bq[i + 2] - 1 >> 2);
		a[indexa * 3 + 1] = a2;
		if (strlen(bq) - i <= 4 && base64List[bq[strlen(bq) - 1]] == '=') {
			break;
		}
		char a3 = (bq[i + 2] - 1 << 6) | (bq[i + 3] - 1 >> 0);
		a[indexa * 3 + 2] = a3;
	}
	cout << "Base64 解码的结果：" << a << endl;
	return a;
}
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

---

# 代码逻辑呢？（来自百度百科，我也是看着它做的）

> //用更接近于编程的思维来说，编码的过程是这样的：
>
> //第一个字符通过右移2位获得第一个目标字符的Base64表位置，根据这个数值取到表上相应的字符，就是第一//个目标字符。
>
> //然后将第一个字符与0x03(00000011)进行与(&)操作并左移4位,接着第二个字符右移4位与前者相或(|)，即获得第二个目标字符。
>
> //再将第二个字符与0x0f(00001111)进行与(&)操作并左移2位,接着第三个字符右移6位与前者相或(|)，获得第三个目标字符。
>
> //最后将第三个字符与0x3f(00111111)进行与(&)操作即获得第四个目标字符。
>
> //在以上的每一个步骤之后，再把结果与 0x3F 进行 AND [位操作](https://baike.baidu.com/item/%E4%BD%8D%E6%93%8D%E4%BD%9C?fromModule=lemma_inlink "位操作")，就可以得到编码后的字符了。
>
> 原文的字节数量应该是3的倍数，如果这个条件不能满足的话，具体的解决办法是这样的：原文剩余的字节根据编码规则继续单独转(1变2，2变3；不够的位数用0补全)，再用=号补满4个字节。这就是为什么有些Base64编码会以一个或两个等号结束的原因，但等号最多只有两个。因为一个原字节至少会变成两个目标字节，所以余数任何情况下都只可能是0，1，2这三个数中的一个。如果余数是0的话，就表示原文字节数正好是3的倍数（最理想的情况）。如果是1的话，转成2个Base64编码字符，为了让Base64编码是4的倍数，就要补2个等号；同理，如果是2的话，就要补1个等号。
