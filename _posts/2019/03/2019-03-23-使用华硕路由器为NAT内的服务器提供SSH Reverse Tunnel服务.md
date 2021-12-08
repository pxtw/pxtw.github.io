---
layout: post
title: '使用华硕路由器为NAT内的服务器提供SSH Reverse Tunnel服务'
date: 2019-03-23T20:54:35+11:00
location: 
---


<h2>
<span style="font-weight: normal;">背景与流程：</span></h2>
<div>
需要使用的SSH服务器处于无法通过Internet访问的内部网络环境，需要Internet上被访问的节点作为<b>直接被客户端访问的单元</b>，建立一个隧道将客户端和内部的SSH服务器连接。</div>
<div>
<br /></div>
<table align="center" cellpadding="0" cellspacing="0" class="tr-caption-container" style="margin-left: auto; margin-right: auto; text-align: center;"><tbody>
<tr><td style="text-align: center;"><a href="https://4.bp.blogspot.com/-zwlYwRWuuVY/XJYhOLPbaGI/AAAAAAAAecc/WKaPlVb5mg0fTilDM2Q-5cMd7PU4-5ToQCLcBGAs/s1600/1553342748%25281%2529.jpg" imageanchor="1" style="margin-left: auto; margin-right: auto;"><img border="0" data-original-height="374" data-original-width="816" height="182" src="https://4.bp.blogspot.com/-zwlYwRWuuVY/XJYhOLPbaGI/AAAAAAAAecc/WKaPlVb5mg0fTilDM2Q-5cMd7PU4-5ToQCLcBGAs/s400/1553342748%25281%2529.jpg" width="400" /></a></td></tr>
<tr><td class="tr-caption" style="text-align: center;">客户端访问公网上暴露的<span style="background-color: lime;">端口</span>，<br />
通过<span style="background-color: yellow;">公网设备</span>建立的隧道，达到最终访问<span style="background-color: orange;">内网服务器</span>的目的。[<a href="https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot" target="_blank">源</a>]</td></tr>
</tbody></table>
<h2>
<span style="font-weight: normal;">需要准备的：</span></h2>
<div>
1. 有一台华硕路由器，且装有Koolshare改版的Merlin固件。（<i>其实只要可以<b>通过SSH进行连接</b>的路由器都是可行的，但我自己的实验是通过华硕AC66U-B1完成的。</i>）</div>
<div>
<span style="font-weight: normal;"><br /></span></div>
<div>
<span style="font-weight: normal;">2. 确保在不开启任何网络代理以及VPN服务的情况下，你的路由器WAN口获得的IP地址，不属于私有地址。（<strike>通常需要使用路由器进行PPPOE进行拨号，且不在私人部署的网络环境中</strike></span><strike>（城中村等），本篇不做讨论</strike>）</div>
<div>
<br /></div>
<div>
通过ipconfig获取网关地址，再通过网关地址（通常是192.168.50.1）访问路由器管理页面，在页面获取WAN口地址。</div>
<table align="center" cellpadding="0" cellspacing="0" class="tr-caption-container" style="margin-left: auto; margin-right: auto; text-align: center;"><tbody>
<tr><td style="text-align: center;"><a href="https://1.bp.blogspot.com/-JCTj8qFwekg/XJYkAyNR5MI/AAAAAAAAeco/Ja2tcFUuab81gnPrbsLZ0NHz7Oal5qfBgCLcBGAs/s1600/1553343469%25281%2529.jpg" imageanchor="1" style="margin-left: auto; margin-right: auto;"><img alt="" border="0" data-original-height="187" data-original-width="445" height="134" src="https://1.bp.blogspot.com/-JCTj8qFwekg/XJYkAyNR5MI/AAAAAAAAeco/Ja2tcFUuab81gnPrbsLZ0NHz7Oal5qfBgCLcBGAs/s320/1553343469%25281%2529.jpg" title="" width="320" /></a></td></tr>
<tr><td class="tr-caption" style="text-align: center;">路由器管理界面获取到的WAN口地址</td></tr>
</tbody></table>
通过任意能查询到本机公网IP的<a href="https://www.myip.com/" target="_blank">应用程序</a>获取本机公网IP（<strike>甚至你都可以直接百度“IP”</strike>）。<br />
<table align="center" cellpadding="0" cellspacing="0" class="tr-caption-container" style="margin-left: auto; margin-right: auto; text-align: center;"><tbody>
<tr><td style="text-align: center;"><a href="https://3.bp.blogspot.com/-azGP4uJavcU/XJYlpqel8_I/AAAAAAAAec0/aF83K4KUH24ckXr8--1h94j7DCbl-2unACLcBGAs/s1600/1553343899%25281%2529.jpg" imageanchor="1" style="margin-left: auto; margin-right: auto;"><img border="0" data-original-height="224" data-original-width="1600" height="44" src="https://3.bp.blogspot.com/-azGP4uJavcU/XJYlpqel8_I/AAAAAAAAec0/aF83K4KUH24ckXr8--1h94j7DCbl-2unACLcBGAs/s320/1553343899%25281%2529.jpg" width="320" /></a></td></tr>
<tr><td class="tr-caption" style="text-align: center;">公网IP地址</td></tr>
</tbody></table>
<br />
<div>
3. 使用华硕自带的DDNS服务为路由器注册一个域名（外部网络（WAN）-&gt;DDNS）。<br />
<div>
<div class="separator" style="clear: both; text-align: center;">
<a href="https://3.bp.blogspot.com/-ceLDQ-5z1BY/XJYmcJyCrNI/AAAAAAAAedA/HdEj23_SQCgv6crmRgbYfWR5d_1M9fMmQCLcBGAs/s1600/1553344090%25281%2529.jpg" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" data-original-height="586" data-original-width="961" height="195" src="https://3.bp.blogspot.com/-ceLDQ-5z1BY/XJYmcJyCrNI/AAAAAAAAedA/HdEj23_SQCgv6crmRgbYfWR5d_1M9fMmQCLcBGAs/s320/1553344090%25281%2529.jpg" width="320" /></a></div>
<div>
<br />
4. 进入系统管理-&gt;系统设置:</div>
</div>
</div>
<div>
<br /></div>
Enable SSH : LAN+WAN<br />
<div>
<div>
Allow SSH Port Forwarding : 是</div>
</div>
SSH service port： 任取（<strike>端口可取的值的范围请自行搜索</strike>）<br />
Allow SSH password login： 是<br />
Enable SSH Brute Force Protection： 是<br />
<div>
<br /></div>
<div>
5. 进入外部网络（WAN）-&gt; DMZ:</div>
<div>
<br /></div>
<div>
把<b>启动DMZ设置为否。</b>因为DMZ的设置会导致设置好的SSH转发无法被捕获，就直接转发给DMZ主机。若不得不设置DMZ，则需要手动在管理页面添加端口映射（明确设置的端口映射有比DMZ更高的优先级）。</div>
<div>
<br /></div>
<div>
<br /></div>
<div>
6. 进入需要内网环境中的SSH服务器对应的主机，执行命令。</div>
<div>
<script src="https://gist.github.com/eMous/3040078fddca5dea74380414844897a8.js"></script></div>
<div>
<br />
<br />
<br /></div>
<div>
<br />
<!--more--><h2>
2019年3月24日补充（重要）：</h2>
<div>
<br /></div>
<div>
由于Koolshare（or Merlin whatever）原生防火墙策略的原因，未被设置成白名单的INPUT数据流都会被丢弃。</div>
<div>
<br /></div>
<div>
所以还需要添加一步额外的操作，如果你不选择关闭防火墙（建议不要关闭防火墙）：</div>
<div>
<br /></div>
<div>
<br /></div>
</div>
<script src="https://gist.github.com/eMous/14d42f2c5f8ccdd594bf5955789596af.js"></script>
