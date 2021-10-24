---
title: "NVM 报 ls-remote command results in “N/A 错误"
date: 2021-01-15T22:20:01+08:00
categories:
  - 随笔
tags:
  - "node"
  - "Linux"
---

# NVM 报 ls-remote command results in “N/A 错误


今天工作中使用 nvm 升级 node 版本遇到了这个错误，经过一番研究终于解决。


出现这个报错的原因有许多种，我遇到的报错实际上是由代理引起。

参考： https://stackoverflow.com/questions/26476744/nvm-ls-remote-command-results-in-n-a

### 几种原因：

#### SSL 证书过期

1. 临时解决方案：


请使用导出用于抓取内容的镜像的非https版本：export NVM_NODEJS_ORG_MIRROR=http://nodejs.org/dist


2. 长久解决方案：


##### 第一种：若您运行curl $NVM\_NODEJS\_ORG_MIRROR

回答翻译：

出现

    curl: (77) error setting certificate verify locations:
      CAfile: /etc/pki/tls/certs/ca-bundle.crt
      CApath: none

则考虑修改 ~/.nvm/nvm.sh
在函数 nvm_download() 里修改，加上 curl -k $*



    if nvm_has "curl"; then
       curl -k $*  #新加的
     elif nvm_has "wget"; then
       # Emulate curl with wget
    ..


**实际上这个改动是啥操作呢？**

先看一下这个脚本函数 `nvm_download` 里到底写得是啥。

```bash
nvm_download() {
  local CURL_COMPRESSED_FLAG
  if nvm_has "curl"; then
    if nvm_curl_use_compression; then
      CURL_COMPRESSED_FLAG="--compressed"
    fi

    // 注意这里！！！
    curl --fail ${CURL_COMPRESSED_FLAG:-} -q "$@"
  elif nvm_has "wget"; then
    # Emulate curl with wget
    ARGS=$(nvm_echo "$@" | command sed -e 's/--progress-bar /--progress=bar /' \
                            -e 's/--compressed //' \
                            -e 's/--fail //' \
                            -e 's/-L //' \
                            -e 's/-I /--server-response /' \
                            -e 's/-s /-q /' \
                            -e 's/-sS /-nv /' \
                            -e 's/-o /-O /' \
                            -e 's/-C - /-c /')
    # shellcheck disable=SC2086
    eval wget $ARGS
  fi
}
```

关注一下核心的下载命令：

`curl --fail ${CURL_COMPRESSED_FLAG:-} -q "$@"`

我们 `curl -h` 对照一下，可以看到终端的输出中的说明是：

`-f, --fail  Fail silently (no output at all) on HTTP errors`

意思就是遇到的 http 错误就不会输出到终端。

第二个参数就是 压缩的命令，没啥好讲的。

第三个参数 `-q` 的说明是：

`-q, --disable       Disable .curlrc`

意思是，禁用 curl 的代理配置文件 `.curlrc`。

那么，答案里想要加上的是啥呢？

` -k, --insecure      Allow insecure server connections when using SSL`

哦，原来就是允许使用 SSL 的时候建立不安全的链接。这个应该就是解决证书过期的问题，没毛病。


##### 二、 使用 wget 代替 curl

若您您或第一种没用，考虑和我一样粗暴解法，直接将 if 语句中的 crul 和 wget 换位置，如下(也就是先考虑 wget 了)

```bash
   nvm_download() {
      local CURL_COMPRESSED_FLAG
      if nvm_has "wget"; then
            # Emulate curl with wget
        ARGS=$(nvm_echo "$@" | command sed -e 's/--progress-bar /--progress=bar /' \
                                -e 's/--compressed //' \
                                -e 's/--fail //' \
                                -e 's/-L //' \
                                -e 's/-I /--server-response /' \
                                -e 's/-s /-q /' \
                                -e 's/-sS /-nv /' \
                                -e 's/-o /-O /' \
                                -e 's/-C - /-c /')
        # shellcheck disable=SC2086
        eval wget $ARGS
      elif nvm_has "curl"; then
        if nvm_curl_use_compression; then
          CURL_COMPRESSED_FLAG="--compressed"
        fi
        curl --fail ${CURL_COMPRESSED_FLAG:-} -q "$@"
      fi
    }
```
没啥好讲的，就是把 wget 执行顺序提前了，达到不使用 curl 的效果。


#### 使用代理导致：

**这是我遇到的情况，不一定符合你的需求**

* 解决方案： 给 curl 添加代理：

注意这里我是给 curl 加上代理了，配置代理的结果就是，须要把上面 `nvm.sh` 中 `download` 函数的 `禁用代理` 参数去除。

1. 在家目录下新建文件 `.curlrc`, 执行：  `touch .curlrc`。
2. 然后填写代理地址，格式如下：

        proxy=http://<代理用户>:<代理密码>@<代理地址>:<代理端口>

或者代理不使用密码链接的：

        proxy=http://<代理地址>:<代理端口>

比如我就加上：

        proxy=http://172.29.240.1:10809

#### 好耶！

成功解决这个问题！！！
