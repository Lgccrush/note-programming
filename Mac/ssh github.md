1 终端获取ssh 

```sh
ssh -v #查看是否安装ssh
cat ~/.ssh/id_rsa.pub   #查看ssh
```



![image-20211011155158868](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/11/15:51:58_image-20211011155158868.png)

2 github配置ssh

![image-20211011155543650](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/11/15:55:43_image-20211011155543650.png)

3 验证

```sh
ssh -T git@github.com 
```

![image-20211011155743544](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/11/15:57:43_image-20211011155743544.png)