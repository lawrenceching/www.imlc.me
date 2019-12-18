# 函数注入（Function Injection）
> 本文为 [Function Injection - OWASP](https://www.owasp.org/index.php/Function_Injection) 的中文翻译。遵守 [署名-相同方式共享 4.0 国际 (CC BY-SA 4.0) 许可协议](https://creativecommons.org/licenses/by-sa/4.0/deed.zh)   

函数注入是指你的应用根据用户输入的函数名来调用对应的函数。函数注入攻击是注入攻击的一种，恶意用户能借此漏洞调用任意函数。有时候，函数的参数也可以注入，最严重的情况可能会让攻击者远程执行任意代码。

该漏洞可以很容易发现，也可能极为隐蔽。取决于具体场景，有时候该漏洞并不容易被利用。但是万一被攻击者利用，很容易造成机密泄露、服务不完整或者不可用或者被追究责任。

## 例子
如果你的网站对外暴露了一条这样的接口，用户可以通过 action 参数提交操作
```
http://testsite.com/index.php?action=edit
```

```php
<?php
$action = $_GET['action'];
$action();
?>
```

那么，攻击者就可以通过构造恶意的操作来获取不应该获取的信息或者执行非法的操作。

```
http://testsite.com/index.php?action=phpinfo
```
