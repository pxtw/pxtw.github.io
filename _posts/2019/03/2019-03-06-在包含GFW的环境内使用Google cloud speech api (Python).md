---
layout: post
title: '在包含GFW的环境内使用Google cloud speech api (Python)'
date: 2019-03-06T20:54:35+11:00
location: 
---


在中国大陆使用Google Api难免会遇到网络无法访问的情况，但是Google官方提供的Python版本的<a href="https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/speech/cloud-client" target="_blank">Google cloud samples</a>在遇到网络不通的问题上竟然不会抛出异常或输出任何错误，而是一个劲的retry。最后在Console呈现出来的就是纯粹的一直在运行……，这对于一个对<a href="https://cloud.google.com/text-to-speech/docs/" target="_blank">Google Cloud</a>完全小白的人来说就特别棘手。<br />
<div>
<br /></div>
<div>
网上也没有相关的具体操作步骤，故做如下记录。</div>
<div>
<br /></div>
<h2>
Google Cloud SDK</h2>
<div>
如果从<span style="color: #212121; font-family: &quot;google sans&quot; , sans-serif;"><a href="https://cloud.google.com/text-to-speech/docs/quickstart-client-libraries#client-libraries-usage-python" target="_blank">Quickstart: Using the client libraries</a>这个页面下手以及考虑到有专门的<a href="https://cloud.google.com/sdk/docs/" target="_blank">SDK页面</a>，就十分容易被混淆。</span></div>
<div>
<br /></div>
<div>
会让人误以为，<strike>这个SDK工具是一个类似于所有开发语言公用的二进制库或者是服务之类，而其提供一个CLI交互接口用于统一管理Gcp项目之类的</strike>。</div>
<div>
<br /></div>
<div>
但其实并不是，这就是<b>纯粹的基于各操作系统终端的SDK</b>而已，例如Windows版本的，它提供CLI命令去执行一些Gcp的基础操作，比如新增虚拟机实例之类的。可以理解成，这是给维护人员使用的功能更狭窄的工具。<a href="https://stackoverflow.com/questions/41326125/google-cloud-sdk-vs-google-cloud-client-libraries" target="_blank">这里</a>讨论了Google cloud SDK和Google Client Libraries的区别。</div>
<div>
<br /></div>
<div>
因此如果仅仅是为了测试Google cloud speech api (Python)，那么SDK就是不必要安装的。</div>
<div>
<br /></div>
<div>
自然而然地，代理设置也将不再是在SDK内不使用命令设置了。</div>
<div>
<br /></div>
<div>
<br /></div>
<h2>
为Python添加网络代理</h2>
<div>
以Http代理为例</div>
<div>
<br /></div>
<div>
所以只需要按照<a href="https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/speech/cloud-client" target="_blank">Google cloud samples</a>的指导操作，并最后尝试为Python添加网络代理即可（经过查询这个Client Libraries，本身不提供代理设置的功能）。</div>
<div>
<br /></div>
<div>
由于Python底层的网络库无非那么几种，而且通用的网络代理设置方法，因此最为简单的方法就是<b>将代理声明到环境变量里去</b>（网络库会自动侦测）。</div>
<div>
<br /></div>
<div>
1. 通过Windows系统设置环境变量（系统环境变量），需要重启。</div>
<div>
<br /></div>
<div>
<span style="color: #50a14f; font-family: &quot;source code pro&quot; , &quot;dejavu sans mono&quot; , &quot;ubuntu mono&quot; , &quot;anonymous pro&quot; , &quot;droid sans mono&quot; , &quot;menlo&quot; , &quot;monaco&quot; , &quot;consolas&quot; , &quot;inconsolata&quot; , &quot;courier&quot; , monospace , &quot;pingfang sc&quot; , &quot;microsoft yahei&quot; , sans-serif; font-size: 14px; white-space: pre;"> http_proxy -&gt; </span><span style="color: #50a14f; font-family: &quot;source code pro&quot; , &quot;dejavu sans mono&quot; , &quot;ubuntu mono&quot; , &quot;anonymous pro&quot; , &quot;droid sans mono&quot; , &quot;menlo&quot; , &quot;monaco&quot; , &quot;consolas&quot; , &quot;inconsolata&quot; , &quot;courier&quot; , monospace , &quot;pingfang sc&quot; , &quot;microsoft yahei&quot; , sans-serif; font-size: 14px; white-space: pre;">http://127.0.0.1:1080</span></div>
<div>
<br /></div>
<div>
2. 直接在Python内部写入临时的系统环境变量。<br />
<br /></div>
<div>
<span style="color: #383a42; font-family: &quot;source code pro&quot; , &quot;dejavu sans mono&quot; , &quot;ubuntu mono&quot; , &quot;anonymous pro&quot; , &quot;droid sans mono&quot; , &quot;menlo&quot; , &quot;monaco&quot; , &quot;consolas&quot; , &quot;inconsolata&quot; , &quot;courier&quot; , monospace , &quot;pingfang sc&quot; , &quot;microsoft yahei&quot; , sans-serif; font-size: 14px; white-space: pre;"> os.environ[</span><span class="hljs-string" style="box-sizing: border-box; color: #50a14f; font-family: &quot;source code pro&quot; , &quot;dejavu sans mono&quot; , &quot;ubuntu mono&quot; , &quot;anonymous pro&quot; , &quot;droid sans mono&quot; , &quot;menlo&quot; , &quot;monaco&quot; , &quot;consolas&quot; , &quot;inconsolata&quot; , &quot;courier&quot; , monospace , &quot;pingfang sc&quot; , &quot;microsoft yahei&quot; , sans-serif; font-size: 14px; outline: 0px; overflow-wrap: break-word; white-space: pre;">'http_proxy'</span><span style="color: #383a42; font-family: &quot;source code pro&quot; , &quot;dejavu sans mono&quot; , &quot;ubuntu mono&quot; , &quot;anonymous pro&quot; , &quot;droid sans mono&quot; , &quot;menlo&quot; , &quot;monaco&quot; , &quot;consolas&quot; , &quot;inconsolata&quot; , &quot;courier&quot; , monospace , &quot;pingfang sc&quot; , &quot;microsoft yahei&quot; , sans-serif; font-size: 14px; white-space: pre;">] = </span><span class="hljs-string" style="box-sizing: border-box; color: #50a14f; font-family: &quot;source code pro&quot; , &quot;dejavu sans mono&quot; , &quot;ubuntu mono&quot; , &quot;anonymous pro&quot; , &quot;droid sans mono&quot; , &quot;menlo&quot; , &quot;monaco&quot; , &quot;consolas&quot; , &quot;inconsolata&quot; , &quot;courier&quot; , monospace , &quot;pingfang sc&quot; , &quot;microsoft yahei&quot; , sans-serif; font-size: 14px; outline: 0px; overflow-wrap: break-word; white-space: pre;">'http://127.0.0.1:1080'</span></div>
<div>
<span class="hljs-string" style="box-sizing: border-box; color: #50a14f; font-family: &quot;source code pro&quot; , &quot;dejavu sans mono&quot; , &quot;ubuntu mono&quot; , &quot;anonymous pro&quot; , &quot;droid sans mono&quot; , &quot;menlo&quot; , &quot;monaco&quot; , &quot;consolas&quot; , &quot;inconsolata&quot; , &quot;courier&quot; , monospace , &quot;pingfang sc&quot; , &quot;microsoft yahei&quot; , sans-serif; font-size: 14px; outline: 0px; overflow-wrap: break-word; white-space: pre;"><br /></span></div>
<div>
ps. 有趣的是Windows的Browser程序竟然并不是通过侦测环境变量来发现代理，而是通过<b><a href="https://stackoverflow.com/questions/5570004/how-to-change-lan-settings-proxy-configuration-programmatically" target="_blank">Internet属性-&gt;连接-&gt;局域网设置中的配置脚本或是代理服务器</a></b>来发现代理。</div>
<div>
<br /></div>
<div>
3. 或者使用（未测试）&nbsp;<a href="https://github.com/Anorov/PySocks" style="color: #1756a9; font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif; font-size: 16px; text-decoration-line: none;">PySocks</a></div>
<div>
<br /></div>
<div style="background-color: #fdfdfd; color: #111111; font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif; font-size: 16px; margin-bottom: 15px; padding: 0px;">
<code style="background-color: #eeeeff; border-radius: 3px; border: 1px solid rgb(232, 232, 232); font-size: 15px; padding: 1px 5px;">pip install PySocks</code></div>
<br />
<figure class="highlight" style="background: rgb(255, 255, 255); color: #111111; font-family: &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif; font-size: 16px; margin: 0px 0px 15px; padding: 0px;"><pre style="background-color: #eeeeff; border-radius: 3px; border: 1px solid rgb(232, 232, 232); font-size: 15px; margin-bottom: 15px; overflow-x: auto; padding: 8px 12px;"><code class="language-python" data-lang="python" style="border-radius: 3px; border: 0px; padding: 1px 0px;"><span class="kn">import</span> <span class="nn" style="color: #555555;">socket</span>
<span class="kn">import</span> <span class="nn" style="color: #555555;">socks</span>
<span class="kn">import</span> <span class="nn" style="color: #555555;">requests</span>

<span class="n">socks</span><span class="o" style="font-weight: bold;">.</span><span class="n">set_default_proxy</span><span class="p">(</span><span class="n">socks</span><span class="o" style="font-weight: bold;">.</span><span class="n">SOCKS5</span><span class="p">,</span> <span class="s" style="color: #dd1144;">"127.0.0.1"</span><span class="p">,</span> <span class="mi" style="color: #009999;">1080</span><span class="p">)</span>
<span class="n">socket</span><span class="o" style="font-weight: bold;">.</span><span class="n">socket</span> <span class="o" style="font-weight: bold;">=</span> <span class="n">socks</span><span class="o" style="font-weight: bold;">.</span><span class="n">socksocket</span>
<span class="k" style="font-weight: bold;">print</span><span class="p">(</span><span class="n">requests</span><span class="o" style="font-weight: bold;">.</span><span class="n">get</span><span class="p">(</span><span class="s" style="color: #dd1144;">'http://ifconfig.me/ip'</span><span class="p">)</span><span class="o" style="font-weight: bold;">.</span><span class="n">text</span><span class="p">)</span></code></pre>
<div>
<code class="language-python" data-lang="python" style="border-radius: 3px; border: 0px; padding: 1px 0px;"><span class="p"><br /></span></code></div>
</figure>