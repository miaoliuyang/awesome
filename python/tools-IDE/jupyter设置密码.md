
安装anaconda后，然后在电脑->右键选择属性->高级系统设置->环境变量->系统变量->path中加入anaconda安装的目录以及anaconda安装目录下的Scripts目录(去掉之前设置的python的目录)

* 进行python 
   ```
   from notebook.auth import passwd
   passwd() 
   ```
   比如’sha1:———————–’，复制该字符串，之后需要写到jupyter配置文件中。


* 退出python后执行： 
  ```
  jupyter notebook --generate-config 
  ```
  将会在home目录下生成一个隐藏文件夹.jupyter，该文件夹中有一个jupyter的配置文件；

* 打开配置文件jupyter_notebook_config.py,将以下信息进行修改 
  ```
  c.NotebookApp.ip = '*'#这里默认是localhost,想要可以被其他电脑访问，要设置成你的电脑ip。 
  c.NotebookApp.password = 'sha1:-----'#这里就是上面步骤1中的字符串 
  c.NotebookApp.port = 8000 # 这里的端口可以自己定义，是之后连接的时候需要设定的。
  ```


重新启动jupyter notebook,输入在步骤1中设置的密码，完美解决无法进入jupyter的问题。
