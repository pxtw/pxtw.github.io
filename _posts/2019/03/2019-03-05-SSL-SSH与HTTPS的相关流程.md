---
layout: post
title: 'SSL/SSH与HTTPS的相关流程'
date: 2019-03-05T20:54:24+11:00
location: 
---


<h2 class="entry-title" style="background-color: white; color: #222222; font-family: arial, helvetica, sans-serif; font-size: 1.6em; font-weight: 400; line-height: 27.2384px; margin: 0px 0px -0.3em; text-transform: uppercase;">
从非对称加密说起</h2>
<div class="entry-content" style="background-color: white; color: #222222; font-family: georgia, times, serif; font-size: 12.8px;">
<div style="margin-bottom: 1em; margin-top: 1em;">
在非对称加密方式出现之前，人们都是使用对称加密的方式安全传输数据。其基本的模型就是：</div>
<ol>
<li>数据发送端使用加密规则A对数据进行加密</li>
<li>传输数据</li>
<li>数据接收端通过加密规则A得到一个必然的解密规则A’，使用解密规则A’对数据进行解密。</li>
</ol>
<div style="margin-bottom: 1em; margin-top: 1em;">
这种加密模型有一个必然的缺陷，就是发送端需要加密传输数据给接收端时，如何安全的协商加密方式。（加密方式的协商，本身就是一个通信过程，然而这个过程是完全裸露无加密算法保护的。）</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
于是，非对称加密出现了。</div>
<ol>
<li>接收方创建两个钥匙，分别作为<strong>约束发送方加密方式的钥匙</strong>（公钥）和<strong>自己解密的钥匙</strong>（私钥）。</li>
<li>使用算法保证，约束发送方加密方式的钥匙可以公开给那些有需求给自己通信的人，而自己解密的钥匙只有自己知道。</li>
<li>这样就确保了，即使窃听者即使在裸露的信道中拿到了公开传播的那把钥匙，也只能用来加密数据，却没有办法解密别人传给接收方的数据。</li>
</ol>
<h2 style="font-family: arial, helvetica, sans-serif; font-weight: 400; margin: 1.5em 0px -0.3em;">
SSH</h2>
<div style="margin-bottom: 1em; margin-top: 1em;">
SSH名为Secure Shell，它的出现使得人们可以使用安全的方式完成FTP，Telnet等所包含的功能。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
SSH是对上述非对称加密的一大实践，服务端提供公钥给客户端，客户端<strong>将对称加密密钥K用公钥加密发送给服务端</strong>。服务端收到通信密钥后，使用对称加密完成之后的通信。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
如果在信道中存在窃听者，他单纯地想通过获取对称加密密钥K的方式来达到窃听的目的。那么，因为加密密钥K的传输是基于服务端提供的公钥加密的，而只有私钥才能解密出加密密钥K，私钥又仅存在服务器中。这就完全的杜绝了这一目的实现的可能。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
但是，如果窃听者并不打算破解密钥K。而是，把自己伪装成服务器：</div>
<ol>
<li>截取服务器的公钥</li>
<li>伪造一套公钥私钥</li>
<li>把自己的公钥发送给客户端</li>
</ol>
<div style="margin-bottom: 1em; margin-top: 1em;">
作为一个角色参与者一套流程，这样就会出现一个很大的破绽，服务器和客户端双方都会被窃听且不知情，这就是所谓的中间人攻击。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
SSH提供的方法是：在公钥发送给客户端的阶段，必须得经过一个流程——显示出一个人类可读的fingerprint来标识公钥，同时登陆人员拿着这个公钥去比对真正的公钥（比如在官方网站，比如在服务器本机记录下来）这样就加大了安全性，来抵抗中间人攻击（<del style="background: rgb(235, 237, 239); color: #31343a;">除非网站上获取的数据也是假的，本机记录的数据也是不可靠的</del>）。如果比对成功，需要人为的输入yes。</div>
<h3 style="font-family: arial, helvetica, sans-serif; font-size: 1.5em; font-weight: 400; margin: 1.5em 0px -0.3em;">
<a href="http://erik-2-blog.logdown.com/posts/74081-ssh-principle" style="border-bottom: 1px solid rgb(154, 161, 174); color: #677284; outline: none; text-decoration-line: none;">SSH 处理过程</a></h3>
<ol>
<li>协议协商阶段</li>
<li>服务端认证阶段</li>
<li>客户端认证阶段</li>
<li>数据传输阶段</li>
</ol>
<h5 style="font-family: arial, helvetica, sans-serif; font-size: 1.3em; font-weight: 400; margin: 1.5em 0px -0.4em;">
协议协商阶段</h5>
<ol>
<li>服务端打开服务端口（默认为22），等待客户端连接</li>
<li>客户端发起TCP连接请求，服务端接收到该请求后，向客户端发送包括SSH协议版本信息</li>
<li>客户端接根据该版本信息与自己的版本，决定将要使用的SSH版本，并向服务端发送选用的SSH版本信息</li>
<li>服务端检查是否支持客户端的决定使用的SSH版本</li>
</ol>
<div style="margin-bottom: 1em; margin-top: 1em;">
至此，双方完成协议协商。如果该过程中，客户端或服务端发送SSH版本无法兼容，任何一方都可以断开连接。</div>
<h5 style="font-family: arial, helvetica, sans-serif; font-size: 1.3em; font-weight: 400; margin: 1.5em 0px -0.4em;">
服务端认证阶段</h5>
<div style="margin-bottom: 1em; margin-top: 1em;">
完成协议协商阶段后，客户端与服务端已经建立<strong>明文</strong>的通信通道，之后进入服务端认证阶段。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
SSH协议中没有明确的服务端认证过程，而通过一系列的服务端与客户端的交互来确定服务端的身份，该过程还会完成Host Key、User Key、Session Key等在客户端与服务端之间的传输。</div>
<ol>
<li>建立连接后，服务端向客户端发送：<ul>
<li>Host Key：用于认证服务端的身份</li>
<li>Server Key：用于帮助建立安全的数据传输通道</li>
<li>8字节的随机数：客户端通过在下一次响应中包含该随机数，防止IP地址欺诈</li>
<li>服务端支持的加密算法、压缩方式和认证方式</li>
</ul>
</li>
</ol>
<div style="margin-bottom: 1em; margin-top: 1em;">
这个时候，客户端和服务端使用Host Key、Server Key和8字节的随机数生成一个128位的MD5值，作为此次会话的session id。</div>
<ol>
<li>客户端在接收到服务端Host Key后，会检查自己的knows host数据库中（一般为~/.ssh/know_hosts文件），是否已经包含当前服务端的Host Key，如果有则继续下一步；如果没有或包含当前服务端的其他Host Key则，有用户决定是否信任该服务端，或终止当前连接。</li>
<li>客户端向服务端发送session Key</li>
</ol>
<div style="margin-bottom: 1em; margin-top: 1em;">
出于性能考虑，SSH采用对称加密（Secret Key），对实际通信内容进行加密，即Session Key。因此Session Key的安全尤为重要，一旦Session Key泄漏给攻击者，那所有的通信数据都可能被攻击者截获。<br />由于当前通道还是采用明文方式，客户端采用Host Key和Server Key对Session Key进行两次加密来加强安全性。</div>
<ol>
<li>服务端得到Session Key后，客户端和服务端就可以通过Session Key对数据进行加密解密操作，到此，双方完成<strong>安全（加密）</strong>通道的建立。 Host Key和Server Key都是Public Key（非对称加密），只有通过服务端的私钥（Private Key）才能对其解密，以得到Session Key。在正式使用安全通道前，客户端要求服务端发送使用Session Key加密的确认信息，以验证服务端的身份。 为避免Session Key的泄漏，SSH还采取了其他安全措施，Session Key仅保存在内存避免其泄漏；周期性更换Server Key，默认为1小时（SSH 2对Server Key安全进一步增强）。</li>
</ol>
<h5 style="font-family: arial, helvetica, sans-serif; font-size: 1.3em; font-weight: 400; margin: 1.5em 0px -0.4em;">
客户端认证阶段</h5>
<div style="margin-bottom: 1em; margin-top: 1em;">
SSH提供多种客户端认证方式。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
SSH-1：</div>
<ul>
<li>Password</li>
<li>Public Key</li>
<li>Kerberos</li>
<li>Rhosts &amp;&amp; RhostsRSA</li>
<li>TIS</li>
</ul>
<div style="margin-bottom: 1em; margin-top: 1em;">
SSH-2：</div>
<ul>
<li>Password</li>
<li>Public Key</li>
<li>hostbased 在SSH-2中考虑Rhosts存在安全漏洞，废弃了这种方式。</li>
</ul>
<div style="margin-bottom: 1em; margin-top: 1em;">
这里之讨论我们经常使用的的Password和Public Key方式。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
此时安全通道已经及建立，之后的所有内容都通过Session Key加密后进行传输。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
<strong>Password</strong></div>
<div style="margin-bottom: 1em; margin-top: 1em;">
Password方式既客户端提供用户和密码，服务端对用户和密码进行匹配，完成认证。类Unix系统中，如OpenSSH的框架，一般通过系统的本地接口完成认证。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
Password的优势是简单，无需任何而外的配置就可以使用。缺点密码不便于记忆，过于简单的密码容易被暴力破解。</div>
<div style="margin-bottom: 1em; margin-top: 1em;">
<strong>Public Key</strong></div>
<div style="margin-bottom: 1em; margin-top: 1em;">
Public Key认证的基本原理是基于分对称加密方式，分别在服务端对一段数据通过公钥进行加密，如果客户端能够证明其可以使用私钥对这段数据进行解密，则可以说明客户端的身份。因为服务端需要使用客户端生成的密钥对的公钥对数据首先加密，所以需要先将公钥存储到服务端的密钥库（Auhtorized Key）。<em>还记得Github中使用git协议push代码前需要先添加SSH KEY吗？</em></div>
<div style="margin-bottom: 1em; margin-top: 1em;">
下面详细介绍一个通过Public Key进行客户端认证的过程。</div>
<ol>
<li>客户端发起一个Public Key的认证请求，并发送RSA Key的模数作为标识符。（如果想深入了解RSA Key详细 –&gt;&nbsp;<a href="http://en.wikipedia.org/wiki/RSA_(algorithm)" style="border-bottom: 1px solid rgb(154, 161, 174); color: #677284; outline: none; text-decoration-line: none;">维基百科</a>）</li>
<li>服务端检查是否存在请求帐号的公钥（Linux中存储在~/.ssh/authorized_keys文件中），以及其拥有的访问权限。如果没有则断开连接</li>
<li>服务端使用对应的公钥对一个随机的256位的字符串进行加密，并发送给客户端</li>
<li>客户端使用私钥对字符串进行解密，并将其结合session id生成一个MD5值发送给服务端。&nbsp;<em>结合session id的目的是为了避免攻击者采用重放攻击（replay attack）。</em></li>
<li>服务端采用同样的方式生成MD5值与客户端返回的MD5值进行比较，完成对客户端的认证。</li>
</ol>
<h5 style="font-family: arial, helvetica, sans-serif; font-size: 1.3em; font-weight: 400; margin: 1.5em 0px -0.4em;">
数据传输阶段</h5>
<div style="margin-bottom: 1em; margin-top: 1em;">
该阶段通过Session Key提供的对称加密（Secret Key）算法，保证需要传输的任何数据的安全。</div>
<h3 style="font-family: arial, helvetica, sans-serif; font-size: 1.5em; font-weight: 400; margin: 1.5em 0px -0.3em;">
SSH组成</h3>
<div>
<div style="margin-bottom: 1em; margin-top: 1em;">
<strong>SSH</strong>&nbsp;主要有三部分组成：</div>
<ol>
<li><a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">传输层</a>协议（我认为<strong>本质上是在传统传输层协议TCP上再构建一个传输层</strong>） [SSH-TRANS] 提供了服务器认证，保密性及完整性。此外它有时还提供压缩功能。 SSH-TRANS 通常运行在 TCP/IP连接上，也可能用于其它可靠数据流上。 SSH-TRANS 提供了强力的<a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">加密技术</a>、密码<a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">主机</a>认证及完整性保护。该协议中的认证基于主机，并且该协议不执行<a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">用户认证</a>。更高层的用户认证协议可以设计为在此协议之上。</li>
</ol>
<ul>
<li><a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">用户认证协议</a>&nbsp;[SSH-USERAUTH] 用于向服务器提供客户端用户鉴别功能。它运行在<a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">传输层</a>协议 SSH-TRANS 上面。当 SSH-USERAUTH 开始后，它从低层协议那里接收会话<a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">标识符</a>（从第一次<a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">密钥</a>交换中的交换哈希 H ）。会话标识符唯一标识此会话并且适用于标记以证明私钥的所有权。 SSH-USERAUTH 也需要知道低层协议是否提供保密性保护。</li>
<li><a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">连接协议</a>&nbsp;[SSH-CONNECT] 将多个加密隧道分成逻辑通道。它运行在<a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">用户认证</a>协议上。它提供了<a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">交互式登录</a>话路、远程命令执行、转发 TCP/IP 连接和转发 X11 连接。<br />一旦建立一个安全传输层连接，客户机就发送一个服务请求。当用户认证完成之后，会发送第二个服务请求。这样就允许新定义的协议可以与上述协议共存。连接协议提供了用途广泛的各种通道，有标准的方法用于建立安全交互式会话外壳和转发（“<a href="https://www.blogger.com/null" rel="noopener" style="border-bottom: 1px solid rgb(154, 161, 174); color: #34425b; outline: none;" target="_blank">隧道技术</a>”）专有 TCP/IP 端口和 X11 连接。<br />通过使用SSH，你可以把所有传输的数据进行加密，这样”中间人”这种攻击方式就不可能实现了，而且也能够防止DNS欺骗和IP欺骗。使用SSH，还有一个额外的好处就是传输的数据是经过压缩的，所以可以加快传输的速度。SSH有很多功能，它既可以代替Telnet，又可以为FTP、PoP、甚至为PPP提供一个安全的”通道”。</li>
</ul>
</div>
<h2 style="font-family: arial, helvetica, sans-serif; font-weight: 400; margin: 1.5em 0px -0.3em;">
SSL</h2>
</div>
