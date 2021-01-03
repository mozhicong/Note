# mfc
⦁	mfc创建窗口
⦁	mfc头文件 afxwin.h
⦁	自定义类继承与 CWinApp 应用程序类 MyApp app 应用程序对象，有且仅有一个
⦁	程序入口InitInstance
⦁	入口里 创建窗口
⦁	窗口类 MyFrame 继承与 CFrameWnd
⦁	MyFrame 构造中 Create(NULL,标题名称)
⦁	创建窗口对象
⦁	显示和更新
⦁	m_pMainWnd = frame; //保存指向应用程序的主窗口的指针
⦁	return TRUE;
⦁	对项目进行配置,配置中选择 在共享DLL中使用

源码
```cpp
mfc.h

#pragma once
#include <afxwin.h>//mfc头文件

class MyApp :public CWinApp//CWinApp应用程序类
{

public:
	//程序入口
	virtual BOOL InitInstance();



};

class MyFrame:public CFrameWnd//窗口框架类
{
public:
	MyFrame();

};
```

```cpp
mfc.cpp

#include "mfc.h"

MyApp app; //全局应用程序对象，有且仅有一个

BOOL MyApp::InitInstance()
{
	//创建窗口
	MyFrame *frame = new MyFrame;
	
	//显示和更新窗口
	frame->ShowWindow(SW_SHOWNORMAL);
	frame->UpdateWindow();

	m_pMainWnd = frame; //保存指向应用程序的主窗口的指针

	return TRUE;
}

MyFrame::MyFrame()
{
	Create(NULL, TEXT("mfc"));
}
```

⦁	消息映射
消息映射是一个将消息和成员函数相互关联的表。比如，框架窗口接受到一个鼠标左击消息的处理程序，然后调用OnLButtonDown。

1)	所有操作类中，声明消息宏映射。
2)	通过放置标识消息的宏来执行消息映射，相应的类将在对BEGIN_MESSAGE_MAP和END_MESSAGE_MAP的调用之间处理消息。


2.1 声明宏 写到.h中
2.2 分界宏 写到.cpp中
2.3 找消息宏 写到分界宏 中间
2.4 把函数原型 声明写到.h中
2.5 把函数的实现写到.cpp中
4.6 鼠标、键盘、绘图

```cpp
mfc.h
class MyFrame:public CFrameWnd//窗口框架类
{
public:
	MyFrame();

	//声明宏 提供消息映射机制
	DECLARE_MESSAGE_MAP()

	afx_msg void OnLButtonDown(UINT, CPoint);
};
```
```cpp
mfc.cpp
//分界宏
BEGIN_MESSAGE_MAP(MyFrame, CFrameWnd)
	ON_WM_LBUTTONDOWN() //鼠标左键按下


END_MESSAGE_MAP()

void MyFrame::OnLButtonDown(UINT, CPoint point)
{
//鼠标按下后执行这里
}
```
全代码
```cpp
mfc.h
#pragma once
#include <afxwin.h>//mfc头文件

class MyApp :public CWinApp//CWinApp应用程序类
{

public:
	//程序入口
	virtual BOOL InitInstance();



};

class MyFrame:public CFrameWnd//窗口框架类
{
public:
	MyFrame();

	//声明宏 提供消息映射机制
	DECLARE_MESSAGE_MAP()

	afx_msg void OnLButtonDown(UINT, CPoint);
	afx_msg void OnChar(UINT, UINT, UINT);
	afx_msg void OnPaint();
};
mfc.cpp
#include "mfc.h"

MyApp app; //全局应用程序对象，有且仅有一个

BOOL MyApp::InitInstance()
{
	//创建窗口
	MyFrame *frame = new MyFrame;
	
	//显示和更新窗口
	frame->ShowWindow(SW_SHOWNORMAL);
	frame->UpdateWindow();

	m_pMainWnd = frame; //保存指向应用程序的主窗口的指针

	return TRUE;
}
```
```cpp
//分界宏
BEGIN_MESSAGE_MAP(MyFrame, CFrameWnd)
	ON_WM_LBUTTONDOWN() //鼠标左键按下
	ON_WM_CHAR() //键盘
	ON_WM_PAINT() //绘图宏

END_MESSAGE_MAP()




MyFrame::MyFrame()
{
	Create(NULL, TEXT("mfc"));
}

void MyFrame::OnLButtonDown(UINT, CPoint point)
{
	//鼠标按下后执行这里
	
	//mfc中的字符串 CString
	CString str; //实例化一个类
	str.Format(TEXT("x = %d,,,,y = %d"), point.x, point.y);	//格式化字符串
	MessageBox(str);

}

void MyFrame::OnChar(UINT key, UINT, UINT)
{
	CString str;
	str.Format(TEXT("你按下了%c键"), key);
	MessageBox(str);
}

void MyFrame::OnPaint()
{
	CPaintDC dc(this);

	dc.TextOutW(100, 100, TEXT("艾欧尼亚昂扬不灭"));
	dc.Ellipse(10, 10, 100, 100);
}
```
⦁	windows字符集	
3.1多字节 字符串 转宽字节
3.2 声明 宽字节字符类型 wchar_t
3.3 统计宽字节字符串 wcslen
3.4 TEXT 做了自适应编码转换
5.5 char* 和 CString之间的转换

代码：
```cpp
//统计字符串长度
	int num = 0;
	const char *p = "aaaa";
	num = strlen(p);

	//统计宽字节的字符串长度
	const wchar_t *p2 = L"bbbb";
	num = wcslen(p2);

	//char* 与CString之间的转换
	//char* -> CString
	const char *p3 = "ccc";
	CString str = CString(p3);
	//CString -> char*
	CStringA tmp;
	tmp = str;
	char *pp = tmp.GetBuffer();
```
⦁	基于对话框编程
对话框是一种特殊类型的窗口，绝大多数Windows程序都通过对话框与用户进行交互。在Visual C++中，对话框既可以单独组成一个简单的应用程序，又可以成为文档/视图结构程序资源。

⦁	创建基于对话框程序
