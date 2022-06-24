# Xray-TLS+Web build/management script
## Table of contents
[1. Screenplay characteristics] (#Screenplay characteristics)

[2. Precautions] (#Precautions)

[3. Installation time description] (#Installation time description)

[4. Script Instructions](#Script Instructions)

[5. Running screenshots](#Running screenshots)

[6. Description of fake website](#Description of fake website)

[7. About TLS handshake, TLS fingerprint and ALPN] (# About tls handshake tls fingerprint and alpn)

[8. About gRPC and WebSocket](#About gRPC and WebSocket)

[9. Installation location] (#installation location)

[10. Dependency list](#Dependency list)

[11. Note] (# Note)
## Screenplay characteristics
1. Support (VLESS/VMess)-(TCP/gRPC/WebSocket)-(XTLS/TLS) + Web construction/management, support multiple protocols coexist

2. Integrated multi-version bbr/sharp installation option
 
3. Support multiple systems (Ubuntu CentOS Debian deepin fedora...)

4. Supports multiple instruction sets (x86 x86_64 arm64 ...)

5. Support ipv6only server (need to set dns64 by yourself)

6. Integrated delete function of Alibaba Cloud Shield and Tencent Cloud Shield (only valid for Alibaba Cloud and Tencent Cloud servers)

7. Use Nginx as a website service

8. Using Xray as a pre-split

9. Use acme.sh to automatically apply for/update domain name certificates

10. Support choosing to build a personal network disk as a disguised webpage
## Precautions
1. This script requires a domain name that resolves to the server (supports cdn)

2. This script takes a long time to install, see **[Installation time description](#Installation time description)**

3. This script is designed for personal VPS users and is not suitable for airport owners (this script does not have functions such as multi-user management/traffic statistics).

4. It is recommended to use this script on a clean system (VPS console - reset system)
## Installation time description
The installation time of this script is relatively long (**[installation time reference](#installation time reference)**), see [here](#Why the script installation time is so long).

This script is suitable for long-term use after installing it once, and it is not suitable for resetting the system installation repeatedly, which will consume a lot of your time. If you need to change the configuration and domain name, etc., there are corresponding options in the management interface.

If there is a need for quick installation, it is recommended to select other scripts in **[Xray-core#Installation](https://github.com/XTLS/Xray-core#Installation)**
### Installation time reference
Installation process:

`[Upgrade system components]->[Install bbr]->[Install php]->Install Nginx->Install Xray->Apply for a certificate->Configuration file->[Configure fake website]`

The part enclosed in `[]` is optional.

**This is the average installation time of a single-core 1G server, for reference only:**
|Project|Duration|
|-|-|
|Upgrade installed software|0-10 minutes|
|Upgrade system|10-20 minutes|
|Install bbr|0-3 minutes|
|Install php|Centos8 (gcc8.3 4.18 kernel): 20-60 minutes|
||Ubuntu20.10 (gcc10.2 5.11-rc3 kernel): 15-20 minutes|
||Debian10(gcc8.3 4.19 kernel): 10-15 minutes|
|Install Nginx|13-15 minutes|
|Install Xray|<half a minute|
|Apply for a certificate|1-2 minutes|
|Profile|<100ms|
|Configure the fake website|Nextcloud: 1-3 minutes|
||Cloudreve: 1-2 minutes|
### Why does the script take so long to install?
There are three reasons why the time is longer than other scripts:
```
1. Integrated the function of installing bbr
2. Integrate the function of updating the system and software packages
3. (main reason) Nginx and php of scripts are compiled from source code, and other scripts usually obtain binary programs directly
```
The main reasons for adopting the compiled form are as follows:
```
1. Easy to manage
2. Easy to adapt to a variety of systems
```
The advantages of compiling over installing binaries directly are:
```
1. High operating efficiency (-O3 optimization is used when compiling)
2. The software version is new (you can compare the version of this script and other scripts Nginx)
```
The disadvantage is that it takes a long time to compile
## Script usage instructions
### 1. Install wget && ca-certificates
Debian base system (including Ubuntu, Debian, deepin):
```bash
apt --no-install-recommends -y install wget ca-certificates || (apt update && apt --no-install-recommends -y install wget ca-certificates)
```
Red Hat base system (including CentOS, fedora):
```bash
dnf -y install wget ca-certificates || yum -y install wget ca-certificates
```
### 2. Get/Update Script
```bash
wget -O Xray-TLS+Web-setup.sh https://github.com/kirin10000/Xray-script/raw/main/Xray-TLS+Web-setup.sh
```
### 3. Execute the script
```bash
bash Xray-TLS+Web-setup.sh
```
### 4. Follow the script prompts to complete the installation
## run screenshot
<div>
    <img width="400" src="https://github.com/kirin10000/Xray-script/blob/main/image/menu.jpg">
</div>
<div>
    <img width="600" src="https://github.com/kirin10000/Xray-script/blob/main/image/protocol.jpg">
</div>

## Fake website description
### The role of the fake website
This website is a website built with your domain name. After the building is completed, you can directly enter your domain name on the browser to visit.

All traffic you proxy with Xray will be disguised as traffic to this site.

Note that disguised websites are not a panacea. According to the experience of some people, as long as your monthly traffic exceeds a certain limit, the operator will block you, no matter what your disguised website is. That is to say, even if you do not proxy at all, and you just visit your website normally and visit too much traffic, you may be blocked.
### Choice of fake website
The common characteristics of traffic using VPS self-built Xray proxy are **single point**, **large traffic**, **long-term**, **GO-TLS fingerprint characteristics**, **same in and out Sex** etc.

* **Single-point** means that few people use it, generally only yourself, even if you share it with friends, it is generally not too much.
* **Permanent** Not only refers to a long period of time, but also refers to using the agent every day for a month or a year.
* **GO-TLS fingerprint feature** **Under the premise of not disguising the browser fingerprint**, it can be determined from the TLS handshake information that the client is a GO program, see [here](#About tls handshake tls fingerprint and alpn).
* **Identity of Inbound and Outbound** means that the traffic entering and leaving the VPS is almost the same in time and size, such as browsing `BiliBili` using Xray proxy, traffic from `BiliBili` to `VPS (Xray server)`, and Traffic from `VPS` to `Xray Client` is almost the same in time and size. **The sameness of access and exit** is a common problem of all agents. There is no good way of disguising, but because the VPS is not in the mainland, if it is not the object of special attention, it will generally not be censored.

Since all the traffic that is proxied by Xray will be disguised as the traffic that visits this website, then we choose to disguise the website as much as possible to choose the website with the same traffic characteristics as the traffic characteristics of the Xray proxy.

1. **Cloudreve 和 Nextcloud**

They are all personal network disks. Personal network disks can be understood as Baidu network disks built with their own VPS. The difference is that the files are stored in the VPS, and he is the administrator of the network disk.

Personal network disks have the most matches with the characteristics mentioned above, including **single point**, **large traffic**, **GO-TLS fingerprint characteristics**, **long-term**, etc. It is recommended that choose.

Regarding the **GO-TLS fingerprint feature**, **without disguising the browser fingerprint**, set alpn to http/1.1, which can disguise as a WebDav client implemented in GO language, see [here] for details. (#about tls handshake tls fingerprint and alpn).

The differences between Cloudreve and Nextcloud are as follows:
||Advantages|Disadvantages|
|-|-|-|
|Nextcloud|More and more powerful functions, more people use |PHP needs to be installed, it takes a lot of extra time to install php (see **[installation time reference](#installation time reference)**), and it also takes up more than Cloudreve. Multiple system resources, so it is not recommended for small computers. |
|Cloudreve|Lightweight, fast installation (no php required), less system resource use|less functions, fewer people use|
2. **403 page**

Basically all big websites have website backends. For example, the website of Bilibili is `www.bilibili.com`. But when playing the video, the video file is provided by another URL. Right-click on `Video Statistics` when playing the video, where `Video Host` is. This type of URL only has content when a specific URL suffix is ​​opened. If the URL is incorrect, an error page is returned. The 403 page is disguised as a website background.

That is to say, disguised as a 403 page, no one except yourself knows whether your website has anything.

3. **Custom Static Website**

You can place your own static website source code, and it is not recommended for Xiaobai to choose.

4. **Custom reverse proxy website**

It is not recommended to choose, because the reverse proxy is often just reverse proxy for several html and js files, and most of the content in the website is still provided by the background of the website. Does not meet the characteristics of large traffic.
## About TLS handshake, TLS fingerprint and ALPN
Although TLS is an encryption technology, there will be some clear text information transmission during the TLS handshake, including SNI information (specified by the serverName parameter), ALPN, cipher suite, etc.

The current TLS standard does not impose strict requirements on these plaintexts, so the formats of these plaintext information under different TLS implementations can be described as various, and the different plaintext features of these different TLS implementations are TLS fingerprints.

Through the TLS fingerprint, you can infer the TLS implementation you are using, such as Chrome's TLS, FireFox's TLS, and the TLS of the GO language official library.

Xray uses the TLS library officially provided by the GO language by default, which is also the TLS library used by almost all GO language programs. Xray can also simulate the fingerprints of Chrome, FireFox, Safari, but currently only supports the TCP protocol.

When using TCP and not masquerading as browser fingerprints, ALPN can be freely defined. It is recommended to set it to http/1.1, so that the Xray client can be disguised as a WebDav client implemented in GO language (such as **[gowebdav](https://github.com/studio-b12/gowebdav)**). WebDav is a network disk-specific protocol, and the protocol is based on HTTP/1.1, see: **[WebDav](https://en.wikipedia.org/wiki/WebDAV)** .

If you choose to disguise the browser fingerprint, the alpn parameter in the client configuration will be invalid, and the ALPN will be fixed to h2, http/1.1. Also, when using WebSocket, ALPN will be fixed to http/1.1; when using gRPC, ALPN will be forced to add h2. Therefore, using WebSocket can still pretend to be a GO language WebDav client, but gRPC cannot.
## About gRPC and WebSocket
When the CDN in use supports both gRPC and WebSocket, how to choose between the two? Their main differences are reflected in the following three aspects: ALPN, latency and performance.

For ALPN, see [here](#About tls handshake tls fingerprint and alpn).

Regarding latency, gRPC comes with mux, so the latency is lower. Note that this refers to the delay of opening the website, mux does not reduce the delay of the game.

Regarding performance, WebSocket has stronger performance. If your device has weak performance, such as a common home router, using WebSocket will be faster.
## install location
**Nginx：**`/usr/local/nginx`

**php：**`/usr/local/php`

**Cloudreve：**`/usr/local/cloudreve`

**Xray：** 见 **[Xray-install](https://github.com/XTLS/Xray-install)**
## Dependency list
The script may automatically install the following dependencies:
|Usage|Debian base system|Red Hat base system|
|-|-|-|
|yumdb set (mark package manual installation)||yum-utils|
|dnf config-manager||dnf-plugins-core|
|setenforce/getenforce(关闭SELinux)|selinux-utils|libselinux-utils|
|ss(check port occupancy)|iproute2|iproute|
|wget|wget|wget|
|curl|curl|curl|
|wget/curl https|ca-certificates|ca-certificates|
|kill/pkill/ps/sysctl/free|procps|procps-ng|
| epel 源 || epel-release |
| epel 源 || epel-next-release |
| remi source || remi-release |
|do-release-upgrade (upgrade system)|ubuntu-release-upgrader-core||
|unzip|unzip|unzip|
|curl|curl|curl|
|Install bbr kernel|linux-base||
|** Compilation base: **|||
|Download source files|wget|wget|
|Unzip the tar source file|tar|tar|
|Unzip the tar.gz source file|gzip|gzip|
|Unzip the tar.xz source file|xz-utils|xz|
|gcc|gcc|gcc|
|g++|g++|gcc-c++|
|make|make|make|
|**acme.sh dependencies: **|||
||curl|curl|
||openssl|openssl|
||cron|crontabs|
|**Compile openssl:**|||
||perl-base (included in libperl-dev)|perl-IPC-Cmd|
||perl-modules-5.32 (included in libperl-dev)|perl-Getopt-Long|
||libperl5.32 (included in libperl-dev)|perl-Data-Dumper|
|||perl-FindBin|
|**Compile Nginx:**|||
||libpcre2-dev|pcre2-devel|
||zlib1g-dev|zlib-devel|
|--with-http_xslt_module|libxml2-dev|libxml2-devel|
|--with-http_xslt_module|libxslt1-dev|libxslt-devel|
|--with-http_image_filter_module|libgd-dev|gd-devel|
|--with-google_perftools_module|libgoogle-perftools-dev|gperftools-devel|
|--with-http_geoip_module|libgeoip-dev|geoip-devel|
|--with-http_perl_module||perl-ExtUtils-Embed|
||libperl-dev|perl-devel|
|**Compile php:**|||
||pkg-config|pkgconf-pkg-config|
||libxml2-dev|libxml2-devel|
||libsqlite3-dev|sqlite-devel|
|--with-fpm-systemd|libsystemd-dev|systemd-devel|
|--with-fpm-acl|libacl1-dev|libacl-devel|
|--with-fpm-apparmor|libapparmor-dev||
|--with-openssl|libssl-dev|openssl-devel|
|--with-kerberos|libkrb5-dev|krb5-devel|
|--with-external-pcre|libpcre2-dev|pcre2-devel|
|--with-zlib|zlib1g-dev|zlib-devel|
|--with-bz2|libbz2-dev|bzip2-devel|
|--with-curl|libcurl4-openssl-dev|libcurl-devel|
|--with-qdbm|libqdbm-dev||
|--with-gdbm||gdbm-devel|
|--with-db4|libdb-dev|libdb-devel|
|--with-tcadb|libtokyocabinet-dev|tokyocabinet-devel|
|--with-lmdb|liblmdb-dev|lmdb-devel|
|--with-enchant|libenchant-2-dev/libenchant-dev|enchant-devel|
|--with-ffi|libffi-dev|libffi-devel|
|--enable-gd|libpng-dev|libpng-devel|
|--with-external-gd|libgd-dev|gd-devel|
|--with-webp|libwebp-dev|libwebp-devel|
|--with-jpeg|libjpeg-dev|libjpeg-turbo-devel|
|--with-xpm|libxpm-dev|libXpm-devel|
|--with-freetype|libfreetype6-dev|freetype-devel|
|--with-gmp|libgmp-dev|gmp-devel|
|--with-imap|libc-client2007e-dev|uw-imap-devel|
| --enable-intl | libicu-dev | libicu-devel |
|--with-ldap|libldap2-dev|openldap-devel|
|--with-ldap-sasl|libsasl2-dev|openldap-devel|
|--enable-mbstring|libonig-dev|oniguruma-devel|
|--with-unixODBC,--with-pdo-odbc|unixodbc-dev|unixODBC-devel|
|--with-pdo-dblib|freetds-dev|freetds-devel|
|--with-pdo-pgsql,--with-pgsql|libpq-dev|libpq-devel|
|--with-pspell|libpspell-dev|aspell-devel|
|--with-libedit|libedit-dev|libedit-devel|
|--with-mm|libmm-dev||
|--with-snmp|libsnmp-dev|net-snmp-devel|
|--with-sodium|libsodium-dev|libsodium-devel|
|--with-password-argon2|libargon2-dev|libargon2-devel|
|--with-tidy|libtidy-dev|libtidy-devel|
|--with-xsl|libxslt1-dev|libxslt-devel|
|--with-zip|libzip-dev|libzip-devel|
|Compile php-imagick: |||
||autoconf|autoconf|
||go|go|
||libmagickwand-dev|ImageMagick-devel|
## Note
1. Link to this article (official website): https://github.com/kirin10000/Xray-script

2. Reference tutorial: https://www.v2fly.org/config/overview.html https://guide.v2fly.org/ https://docs.nextcloud.com/server/latest/admin_manual/installation/source_installation. html https://docs.cloudreve.org/

3. Domain name certificate application: https://github.com/acmesh-official/acme.sh

4. The bbr script comes from: https://github.com/teddysun/across/blob/master/bbr.sh

5. The bbr2 script comes from: https://github.com/yeyingorg/bbr2.sh (Ubuntu Debian) https://github.com/jackjieYYY/bbr2 (CentOS)

6. The bbrplus script comes from: https://github.com/chiakge/Linux-NetSpeed

#### This script is only for communication and learning, please do not use this script to do illegal things. If you do illegal things outside the Internet, you will be punished by law! !
