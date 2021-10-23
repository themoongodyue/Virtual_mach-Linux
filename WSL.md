#jupyter服务器
 **1.生成的配置文件，后来用来设置服务器的配置**
    $ jupyter notebook --generate-config
 **2.设置Jupyter Notebook密码**
    In [1]: from IPython.lib import passwd
    In [2]: passwd()
    Enter password: 
    Verify password: 
    Out[2]: '这里是密码'
 **3.设置服务器配置文件**
    $ vim ~/.jupyter/jupyter_notebook_config.py
 **4.在末尾增加以下几行配置信息**
    c.NotebookApp.ip = '*' #所有绑定服务器的IP都能访问，若想只在特定ip访问，输入ip地址即可
    c.NotebookApp.port = 6666 #将端口设置为自己喜欢的吧，默认是8888
    c.NotebookApp.open_browser = False #我们并不想在服务器上直接打开Jupyter Notebook，所以设置成False
    c.NotebookApp.notebook_dir = '/root/jupyter_projects' #这里是设置Jupyter的根目录，若不设置将默认root的根目录，不安全
    c.NotebookApp.allow_root = True # 为了安全，Jupyter默认不允许以root权限启动jupyter
 **5.在windows设置ip转发，将wsl的服务转发出来**
    netsh  interface portproxy add v4tov4 listenaddress=127.0.0.1 listenport=80 connectaddress=10.0.40.100 connectport=80
第一个ip是本机作为终端的ip，第二个ip是本机作为路由器的终端
