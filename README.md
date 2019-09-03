docker-ssp
================================

LDAP ToolBox（LTB）自助服务密码实用程序的dockerfile，它是一个允许用户在LDAP目录中更改其密码的PHP应用程序。

## 参考

[官方文档](http://ltb-project.org/wiki/documentation/self-service-password)



## 构建自己的镜像

```bash
git clone https://github.com/wandouduoduo/docker-ssp.git
cd docker-ssp
```
在本地编辑 `assets/config.inc.php` ，然后再构建镜像
```bash
docker build -t="$USER/ssp:1.0" .
```
运行:
```bash
docker run --name sunssp -p 10001:80 -d $USER/ssp:1.0
```

## 问题

我们可以在配置文件`config.inc.php`添加下面选项，来进行debug日志输出追踪:
```php
ldap_set_option(NULL, LDAP_OPT_DEBUG_LEVEL, 7);
```
进入容器中，查看apache日志:
```bash
docker exec -ti $(docker ps | grep 'sunssp' | awk '{print $1}') tail -fn222 /var/log/apache2/error.log
```

## 模板范例

`config.inc.php`

```php
#关闭 问题验证 和 短信验证(视个人需要)：
$use_questions=false;
$use_sms= false;

#配置 LDAP
$ldap_url = "ldap://ldap.xxxxx.net";
$ldap_starttls = false;
$ldap_binddn = "cn=Manager,dc=ldap,dc=xxxxxx,dc=net";   
$ldap_bindpw = "xxxxxxxxx";
$ldap_base = "dc=ldap,dc=xxxxxx,dc=net";
$ldap_login_attribute = "cn"; 
$ldap_fullname_attribute = "cn"; 
$ldap_filter = "(&(objectClass=person)($ldap_login_attribute={login}))";
$who_change_password = "manager";   #指定LDAP 以什么用户身份更改密码
#配置邮件
$mail_from = "elk@xxxxx.com";
$mail_from_name = "企业账号密码重置";
$mail_signature = "";
$notify_on_change = true;      #密码修改成功后，向用户发送通知邮件
$mail_sendmailpath = '/usr/sbin/sendmail';   #需安装sendmail服务 yum install -y sendmail
$mail_protocol = 'smtp'; 
$mail_smtp_debug = 0;
$mail_debug_format = 'html'; 
$mail_smtp_host = 'smtp.gmail.com';
$mail_smtp_auth = true; 
$mail_smtp_user = 'elk@xxxxxx.com';
$mail_smtp_pass = 'xxxxxx';
$mail_smtp_port = 587;
$mail_smtp_timeout = 30;
$mail_smtp_keepalive = false;
$mail_smtp_secure = 'tls';
$mail_contenttype = 'text/plain';
$mail_wordwrap = 0;
$mail_charset = 'utf-8';
$mail_priority = 3;
$mail_newline = PHP_EOL;
```

配置完成，登录网页访问，通过网页修改账号密码验证即可！！

