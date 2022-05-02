# 使用java执行Powershell命令并输出
```java
package res;

import java.io.BufferedReader;
import java.io.InputStreamReader;

public class PowerShellCommand {
    String output =new String();
    String error =new String();
    public PowerShellCommand(String command) {

        try {
            //String command = "powershell.exe  your command";
            //Getting the version
//            command = "powershell.exe .\\sysInfo.ps1 -encoding utf-8";
            // Executing the command
            Process powerShellProcess = Runtime.getRuntime().exec(command);
            // Getting the results
            System.out.println();
            powerShellProcess.getOutputStream().close();
            String line;
            System.out.println("Standard Output:");
            BufferedReader stdout = new BufferedReader(new InputStreamReader(
                    powerShellProcess.getInputStream(), "GBK"));
            while ((line = stdout.readLine()) != null) {
                System.out.println(line);
                output += line + "\n";
//                System.out.println(result1);
            }
            stdout.close();
            System.out.println("Standard Error:");
            BufferedReader stderr = new BufferedReader(new InputStreamReader(
                    powerShellProcess.getErrorStream(), "GBK"));
            while ((line = stderr.readLine()) != null) {
                System.out.println(line);
                error = line;
            }
            stderr.close();
            System.out.println("Done");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public String getOutput(){
        return output;
    }

    public String getError() {
        return error;
    }

    public static void main(String[] args) {
        new PowerShellCommand("powershell.exe arp -a | where {$_ -match \"动态\\b\"}");
        new PowerShellCommand("powershell.exe arp -a | where {$_ -match \"\"\"动态\\b\"\"\"}");
    }

}
```
注意：在读取powershell输出内容时，如果内容中存在中文会出现乱码情况，这是因为其输出流默认编码格式为GBK，而java默认编码格式为Unicode，两者不一致导致的。只需在读取时将
`new InputStreamReader(powerShellProcess.getInputStream())`
改为`new InputStreamReader( powerShellProcess.getInputStream(), "GBK")`即可（上面代码已修改，放心使用）

引用链接：
1. https://www.pstips.net/question/8759.html
2. https://stackoverflow.com/questions/29545611/executing-powershell-commands-in-java-program
## 使用上述代码执行where语句筛选指定记录时遇到的问题：
最近想使用arp广播发现局域网中所有连接的活跃设备，因此在powershell执行命令`arp -a | where {$_ -match "动态\b"}`,发现可以正常输出，如图：
![](javaConnectPowershell_md_files/image_20220502145352.png?v=1&type=image&token=V1:7qKy0PYf2ZK5hattZues5yxyGEkDEc-nm7Bo7I_9diM)
使用java执行powershell命令
```java
public static void main(String[] args) {
        new PowerShellCommand("powershell.exe arp -a | where {$_ -match \"动态\\b\"}");
}
```
发现错误：
![](javaConnectPowershell_md_files/image_20220502145514.png?v=1&type=image&token=V1:r_sXSgO6RknHM69Rwca4W9qLdkyHz-FzIXZLkgLIYLY)
怀疑是String类型中的双引号`"`未被成功转义，使用
```java
public static void main(String[] args) {
        new PowerShellCommand("powershell.exe arp -a | where {$_ -match \"\"\"动态\\b\"\"\"}");
}
```
发现可以成功输出
![](javaConnectPowershell_md_files/image_20220502145930.png?v=1&type=image&token=V1:2GrPSv_UfCVunQ2IVo2d844viTPU7YHZVlEGXzdHavI)

