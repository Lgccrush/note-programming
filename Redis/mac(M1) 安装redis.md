### 通过brew安装

```bash
brew install redis 
```

**如果出现报错**

```bash
Warning: You are using macOS 12.
We do not provide support for this pre-release version.
...
```

![image-20221017230547013](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/10/17/23:05:47_image-20221017230547013.png)

**执行下面命令**

```bash
cd "$(brew --repo)" && git fetch && git reset --hard origin/master && brew update
```

![image-20221017230741816](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/10/17/23:07:41_image-20221017230741816.png)

**等待出现**

```bash
You have 2 outdated formulae installed.
You can upgrade them with brew upgrade
or list them with brew outdated.
```

![image-20221017230754648](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/10/17/23:07:54_image-20221017230754648.png)

**再执行**

```bash
brew install redis 
```

![image-20221017230948968](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/10/17/23:09:49_image-20221017230948968.png)

**安装成功后启动redis执行命令**

```bash
brew services start redis
```

![image-20221017231050878](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/10/17/23:10:50_image-20221017231050878.png)