---
layout: post
title:  "Komut satırından MySQL ile çalışmak - Bölüm 1"
description: "Komut satırı kullanarak MySQL veritabanlarını ve kullanıcılarını yönetmek - bölüm 1"
date:   2015-01-28 13:15:00
categories:
- mysql
- database
---

Web uygulamaları ile çalışırken hepimizin veritabanına ihtiyacı olmuştur. Piyasada kullanılan uygulamalar genel olarak MySQL ve türevlerini (MariaDB, Percona gibi)  destekler ve çoğunlukla paket sistemler, ana veritabanı uygulaması olarak MySQL ve türevlerini kurar / kullanır.

Her ne kadar MySQL Workbench veya phpMyAdmin (PMA) gibi güzel uygulamalar piyasada olsa da, bu uygulamaların kurulup kullanılması bir parça maliyetli olabiliyor. Örneğin, MySQL Workbench bir sürü Python kütüphanesine ihtiyaç duyar ve kullanıcı işletim sisteminde bölge olarak Türkiye seçerse veya Türkçe bir işletim sistemi kullanırsa, hata verip kapanabilir. PMA ise sunucu üzerinde çalışır bir Apache ve PHP kurulumu ister. Ekstra kurulan her servisin güvenliği / güncellemesi üzerinde de çalışmak zorunda kalan sistem yöneticileri, *less is more* mantığı ile PMA'i pek sevmezler ve zorunda kalmadıkça da kurmazlar. Sistem yöneticileri PMA kurmamak için bir sürü başka sebep üretebilir, ama onları karıştırmayalım :)

Bu açıdan bakıldığında, zaman zaman elimizde yalnızca SSH ile bağlanabileceğimiz bir sunucu ve komut satırından çalışan bir *mysql client* olabilir. Buna hazırlıklı olmamız gerekir. Bu yazıda da komut satırından MySQL kullanmak ile ilgili bazı temel konulardan bahsedeceğim.

## Kullanılan MySQL istemcisi

Öncelikle sürüm bilgisi verelim.

{% highlight bash %}
$ mysql --version
mysql  Ver 14.14 Distrib 5.5.41, for debian-linux-gnu (x86_64) using readline 6.3
{% endhighlight %}

## Sunucuya giriş yapmak

Komut satırı kullanarak MySQL sunucumuza giriş yapmak için `mysql -h localhost -u root -p` komutunu kullanacağız. Burada

* **-h** sunucu adresini belirtir. *db.example.com* gibi bir adres yazmak da mümkündür.
* **-u** giriş yapacak kullanıcıyı belirler. Biz en yetkili *root* kullanıcısı ile giriş yapıyoruz.
* **-p** ise programın bize şifre sormasını sağlar. Eğer kullanıcı için bir şifre belirlememişseniz, bu parametreyi yazmanıza gerek yok.

Bağlandıktan sonra aşağıdakine benzer bir ekran çıktısı alacağız:

{% highlight bash %}
$ mysql -h localhost -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 51
Server version: 5.5.41-0ubuntu0.14.04.1-log (Ubuntu)

Copyright (c) 2000, 2014, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
{% endhighlight %}

## Bazı temel sorgular

Şu anda sunucuya giriş yapmış ve sorgu yazabilir hale gelmiş durumdayız. Örnek olarak aşağıdaki sorguları kullanalım:

* **SHOW DATABASES;** sorgusu ile sunucu üzerinde *yetkimiz dahilinde olan veritabanlarını* görebiliriz.
* **USE mysql;** sorgusu ile *mysql* isimli veritabanını seçebiliriz.
* **SELECT User FROM mysql.user;** sorgusu ile veritabanı sunucusu üzerindeki kullanıcıların listesini görebiliriz. Eğer **USE mysql;** sorgusunu yazmışsak, **SELECT User FROM user** sorgusu yeterli olacaktır.
* **SHOW TABLES;** veritabanı içindeki tablo listesini gösterecektir.
* **DESCRIBE user;** ise *user* tablosunun yapısını gösterecektir.

Aşağıda bu komutların örneklerini bulabilirsiniz.

### Sorgu: use mysql;

{% highlight bash %}
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
{% endhighlight %}

### Sorgu: show tables;

{% highlight bash %}
mysql> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
24 rows in set (0.00 sec)
{% endhighlight %}

### Sorgu: describe user;

{% highlight bash %}
mysql> describe user;
+------------------------+-----------------------------------+------+-----+---------+-------+
| Field                  | Type                              | Null | Key | Default | Extra |
+------------------------+-----------------------------------+------+-----+---------+-------+
| Host                   | char(60)                          | NO   | PRI |         |       |
| User                   | char(16)                          | NO   | PRI |         |       |
| Password               | char(41)                          | NO   |     |         |       |
| Select_priv            | enum('N','Y')                     | NO   |     | N       |       |
| Insert_priv            | enum('N','Y')                     | NO   |     | N       |       |
| Update_priv            | enum('N','Y')                     | NO   |     | N       |       |
| Delete_priv            | enum('N','Y')                     | NO   |     | N       |       |
| Create_priv            | enum('N','Y')                     | NO   |     | N       |       |
| Drop_priv              | enum('N','Y')                     | NO   |     | N       |       |
| Reload_priv            | enum('N','Y')                     | NO   |     | N       |       |
| Shutdown_priv          | enum('N','Y')                     | NO   |     | N       |       |
| Process_priv           | enum('N','Y')                     | NO   |     | N       |       |
| File_priv              | enum('N','Y')                     | NO   |     | N       |       |
| Grant_priv             | enum('N','Y')                     | NO   |     | N       |       |
| References_priv        | enum('N','Y')                     | NO   |     | N       |       |
| Index_priv             | enum('N','Y')                     | NO   |     | N       |       |
| Alter_priv             | enum('N','Y')                     | NO   |     | N       |       |
| Show_db_priv           | enum('N','Y')                     | NO   |     | N       |       |
| Super_priv             | enum('N','Y')                     | NO   |     | N       |       |
| Create_tmp_table_priv  | enum('N','Y')                     | NO   |     | N       |       |
| Lock_tables_priv       | enum('N','Y')                     | NO   |     | N       |       |
| Execute_priv           | enum('N','Y')                     | NO   |     | N       |       |
| Repl_slave_priv        | enum('N','Y')                     | NO   |     | N       |       |
| Repl_client_priv       | enum('N','Y')                     | NO   |     | N       |       |
| Create_view_priv       | enum('N','Y')                     | NO   |     | N       |       |
| Show_view_priv         | enum('N','Y')                     | NO   |     | N       |       |
| Create_routine_priv    | enum('N','Y')                     | NO   |     | N       |       |
| Alter_routine_priv     | enum('N','Y')                     | NO   |     | N       |       |
| Create_user_priv       | enum('N','Y')                     | NO   |     | N       |       |
| Event_priv             | enum('N','Y')                     | NO   |     | N       |       |
| Trigger_priv           | enum('N','Y')                     | NO   |     | N       |       |
| Create_tablespace_priv | enum('N','Y')                     | NO   |     | N       |       |
| ssl_type               | enum('','ANY','X509','SPECIFIED') | NO   |     |         |       |
| ssl_cipher             | blob                              | NO   |     | NULL    |       |
| x509_issuer            | blob                              | NO   |     | NULL    |       |
| x509_subject           | blob                              | NO   |     | NULL    |       |
| max_questions          | int(11) unsigned                  | NO   |     | 0       |       |
| max_updates            | int(11) unsigned                  | NO   |     | 0       |       |
| max_connections        | int(11) unsigned                  | NO   |     | 0       |       |
| max_user_connections   | int(11) unsigned                  | NO   |     | 0       |       |
| plugin                 | char(64)                          | YES  |     |         |       |
| authentication_string  | text                              | YES  |     | NULL    |       |
+------------------------+-----------------------------------+------+-----+---------+-------+
42 rows in set (0.01 sec)
{% endhighlight %}

İsterseniz **SHOW VARIABLES;** sorgusu ile o anda kullandığınız MySQL sunucusunun ayarları hakkında bilgi sahibi olabilirsiniz.

Bu yazının ilk bölümü tamamlandı. Gelecek yazıda veritabanı oluşturmak ve kullanıcı eklemek hakkında bilgi vereceğim.

*ORB.*
