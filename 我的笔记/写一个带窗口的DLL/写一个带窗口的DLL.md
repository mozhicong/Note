# 写一个带窗口的DLL
写一个带窗口的DLL
1.	新建一个MFC DLL项目
2.	添加一个Dialog的一个资源文件
3.	在InitInstance函数中开辟一个线程
4.	把资源切换到我们的模块中
5.	创建界面
6.	删除资源(关闭界面才会执行过来)
7.	释放模块
8.	释放内核句柄
9.	把资源切换回去
全代码：
```cpp
// MFCLibrary1.cpp: 定义 DLL 的初始化例程。
//

#include "pch.h"
#include "framework.h"
#include "MFCLibrary1.h"
#include "CMainView.h"

#ifdef _DEBUG
#define new DEBUG_NEW
#endif


//
//TODO:  如果此 DLL 相对于 MFC DLL 是动态链接的，
//		则从此 DLL 导出的任何调入
//		MFC 的函数必须将 AFX_MANAGE_STATE 宏添加到
//		该函数的最前面。
//
//		例如: 
//
//		extern "C" BOOL PASCAL EXPORT ExportedFunction()
//		{
//			AFX_MANAGE_STATE(AfxGetStaticModuleState());
//			// 此处为普通函数体
//		}
//
//		此宏先于任何 MFC 调用
//		出现在每个函数中十分重要。  这意味着
//		它必须作为以下项中的第一个语句:
//		出现，甚至先于所有对象变量声明，
//		这是因为它们的构造函数可能生成 MFC
//		DLL 调用。
//
//		有关其他详细信息，
//		请参阅 MFC 技术说明 33 和 58。
//

// CMFCLibrary1App

BEGIN_MESSAGE_MAP(CMFCLibrary1App, CWinApp)
END_MESSAGE_MAP()


// CMFCLibrary1App 构造

CMFCLibrary1App::CMFCLibrary1App()
{
	// TODO:  在此处添加构造代码，
	// 将所有重要的初始化放置在 InitInstance 中
}


// 唯一的 CMFCLibrary1App 对象

CMFCLibrary1App theApp;

HANDLE g_hUIThread;
HANDLE g_hFreeThread;

// CMFCLibrary1App 初始化
void UIThread()
{
	//资源切换(把资源切换到我们的模块中)
	AFX_MANAGE_STATE(AfxGetStaticModuleState());
	HINSTANCE hResOld = AfxGetResourceHandle(); //保存旧句柄
	AfxSetResourceHandle(theApp.m_hInstance);

	//创建界面
	CMainView * MainDlg = new CMainView();
	
	MainDlg->DoModal();

	//关闭窗口才会执行过来
	//删除界面内存
	delete MainDlg;
	MainDlg = NULL;

	//释放模块
	g_hFreeThread = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)FreeLibrary, theApp.m_hInstance, 0, 0);

	CloseHandle(g_hUIThread); //释放界面线程句柄
	CloseHandle(g_hFreeThread); //释放销毁线程句柄
	//资源切换回去
	AfxSetResourceHandle(hResOld);
}

BOOL CMFCLibrary1App::InitInstance()
{
	CWinApp::InitInstance();

	//开辟一个线程
	g_hUIThread = ::CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)UIThread, NULL, 0, 0);

	return TRUE;
}
```