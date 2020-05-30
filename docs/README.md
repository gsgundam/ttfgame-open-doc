# 1 概述

TTFGame开放平台是基于TTFGame生态与区块链所打造的开放性游戏平台。

![preview](/img/ttfgame_preview_01.png)

> TTFGame生态下有多个基础平台支持，当前开放平台仅针对区块链部分。

  <br />
  <br />

# 2 开发前准备

## 2.1 创建开发者账号

通过[开发者注册页面](http://ttftest.dashgame.com/admin/index/register1)，创建一个开发者账号。

![preview](/img/ttfgame_preview_02.png)

验证邮箱。

![preview](/img/ttfgame_preview_03.png)

录入信息等待审核验证。

![preview](/img/ttfgame_preview_04.png)

## 2.2 添加测试人员账号

通过验证以后，可以通过左侧菜单添加测试人员账号。

![preview](/img/ttfgame_preview_06.png)

其中，手机号和密码是必填项。

## 2.3 创建游戏

通过验证以后，可以通过左侧菜单创建游戏。

![preview](/img/ttfgame_preview_05.png)

创建好游戏以后，需要设置一些必要参数，以便之后接入游戏。包括：

```
平台ID
区服ID
支付回调地址（正式版的服务端支付回调地址）
登陆地址（正式版的游戏访问地址）
官方测试链接（开放平台测试版的官方链接地址，可与游戏详情里的一致）
支付回调测试地址（开放平台测试版的服务端支付回调地址））
登录测试地址（开放平台测试版的游戏访问地址）
```
![preview](/img/ttfgame_preview_07.png)

设置完成以后，如果有需要调整的，可点击编辑按钮更改。

![preview](/img/ttfgame_preview_08.png)

创建和修改以后，请等待或联系管理员进行审核，以便添加和修改的内容生效。

![preview](/img/ttfgame_preview_09.png)

当自行检测游戏通过，并经过管理员审核以后，就可以点击发布按钮，发布游戏到正式客户端。

![preview](/img/ttfgame_preview_10.png)

## 2.4 下载测试App

游戏创建并接入完成后，可以通过[安卓测试App](https://cdn.gocloudcache.com/site/client/assets/open/download/com.dashgame.dmgp.open.test.apk)来进行测试。

也可以通过[H5版本](https://open.shock.chat/#/pages/login/login)来进行在线测试。

**重要：请使用测试人员账号登录，才能看到仅在测试状态的游戏。**

  <br />
  <br />    

# 3 接入指南

## 3.1 接口说明

### 3.1.1 用户登录

**功能**：   玩家进入游戏

**请求方式**： GET

**请求参数**：

-- 用户名：		    username (urlencode之后的值)

-- 平台ID： 		platform（后台填写）

-- App ID：		    appid（后台获取）

-- 区服ID：		    area（后台填写，如1,2,3…）

-- 签名：  		    sign

-- 师傅用户名：		invite_user（可能为空）

-- 邀请码：		    shortcode


**签名规则**： 

`md5('appid=' + [appid] +  '&area=' + [area] + '&key=' + [appkey] + '&platform=' + [platform] + '&username=' + username)`

(appkey为App密钥；键值按a-z排序)

(如果后台填写了登录地址，App的游戏入口会使用请求参数组装成访问地址：[后台填写的登录地址]?username=[username]&appid=[appid]&platform=[platform]&area=[area]&sign=[sign]，否则会直接跳转填写的官网地址)



### 3.1.2 支付


#### 3.1.2.1 引入SDK

`<script src="https://game.ttf.one/assets/sdk/toolpack.js"></script>`

初始化sdk
```
var tool = new bcpgTool();
tool.init();
```

#### 3.1.2.2 请求支付页面

##### 发起支付

`tool.initPay({支付参数})`

**参数定义**：

--用户名：     		username (urlencode之后的值)

--平台ID：     		platform (后台填写)

--App ID：     		appid（后台获取）

--区服ID：     		area（后台填写，如1,2,3…）

--订单号：     		order

--价格： 		    amount

--商品名		    goodsname

--商品数量：	    quantity (如：6，不传则默认为1)

--签名：  		    sign

**签名规则**： 

`md5('appid=' + [appid] +  '&area=' + [area] + '&key=' + [paykey] + '&order=' + [order] + '&platform=' + [platform] + '&username=' + [username])`

(paykey为支付密钥；键值按a-z排序)


##### 客户端监听回调
```
window.addEventListener('pay_back', function(event){
    console.log(JSON.stringify(event.data))
    //客户端处理返回值
});
```

##### 接口返回
```
数据格式： JSON
code  : 1（无错误）/401（未登录授权）/9201（用户取消）/0（其他错误）
msg :1-确定/401-请登录后操作/9201-用户取消/0-没有找到游戏信息/0-没有找到用户信息/0-上传的金额有误/0-签名错误
data:{
        order:订单号,
        username:用户名,
        platform:平台ID,
        appid:App ID,
        area:区服ID,
        amount:支付金额,
	    method:支付渠道
     }
```

##### 回调地址返回
```
{
        order: 订单号,
        username: 用户名,
        platform: 平台ID,
        appid: App ID,
        area: 区服ID,
        amount: 支付金额,
        goodsname: 商品名,
        quantity: 商品数量,
        goods: 商品ID,
        sign: 签名
     }
```

**签名规则**： 

`md5('appid=' + [appid] +  '&area=' + [area] + '&key=' + [paykey] + '&order=' + [order] + '&platform=' + [platform] + '&username=' + [username])`

(paykey为支付密钥；键值按a-z排序)

**重要：接收到回调以后，请返回字符串`success`，以标识成功收到回调信息**


## 3.2 其他


### 3.2.1 SDK地址

`https://game.ttf.one/assets/sdk/toolpack.js`

(也可使用：`https://cdn.gocloudcache.com/site/client/assets/sdk/toolpack.js`)


### 3.2.2 签名规则

所有参数按key的ASCII升序排列，然后把参数构造成以下格式的字符串

`a=xxx&b=xxx&c=xxx`

小写md5()的结果就是参数中的sign

例如：`md5('appid=' + [appid] +  '&area=' + [area] + '&key=' + [appkey/paykey] + '&order=' + [order] + '&platform=' + [platform] + '&username=' + [username])`

