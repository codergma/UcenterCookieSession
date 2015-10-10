# Ucenter_Cookie_Session
注意：php实现登录系统，通过三种方式(cookie,session,redis)记录用户是否在线。 
本项目重点演示通过cookie,session两种方式实现登录系统（这两种不推荐使用，最好是借助redis或者memcached等缓存系统）， 
包括注册，登录，页面跳转，登出等流程，以及其中的一些注意事项，如正则表达式限制用户输入口令， 密码加密方式，如何保存
用户登录状态。找回密码和口令防守没有在本项目中演示，请Fork Ucenter_redis。 

##演示的要点有： 

* 用户名和口令： 		
>数据结构

>>树

>>>二叉树

>>>>平衡二叉树

>>>>>满二叉树

>正则表达式限制用户输入口令； 
>		
>密码加密保存 md5(md5(passwd+salt))；		 
>允许浏览器保存口令； 		
>口令在网上传输协议http;		 
* 用户登录状态：因为http是无状态的协议，每次请求都是独立的， 所以这个协议无 法记录用户访问状态，在多个页面跳转中如何知道用户登陆状态呢? 那就要在每个页面都要对用户身份进行认证。		
我将使用三种方法实现用户登录状态验证(在下才疏学浅望指正): 		
>	(1) 在cookie中保存用户名和密码，每次页面跳转的时候进行密码验证，正确则登录状态； 		
>	这个方法显然太挫了，每次都要与数据库交互显然严重影响性能，那么你可能想，直接 		
>	在cookie中保存登录状态true|false，这更挫，太不安全了，不过你可以在session 		
>	中保存，这就是第二种实现方法。 		
>	(2)在session中保存登陆状态true|false|更多信息,在页面跳转的时候获取session即可， 		
>	因为session是保存在服务器中的， 所以相对安全一些,但是由于默认session是保存在 		
>	文件中的，所以当用户数量上来之后，会产生大量小文件，影响系统性能，当然你可以更 		
>	换文件系统，或者指定其他存储方式，比如数据库。 		
>	(3)解决上面所说的性能问题，这时候就要用到Inmemory的key-value型数据库了，以前memcache 		
>	用的比较广泛，现在redis等越来越火了。没有在本项目中演示，请Fork Ucenter_redis. 		
* 使用cookie的一些原则： 		
>	(1)cookie中保存用户名，登录序列，登录token; 		
>>		用户名：明文； 		
>>		登录序列：md5散列过的随机数,仅当强制用户输入口令时更新; 		
>>		登陆token：md5散列过的随机数，仅一个登陆session内有效，新的session会更新他。 		
>	(2)上述三个东西会存放在服务器上，服务器会验证客户端cookie与服务器是否一致； 		
>	(3)这样设计的效果 		
>	(a)登录token是单实例，一个用户只能有一个登录实例； 		
>	(b)登录序列用来做盗用行为检测 		
* 找回密码功能 		
>	(1)不要使用安全问答		 
>	(2)通过邮件自行重置。当用户申请找回密码时，系统生成一个md5唯一的随机字串 		
>	放在数据库中，然后设置上时限，给用户发一个邮件，这个链接中包含那个md5 		
>	，用户通过点击那个链接来自己重置新的口令。 		
>	(3)更好的做法多重认证。 		
* 口令探测防守 		
>	(1)验证码 		
>	(2)用户口令失败次数,并且增加尝试的时间成本 		
>	(3)系统全局防守,比如系统每天5000次口令错误，就认为遭遇了攻击， 		
>	然后增加所有用户输错口令的时间成本。 		
>	(4)使用第三方的OAuth和OpenID 		
总结了登录系统设计的注意点包括密码保存方式，找回方式，面对hack攻击采取的应对措施等。 

本文参考链接：
http://coolshell.cn/articles/5353.html
