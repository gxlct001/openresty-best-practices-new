# Lua 的包管理工具

NodeJs 有 npm，Python 有 pip，Ruby 有 gem，PHP 有 composer，Lua 有什么呢？
答案是 OPM 和 LuaRocks，本文就介绍这两个工具。

## 一，OPM

### 1，简介

OPM（OpenResty Package Manager）是 OpenResty 自带的包管理器，在你安装好 OpenResty 之后，应该可以需要看一下 OPM 是否可以直接使用。

- 项目地址：[openresty/opm](https://github.com/openresty/opm)
- 项目网站：[opm.openresty.org](https://opm.openresty.org/)

### 2，查看版本

```bash
$ which opm
或者
$ opm --help
```

如果发现 OPM 没有安装，需要手动安装，命令如下：

```bash
$ resty-doc
-bash: resty-doc: command not found

$ opm -v
ERROR: no command specified.
```

### 3，安装

```bash
$ yum install openresty-opm
```

再次查看 opm 的安装路径:

```bash
$ which opm
/usr/bin/opm
```

包缓存路径:

```
/root/.opm/cache/tokers
```

### 4，使用

我们可以试着去找找发送 http 请求的库

```bash
$ opm search http
```

### 5，安装 lua-http-requests 包

```bash
$ opm install lua-resty-requests
ERROR: package name lua-resty-requests is not prefixed by an account name.
Finding candidates...
tokers/lua-resty-requests                         Yet Another HTTP library for OpenResty
```

需要把包的维护者名称也加上：

```bash
$ opm install tokers/lua-resty-requests
* Fetching tokers/lua-resty-requests
  Downloading https://opm.openresty.org/api/pkg/tarball/tokers/lua-resty-requests-0.7.2.opm.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 15972  100 15972    0     0  36972      0 --:--:-- --:--:-- --:--:-- 36972
Package tokers/lua-resty-requests 0.7.2 installed successfully under /usr/local/openresty/site/ .
```

可以看到，安装成功，包的文件路径是： `/usr/local/openresty/site/`。


## 二，LuaRocks

### 1，简介

LuaRocks 是 OpenResty 世界的另一个包管理器，诞生在 OPM 之前。不同于 OPM 里只包含 OpenResty 相关的包，LuaRocks 里面还包含 Lua 世界的库。

- 项目地址：[LuaRocks](https://github.com/luarocks/luarocks)
- 项目网站：[luarocks.org](https://luarocks.org/)

### 2，下载

```bash
// 当前最新版本是 3.7.0
$ wget http://luarocks.github.io/luarocks/releases/luarocks-3.7.0.tar.gz
```
### 3，解压

```bash
$ tar -zxvf luarocks-3.7.0.tar.gz
```

### 4，执行 configure

```bash
$ cd luarocks-3.7.0/
$ ./configure \
--prefix=/usr/local/openresty/luajit \
--with-lua=/usr/local/openresty/luajit/ \
--with-lua-include=/usr/local/openresty/luajit/include/luajit-2.1
```

### 5，参数说明
- --prefix: 设定 luarocks 的安装目录（默认 /usr/local/）
- --with-lua: 系统中已安装的 lua 的根目录
- --lua-suffix: 版本后缀，因为 OpenResty 的 lua 解释器使用的是 luajit，所以此处应该写 jit (不再是必选项)
- --with-lua-include: 设置 lua 引入一些头文件的目录

### 6，configure 返回结果如下：

```
--lua-suffix is no longer necessary.
The suffix is automatically detected.

Configuring LuaRocks version 3.7.0...

Lua version detected: 5.1
Lua interpreter found: /usr/local/openresty/luajit/bin/luajit
lua.h found: /usr/local/openresty/luajit/include/luajit-2.1/lua.h
unzip found in PATH: /usr/bin

Done configuring.

LuaRocks will be installed at......: /usr/local/openresty/luajit
LuaRocks will install rocks at.....: /usr/local/openresty/luajit
LuaRocks configuration directory...: /usr/local/openresty/luajit/etc/luarocks
Using Lua from.....................: /usr/local/openresty/luajit
Lua include directory..............: /usr/local/openresty/luajit/include/luajit-2.1

* Type make and make install:
  to install to /usr/local/openresty/luajit as usual.
* Type make bootstrap:
  to install LuaRocks into /usr/local/openresty/luajit as a rock.
```

### 7，make && make install

```
$ make && make install
```

执行完上面的安装命令，可以看到 luarocks 已经安装在了 `/usr/local/openresty/luajit/bin/` 下面。


### 8，添加到 PATH 中

```
$ echo 'export PATH=$PATH:/usr/local/openresty/luajit/bin'>> ~/.bash_profile
```

### 9，执行 source，使之立即生效

```
$ source ~/.bash_profile
```

### 10，使用

我们可以试着去找找发送 http 请求的库
```bash
$ luarocks search lua-resty-http
```

### 11，安装 lua-http-requests 包

```bash
$ luarocks install lua-resty-http
```

安装结果：(被安装到了 luarocks 源码包目录下了)
```
Installing https://luarocks.org/lua-resty-http-0.16.1-0.src.rock
Warning: Failed downloading. Attempting mirror at https://raw.githubusercontent.com/rocks-moonscript-org/moonrocks-mirror/master//lua-resty-http-0.16.1-0.src.rock
Warning: Failed downloading. Attempting mirror at https://luafr.org/luarocks//lua-resty-http-0.16.1-0.src.rock

lua-resty-http 0.16.1-0 depends on lua >= 5.1 (5.1-1 provided by VM)
No existing manifest. Attempting to rebuild...
lua-resty-http 0.16.1-0 is now installed in /usr/local/src/luarocks-3.7.0/./lua_modules (license: 2-clause BSD)
```
