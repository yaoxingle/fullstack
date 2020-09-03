三种配置环境变量

一、修改/etc/profile文件

> 所有用户的shell都使用这些环境变量

vim /etc/profile

export JAVA_HOME=/HOME/d5000/jiangxi/jdk1.7

export PATH=$PATH:$JAVA_HOME/bin

export CLASSPATH=.:$JAVA_HOME/lib

source /etc/profile

二、修改.bash_profile文件

> 用户级别，用户主目录下

三、修改.bashrc

> 用户级别，用户主目录下





export JAVA_HOME=/home/d5000/jiangxi/jdk1.8
export CLASSPATH=.:$JAVA_HOME/lib:$CLASSPATH
export PATH=${JAVA_HOME}/bin::$PATH