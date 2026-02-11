## কীভাবে একটা ডাইনামিক ওয়েবসাইট মোবাইলেই run করতে হয়?
##### নিচের ধাপগুলো অনুসরণ করো:
### Step - 1
প্রথম Google Chrome বা অন্য কোনো ব্রাউজার অ্যাপ ওপেন করে https://f-droid.org তে ভিজিট করো। তারপর f-droid এর সার্চবারে গিয়ে লিখ: **“Termux”**। প্রথম অ্যাপটি ডাউনলোড করে নাও। Then এটা তোমার ফোনে ইনস্টল করে নাও।
>নোট: প্লে-স্টোরের Termux এখন আর আপডেট হয় না, তাই F-Droid ভার্সনটি ব্যবহার করা সবথেকে নিরাপদ। [F-Droid কি তা জানো](https://jahidul.pages.dev/about-f-droid)।

### Step - 2
Termux ওপেন করো। সবার নিচে ~$ দেখতে পাবে। সেখানে 
```
termux-setup-storage && pkg update && pkg upgrade -y && pkg install apache2 php php-fpm mariadb nano -y && mysql_install_db && echo "সব রিসোর্স সফলভাবে ইনস্টল হয়েছে!"
```
—এই কোডটি কপি করে নিয়ে পেস্ট করো। তারপর কিবোর্ড এর এন্টার চাপো।

Termux স্টোরেজ পারমিশন চাইবে। 'Allow' চেপে দাও।

এন্টার চাপার পর অনেকগুলো লেখা আসতেই থাকবে আসতেই থাকবে ভয় পেও না।

যদি কোনো জায়গায় মেসেজ আসে “do you want to continue? Y/n” তাহলে কিবোর্ড থেকে "y" লিখে এন্টার চাপো।

এছাড়াও যদি কোথাও Termux আটকে যায় বা স্ক্রিন ফ্রিজ হয়ে যায় তাহলে ৫ বা ১০ মিনিট অপেক্ষা করার পর শুধু একবার এন্টার চেপে দাও। Termux আবার চলতে শুরু করবে।

সবশেষে Termux এ আবার “~$” দেখতে পেলে বুঝবে এই প্রসেস শেষ হয়েছে।

>এন্টার চাপার মানে হচ্ছে তুমি গুগল বা ইউটিউবে কিছু লিখে সার্চ করার জন্য কীবোর্ডের যেই বাটনে চাপো সেখানে চাপ দেওয়া।

### Step - 3
আবার Termux এ 
```
nano /data/data/com.termux/files/usr/etc/apache2/httpd.conf
```
লিখে এন্টার চাপো। তারপর তোমার সামনে অনেক লেখা আসবে সবকিছু ডিলিট করে নিচে কোডটা পেস্ট করো। 

*(সব লেখা একসাথে কাটতে বা ডিলিট করতে কিবোর্ড থেকে Alt+T চাপ দাও)*

```apache
ServerRoot "/data/data/com.termux/files/usr"

Listen 8080

ServerName localhost

LoadModule mpm_prefork_module libexec/apache2/mod_mpm_prefork.so
LoadModule authn_core_module libexec/apache2/mod_authn_core.so
LoadModule authz_core_module libexec/apache2/mod_authz_core.so
LoadModule unixd_module libexec/apache2/mod_unixd.so
LoadModule dir_module libexec/apache2/mod_dir.so
LoadModule mime_module libexec/apache2/mod_mime.so
LoadModule log_config_module libexec/apache2/mod_log_config.so
LoadModule alias_module libexec/apache2/mod_alias.so
LoadModule rewrite_module libexec/apache2/mod_rewrite.so
LoadModule authz_host_module libexec/apache2/mod_authz_host.so
LoadModule access_compat_module libexec/apache2/mod_access_compat.so

# CGI & Proxy (for PHP)
LoadModule proxy_module libexec/apache2/mod_proxy.so
LoadModule proxy_fcgi_module libexec/apache2/mod_proxy_fcgi.so

User nobody
Group nobody

ServerAdmin Jahidul@noexist.com

DocumentRoot "/storage/emulated/0/ফোল্ডার-নেম"

<Directory />
    AllowOverride none
    Require all denied
</Directory>

<Directory "/storage/emulated/0/ফোল্ডার-নেম">
    AllowOverride All
    Require all granted
</Directory>

DirectoryIndex index.php index.html

TypesConfig etc/apache2/mime.types
AddType application/x-httpd-php .php

# Logs
ErrorLog var/log/apache2/error.log
CustomLog var/log/apache2/access.log common

# PHP-FPM handler (THIS IS THE KEY PART)
<FilesMatch \.php$>
    SetHandler "proxy:unix:/data/data/com.termux/files/usr/var/run/php-fpm.sock|fcgi://localhost/"
</FilesMatch>

# Custom 404 page
ErrorDocument 404 /404.html
```


কোডে বাংলায় **"ফোল্ডার-নেম"** লেখার জায়গায় তোমার ওয়েবসাইটের নাম দাও অথবা ইচ্ছামত যেকোনো একটা নাম দাও। 

তারপর কীবোর্ডের উপরে থাকা CTRL+ কিবোর্ড এর "O" বাটন চাপো। তোমার পেস্ট করা কোড সেভ হয়ে যাবে। Then, তুমি আবার CTRL+ কীবোর্ডের "x" চাপো। তারপর তুমি সেখান থেকে বেরিয়ে আসবে আবার সবার নিচে ~$ দেখতে পাবে।

**NOTE: ফাইল ম্যানেজার অ্যাপ ব্যবহার করে "ফোল্ডার-নেম" এর জায়গায় যেই নাম দিয়েছো সেই নামে একটা ফোল্ডার বানিয়ে সেখানে তোমার index.html ফাইলসহ প্রয়োজনীয় সব ফাইল রাখতে ভুলে যেও না যেন! না হলে কিন্তু কিছুই কাজ করবে না।**

### Step - 4
ফাইল ম্যানেজার দিয়ে তোমার তৈরি করা ফোল্ডারের ভিতরে .htaccess নামে একটা ফাইল বানাও এবং সেখানে নিচের কোড পেস্ট করো:

>.htaccess ফাইলটি তুমি Acode অ্যাপ দিয়ে বানাতে পারো। অথবা তোমার বর্তমান ব্যবহার করা টেক্সট এডিটর দিয়েও বানাতে পারো।
```
# =========এটি জাহিদুল ইসলাম শুভর তৈরি করা .htaccess ফাইল=========
RewriteEngine On
# RewriteBase /

# PHP ফাইলের জন্য (সব লেভেলে)
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME}\.php -f
RewriteRule ^(.+?)/?$ $1.php [L]

# HTML ফাইলের জন্য (সব লেভেলে)
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME}\.html -f
RewriteRule ^(.+?)/?$ $1.html [L]

# হোমপেজের জন্য (যদি খালি URL আসে)
RewriteRule ^$ index.html [L]

# 404 হ্যান্ডলিং (URL চেঞ্জ সহ)
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /404 [L,R=301]

# /404 URL কে 404.html এ ম্যাপ করুন
RewriteRule ^404/?$ 404.html [L]

# ==========নিচের কোডগুলো advanced ডেভেলপারদের জন্য নতুনদের জন্য নই=============

# ♡ কোডটি তৈরি করেছেন জাহিদুল ইসলাম শুভ | TuniVerese ডেভেলপার ♡

# ==============যখন ফাইল/ফোল্ডারে অ্যাক্সেস নিষেধ:===============

# ইউজারের জন্য নিষিদ্ধ পেজ
RewriteRule ^(example)(/.*)?$ - [F,L]
RewriteRule ^(test\.php)$ - [F,L]
ErrorDocument 403 /403.html

# Maintenance পেজ | এখনও কাজ চলছে এমন পেজ
RewriteRule ^(private|admin)(/.*)?$ - [R=501,L]
RewriteRule ^(config\.php|secret\.txt|backup\.sql)$ - [R=501,L]
ErrorDocument 501 /501.html

# নোট: RewriteRule কনফিগারেশনের জন্য "501","400","401","403,"404,"405","408","410","429","451","500","501","502","503","504","505" ইত্যাদির বেশি কোড ইউজ করা যাবে না। যদিও আরও কিছু কোড রয়েছে যেগুলোও কাজ করতে পারে তবে আমার মনে হয় ব্যবহারের জন্য এগুলোই যথেষ্ট হবে। R এর এই কোডগুলো ভুল হলে Apache এরর 500 পেজ দেখাতে পারে। তাই শুধু এই কোডগুলোর মধ্যেই সীমাবদ্ধ থাকায় সবচেয়ে নিরাপদ।। 

# ___________________ফাইল সমাপ্ত হয়েছে________________
```

### Step - 5
এখন আবার Termux এ নিচের কোডটা পেস্ট করে এন্টার দাও: 
```
echo 'server() { if [ "$1" == "start" ]; then apachectl start && php-fpm && echo "Started"; elif [ "$1" == "stop" ]; then apachectl stop && pkill php-fpm && echo "Stopped"; fi; }' >> ~/.bashrc && source ~/.bashrc
```

### Congratulations! 
**তোমার কাজ শেষ।** এখন শুধু Termux এ "server start" লিখ। তারপর ব্রাউজারে গিয়ে "localhost:8080" লিখ। এখন তোমার ডাইনামিক ওয়েবসাইট দেখতে পাবে! [Termux অ্যাপ সম্পর্কে জানতে এখানে চাপো](https://jahidul.pages.dev/about-termux)।
### সহায়ক তথ্য (এগুলো না পড়লেও চলবে)
সুন্দর UI ও শক্তিশালী একটা ফাইল ম্যানেজার হিসেবে গুগলের তৈরি করা "Files by Google" অ্যাপটি তোমরা ব্যবহার করতে পারো। এই অ্যাপটি তোমরা প্লে স্টোরে পেয়ে যাবে। আমিও এটাই ব্যবহার করি।

টেক্সট এডিটর হিসেবে Acode ব্যবহার করতে পারো। আমিও এটাই ব্যবহার করি। এই অ্যাপটিও তোমরা প্লে স্টোরে পেয়ে যাবে। আমি তোমাদের প্লে স্টোরের অ্যাপটার বদলে F-Droid এর অ্যাপটা ব্যবহার করার পরামর্শ দিবো। কারণ প্লে স্টোরের অ্যাপটিতে প্রচুর বিজ্ঞাপন (অ্যাড) আসে।

এখানে আমরা server সফটওয়্যার হিসেবে Apache নামক একটা সফটওয়্যার ব্যবহার করেছি। [Apache কি তা জানতে এখানে চাপো](https://jahidul.pages.dev/about-apache)।

তাছাড়া ব্যাকঅ্যান্ড হিসেবে PHP ও ডাটাবেজ হিসেবে MariaDB ব্যবহার করেছি। [PHP ও MariaDB কি তা জানতে এখানে চাপো](https://jahidul.pages.dev/about-php&mariadb)।
