# vi 快捷键

Linux终端常用快捷键：
```
Ctrl+ d 删除一个字符，相当于通常的Delete键（命令行若无任何字符，则相当于exit；处理多行标准输入时也表示EOF
）
Ctrl+ h 退格删除一个字符，相当于通常的Backspace键
Ctrl+ u 删除光标之前到行首的字符
Ctrl+ k 删除光标之前到行尾的字符
Ctrl+ c 取消当前行输入的命令，相当于Ctrl+ Break
Ctrl+ a 光标移动到行首（Ahead ofline），相当于通常的Home键
Ctrl+ e 光标移动到行尾（End ofline）
Ctrl+ f 光标向前(Forward)移动一个字符位置
Ctrl+ b 光标往回(Backward)移动一个字符位置
Ctrl+ l 清屏，相当于执行clear命令
Ctrl+ p 调出命令历史中的前一条（Previous）命令，相当于通常的上箭头
Ctrl+ n 调出命令历史中的下一条（Next）命令，相当于通常的上箭头
Ctrl+ r 显示：号提示，根据用户输入查找相关历史命令（reverse-i-search）
<<<<<<< HEAD
```

- 替换
```
:s/vivian/sky/ 替换当前行第一个 vivian 为 sky
:s/vivian/sky/g 替换当前行所有 vivian 为 sky
:%s/vivian/sky/（等同于：g/vivian/s//sky/）替换每一行的第一个 vivian 为 sky
:%s/vivian/sky/g（等同于：g/vivian/s//sky/g）替换每一行中所有 vivian 为 sky
=======
>>>>>>> 5789a7766ad25e419d5952f9d84591c6007f79aa
```