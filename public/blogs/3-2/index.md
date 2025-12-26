

```
Sub 删除符号并判断类型()

  Dim cell As Range
  Dim str As String
  Dim i As Integer
  Dim isPureLetter As Boolean
  Dim isPureNumber As Boolean
  Dim hasChinese As Boolean

  For Each cell In Range("AB2:AB30000") ' 遍历AB2到AB30000的单元格

    str = cell.Value ' 获取单元格内容

    ' 判断是否包含中文
    hasChinese = False
    For i = 1 To Len(str)
      If AscW(Mid(str, i, 1)) > 255 Then ' 中文字符的ASCII码大于255
        hasChinese = True
        Exit For
      End If
    Next i

    ' 如果包含中文，则删除符号（区分全角半角）
    If hasChinese Then
      str = Replace(str, "！", "") ' 全角感叹号
      str = Replace(str, "＠", "") ' 全角@
      str = Replace(str, "＃", "") ' 全角#
      str = Replace(str, "＄", "") ' 全角$
      str = Replace(str, "％", "") ' 全角%
      str = Replace(str, "＾", "") ' 全角^
      str = Replace(str, "＆", "") ' 全角&
      str = Replace(str, "＊", "") ' 全角*
      str = Replace(str, "（", "") ' 全角(
      str = Replace(str, "）", "") ' 全角)
      str = Replace(str, "－", "") ' 全角-
      str = Replace(str, "＿", "") ' 全角_
      str = Replace(str, "＋", "") ' 全角+
      str = Replace(str, "＝", "") ' 全角=
      str = Replace(str, "［", "") ' 全角[
      str = Replace(str, "］", "") ' 全角]
      str = Replace(str, "｛", "") ' 全角{
      str = Replace(str, "｝", "") ' 全角}
      str = Replace(str, "｜", "") ' 全角|
      str = Replace(str, "；", "") ' 全角;
      str = Replace(str, "：", "") ' 全角:
      str = Replace(str, "’", "") ' 全角'
      str = Replace(str, "，", "") ' 全角逗号
      str = Replace(str, "＜", "") ' 全角<
      str = Replace(str, "＞", "") ' 全角>
      str = Replace(str, "／", "") ' 全角/
      str = Replace(str, "？", "") ' 全角?
      str = Replace(str, "。", "") ' 全角句号
      str = Replace(str, "～", "") ' 全角~
      str = Replace(str, "｀", "") ' 全角`
      str = Replace(str, "　", "") ' 全角空格

      str = Replace(str, "!", "") ' 半角感叹号
      str = Replace(str, "@", "") ' 半角@
      str = Replace(str, "#", "") ' 半角#
      str = Replace(str, "$", "") ' 半角$
      str = Replace(str, "%", "") ' 半角%
      str = Replace(str, "^", "") ' 半角^
      str = Replace(str, "&", "") ' 半角&
      str = Replace(str, "*", "") ' 半角*
      str = Replace(str, "(", "") ' 半角(
      str = Replace(str, ")", "") ' 半角)
      str = Replace(str, "-", "") ' 半角-
      str = Replace(str, "_", "") ' 半角_
      str = Replace(str, "+", "") ' 半角+
      str = Replace(str, "=", "") ' 半角=
      str = Replace(str, "[", "") ' 半角[
      str = Replace(str, "]", "") ' 半角]
      str = Replace(str, "{", "") ' 半角{
      str = Replace(str, "}", "") ' 半角}
      str = Replace(str, "|", "") ' 半角|
      str = Replace(str, ";", "") ' 半角;
      str = Replace(str, ":", "") ' 半角:
      str = Replace(str, "'", "") ' 半角'
      str = Replace(str, ",", "") ' 半角逗号
      str = Replace(str, "<", "") ' 半角<
      str = Replace(str, ">", "") ' 半角>
      str = Replace(str, "/", "") ' 半角/
      str = Replace(str, "?", "") ' 半角?
      str = Replace(str, ".", "") ' 半角句号
      str = Replace(str, "~", "") ' 半角~
      str = Replace(str, "`", "") ' 半角`
      str = Replace(str, " ", "") ' 半角空格

      cell.Offset(0, 1).Value = str ' 在AC列输出结果

    Else ' 如果不包含中文，则判断是否为纯字母、纯数字或字母数字组合
      ' 判断是否为纯字母或纯数字
      isPureLetter = True
      isPureNumber = True
      For i = 1 To Len(str)
        If Not (Mid(str, i, 1) >= "a" And Mid(str, i, 1) <= "z" Or Mid(str, i, 1) >= "A" And Mid(str, i, 1) <= "Z") Then
          isPureLetter = False
        End If
        If Not (Mid(str, i, 1) >= "0" And Mid(str, i, 1) <= "9") Then
          isPureNumber = False
        End If
      Next i

      ' 输出结果
      If isPureLetter Or isPureNumber Or (Not isPureLetter And Not isPureNumber And Len(str) > 0) Then ' 修改判断条件
        cell.Offset(0, 1).Value = "无" ' 在AC列输出结果
      Else
        cell.Offset(0, 1).Value = str
      End If
    End If

  Next cell

End Sub
```

### 主要修改说明

- 在删除符号时，分别列出了全角和半角符号进行替换，确保能够区分处理。

### 使用方法

### 使用方法

1. 打开VBA编辑器（Alt + F11）。
2. 在“插入”菜单中选择“模块”。
3. 将上述代码复制到模块中。
4. 关闭VBA编辑器。
5. 在Excel中运行宏（可以通过“视图”菜单中的“宏”命令来运行）。