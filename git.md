# GIT

## post-receive

```
#!/bin/sh
unset GIT_DIR

deployPath='/home/user/your_repo'

cd $deployPath
git pull origin master
```

**unset GIT_DIR 这句不能省略**

## 在vscode里使用
它也是会从.git/config里读取remote的配置，这里就配置了相应的puttykeyfile。好像要用cmd运行一下git之后，才可以在vscode里和远程仓库交互，看来vscode也是直接用.ssh目录下的id_rsa，而不是putty那个。