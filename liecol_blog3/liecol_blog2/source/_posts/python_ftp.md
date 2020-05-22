---
title: 如何在Python里使用FTP
date: 2017-12-20 15:57:15
tags: [python,ftp]
categories:
- python
---

Python作为一款集包大成的语言，自然必不可少ftplib，而ftplib模块就定义了FTP类用于上传或下载文件。
##### 以下是FTP的常用函数:
```python
ftp=FTP() #设置ftp变量
ftp.set_debuglevel(2)  #开启调试级别2
ftp.connect("IP","port")  #连接的ftp 地址、端口
ftp.login("user","passwd")  #ftp的用户名、密码
ftp.cmd("liecol/test")  #进入ftp远程目录
bufsize=1024  #设置的缓冲区大小
file_handle=open("test.py","wb").write #以写模式在本地打开文件
ftp.retrbinaly("RETR test.py",file_handle,bufsize) #接收ftp服务器上文件并写入本地文件
ftp.set_debuglevel(0)  #关闭调试模式
ftp.quit()  #退出ftp

ftp.cwd(path)  #设置FTP操作的路径
ftp.dir()   #显示ftp目录下的所有信息
ftp.nlst()  #获取目录下的文件
ftp.mkd(pathname)  #新建ftp目录
ftp.pwd()  #返回当前所在位置
ftp.rmd(dirname)  #删除远程目录
ftp.delete(filename)  #删除远程文件
ftp.rename(fromname, toname) #修改ftp文件名字
ftp.storbinaly("STOR test.txt",file_handel,bufsize)  #上传目标文件
ftp.retrbinary("RETR test.txt",file_handel,bufsize)  #下载FTP文件
```
<!--more-->

笔者这边写了一个下载并删除30天前文件的Class(yymmdd)供大家参考：

```python
class DownloadFtp:
    ftp = ftplib.FTP()
    def __init__(self, host, port='21'):
        # self.ftp.set_debuglevel(2)
        # self.ftp.set_pasv(0)
        self.ftp.connect(host, port)
    
    # 登录
    def Login(self, username='', password=''):
        self.ftp.login(username, password)

    # 下载单文件
    def DownLoadFile(self, LocalFile, RemoteFile, dirpath=""):
        log.info("DownLoad File: %s" % RemoteFile)
        try:
            self.ftp.cwd(dirpath)
            bufsize = 4096
            file_handler = open(LocalFile, 'wb')
            self.ftp.retrbinary("RETR %s" % (RemoteFile), file_handler.write, bufsize)
            self.ftp.set_debuglevel(0)
            file_handler.close()
            return True
        except:
            return False
    
    # 删除文件
    def DelFile(self, FileName):
        log.info("Del File: %s" % FileName)
        self.ftp.delete(FileName)

    # 获取文件夹(未写包含文件夹情况)
    def DownLoadFileTree(self, LocalDir, RemoteDir, FtpDel=False):
        if os.path.isdir(LocalDir) == False:
            os.makedirs(LocalDir)
        self.ftp.cwd(RemoteDir)
        RemoteNames = self.ftp.nlst()
        for file in RemoteNames:
            Local = os.path.join(LocalDir, file)
            time_format = datetime.today() + timedelta(-30)
            time_format = time_format.strftime('%Y%m%d')
            # 未考虑文件名含其他8位数字情况
            file_date = re.findall(r"[1-9]\d*|0", file)
            time_yes = datetime.today() + timedelta(-1)
            time_today = time_yes.strftime('%Y%m%d')
            # time_today = datetime.today().strftime('%Y%m%d')
            if file_date:
                if str(time_format) >= file_date[0] and FtpDel:
                    # 未考虑dir情况
                    re_del = self.DelFile(file)
                elif str(time_today) == file_date[0]:
                    self.DownLoadFile(Local, file)
        self.ftp.cwd("..")
        return

    # 获取文件信息
    def find(self,filename):
        ftp_f_list = self.ftp.nlst()
        if filename in ftp_f_list:
            return True
        else:
            return False

    # 关闭
    def close(self):
        self.ftp.quit()

```