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