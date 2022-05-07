# PowerShell修改注册表实现禁用和启用U盘功能
我们都知道，利用注册表可以实现禁用U盘的功能（不会的可以参考下[公司电脑如何禁用U盘 (baidu.com)](https://baijiahao.baidu.com/s?id=1711677291142123259)），可是如何编写Powershell命令修改注册表以达到相同的功能呢？
首先使用PowerShell切换到指定注册表路径
```powershell
cd HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR
```
效果如图：
![56847b8f3c3f18513759fd7c625bdcad.png](56847b8f3c3f18513759fd7c625bdcad.png)
然后执行命令`Set-ItemProperty -Path . -PSProperty start -Value 4`即可实现禁用U盘。
**需要注意几点**：
1. 这里的`.`表示当前路径，即`HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR`，是`Set-ItemProperty`命令的必要参数，不可省略
2. 本禁用功能只在执行命令后的电脑上生效，对于在执行命令前已插入的U盘无效，不能做到禁用已插入的U盘的功能。
3. 如果嫌步骤麻烦，可以复制下面的代码直接使用：
```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR -PSProperty start -Value 4
```
## 实现效果
为便于观察，我们可以按住`win + R`，输入`regedit`并回车找到路径`HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR`，如图：
![d394b8b2f4bc1de8d223f532e4958059.png](d394b8b2f4bc1de8d223f532e4958059.png)
可以看到，在未执行命令时Start的值是3
执行命令后：
![a0257bb0897a6e973b1b8f0c585690c3.png](a0257bb0897a6e973b1b8f0c585690c3.png)
可以看到，Start的值已被成功修改为4。
同样的，若要启用U盘设备，只需将Start的值改为4即可，命令如下：
`Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR -PSProperty start -Value 3`
效果如图：
![bfb0df47d695b19ff0d5c23e763fe721.png](bfb0df47d695b19ff0d5c23e763fe721.png)
## 拓展：远程连接计算机实现PowerShell修改注册表实现禁用和启用U盘功能
只需执行以下命令即可
```powershell
 Invoke-Command -ComputerName 此处填写要连接的远程计算机IP地址 -Credential 此处填写要连接的用户名\administrator -scriptblock {Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR -PSProperty start -Value 4}
 # 举例：
 Invoke-Command -ComputerName 192.168.174.218 -Credential User\administrator -scriptblock {Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR -PSProperty start -Value 4}
```
如有问题，可以参考微软官方文档：[PowerShell 远程常见问题解答 - PowerShell | Microsoft Docs](https://docs.microsoft.com/zh-cn/powershell/scripting/learn/remoting/powershell-remoting-faq?view=powershell-7.2)

参考链接：
1. [如何封鎖與解除USB埠，以確保電腦資料與安全 - 挨踢路人甲 (walker-a.com)](https://walker-a.com/archives/3701#:~:text=%E5%9C%A8%E3%80%8CHKEY_LOCAL_MACHINESYSTEMCurrentControlSetServicesUSBSTOR%E3%80%8D%E8%B7%AF%E5%BE%91%E4%B8%8B%E6%89%BE%E5%88%B0%E3%80%8CStart%E3%80%8D%E9%A0%85%E7%9B%AE%E4%BE%86%E4%BF%AE%E6%94%B9%EF%BC%88%E5%A6%82%E4%B8%8B%E5%9C%96%EF%BC%89%E3%80%82,%E5%B0%87%E3%80%8CStart%E3%80%8D%E4%BF%AE%E6%94%B9%E7%82%BA%E3%80%8C4%E3%80%8D%E4%B8%A6%E6%8C%89%E4%B8%8B%E3%80%94%E7%A2%BA%E5%AE%9A%E3%80%95%E5%8D%B3%E5%8F%AF%E5%B0%87USB%E7%9A%84%E5%8A%9F%E8%83%BD%E9%97%9C%E9%96%89%EF%BC%8C%E6%83%B3%E8%A6%81%E6%89%93%E9%96%8B%E5%86%8D%E5%B0%87%E5%85%B6%E5%80%BC%E6%94%B9%E7%82%BA3%E5%8D%B3%E5%8F%AF%E6%81%A2%E5%BE%A9%EF%BC%8C%E6%98%AF%E4%B8%8D%E6%98%AF%E5%BE%88%E7%B0%A1%E5%96%AE%E5%91%A2%EF%BC%9F)
2. [注册表限制使用U盘的几种方法_天下第一刀2020的博客-CSDN博客_注册表禁用u盘](https://blog.csdn.net/lovegod12/article/details/4161124)