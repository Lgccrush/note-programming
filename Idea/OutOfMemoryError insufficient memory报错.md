IDEA:Error: java: OutOfMemoryError: insufficient memory

点击file，选择Invalidate Caches ，清理一下，是一种解决途径，反正我解决了，哈哈

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181119143454697.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTQ3OTE2,size_16,color_FFFFFF,t_70)

如果还不行调整一下Compiler下面的Compiler Process heap size 参数，默认的是700。如果2048还不能解决问题，试着将它调得更大一些吧，嘿~

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181119144657899.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTQ3OTE2,size_16,color_FFFFFF,t_70)

https://blog.csdn.net/qq_36147916/article/details/84248572