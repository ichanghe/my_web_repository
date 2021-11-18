# 电脑配置双git账号 
git clone 项目地址报错

### 0、起因
git clone 提示没有clone的权限
于是查看具体的报错信息
```bash
ssh -T -v git@github.com
```

## 1、生成ssh-key,添加ssh-key
``` bash
cd ~/.ssh
ssh-keygen -t rsa -C "xxx@qq.com"
#设置文件名，自动生成私钥和公钥(.pub)
#Enter file in which to save the key (/c/Users/icecr/.ssh/id_rsa): id_rsa_personal
#将ssh key添加到SSH agent中 
ssh-add ~/.ssh/id_rsa_personal
```
## 2、将生成的ssh-key添加到github
## 3、新建的config文件，配置项目域名对应的key

```
#Default 第一个账号(xxxxxx@qq.com)
 
Host github.com
Hostname github.com
IdentityFile ~/.ssh/id_rsa_personal
  
#second 第二个账号（xxxxx@xxxx.com.cn）
    
Host gitlab.admin.xxx.com.cn
HostName gitlab.admin.xxx.com.cn
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_bm
```
## 4、具体的项目配置
具体到某个项目下，可以设置该项目的git配，配置信息存储在项目目录下.git/config文件中。
如果有--global全局的配置，全局配置存储在~/.gitconfig
```
git config user.name yourname
git config user.email xxxx@qq.com
```
### 5、测试配置结果

```
ssh -T git@github.com
```
