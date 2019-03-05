晚上Bugku练习日志审计题目，解题思路分享给大家，希望大家一起能交流学习共进步！

原始数据如下图所示：

![1551790960968](C:\Users\jcs\AppData\Roaming\Typora\typora-user-images\1551790960968.png)

可以看出，需要进行URL解码

使用解码工具进行处理（Burpsuite自带Decoder模块或其他工具都可），此处使用Huntpad进行解码

![1551791072643](C:\Users\jcs\AppData\Roaming\Typora\typora-user-images\1551791072643.png)

注意此处有个坑，应为最开始位置对应的攻击信息是干扰信息，最终的Flag信息在下方

![1551791171495](C:\Users\jcs\AppData\Roaming\Typora\typora-user-images\1551791171495.png)

在此log信息中，主要有三处关键信息：字符位置索引、字符ASCII码比较值、判断返回值

由攻击可判断出，当返回码为200时，为前方判断成功，返回码为404时，为前方判断失败，例如：比较索引位置为1的字符ASCII码值，如下图：

![1551791653646](C:\Users\jcs\AppData\Roaming\Typora\typora-user-images\1551791653646.png)

当以64、96进行比较时，返回200，说明字符ASCII值大于64、96；

当与112、104进行比较时，返回404，说明字符ASCII值小于或等于112、104；

再与100、102进行比较时，分别返回200、404，说明字符ASCII值大于100，小于或等于102；

再与101进行比较时，返回200，说明字符ASCII值大于101，因此结合上条判断：第一位字符ASCII值为102，即为"f"

类似，可进行其他位置的判断，其实仅关注最终一条的判断结果即可：

若返回200，则说明此时ASCII值与前面比较值相比，是大于的，进行 +1操作即可；

若返回404，则说明此时ASCII值与前面比较值相比，是等于的，直接使用前面比较值即可



由于手工比较相对较笨，因此编码Python脚本进行自动化查询（所选用内容为清洗过数据：即首先进行URL解码，其次将前面无用数据进行删除，仅保留与flag相关的文本信息，如下图：

![1551792084649](C:\Users\jcs\AppData\Roaming\Typora\typora-user-images\1551792084649.png)

具体代码如下所示：

![1551792154167](C:\Users\jcs\AppData\Roaming\Typora\typora-user-images\1551792154167.png)

以上就是解决Bugku中日志审计的思路，有问题请随时联系我，谢谢。

Python代码编写水平请大佬轻喷......