<h1>Progressive Web App(PWA)</h1>
<h2>一、前期介绍</h2>
<p>PWA的中文名叫做<strong>渐进式网页应用</strong>，早在2014年， W3C 公布过 <code>Service Worker</code> 的相关草案，但是其在生产环境被 <code>Chrome</code> 支持是在 2015 年。因此，如果我们把 PWA 的关键技术之一 <code>Service Worker</code> 的出现作为 <code>PWA</code> 的诞生时间，那就应该是 2015 年。</p>
<p>自 2015 年以来，<code>PWA</code> 相关的技术不断升级优化，在用户体验和用户留存两方面都提供了非常好的解决方案。<code>PWA</code> 可以将 <code>Web</code> 和 <code>App</code> 各自的优势融合在一起：渐进式、可响应、可离线、实现类似 <code>App</code> 的交互、即时更新、安全、可以被搜索引擎检索、可推送、可安装、可链接。</p>
<p><strong>需要特别说明的是，<code>PWA</code> 不是特指某一项技术，而是应用了多项技术的 <code>Web App</code>。其核心技术包括 <code>App Manifest</code>、<code>Service Worker</code>、<code>Web Push</code>，等等。</strong></p>
<h3 class="heading" data-id="heading-5">3、为什么W3C和谷歌在推广这项技术</h3>
<p>这就要从前端的现状说起：</p>
<p>Native APP 用起来很流畅，但是也有其天然的基因缺陷：</p>
<blockquote>
<ul>
<li>由于其天生封闭的基因，内容无法被索引</li>
<li>用户 80% 的时间被 Top3 的超级 App 占据，对于站点来说，应用分发的性价比也越来越不划算</li>
<li>要使用它，首先还需要下载几十兆上百着兆的安装包</li>
</ul>
</blockquote>
<p>WEB前端虽然天生具有开放的基因，但是很多时候页面会卡顿，用户体验不佳。虽然社区之前也做过很多努力，例如<code>virtual dom</code>、<code>spa</code>、混合编程、用<code>canvas</code>将整个页面画出来，用户体验也有了很大的改善，但是仍然无法解决几个重要的问题：</p>
<blockquote>
<ul>
<li>离线时用户无法使用</li>
<li>无法接收消息推送</li>
<li>移动端没有一级入口</li>
</ul>
</blockquote>
<p>W3C和谷歌看到了这些问题，于是推出了<code>PWA</code>。</p>
<h3>4、PWA的核心目标</h3>
<p><code>PWA</code>的核心目标就是提升 Web App 的性能，改善 Web App 的用户体验。媲美native的流畅体验，将网络之长与应用之长相结合。</p>
<h2>二、特点</h2>
<h3>1、PWA的特点</h3>
<p>PWA具有以下一些特点：</p>
<p></p><figure><img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef7efe9be7f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1"><figcaption></figcaption></figure><p></p>
<p>这边有一个关于PWA特点的<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Fprogressive-web-apps%2Fchecklist%23exemplary" rel="nofollow noopener noreferrer">列表清单</a>，有兴趣的同学可以点进去看看.</p>
<h3>2、特点详解</h3>
<h4>（1）、可安装</h4>
<p>1、可安装指的是可以像原生APP在主屏幕上留有图标。</p>
<p>2、但是这需要我们提供 <code>Web app manifest</code>，<code>manifest.json</code> 是一个简单的JSON文件，我们在 <code>html</code> 页面如下引用他：</p>
<img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef7c6850656?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<p>它描述了我们的图标在主屏幕上如何显示，以及图标点击进去的启动页是什么，它的JSON格式如下所示：</p>
<img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef7c13fbe12?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<p>其中：</p>
<blockquote>
<ul>
<li><code>start_url</code> 可以设置启动网址</li>
<li><code>icons</code> 会帮我萌设置各个分辨率下页面的图标</li>
<li><code>background_color</code> 会设置背景颜色， Chrome 在网络应用启动后会立即使用此颜色，这一颜色将保留在屏幕上，直至网络应用首次呈现为止。</li>
<li><code>theme_color</code> 会设置主题颜色</li>
<li><code>display</code> 设置启动样式</li>
</ul>
</blockquote>
<p>关于<code>manifest.json</code>里字段更加具体的含义，感兴趣的同学可以参考<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FManifest">MDN文档</a>或者谷歌开发者文档里的<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Ffundamentals%2Fweb-app-manifest%2F">这篇文章</a></p>
<p>3、实际上 <a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fm.douban.com%2Fpage%2Fvhn1tgl" rel="nofollow noopener noreferrer">豆瓣移动端</a>就是一个<code>PWA</code>应用，如果我们用高版本的浏览器打开，就会发现有横幅提示，不过需要注意的是，IOS似乎还不支持，以下王二用安卓手机来做一个演示：</p>
<p>打开豆瓣后，浏览器会提示添加到主屏幕：
</p><img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef7c1a3df56?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<p>点击确定后，他会提示添加成功，然后在主屏幕上留有一个豆瓣的图标。</p>
<img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef7c17d55a0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef7c2bd7e6b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<h4>（2）、离线使用</h4>
<p>1、<code>PWA</code> 另一项令人兴奋的特性就是可以离线使用,其背后用到的技术是 <code>Service worker</code> ;</p>
<p>2、<code>Service worker</code>实际上是一段脚本，在后台运行。作为一个独立的线程，运行环境与普通脚本不同，所以不能直接参与 Web 交互行为。<code>Service Worker</code> 的出现是正是为了使得 <code>Web App</code> 也可以做到像 <code>Native App</code> 那样可以离线使用、消息推送的功能。</p>
<p>我们可以把<code>Service worker</code>当做是一种客户端代理，</p>
<img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef92c172edf?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<p>3、我们来看看如何注册一个 <code>Service Worker</code></p>
<img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef93241a1b0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<p>关于<code>Service Worker</code>更加详细的介绍，感兴趣的同学可以参考<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FServiceWorker">MDN文档</a>或者Github上的<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fgooglechrome.github.io%2Fsamples%2Fservice-worker%2Fbasic%2Findex.html" >Basic Service Worker Sample</a>这篇介绍。</p>
<p>4、 当然 <code>Service Worker</code> 也有生命周期，参考下图：</p>
<img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef9519812a6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<p>关于 <code>Service Worker</code> 生命周期的详细介绍可以参考下面这张图：</p>
<img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef975ed5d6e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<p>关于<code>Service Worker</code> 生命周期更加详细的介绍，感兴趣的同学可以参考<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FService_Worker_API%2FUsing_Service_Workers">MDN文档</a></p>
<h4>（3）、消息推送</h4>
<p>消息推送具体可以参考谷歌的官方教程<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Ffundamentals%2Fcodelabs%2Fpush-notifications%2F">Your First Web Push Notification</a>,里面有详细的代码Demo和说明，以及相应的后台配置（带好梯子）。</p>
<h2>3、开发者如何了解是否具备这些特点</h2>
<p>说了这么多，那我们开发者如何了解一个网页是否具备了 <code>PWA</code> 的一些特点呢？</p>
<p>这时候谷歌开发者工具可以帮上我们的忙，在 <code>Chrome</code>浏览器的开发者工具里有一个<code>Audits</code>面板，它可以帮我们检测网页是否具备了PWA的一些特点：</p>
<img src="https://user-gold-cdn.xitu.io/2018/1/27/16137ef9d67d51c3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<h2>三、有哪些企业已经在使用PWA？</h2>
<p>1、国内已经有<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fm.douban.com%2F" rel="nofollow noopener noreferrer">豆瓣</a>、
<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fh5.ele.me%2F">饿了么</a>使用了部分PWA技术；</p>
<p>2、国外用PWA的企业相对较多，例如<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fmobile.twitter.com%2Fhome">Twitter</a>、
<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fflipboard.com%2F">Filpboard</a>;</p>
<p>3、特别需要注意的是Twitter ，Twitter 在 2017 年上线了 <code>Twitter Lite PWA</code>，其获得了相当惊人的收益：</p>
<blockquote>
<ul>
<li>平均用户停留时长增长 65%</li>
<li>Web 站点发推的数量增长 75%</li>
<li>跳出率降低 20%</li>
</ul>
</blockquote>
<p>Twitter Lite 能取得这样的成绩，归功于 PWA 的新技术和用户体验至上的设计原则：它通过 Service Worker 缓存文件，让页面可以离线，同时降低网络消耗；通过 Web Push 接受服务器推送的消息；采用 App Shell 的设计模型，配合 Service Worker 能让页面瞬间展现。</p>
<p>4、另外一些采用了PWA的网站可以参考<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fpwa.rocks%2F">这个链接</a></p>
<h2>四、代码细节</h2>
<h3>1、代码可以参考的是谷歌的官方教程</h3>
<p>1、<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Ffundamentals%2Fcodelabs%2Fyour-first-pwapp%2F">Your First Progressive Web App</a>会帮我们了解如何从头构建一个PWA应用。
2、<a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Ffundamentals%2Fcodelabs%2Fdebugging-service-workers%2F">Debugging Service Workers</a>会辅导我们如何调试<code>Service Worker</code>。</p>
<h3>2、缓存策略</h3>
<p>有缓存可以优化用户体验，但是如果没有合理的缓存策略，我们会发现我们的网站将会很难更新。</p>
<p>在用户重新加载新的<code>Service Worker</code>的时候，我们需要将之前旧的资源文件都删掉,如下图所示：</p>
<p></p><figure><img src="https://user-gold-cdn.xitu.io/2018/1/27/16137efa47b32d55?imageView2/0/w/1280/h/960/format/webp/ignore-error/1"></figure><p></p>
<p>更多的缓存策略有兴趣的同学可以参考这篇文章 -- <a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fjakearchibald.com%2F2014%2Foffline-cookbook%2F%23cache-then-network" >The offline cookbook</a></p>
<h3>3、官方工具</h3>
<p>除了前面提到的手工编写 <code>Service Worker</code> 脚本, Google 提供了 <code>sw-toolbox</code> 和 <code>sw-precache</code> 两个工具方便快速生成 <code>service-worker.js</code> 文件:</p>
<blockquote>
<ul>
<li><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FGoogleChromeLabs%2Fsw-precache">sw-precache</a> 可以用来生成配置使 PWA 在安装时进行静态资源的缓存</li>
<li><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FGoogleChromeLabs%2Fsw-toolbox">sw-toolbox</a> 提供了动态缓存使用的通用策略, 这些动态的资源不合适用 sw-precache 预先缓存。</li>
</ul>
</blockquote>
<h2>五、小结</h2>
<p>Progressive Web App仍处于初级阶段，国内普及度还不够，但我们不可忽视其背后的的技术，以及对前端全新的定位。或许它会像十年前的AJAX那样重新改变前端的生态。</p>
<h2>参考文章（部分链接需要带好梯子）</h2>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fs%2Fresults%2F%3Fq%3Dpwa%26p%3D%252Fweb%252F">谷歌开发者网站关于PWA的搜索结果</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Ffundamentals%2Fcodelabs%2Fyour-first-pwapp%2F">Your First Progressive Web App</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Fprogressive-web-apps%2Fchecklist%23exemplary">Exemplary Progressive Web App Checklist</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fjakearchibald.com%2F2014%2Foffline-cookbook%2F%23cache-then-network">The offline cookbook</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Ffundamentals%2Fcodelabs%2Fpush-notifications%2F">Your First Web Push Notification</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Ffundamentals%2Fcodelabs%2Fdebugging-service-workers%2F">Debugging Service Workers</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fpwa.rocks%2F">PWA网站清单列表</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fwww.juhe.cn%2Fnews%2Findex%2Fid%2F2406">PWA是否能带来新一轮大前端技术洗牌？</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F25524382">PWA 入门: 理解和创建 Service Worker 脚本</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Ffundamentals%2Fweb-app-manifest%2F">web-app-manifest</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fgooglechrome.github.io%2Fsamples%2Fservice-worker%2Fbasic%2Findex.html">Basic Service Worker Sample</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FServiceWorker">'ServiceWorker'MDN文档</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FManifest">'ServiceWorker'MDN文档</a></p>
<p><a target="_blank" href="https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FService_Worker_API%2FUsing_Service_Workers" >'Using_Service_Workers'MDN文档</a></p>
</div>

https://juejin.im/post/5a6c86e451882573505174e7
