# Maven 

1. Mac maven 全局配置

```shell
vim ~/.bash_profile
```

在bash_profile中配置好maven的环境变量

```shell
source ~/.bash_profile
```

这样只在当前窗口生效，所以需要在zshrc中加入脚本保证永久生效

```shell
vim ~/.zshrc
```

在文件中添加

```shell
if [ -f ~/.bash_profile ]; then
   source ~/.bash_profile
fi
```

然后再执行：

```shell
source ~/.zshrc
```

就可以永久生效了

