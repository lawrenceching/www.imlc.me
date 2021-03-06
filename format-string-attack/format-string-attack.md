# 格式化字符串攻击（Format String Attack）
> 本文为 [Format string attack](https://www.owasp.org/index.php/Format_string_attack) 的中文翻译。遵守 [署名-相同方式共享 4.0 国际 (CC BY-SA 4.0) 许可协议](https://creativecommons.org/licenses/by-sa/4.0/deed.zh)   

## 描述
格式化字符串攻击是指恶意用户提交的字符串数据被当成命令执行。利用此漏洞，攻击者可以执行恶意代码、读取栈信息或者导致分页错误。这会降低你应用的安全性和稳定性。

要明白该攻击，首先你要明白该攻击需要具备的元素。

* 格式化函数需要是一个遵守 ANSI C 的函数。这些函数把原始变量转换成人类可读格式。例如 `printf`和`fprintf`。

* 格式化函数接受一个 ASCII Z 的字符串作为参数。字符串包含了文本和格式化参数，例如 `printf ("The magic number is: %d\n", 1911)`

* 字符串格式化参数决定了如何去格式化字符串

如果没有做合适的输入校验，应该就可能受到此种攻击。在这种情况下，如果一个携带了字符串格式化参数（如 `%x`）的输入数据原原本本被传递到格式化函数中，在执行转换时，格式化函数会要求额外的输入数据（因为多了个 `%x`）。我们无法提供额外的输入数据，此时格式化函数就会去从栈中读取数据或者写入数据到栈中。

攻击者可以利用此漏洞，精心地构输入来改变格式化函数的行为，来达到停止服务或者执行任意代码的目的。如果你源代码中使用了类似的格式化函数，那么你的程序很可能就会暴露在这种攻击之下。例如，你的网页中根据用户提交的用户名，通过 `printf`  函数来输出网页的其他内容，那么你就可能处在格式化字符串攻击的风险之中。

原文列出了容易遭受攻击的字符串格式化函数和对应的格式化参数。并且提供了2个基于 C++ 的列子。详细内容请点击
[Format string attack - OWASP](https://www.owasp.org/index.php/Format_string_attack)

