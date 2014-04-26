---
title: CAS认证系统认证流程
category: way-to-explore
layout: post
---

# 什么是CAS？

深大有一个统一的身份认证系统，登录Blackboard、图书馆帐号都免不了跟它打交道。

![深大统一身份认证系统][1]

这个系统就是一个CAS认证系统。CAS是Yale大学发起的一个开源项目，其目标是为Web应用系统提供一种可靠的单点登录方法，使得在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。(以上文字来源于Google)

# 如何接入CAS认证系统

应用开发者要怎么使自己的应用接入这个CAS认证系统呢？下图大致说明了CAS的认证流程。

![CAS认证流程][2]

# 代码示例

理解了认证流程，下面这段示例代码(php语言)也就不难理解了：

    <?php
        session_start(); 
        header("Content-Type: text/html;charset=gb2312");
    
         // 我们的应用的地址
        $MyServer = "http://eme.example.com";
         // 认证服务器的地址
        $CASserver = "https://auth.example.com/cas.aspx/";
         // 回调页面的地址(也就是这个页面的地址)
        $ReturnURL = "http://eme.example.com/sdcms/test";
            
         // 认证服务器返回一个标识用户唯一信息的键 ticket
        if(isset($_REQUEST["ticket"]))
         {
             // 根据这个键构造请求用户信息的连接
             $URL = $CASserver . "serviceValidate?ticket=" . $_GET["ticket"] . "&service=". $ReturnURL;
             // 创建一个http请求，从服务器获取数据
             $xmlhttp = new COM("MSXML2.XMLHTTP"); 
             $xmlhttp->open("GET",$URL,false,null,null); 
             $xmlhttp->send(); 
             $xmlString = $xmlhttp->responseText;
             // 从返回的数据中取出需要的
             $_SESSION['cas']['name']= RegexLog($xmlString, "name");//姓名
             $_SESSION['cas']['organize']= RegexLog($xmlString, "organize");//单位
             $_SESSION['cas']['gender']= RegexLog($xmlString, "gender");//性别
             $_SESSION['cas']['no']= RegexLog($xmlString, "no");//编号
             $_SESSION['cas']['icaccount']= RegexLog($xmlString, "icaccount");//卡号
             $_SESSION['cas']['rank']= RegexLog($xmlString, "rank");//用户类别
             
             // 重定向(这样用户的地址栏就看不到ticket这串键值了，
             // 再次访问这个页面也不用再去认证服务器那边再请求一次数据)
             header("Location: ".$ReturnURL);
        }
        
        // 通过正则匹配返回的字符串，取出$subStr所对应的数据
        function RegexLog($xmlString,$subStr)
        {
            preg_match('/&lt;cas:'.$subStr.'>(.*)&lt;\/cas:'.$subStr.'>/i',$xmlString,$matches);
            return $matches[1];  
        }
    ?>
    <div>
      <?php
          // 测试页，所以以此打印出获取到的用户信息
          if(isset($_SESSION['cas']))
          {
              foreach($_SESSION['cas'] as $key => $value)
                  echo $key."=>".$value."<br />";
          }
      ?>
    </div>
    <hr />
    <div>
      <a href="<? echo $CASserver."login?service=".$ReturnURL ?>">点击登录</a>
      <a href="<? echo $ReturnURL ?>">注销SESSION</a>
      <a href="<? echo $CASserver."logout?ReturnUrl=".$ReturnURL ?>">注销CAS</a>
    </div>

对我们应用而言，最关键的步骤是第5、6、7步，第五步获取ticket的值，这个值就是用户给我们的授权。我们拿着这个授权，就能向CAS服务器索要这个用户的数据(前提是这份授权能要到那些数据)，这就是第六步干的事情。CAS认证服务器在第七步返回给了我们所要的数据，我们所需要做的，就是把数据取出来，收好，就完成了整个流程。

# 使用技巧

## session的作用

在启用了session的服务器上，用户访问我们的应用时，服务器会为这个用户生成一个session，这个session能用来唯一标识一个用户，用户执行到第四步的时候，他访问我们应用的页面时，不仅带回了ticket的值，也把session的值带回了，因此我们能根据session和ticket把我们的用户，和CAS认证服务器那边的用户对应起来（我们就能知道是哪个用户去CAS那边认证了）这个有点难懂，结合session的原理和CAS的流程多理解一下。

 [1]: http://sr1-me.qiniudn.com/130919/01.png
 [2]: http://sr1-me.qiniudn.com/130919/02.png
