title: 通过cname优选域名/IP接入cloudflare
description: Welcome to Hugo Theme Stack
date: 2022-03-06 00:00:00+0000
slug: 通过cname优选域名或ip接入cloudflare
categories:
    - Example Category
tags:
    - Example Tag
weight: 3 
# 通过cname优选域名/IP接入cloudflare

![图片1](https://p0.meituan.net/csc/2257cd91224cde5cffeea45da40423394062.png)


## 准备：
一个已经修改ns接入的CF域名作为**承接域名**，一个想用cname接入cloudflare的域名，一张外币卡/信用卡/已绑卡的PayPal（不会自动扣费），一个优选域名（优选域名的获取可以参考[CF优选域名](https://blog.qmsdh.com/index.php/archives/22/)，优选IP的获取方式可参考[CF自选IP](https://blog.qmsdh.com/index.php/archives/23/)）

## 背景
在曾经，通过 CNAME 接入 Cloudflare 并不是一件难事。

通过 Cloudflare 的合作伙伴计划（Cloudflare Partner）或者 Plesk（一个非常受欢迎的网站管理面板，类似大名鼎鼎的 CPanel）面板的插件订阅，这一点可以很轻易做到。

从 2021 年 11 月开始，Cloudflare 禁用了 Partner 使用的 zone_setAPI 以避免滥用（因为该 API 接入不需要验证域名所有权），通过 Partner 实现 CNAME 接入的方式近乎落幕，仅剩 Plesk 空间存量的 ServerShield by Cloudflare 插件订阅能够正常接入。几个月过去了，官方也并没有对未来合作伙伴如何进行接入新域名给出任何解释。

截至目前，通过 CNAME 接入 Cloudflare 就只剩下了 SaaS 这一条路子。

## 配置接入
打开一个域名，选择【SSL/TLS】下的【自定义主机名】，点击【启用CloudFlare for SaaS】后根据指示绑定外币卡或者PayPal，订阅CloudFlare for SaaS功能。

![图片2](https://p1.meituan.net/csc/a081ecb7f048d7b88da3405634e382cc59054.jpg)

CloudFlare for SaaS订阅本身是针对整个计费账户的，所以通过Partner接入的域名出现【请联系客户成功经理以启用适用于SaaS的SSL】时，只需要选择个通过官方NS激活的域名启用订阅后即可使用。这里猜测可能是Partner接入的商务权限交给了合作伙伴，方便下放优惠和服务那些，我们绕过去就行了。

![图片3](https://p1.meituan.net/csc/93bc9e10c0633cb20ce64bddec9152e529289.jpg)

激活页面中文翻译比较滞后，从英文的可以看到免费额度已经进行更新，可以放心使用。

![图片4](https://p1.meituan.net/csc/3ae5a36892ebe9589bdabc82073dd64790072.jpg)

## 设置源站
选择一个**承载的域名**zone点进去，依然是【SSL/TLS】下的【自定义主机名】，首先要设置附加上域名的源站。在这之前要在承载的域名zone中设置一个子域名作为源站的来源，比如origin.a.com，在Partner或者官方DNS设置好它的源站（注意是是在CF里添加，和正常添加网站的流程一样）。

![图片5](https://p0.meituan.net/csc/e3f2865a9fa011ab5018a99ddfd3ef2b47908.jpg)

SaaS这里的源站叫回退源（Fallback Origin），输入刚才设置的子域名并点击【Add Fallback Origin】，它会同步这个子域名设置的源站作为后续在此接入域名的源站。有些人就会问了，这样设置那不是后续SaaS添加的所有其他域名就只能用同一个源站了？答案确实是这样，为每个SaaS域名自定义源站需要Enterprise以上套餐，有多域名需求多开几个zone吧。

![图片6](https://p0.meituan.net/csc/b3debe06748cc0ce279e5b31828dea8238662.jpg)

## 添加自定义主机名
后续的工作就很简单了，点击【添加自定义主机名】，输入你要添加的未在CF接入的子域名。建议直接选择TXT验证，因为除了证书还有另一条TXT记录要添加，一起加上去比较方便。

![图片7](https://p0.meituan.net/csc/4639b7887d1e6d32867e03d5d347aa5730776.jpg)

## 验证域名所有权
添加完成后，按要求解析证书和主机名两个TXT记录，解析生效后10分钟左右即可验证通过，到此这个SaaS域名就正确的添加到了你的zone中并接入了CF。

![图片8](https://p0.meituan.net/csc/a248f19997493d3ad1f3e269a0d5b46566642.jpg)

特别提醒，如图这里CF给出的验证TXT名称是应完整域名的解析记录，所以在自己的第三方DNS配置的时候，填入的主机名应当是example和_cf-custom-hostname.example，如果直接复制框内的内容把根域名b.com填进了主机名全域就变成了example.b.com.b.com了，是错误的。配置完成之后你可以通过直接复制的域名来检查TXT记录是否匹配，推荐MySSL的工具（[点击前往](https://myssl.com/dns_check.html#ssl_verify)）。

![图片9](https://p1.meituan.net/csc/c115c057b2876cd491572e2ef4acda9b50738.png)

经过测试，不论以何种方式验证并签发证书，只要保持SSL正确指向CloudFlare，系统就能够在到期前一个月自动为你续期证书，无需进行手动的操作。相应的，SaaS页面Certificate validation method也会变成HTTP Validation。

## SaaS域名解析(优选域名)
CF不会告诉你解析到哪个cname接入，这时候将你接入的SaaS域名cname解析到[优选域名](https://blog.qmsdh.com/index.php/archives/22/)即可。

此外，对于防火墙规则、页面规则，直接将添加进的域名输入其中即可圈定范围，完成对于其细则的设置。

## 结语
CloudFlare for SaaS是官方提供的一项非常方便的免费功能，弥补了早期未通过Partner接入只能强制NS接入的缺憾。有官方保障、灵活CNAME、免费的优点，也有源站不灵活等缺点，肯定还是不如已经通过Partner/Plesk接入的域名灵活。

本文引用文章：  
[https://luotianyi.vc/6185.html](https://luotianyi.vc/6185.html)
