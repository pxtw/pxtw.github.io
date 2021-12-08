---
layout: post
title: '给华硕AC66U B1路由器安装梅林KoolShare改版固件'
date: 2019-03-02T20:54:35+11:00
location: 
---


使用华硕自带的更新功能升级官方固件后，路由器本身会阻止第三方的固件安装。<br />
<br />
<table align="center" cellpadding="0" cellspacing="0" class="tr-caption-container" style="margin-left: auto; margin-right: auto; text-align: center;"><tbody>
<tr><td style="text-align: center;"><a href="https://1.bp.blogspot.com/-iWK89NDTmM4/XHoMRQ_CAHI/AAAAAAAAdoQ/_yUIJRMnf7AUkedBxUcHl1zH93KuPb7PACLcBGAs/s1600/Snipaste_2019-03-02_12-52-56.png" imageanchor="1" style="margin-left: auto; margin-right: auto;"><img border="0" data-original-height="257" data-original-width="787" height="104" src="https://1.bp.blogspot.com/-iWK89NDTmM4/XHoMRQ_CAHI/AAAAAAAAdoQ/_yUIJRMnf7AUkedBxUcHl1zH93KuPb7PACLcBGAs/s320/Snipaste_2019-03-02_12-52-56.png" width="320" /></a></td></tr>
<tr><td class="tr-caption" style="text-align: center;">Invalid Firmware Upload</td></tr>
</tbody></table>
这个时候可以通过<b>救援模式</b>强行刷入固件。<br />
<br />
所以需要有两个知识点：1.如何进入救援模式？ 2.如何刷入固件？<br />
<br />
<h2>
如何进入救援模式</h2>
<br />
1. 先将路由器的WAN口缆线拔出，再将电脑通过线缆连接到路由器LAN上。（此时路由器上只有一根网线和一根电源线）<br />
<br />
<br />
2. 打开电脑，手动设置IP，192.168.1.100。子网掩码，255.255.255.0。网关，192.168.1.1。<br />
<br />
3. 按住路由器reset键（<b>注意分清楚reset键和WPS键，AC66U B1的reset键是扁平的</b>），一秒后拔出电源线，五秒后插上电源线，等待Power指示灯缓慢闪烁，松开reset键。<br />
<br />
<h2>
如何刷入固件</h2>
<br />
打开救援软件（救援软件目前已经不能在华硕官方获得（至少我按照网络上的教程发现官方已经下架）），上传KoolShare改版固件。<br />
<br />
<table align="center" cellpadding="0" cellspacing="0" class="tr-caption-container" style="margin-left: auto; margin-right: auto; text-align: center;"><tbody>
<tr><td style="text-align: center;"><a href="https://4.bp.blogspot.com/-unGitzWPCH8/XHoOTnIrp6I/AAAAAAAAdoc/sZrktPVr6SQ-HKC3OqVJwBpjIPlG0FTQgCLcBGAs/s1600/Snipaste_2019-03-01_23-20-26.png" imageanchor="1" style="margin-left: auto; margin-right: auto;"><img border="0" data-original-height="280" data-original-width="593" height="151" src="https://4.bp.blogspot.com/-unGitzWPCH8/XHoOTnIrp6I/AAAAAAAAdoc/sZrktPVr6SQ-HKC3OqVJwBpjIPlG0FTQgCLcBGAs/s320/Snipaste_2019-03-01_23-20-26.png" width="320" /></a></td></tr>
<tr><td class="tr-caption" style="text-align: center;">固件上传中</td></tr>
</tbody></table>
<br />
上传完毕后，路由器将重启。此时，恢复IP为自动获取，并且将路由器WAN口连上光猫。<br />
<br />
<br />
<br />重启成功后进入管理界面，在【系统管理】 – 【系统设置】内勾选：Format JFFS partition at next boot（下次重启格式化jffs分区） 和 Enable JFFS custom scripts and configs（开启jffs自定义脚本），点击应用本页面设置，成功应用后重启路由器；<div>
<br />然后进入软件中心，首先更新软件中心到最新版本，然后即可安装并使用插件。</div>
<div>
<br /></div>
<div>
<br /></div>
<div>
下载：</div>
<div>
<br /></div>
<div>
Koolshare固件（AC66U B1）</div>
<div>
<div>
链接：https://pan.baidu.com/s/1rnxpjUmdlplLLak1eBnxDw&nbsp;</div>
<div>
提取码：as7p&nbsp;</div>
<div>
复制这段内容后打开百度网盘手机App，操作更方便哦</div>
</div>
<div>
<br /></div>
<div>
救援软件（应该是通用的）</div>
<div>
<div>
链接：https://pan.baidu.com/s/1xoqfUrWmcw-JkrGrBZUT_g&nbsp;</div>
<div>
提取码：3lbf&nbsp;</div>
<div>
复制这段内容后打开百度网盘手机App，操作更方便哦</div>
</div>
<div>
<br /></div>
