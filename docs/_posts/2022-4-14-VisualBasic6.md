# 安装

* 从msdn下载vb6.0安装包
* 安装时卡在 正在检查已安装的。。。
* 推测是Win10不能兼容99年的vb6.0
* 下载虚拟机
* 安装XP系统
* 安装vmvare tools
* 设置共享文件夹
* 将vb6.0传送到虚拟机
* 安装vb6.0
* 安装路径最好为全英文且不可有空格
* 安装msdn出问题，选择路径报错
* msdn是帮助程序，暂时不安装，跳过

# 编程

实现简单的读取计算机txt文件，并写入新的文件

**编辑器页面**

![编辑器界面](https://gitee.com/modige/modige/raw/master/_posts/imgs/vbtxt2.jpg)



**成品演示**

![软件预览](https://gitee.com/modige/modige/raw/master/_posts/imgs/vbtxt.jpg)

```vb
'注释用单引号
'对应驱动器列表组件   将该驱动器下的文件夹名称用变量Dir1
Private Sub Drive1_Change()
Dir1.Path = Drive1.Drive
End Sub

'对应文件夹列表组件   
'将该驱动器下的文件名称用变量File1存放

Private Sub Dir1_Change()
File1.Path = Dir1.Path
End Sub

'对应文件路径组件   
'当点击文件框中的某个文件后，将该文件对应的路径及文件名存放在
'变量 filePath 中，此时会自动显示在文件路径框

Private Sub File1_Click()
filePath.Text = ""   ' 这一句是为了清理上一次点击留下的文件名，不然多次点击会将多个文件名称拼接在一起
filePath.Text = Dir1.Path + "\" + File1.FileName
End Sub


'处理打开按钮
'点击打开后，从变量filePath中读取文件的路径及文件名
'根据文件名将文件中的数据读取到内存
'创建新的文件d:\new.txt，并把之前的数据写入其中
'关闭文件对象
Private Sub Command1_Click()
Dim strFile     As String

Dim intFile     As Integer

Dim strData     As String

strFile = filePath.Text
intFile = FreeFile
Open strFile For Input As intFile
strData = StrConv(InputB(FileLen(strFile), intFile), vbUnicode)

Open "d:\new.txt" For Output As #2
Print #2, strData
Close #1
Close #2
End Sub
```

