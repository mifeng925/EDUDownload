# 腾讯课堂回放下载



[TOC]

## 1.关于

手术刚刚做完  不能长时间坐着 实属无聊  晚上站撸代码做点啥呢 搞腾讯课堂吧

​	腾讯课堂支持回访 但是不支持下载 这个真的很难搞 一定要在网页观看  但是嘞 我就是想下载 发现没有接口 用第三方探测 发现可以探测出来 也可以正常下载 那么 就开搞吧 ！



## 2.抓包

打开浏览器 进入`开发者模式` 切换到 Network 抓取浏览器分包  我们刷新下  发现一大堆玩意  杂七杂八的先不管  直奔重点  现在主流播放视频的真是地址基本都是`m3u8`文件。

| 基维百科                                                     |
| :----------------------------------------------------------- |
| M3U8是[Unicode](https://zh.wikipedia.org/wiki/Unicode)版本的M3U，用[UTF-8](https://zh.wikipedia.org/wiki/UTF-8)编码。"M3U"和"M3U8"文件都是[苹果](https://zh.wikipedia.org/wiki/苹果)公司使用的[HTTP Live Streaming](https://zh.wikipedia.org/wiki/HTTP_Live_Streaming) `HLS`格式的基础，这种格式可以在[iPhone](https://zh.wikipedia.org/wiki/IPhone)和[Macbook](https://zh.wikipedia.org/wiki/Macbook)等设备播放 |
| [HLS](https://links.jianshu.com/go?to=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FHTTP_Live_Streaming) 的工作原理是把整个流分成一个个小的基于 [HTTP](https://links.jianshu.com/go?to=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FHTTP) 的文件来下载，每次只下载一些。当媒体流正在播放时，客户端可以选择从许多不同的备用源中以不同的速率下载同样的资源，允许流媒体会话适应不同的数据速率。在开始一个流媒体会话时，客户端会下载一个包含元数据的 extended M3U (m3u8) playlist文件，用于寻找可用的媒体流。  [HLS](https://links.jianshu.com/go?to=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FHTTP_Live_Streaming) 只请求基本的 [HTTP](https://links.jianshu.com/go?to=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FHTTP) 报文，与[实时传输协议（RTP）](https://links.jianshu.com/go?to=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2F%E5%AE%9E%E6%97%B6%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)不同，[HLS](https://links.jianshu.com/go?to=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FHTTP_Live_Streaming) 可以穿过任何允许 [HTTP](https://links.jianshu.com/go?to=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FHTTP) 数据通过的防火墙或者代理服务器。它也很容易使用[内容分发网络](https://links.jianshu.com/go?to=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2F%E5%85%A7%E5%AE%B9%E5%88%86%E7%99%BC%E7%B6%B2%E7%B5%A1)来传输媒体流。 |

讲人话：就是将一个视频分割成 成百上千个小视频段 然后顺序一个一个播放。

------

弄懂`m3u8`文件了  我们直接分析抓到的`m3u8`地址 https://1258712167.vod2.myqcloud.com/fb8e6c92vodtranscq1258712167/76496b335285890803204566977/drm/voddrm.token.dWluPTE5NTQ4OTgwMjt2b2RfdHlwZT0wO2NpZD0xNjExMDIzO3Rlcm1faWQ9MTAxNzEwMzQ0O3Bsc2tleT0wMDA0MDAwMDBkYjZiNWMwYjBlNGUwZjY1MGRkNjBiNGYwZDRkZmMzYTQ0ODA5NTNjNDBmODI0ZmFiODYxZTg4MGE0MDZiN2YwYWMwMmUzMGYyZDNiZDE5O3Bza2V5PUJnTHJZUnNsR1Rycm5sQllONlQzbGFRcEtneFp5UEViVWpyR2ZMV293Mmtf.v.f30741.m3u8?t=5ef35811&exper=0&us=7825963472887657075&sign=fe8aa3192399830d3bfacdaf31c71f35

是个这个玩意 还带了很多看不懂的东西 。咋办 一看就是蒙蔽的  好像没办法入手了  因为每个回访 都是不一样的加密参数。

## 3.解密URL

关于上面一段连接  我们做一个分割 ：

1. `http://1258712167.vod2.myqcloud.com/fb8e6c92vodtranscq1258712167/76496b335285890803204566977/drm/voddrm.token.`
2. `dWluPTE5NTQ4OTgwMjt2b2RfdHlwZT0wO2NpZD0xNjExMDIzO3Rlcm1faWQ9MTAxNzEwMzQ0O3Bsc2tleT0wMDA0MDAwMDBkYjZiNWMwYjBlNGUwZjY1MGRkNjBiNGYwZDRkZmMzYTQ0ODA5NTNjNDBmODI0ZmFiODYxZTg4MGE0MDZiN2YwYWMwMmUzMGYyZDNiZDE5O3Bza2V5PUJnTHJZUnNsR1Rycm5sQllONlQzbGFRcEtneFp5UEViVWpyR2ZMV293Mmtf`
3. `v.f30741.m3u8?t=5ef35811&exper=0&us=7825963472887657075&sign=fe8aa3192399830d3bfacdaf31c71f35`

为什么这么分割呢  多分析几个回访  对比下  不变与变的地方 你就也会这么分割开来了

---

初步猜测 第二部分应该是加密过的  不然着乱七八糟的 也不像是明文啊  那是啥加密呢 不管。。

第三部分是`m3u8`文件 带上一堆参数  不用看 这些参数肯定是上面的请求得来的  要知道这些参数 去分析下 	前面抓取的包  挨个分析。。没得办法好像  我就是这么傻的玩的  也许这些参数还会塞在cookie里  具体哪里 咱们还不知道 。

第一部分最简单了 头地址 固定不变  不管他 。

ok！回到第一步 这是傻玩意呢  咋一眼看过去真的 一脸蒙蔽  难道让我去看`js`部分嘛。。头疼。但是我多分析几个回访 发现一个问题 有时候参数结尾会有 `==` 我擦  似曾相识啊朋友  `BASE64`编码  希望啊  直接解码下 ：

```apl
uin=195489802;vod_type=0;cid=1611023;term_id=101710344;plskey=000400000db6b5c0b0e4e0f650dd60b4f0d4dfc3a4480953c40f824fab861e880a406b7f0ac02e30f2d3bd19;pskey=BgLrYRslGTrrnlBYN6T3laQpKgxZyPEbUjrGfLWow2k
```

直接变成明文了  现在大问题直接了吧。。但是吼  里面又是一堆不认识的小朋友。。咋办  遇到不认识的就删

```apl
uin=;vod_type=0;cid=1611023;term_id=101710344;plskey=;pskey=
```

删除这样  `cid`  和 `term_id`别删了  这是课堂房间的 `id` 我咋知道。。你看看浏览器的地址 你也知道了。。这两位朋友必须熟一下   然后 `BASE64`编码下  发现可以正常显示`m3u8`文件。最难的一步完成了

------

第三步的参数哪来的嘞。。这个好像也没得办法只能一个个看刚刚抓出的请求。。会发现 请求并不多 文件除去外就几个了 挨个看 发现了 

```apl
URL:	https://ke.qq.com/cgi-bin/qcloud/get_token?term_id=101710344&fileId=5285890803204566977&bkn=39813843&t=0.9947
Response:	{"retcode":0,"result":{"sign":"777c64e0be4645df3a3de40c0de87bd9","us":"7825963472887657075","exper":0,"t":"5ef35811"}}
```

可以看出来 `json`返回的就是我们第三部分的参数 都对上了  那是不是问题就解决了。。我也是这么想的  但是上的`URL`又有几个我不认识的参数。。`fileId`  `bkn` `t` 这三个又是谁的朋友 哪里跑出来的。抬头看看浏览器的地址 可以看到 `fileid` 和 `vid` 另外2个参数 我也找半天 找不到 那没办法 就删掉吧。。发现也是可以的  搞定 最后一点 后面的参数搞定了 `m3u8` 前面的是啥 继续分析抓到的分包 发现

```apl
https://playvideo.qcloud.com/getplayinfo/v2/1258712167/5285890803204566977?exper=0&sign=777c64e0be4645df3a3de40c0de87bd9&t=5ef35811&us=7825963472887657075
```

几个参数我们都很熟 返回的是个 `json` 分析下里面  发现也有几个`m3u8`文件 对应的不同分辨率  但是 这里的`m3u8`文件不能用 不是真正的播放地址  你可以抓出来对比下 可以发现 这里面的`m3u8`文件 短很多 短在哪里呢  就是刚刚的加密参数 `BASE64`编码哪里 但是可以利用这个`m3u8`文件 加上我们刚刚加密的部分 拼接成一个完整可用的`m3u8`地址 然后下载就好了 。

## 4.完成

根据上面的分析 现在只要一个回放的URL 就可以拿到 `m3u8` 地址  然后就可以下载下来了 关于 `m3u8` 下载的方式导出都是 搜索一下就好了 其实是我不会写哈哈哈哈哈  不知道为什么 我下载下来播放不了  用自己代码写的是这样的  但是用`ffmpeg` 下载下来就可以播放 肯定是我的问题了 。`ffmpeg` 不懂的话可以百度下  我也只懂一点点皮毛用法 吹不了。 到此任务结束 。



