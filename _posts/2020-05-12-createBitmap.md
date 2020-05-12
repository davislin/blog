---
layout: post
title: c＃用hwnd截圖，無論是否被遮擋?
---
{% highlight c# %}
 [DllImport("gdi32.dll")]
 public static extern IntPtr CreateCompatibleDC(
 IntPtr hdc // handle to DC
 );
 [DllImport("gdi32.dll")]
 public static extern IntPtr CreateCompatibleBitmap(
 IntPtr hdc, // handle to DC
 int nWidth, // width of bitmap, in pixels
 int nHeight // height of bitmap, in pixels
 );
 [DllImport("gdi32.dll")]
 public static extern IntPtr SelectObject(
 IntPtr hdc, // handle to DC
 IntPtr hgdiobj // handle to object
 );
 [DllImport("gdi32.dll")]
 public static extern int DeleteDC(
 IntPtr hdc // handle to DC
 );
 [DllImport("user32.dll")]
 public static extern bool PrintWindow(
 IntPtr hwnd, // Window to copy,Handle to the window that will be copied.
 IntPtr hdcBlt, // HDC to print into,Handle to the device context. 
 UInt32 nFlags // Optional flags,Specifies the drawing options. It can be one of the following values. 
 );
 [DllImport("user32.dll")]
 public static extern IntPtr GetWindowDC(
 IntPtr hwnd // Window to copy,Handle to the window that will be copied.
 );
#region 使用PrintWindow方法抓取窗口，無論控件是否被遮擋
/// 
/// 窗口的截圖，窗口被遮擋時也可以正確截圖，使用PrintWindow方法  
///   
///需要被截圖的窗口  
/// 窗口的截圖，控件被遮擋時也可以正確截圖  
private static Bitmap GetWindow(IntPtr hWnd)
{
    IntPtr hscrdc = GetWindowDC(hWnd);
    Rectangle rect = new Rectangle();
    GetWindowRect(hWnd, out rect);
    IntPtr hbitmap = CreateCompatibleBitmap(hscrdc, rect.Width, rect.Height);
    IntPtr hmemdc = CreateCompatibleDC(hscrdc);
    SelectObject(hmemdc, hbitmap);
    PrintWindow(hWnd, hmemdc, 0);
    Bitmap bmp = Bitmap.FromHbitmap(hbitmap);
    DeleteDC(hscrdc);//刪除用過的對象  
    DeleteDC(hmemdc);//刪除用過的對象  
    return bmp;
}
#endregion 

{% endhighlight %}
