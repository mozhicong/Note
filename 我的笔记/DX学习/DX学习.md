# 创建win32窗口程序

## 1. WinMain函数

```cpp
int WINAPI WinMain(HINSTANCE hInstance,
                   HINSTANCE hPrevInstance, 
                   LPSTR lpCmdLine, 
                   int nShowCmd);
```

WinMain是一个函数，该函数的功能是被系统调用，作为一个32位应用程序的入口点。WinMain函数应初始化应用程序，显示主窗口，进入一个消息接收一发送循环，这个循环是应用程序执行的其余部分的顶级控制结构。

**hinstance：**应用程序当前实例的句柄。

**hPrevlnstance：**应用程序的先前实例的句柄。对于同一个程序打开两次，出现两个窗口第一次打开的窗口就是先前实例的窗口。对于一个32位程序，该参数总为[NULL](https://baike.baidu.com/item/NULL/19660386)。

如果需要检测另外一个实例是否已经存在，则使用CreateMutex函数创建一个独一无二的名字。即使互斥名已经存在，CreateMutex函数也是成功的，但是GetLastError函数将返回 ERROR_ALREADY_EXISTS，这就表明应用程序有另外一个实例存在，因为它首先创建了互斥名。

**lpCmdLine：**指向应用程序命令行的字符串的[指针](https://baike.baidu.com/item/指针)，不包括执行文件名。获得整个命令行，参看[GetCommandLine](https://baike.baidu.com/item/GetCommandLine)。

第三个参数lpCmdLine是一个以空终止的字符串，指定传递给应用程序的[命令行参数](https://baike.baidu.com/item/命令行参数)。例如：在D盘下有一个sunxin.txt文件，当我们用鼠标双击这个文件时将启动记事本程序（notepad.exe），此时系统会将D:\sunxin.txt作为命令行参数传递给记事本程序的WinMain函数，记事本程序在得到这个文件的全路径名后，就在窗口中显示该文件的内容。要在VC++[开发环境](https://baike.baidu.com/item/开发环境)中向应用程序传递参数，可以单击菜单【Project】→【Settings】，选择“Debug”选项卡，在“Program arguments”编辑框中输入你想传递给应用程序的参数。

**nCmdShow：**指明窗口如何显示。该参数可以是下列值之一：

SW_HIDE：隐藏窗口并且激活另外一个窗口。

SW_MINIMIZE：最小化指定的窗口，并且激活在系统表中的顶层窗口。

SW_RESTORE：激活并显示窗口。如果窗口已经最小化或最大化，系统将以恢复到原来的尺寸和位置显示窗口（与SW_SHOWNORMAL相同）。

SW_SHOW：激活一个窗口并以原来的尺寸和位置显示窗口。

SW_SHOWMAXIMIZED：激活窗口并且将其最大化。

SW_SHOWMINIMIZED：激活窗口并将其最小化（以图标显示）。

SW_SHOWMINNOACTIVE：将一个窗口显示为图标。激活窗口维持活动状态。

SW_SHOWNA：以窗口的当前状态显示窗口。激活窗口保持活动状态。

SW_SHOWNOACTIVATE：以窗口的最近一次的尺寸和位置显示窗口。不激活窗口。

SW_SHOWNORMAL：激活并显示窗口。如果窗口最大化或最小化，系统将其恢复到原来的尺寸和位置（与SW_RESTORE相同）。

## 2. 注册窗口类

```cpp
WNDCLASSEX wc;
	wc.cbClsExtra = NULL;
	wc.cbWndExtra = NULL;
	wc.cbSize = sizeof(WNDCLASSEX);
	wc.hbrBackground = (HBRUSH)GetStockObject(WHITE_BRUSH);
	wc.hCursor = LoadCursor(NULL, IDC_ARROW);
	wc.hIcon = LoadIcon(NULL, IDI_APPLICATION);
	wc.hIconSm = NULL;
	wc.hInstance = hInstance;
	wc.lpfnWndProc = WndProc;
	wc.style = CS_HREDRAW | CS_VREDRAW;
	wc.lpszClassName = L"MyClassName";
	wc.lpszMenuName = NULL;
	RegisterClassEx(&wc);
```

### 2.1 WNDCLASSEX结构体

WNDCLASSEX是一个窗口，最重要的成员是[lpfnWndProc](https://baike.baidu.com/item/lpfnWndProc/8717371)。前缀 lpfn 表示该成员是一个指向函数的长[指针](https://baike.baidu.com/item/指针/2878304)。在 Win32中由于内存模式是 FLAT 型，所以没有 near 或 far 的区别。

**描述**

每一个[窗口类](https://baike.baidu.com/item/窗口类)必须有一个窗口过程，当 Windows 把属于特定窗口的消息发送给该窗口时，该窗口的窗口类负责处理所有的消息，如键盘消息或鼠标消息。由于窗口过程差不多智能地处理了所有的窗口[消息循环](https://baike.baidu.com/item/消息循环)，所以您只要在其中加入消息处理过程即可。

**结构体原型：**

```cpp
typedef struct WNDCLASSEX {
UINT cbSize;
UINT style;
WNDPROC lpfnWndProc;
int cbClsExtra;
int cbWndExtra;
HINSTANCE hInstance;
HICON hIcon;
HCURSOR hCursor;
HBRUSH hbrBackground;
LPCTSTR lpszMenuName;
LPCTSTR lpszClassName;
HICON hIconSm;
} WNDCLASSEX, *PWNDCLASSEX;
```

**说明**

WNDCLASSEX 结构用于注册[窗口类](https://baike.baidu.com/item/窗口类)

注册实例：

```cpp
ATOM MyRegisterClass( HINSTANCE hInstance )
{
WNDCLASSEX wcex;
wcex.cbSize = sizeof( WNDCLASSEX );
wcex.style = CS_HREDRAW | CS_VREDRAW;
wcex.lpfnWndProc = (WNDPROC) WndProc;
wcex.cbClsExtra = 0;
wcex.cbWndExtra = 0;
wcex.hInstance = hInstance;
wcex.hIcon = LoadIcon( NULL, IDI_APPLICATION );
wcex.hCursor = LoadCursor( NULL, IDC_ARROW );
wcex.hbrBackground = (HBRUSH) GetStockObject( WHITE_BRUSH );
wcex.lpszMenuName = NULL;
wcex.lpszClassName = "My Class";
wcex.hIconSm = LoadIcon( NULL, IDI_WINLOGO );
return RegisterClassEx( &wcex );
}
```

**参数**

**1.cbSize：**

WNDCLASSEX 的大小。我们可以用sizeof（WNDCLASSEX）来获得准确的值。

**2.style：**

从这个[窗口类](https://baike.baidu.com/item/窗口类)派生的窗口具有的风格。您可以用“or”[操作符](https://baike.baidu.com/item/操作符/8978896)来把几个风格或到一起。

**3.lpfnWndProc：**

窗口处理函数的指针。

**4.cbClsExtra：**

指定紧跟在窗口类结构后的附加字节数。

**5.cbWndExtra：**

指定紧跟在窗口实例的附加字节数。如果一个应用程序在资源中用CLASS[伪指令](https://baike.baidu.com/item/伪指令)注册一个对话框类时，则必须把这个成员设成DLGWINDOWEXTRA。

**6.hInstance：**

本模块的实例句柄。

**7.hIcon：**

图标的句柄。

**8.hCursor：**

光标的句柄。

**9.hbrBackground：**

背景画刷的句柄。

**10.lpszMenuName**：

指向菜单的指针。

**11.lpszClassName：**

指向类名称的指针。

**12.hIconSm：**

和[窗口类](https://baike.baidu.com/item/窗口类)关联的小图标。如果该值为NULL。则把hIcon中的图标转换成大小合适的小图标。

### 2.2注册窗口类

```cpp
RegisterClassEx(&wc);
```

wc为WNDCLASS的结构体

## 3. 创建窗口

```cpp
HWND hwnd = CreateWindow(wc.lpszClassName,L"我的第一个窗口程序",WS_OVERLAPPEDWINDOW,100,100,200,300,NULL,NULL,wc.hInstance,NULL);
```



### CreateWindow

[Windows](https://baike.baidu.com/item/Windows)API函数， 在WinUser.h中根据是否已定义Unicode被分别定义为CreateWindowW和CreateWindowA，然后两者又被分别定义为对CreateWindowExW和CreateWindowExA[函数](https://baike.baidu.com/item/函数)的[调用](https://baike.baidu.com/item/调用/3351318)

**函数原型**

```cpp
HWND CreateWindow(
LPCTSTR lpClassName,
LPCTSTR lpWindowName,
DWORD dwStyle,
int x,
int y,
int nWidth，
int nHeight，
HWND hWndParent,
HMENU hMenu，
HANDLE hlnstance，
LPVOID lpParam）；
```

**主要参数**

**pClassName**

指向一个空结束的字符串或整型数atom。如果该参数是一个[整型量](https://baike.baidu.com/item/整型量)，它是由此前调用theGlobalAddAtom函数产生的全局量。这个小于0xC000的16位数必须是lpClassName参数字的低16位，该参数的高位必须是0。

如果lpClassName是一个[字符串](https://baike.baidu.com/item/字符串)，它指定了窗口的类名。这个类名可以是任何用函数RegisterClass注册的类名，或是任何预定义的控制类名。请看说明部分的列表。

**LPWindowName**

指向一个指定窗口名的空结束的字符串[指针](https://baike.baidu.com/item/指针)。

如果窗口风格指定了标题条，由lpWindowName指向的窗口标题将显示在标题条上。当使用Createwindow函数来创建控制例如按钮，选择框和静态控制时，可使用lpWindowName来指定控制文本。

**dwStyle**

指定创建窗口的风格。该参数可以是下列窗口风格的组合再加上说明部分的控制风格。风格意义：

WS_BORDER：创建一个带边框的窗口。

WS_CAPTION：创建一个有标题框的窗口（包括WS_BORDER风格）。

WS_CHILD：创建一个子窗口。这个风格不能与WS_POPUP风格合用。

WS_CHILDWINDOW：与WS_CHILD相同。

WS_CLIPCHILDREN：当在父窗口内绘图时，排除子窗口区域。在创建父窗口时使用这个风格。

WS_CLIPSIBLINGS：排除子窗口之间的相对区域，也就是，当一个特定的窗口接收到WM_PAINT消息时，WS_CLIPSIBLINGS 风格将所有层叠窗口排除在绘图之外，只重绘指定的子窗口。如果未指定WS_CLIPSIBLINGS风格，并且子窗口是层叠的，则在重绘子窗口的客户区时，就会重绘邻近的子窗口。

WS_DISABLED：创建一个初始状态为禁止的子窗口。一个禁止状态的窗口不能接受来自用户的输入信息。

WS_DLGFRAME：创建一个带对话框边框风格的窗口。这种风格的窗口不能带标题条。

WS_GROUP：指定一组控制的第一个控制。这个控制组由第一个控制和随后定义的控制组成，自第二个控制开始每个控制，具有WS_GROUP风格，每个组的第一个控制带有WS_TABSTOP风格，从而使用户可以在组间移动。用户随后可以使用光标在组内的控制间改变键盘焦点。

WS_HSCROLL：创建一个有水平滚动条的窗口。

WS_ICONIC：创建一个初始状态为最小化状态的窗口。与WS_MINIMIZE风格相同。

WS_MAXIMIZE：创建一个初始状态为最大化状态的窗口。

WS_MAXIMIZEBOX：创建一个具有最大化按钮的窗口。该风格不能与WS_EX_CONTEXTHELP风格同时出现，同时必须指定WS_SYSMENU风格。

WS_OVERLAPPED：产生一个层叠的窗口。一个层叠的窗口有一个标题条和一个边框。与WS_TILED风格相同。

WS_OVERLAPPEDWINDOW：创建一个具有WS_OVERLAPPED，WS_CAPTION，WS_SYSMENU WS_THICKFRAME，WS_MINIMIZEBOX，WS_MAXIMIZEBOX风格的层叠窗口，与WS_TILEDWINDOW风格相同。

WS_POPUP：创建一个弹出式窗口。该风格不能与WS_CHILD风格同时使用。

WS_POPUPWINDOW：创建一个具有WS_BORDER，WS_POPUP,WS_SYSMENU风格的窗口，WS_CAPTION和WS_POPUPWINDOW必须同时设定才能使窗口某单可见。

WS_SIZEBOX：创建一个可调边框的窗口，与WS_THICKFRAME风格相同。

WS_SYSMENU：创建一个在标题条上带有[窗口菜单](https://baike.baidu.com/item/窗口菜单)的窗口，必须同时设定WS_CAPTION风格。

WS_TABSTOP：创建一个控制，这个控制在用户按下Tab键时可以获得键盘焦点。按下Tab键后使键盘焦点转移到下一具有WS_TABSTOP风格的控制。

WS_THICKFRAME：创建一个具有可调边框的窗口，与WS_SIZEBOX风格相同。

WS_TILED：产生一个层叠的窗口。一个层叠的窗口有一个标题和一个边框。与WS_OVERLAPPED风格相同。

WS_TILEDWINDOW:创建一个具有WS_OVERLAPPED，WS_CAPTION，WS_SYSMENU， WS_THICKFRAME，WS_MINIMIZEBOX，WS_MAXIMIZEBOX风格的层叠窗口。与WS_OVERLAPPEDWINDOW风格相同。

WS_VISIBLE：创建一个初始状态为可见的窗口。

WS_VSCROLL：创建一个有垂直滚动条的窗口。

**X**

指定窗口的初始水平位置。对一个层叠或弹出式窗口，X参数是屏幕坐标系的窗口的左上角的初始X坐标。对于子窗口，x是子窗口左上角相对父窗口客户区左上角的初始X坐标。如果该参数被设为CW_USEDEFAULT则系统为窗口选择缺省的左上角坐标并忽略Y参数。CW_USEDEFAULT只对层叠窗口有效，如果为弹出式窗口或子窗口设定，则X和y参数被设为零。

**Y**

指定窗口的初始垂直位置。对一个层叠或弹出式窗口，y参数是屏幕坐标系的窗口的左上角的初始y坐标。对于子窗口，y是子窗口左上角相对父窗口客户区左上角的初始y坐标。对于列表框，y是列表框客户区左上角相对父窗口客户区左上角的初始y坐标。如果层叠窗口是使用WS_VISIBLE风格位创建的并且X参数被设为CW_USEDEFAULT，则系统将忽略y参数。

**nWidth**

以设备单元指明窗口的宽度。对于层叠窗口，nWidth或是屏幕坐标的窗口宽度或是CW_USEDEFAULT。若nWidth是CW_USEDEFAULT，则系统为窗口选择一个缺省的高度和宽度：缺省宽度为从初始X坐标开始到屏幕的右边界，缺省高度为从初始Y坐标开始到目标区域的顶部。CW_USEDEFAULT只对层叠窗口有效；如果为弹出式窗口和子窗口设定CW_USEDEFAULT标志则nWidth和nHeight被设为零。

**nHeight**

以设备单元指明窗口的高度。对于层叠窗口，nHeight是屏幕坐标的窗口宽度。若nWidth被设为CW_USEDEFAULT，则系统忽略nHeight参数。

**hWndParent**

指向被创建窗口的父窗口或所有者窗口的[句柄](https://baike.baidu.com/item/句柄)。若要创建一个子窗口或一个被属窗口，需提供一个有效的[窗口句柄](https://baike.baidu.com/item/窗口句柄)。这个参数对弹出式窗口是可选的。Windows NT 5.0；创建一个消息窗口，可以提供HWND_MESSAGE或提供一个己存在的消息窗口的句柄。

**hMenu**

菜单句柄，或依据窗口风格指明一个子窗口标识。对于层叠或弹出式窗口，hMenu指定窗口使用的菜单：如果使用了菜单类，则hMenu可以为NULL。对于子窗口，hMenu指定了该子窗口标识（一个[整型量](https://baike.baidu.com/item/整型量)），一个对话框使用这个整型值将事件通知父类。应用程序确定子窗口标识，这个值对于相同父窗口的所有子窗口必须是唯一的。

**hlnstance**

与窗口相关联的模块实例的[句柄](https://baike.baidu.com/item/句柄)。

**lpParam**

指向一个值的[指针](https://baike.baidu.com/item/指针)，该值传递给窗口WM_CREATE消息。该值通过在IParam参数中的[CREATESTRUCT](https://baike.baidu.com/item/CREATESTRUCT)结构传递。如果应用程序调用CreateWindow创建一个MDI客户窗口，则lpParam必须指向一个CLIENTCREATESTRUCT结构。

返回值：如果函数成功，返回值为新窗口的句柄：如果函数失败，返回值为NULL。若想获得更多错误信息，请调用GetLastError函数。

**备注**

在返回前，CreateWindow给窗口过程发送一个WM_CREATE消息。对于层叠，弹出式和子窗口，CreateWindow给窗口发送WM_CREATE，WM_GETMINMAXINFO和WM_NCCREATE消息。消息WM_CREATE的IParam参数包含一个指向[CREATESTRUCT](https://baike.baidu.com/item/CREATESTRUCT)结构的指针。如果指定了WS_VISIBLE风格，CreateWindow向窗口发送所有需要激活和显示窗口的消息。

## 4. 显示窗口

```cpp
ShowWindow(hwnd, SW_SHOWDEFAULT);
```

**ShowWindow**

函数功能：该函数设置指定窗口的显示状态。

函数原型：BOOL ShowWindow(HWND hWnd, int nCmdShow);

**参数**

**hWnd:**指窗口句柄。

**nCmdShow：**指定窗口如何显示。如果发送应用程序的程序提供了STARTUPINFO结构，则应用程序第一次调用ShowWindow时该参数被忽略。否则，在第一次调用ShowWindow函数时，该值应为在函数WinMain中nCmdShow参数。在随后的调用中，该参数可以为下列值之一：

SW_FORCEMINIMIZE：在WindowNT5.0中最小化窗口，即使拥有窗口的线程被挂起也会最小化。在从其他线程最小化窗口时才使用这个参数。nCmdShow=11。

SW_HIDE：隐藏窗口并激活其他窗口。nCmdShow=0。

SW_MAXIMIZE：最大化指定的窗口。nCmdShow=3。

SW_MINIMIZE：最小化指定的窗口并且激活在Z序中的下一个顶层窗口。nCmdShow=6。

SW_RESTORE：激活并显示窗口。如果窗口最小化或最大化，则系统将窗口恢复到原来的尺寸和位置。在恢复最小化窗口时，应用程序应该指定这个标志。nCmdShow=9。

SW_SHOW：在窗口原来的位置以原来的尺寸激活和显示窗口。nCmdShow=5。

SW_SHOWDEFAULT：依据在STARTUPINFO结构中指定的SW_FLAG标志设定显示状态，STARTUPINFO 结构是由启动应用程序的程序传递给CreateProcess函数的。nCmdShow=10。

SW_SHOWMAXIMIZED：激活窗口并将其最大化。nCmdShow=3。

SW_SHOWMINIMIZED：激活窗口并将其最小化。nCmdShow=2。

SW_SHOWMINNOACTIVE：窗口最小化，激活窗口仍然维持激活状态。nCmdShow=7。

SW_SHOWNA：以窗口原来的状态显示窗口。激活窗口仍然维持激活状态。nCmdShow=8。

SW_SHOWNOACTIVATE：以窗口最近一次的大小和状态显示窗口。激活窗口仍然维持激活状态。nCmdShow=4。

SW_SHOWNORMAL：激活并显示一个窗口。如果窗口被最小化或最大化，系统将其恢复到原来的尺寸和大小。应用程序在第一次显示窗口的时候应该指定此标志。nCmdShow=1。

**返回值**

如果窗口之前可见，则返回值为非零。如果窗口之前被隐藏，则返回值为零。

（参考信息MSDN）

Return Values

If the window was previously visible, the return value is nonzero.

If the window was previously hidden, the return value is zero.

## 5. 更新窗口

```cpp
UpdateWindow(hwnd);
```

**UpdateWindow**

UpdateWindow，是计算机用语。意思是更新指定窗口的客户区。

**功能**

如果窗口更新的区域不为空，UpdateWindow函数就发送一个WM_PAINT消息来更新指定窗口的客户区。函数绕过应用程序的[消息队列](https://baike.baidu.com/item/消息队列)，直接发送WM_PAINT消息给指定窗口的窗口过程，如果更新区域为空，则不发送消息。

**函数原型**

```cpp
BOOL UpdateWindow(
HWND hWnd // 窗口的句柄
);
```

**参数**

[hWnd](https://baike.baidu.com/item/hWnd/1397706)

[输入] 要更新的窗口的句柄.

**返回值**

如果[函数调用](https://baike.baidu.com/item/函数调用)成功，[返回值](https://baike.baidu.com/item/返回值/9629649)为非零值。

如果函数调用不成功，返回值为零。

在Windows NT/2000/XP中，我们可以使用API 函数 GetLastError 来得到扩展的错误信息。

## 6. 消息循环

```cpp
MSG msg;
	while (GetMessage(&msg,NULL,0,0))
	{
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}
```

**MSG**

MSG是[Windows](https://baike.baidu.com/item/Windows)程序中的结构体。在Windows程序中，消息是由MSG结构体来表示的。成员变量含义：第一个[成员变量](https://baike.baidu.com/item/成员变量/11024994)hwnd表示消息所属的窗口；第二个[成员变量](https://baike.baidu.com/item/成员变量/11024994)message指定了消息的[标识符](https://baike.baidu.com/item/标识符/7105638)。第三、第四个[成员变量](https://baike.baidu.com/item/成员变量/11024994)wParam和lParam，用于指定消息的附加信息。最后两个[变量](https://baike.baidu.com/item/变量)分别表示消息投递到[消息队列](https://baike.baidu.com/item/消息队列/4751675)中的时间和鼠标的当前位置。

**结构体**

在Windows程序中，消息是由MSG结构体来表示的。MSG结构体的定义如下（参见MSDN）：

```cpp
typedef struct tagMSG {

HWND hwnd;

UINT message;

WPARAM wParam;

LPARAM lParam;

DWORD time;

POINT pt;

} MSG;
```

该[结构体](https://baike.baidu.com/item/结构体)中各[成员变量](https://baike.baidu.com/item/成员变量)的含义如下：

第一个[成员变量](https://baike.baidu.com/item/成员变量)hwnd表示消息所属的窗口。我们通常开发的程序都是窗口[应用程序](https://baike.baidu.com/item/应用程序)，一个消息一般都是与某个窗口相关联的。例如，在某个[活动窗口](https://baike.baidu.com/item/活动窗口)中按下鼠标左键，产生的按键消息就是发给该窗口的。在Windows程序中，用HWND类型的[变量](https://baike.baidu.com/item/变量)来标识窗口。

第二个[成员变量](https://baike.baidu.com/item/成员变量)message指定了消息的[标识符](https://baike.baidu.com/item/标识符)。在Windows中，消息是由一个数值来表示的，不同的消息对应不同的数值。但是由于数值不便于记忆，所以Windows将消息对应的数值定义为WM_XXX宏（WM是Window Message的缩写）的形式，XXX对应某种消息的英文拼写的大写形式。例如，鼠标左键按下消息是WM_LBUTTONDOWN，键盘按下消息是WM_KEYDOWN，字符消息是WM_CHAR，等等。在程序中我们通常都是以WM_XXX宏的形式来使用消息的。

提示：如果想知道WM_XXX消息对应的具体数值，可以在[Visual C++](https://baike.baidu.com/item/Visual C%2B%2B)[开发环境](https://baike.baidu.com/item/开发环境)中选中WM_XXX，然后单击鼠标右键，在弹出菜单中选择goto definition，即可看到该宏的具体定义。跟踪或查看某个[变量](https://baike.baidu.com/item/变量)的定义，都可以使用这个方法。

第三、第四个[成员变量](https://baike.baidu.com/item/成员变量)wParam和lParam，用于指定消息的附加信息。例如，当我们收到一个[字符](https://baike.baidu.com/item/字符)消息的时候，message[成员变量](https://baike.baidu.com/item/成员变量)的值就是WM_CHAR，但用户到底输入的是什么字符，那么就由wParam和lParam来说明。wParam、lParam表示的信息随消息的不同而不同。如果想知道这两个[成员变量](https://baike.baidu.com/item/成员变量)具体表示的信息，可以在MSDN中关于某个具体消息的说明文档查看到。读者可以在VC++的开发环境中通过goto definition查看一下WPARAM和LPARAM这两种类型的定义，可以发现这两种类型实际上就是unsigned int和long。

最后两个[变量](https://baike.baidu.com/item/变量)分别表示消息投递到[消息队列](https://baike.baidu.com/item/消息队列)中的时间和鼠标的当前位置。

hwnd

Identifies the window whose window procedure receives the message.

message

Specifies the message number.

wParam

Specifies additional information about the message. The exact meaning depends on the value of the message member.

lParam

Specifies additional information about the message. The exact meaning depends on the value of the message member.

time

Specifies the time at which the message was posted.

pt

Specifies the cursor position, in screen coordinates, when the message was posted.

**TranslateMessage**

TranslateMessage函数用于将虚拟键消息转换为字符消息。

**基本信息**

函数功能：该函数将虚拟键消息转换为字符消息。字符消息被寄送到调用线程的[消息队列](https://baike.baidu.com/item/消息队列)里，当下一次线程调用函数[GetMessage](https://baike.baidu.com/item/GetMessage)或[PeekMessage](https://baike.baidu.com/item/PeekMessage)时被读出。

函数原型：`BOOL TranslateMessage( CONST MSG*lpMsg );`

**参数**

IpMsg：指向含有消息的MSG结构的[指针](https://baike.baidu.com/item/指针)，该结构里含有用函数GetMessage或PeekMessage从调用线程的[消息队列](https://baike.baidu.com/item/消息队列)里取得的消息信息。

返回值：如果消息被转换（即，字符消息被寄送到调用线程的消息队列里），返回非零值。如果消息是WM_KEYDOWN，WM_KEYUP WM_SYSKEYDOWN或WM_SYSKEYUP，返回非零值，不考虑转换。如果消息没被转换（即，字符消息没被寄送到调用线程的消息队列里），返回值是零。

备注：此函数不修改由参数IpMsg指向的消息。

WM_KEYDOWN和WM_KEYUP组合产生一个WM_CHAR或WM_DEADCHAR消息。

WM_SYSKEYDOWN和WM_SYSKEYUP组合产生一个WM_SYSCHAR或 WM_SYSDEADCHAR消息。TranslateMessage为那些由键盘驱动器映射为ASCll字符的键产生WM_CHAR消息。

如果应用程序为其他用途处理虚拟键消息，不应调用TranslateMessage。例如，如果调用[TranslateAccelerator](https://baike.baidu.com/item/TranslateAccelerator)返回一个非零值，应用程序不应调用TranslateMessage。

Windows CE：Windows CE不支持扫描码或扩展键标志，因此，不支持由TranslateMessage产生的WM_CHAR消息中的IKeyData参数（IParam）取值16-24。

TranslateMessage只能用于转换调用GetMessage或PeekMessage接收的消息。

**DispatchMessage**

函数功能：该函数分发一个消息给窗口程序。通常消息从GetMessage函数获得或者TranslateMessage函数传递的。消息被分发到回调函数（过程函数)，作用是消息传递给操作系统，然后操作系统去调用我们的回调函数，也就是说我们在窗体的过程函数中处理消息。

函数原型：`LONG DispatchMessage（CONST MSG*lpmsg）；`

**参数：**

**lpmsg：**指向含有消息的MSG结构的[指针](https://baike.baidu.com/item/指针)。

返回值：返回值是窗口程序返回的值。尽管返回值的含义依赖于被调度的消息，但返回值通常被忽略。

备注：MSG结构必须包含有效的消息值。如果参数lpmsg指向一个WM_TIMER消息，并且WM_TIMER消息的参数IParam不为NULL，则调用IParam指向的函数，而不是调用窗口程序。

## 7. 窗口回调（窗口过程）

**Wndproc**

 Wndproc是Windows操作系统向应用程序发送一系列消息之一，每个窗口会有一个窗口过程的回调函数，分别是窗口句柄、消息ID、WPARAM、LPARAM。

**窗口过程**

左键按下和左键抬起，应用程序将通过*[GetMessage](https://baike.baidu.com/item/GetMessage)*等方法

[![Wndproc应用例子](DX学习.assets/0823dd54564e9258fcdac4959c82d158cdbf4eda)](https://baike.baidu.com/pic/Wndproc/6005293/0/245e8bcafc1bd0cdc817683b?fr=lemma&ct=single)Wndproc应用例子

最终将消息提交到**窗口过程**（**WndProc**[英文全称windows process]**)**指向一个应用程序定义的窗口过程的[指针](https://baike.baidu.com/item/指针)。

**回调函数**

每个窗口会有一个称为窗口过程的**回调函数**(**WndProc**)，它带有四个参数，分别为：

[窗口句柄](https://baike.baidu.com/item/窗口句柄)(Window Handle) HWND,

消息ID(Message ID) UINT,

和两个消息参数(wParam, lParam)WPARAM、LPARAM,

WndProc的第一个参数hWnd就是当前接收消息的窗口句柄，第二个参数就是被传送过来的消息，第三、第四个参数都是附加在消息上的数据，这和MSG结构体是一样的。

**窗口程序**

[指针](https://baike.baidu.com/item/指针)

[函数原型]

```cpp
LRESULT CALLBACK WndProc( //WndProc名称可自由定义
    HWND hwnd,
    UINT uMsg,
    WPARAM wParam,
    LPARAM lParam
)；
```

pOldProc:Pointer; //子类化前的窗口程序（WndProc）指针

## 全代码

```cpp
#include <Windows.h>

LRESULT CALLBACK WndProc(HWND,UINT,WPARAM,LPARAM);

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nShowCmd)
{
	//1.注册窗口类
	WNDCLASSEX wc;
	wc.cbClsExtra = NULL;
	wc.cbWndExtra = NULL;
	wc.cbSize = sizeof(WNDCLASSEX);
	wc.hbrBackground = (HBRUSH)GetStockObject(WHITE_BRUSH);
	wc.hCursor = LoadCursor(NULL, IDC_ARROW);
	wc.hIcon = LoadIcon(NULL, IDI_APPLICATION);
	wc.hIconSm = NULL;
	wc.hInstance = hInstance;
	wc.lpfnWndProc = WndProc;
	wc.style = CS_HREDRAW | CS_VREDRAW;
	wc.lpszClassName = L"MyClassName";
	wc.lpszMenuName = NULL;
	RegisterClassEx(&wc);

	//2.创建窗口
	HWND hwnd = CreateWindow(wc.lpszClassName,L"我的第一个窗口程序",WS_OVERLAPPEDWINDOW,100,100,200,300,NULL,NULL,wc.hInstance,NULL);
	
	//3.显示窗口
	ShowWindow(hwnd, SW_SHOWDEFAULT);
	
	//4.更新窗口
	UpdateWindow(hwnd);

	//5.消息循环
	MSG msg;
	while (GetMessage(&msg,NULL,0,0))
	{
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}

	return 0;
};

LRESULT CALLBACK WndProc(HWND hwnd, UINT message, WPARAM wParam, LPARAM lParam)
{

	switch (message)
	{
	default:
		return DefWindowProc(hwnd,message,wParam,lParam);
	}
}
```

# 创建D3D窗口

**Direct3DCreate9**

**函数原型:**`IDirect3D9 * WINAPI Direct3DCreate9(UINT SDKVersion)`

该函数的参数必须是D3D_SDK_VERSION,只有如此方能保证应用程序使用正确的头文件，如果该[函数调用](https://baike.baidu.com/item/函数调用)失败，将返回一个NULL[指针](https://baike.baidu.com/item/指针)

该函数的返回值是一个指向接口IDirect3D9的[指针](https://baike.baidu.com/item/指针)

注意：这个函数的使用需要有库文件“d3d9.lib”的存在。

**IDirect3D9**

Microsoft Direct3D 9 接口类。

IDirect3D9接口是创建Microsoft Direct3D 目标和配置环境的应用方法类。这个接口包含了枚举和检索设备性能和方法。

这个接口可使用**[Direct3DCreate9](https://baike.baidu.com/item/Direct3DCreate9)**函数创建。.

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **CheckDepthStencilMatch**                                   | Determines whether a depth-stencil format is compatible with a render-target format in a particular display mode. |
| **CheckDeviceFormat**                                        | Determines whether a surface format is available as a specified resource type and can be used as a texture, depth-stencil buffer, or render target, or any combination of the three, on a device representing this adapter. |
| **CheckDeviceFormatConversion**                              | Tests the device to see if it supports conversion from one display format to another. |
| **CheckDeviceMultiSampleType**                               | Determines if a multisampling technique is available on this device. |
| **CheckDeviceType**                                          | Verifies whether a hardware accelerated device type can be used on this adapter. |
| [**CreateDevice**](https://baike.baidu.com/item/CreateDevice) | 创建一个设备来表示显示适配器。                               |
| **EnumAdapterModes**                                         | Queries the device to determine whether the specified adapter supports the requested format and display mode. This method could be used in a loop to enumerate all the available adapter modes. |
| **GetAdapterCount**                                          | Returns the number of adapters on the system.                |
| **GetAdapterDisplayMode**                                    | Retrieves the current display mode of the adapter.           |
| **GetAdapterIdentifier**                                     | Describes the physical display adapters present in the system when the **IDirect3D9** interface was instantiated. |
| **GetAdapterModeCount**                                      | Returns the number of display modes available on this adapter. |
| **GetAdapterMonitor**                                        | Returns the handle of the monitor associated with the Direct3D object. |
| [**GetDeviceCaps**](https://baike.baidu.com/item/GetDeviceCaps) | Retrieves device-specific information about a device.        |
| **RegisterSoftwareDevice**                                   | Registers a pluggable software device. Software devices provide software rasterization enabling applications to access a variety of software rasterizers. |

相关类型：

```cpp
typedef struct IDirect3D9 *LPDIRECT3D9, *PDIRECT3D9;
```

引用：

| 头文件 | D3D9.h   |
| ------ | -------- |
| 库文件 | D3d9.lib |