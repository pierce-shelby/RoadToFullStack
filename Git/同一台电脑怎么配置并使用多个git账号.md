[:arrow_left:](/README.md)
# 同一台电脑怎样配置并使用多个git账号

## 一、生成密钥

首先生成账号一的密钥，执行命令即可。

```shell
ssh-keygen -t rsa -C "one@test.com"

//接下来回车两次
/*第一次其实是让输生成密钥的名字，生成后放后你git命令所在的目录
效果同2.生成密钥
第二次是让输入密码，因为并不机密所以可以空着直接回车就好
*/
```

接下来生成账号二的密钥，执行命令即可。

```shell
ssh-keygen -t rsa -C "two@test.com" -f /c/Users/Administrator/.ssh/id_rsa_two

// -f 后的目录请参照实际情况
```

接下来home目录下的.ssh隐藏目录里会出现四个密钥文件，分别是两个私钥和两个公钥。取公钥放到不同的且对应的平台账号上即可。

## 二、配置私钥

由于采取的密钥命名不是默认的名称，所以需要显示配置私钥地址给ssh agent。

```shell
ssh-add ~/.ssh/id_rsa_two //linux用这个

ssh-add id_rsa_two //windows用这个

//显示：
//Identity added: id_rsa_two (two@test.com)
```

通过`ssh-add -l`来确认结果

然后更新并配置config(.ssh/config)

**说明:** 

- \# 注释
- Host : 为域名别名
- Hostname : 真实地址
- User : 用户名
- IdentityFile : rsa存放地址

### Windows

```
# 默认github账号
Host github
  HostName github.com
  IdentityFile C:\Users\Administrator\.ssh\id_rsa

# github账号2
Host github_two
  HostName github.com
  IdentityFile C:\Users\Administrator\.ssh\id_rsa_two
```

### Linux

```
#默认github账号
Host github.com
  HostName github.com
  IdentityFile ~/.ssh/id_rsa

# github账号2
Host github_two
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_two
```

这样，就可以通过使用github.com别名github_two来明确说你要是使用id_rsa_two的SSH key来连接github，即使用工作账号进行操作。如果是不同的HostName，即可不适用别名。

命令：`git remote add origin git@github_two:xxxx/test.git`

```
#本地建库
$ git init
$ git commit -am "first commit'

#push到github上去
$ git remote add origin git@github_two:xxxx/test.git
$ git push origin master
```

## 进阶

使用两个账号时，如果对应的user.name和user.email相同，则可以全局配置为相同的name和email。

如果不同的话，只能对每个repository配置name和email。

```shell
清除全局
$ git config --global --unset user.name
$ git config --global --unset user.email

设置全局
$ git config --global user.name 'sky_two'
$ git config --global user.email 'sky_two@test.com'
```

```shell
设置单个repository
$ git config user.name 'sky_two'
$ git config user.email 'sky_two@test.com'
```

