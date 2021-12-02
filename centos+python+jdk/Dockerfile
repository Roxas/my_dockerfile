# 基础镜像 centos + python3 + pyspark
FROM centos:centos7

# 安装常用组件
RUN yum update -y && yum -y install wget curl unzip zip gcc make lsof \ 
    zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel \
    readline-devel tk-devel gdbm-devel libffi-devel libpcap-devel \
    xz-devel git make libaio libnsl \
    java-1.8.0-openjdk java-1.8.0-openjdk-devel \
    openssh-server libtirpc-devel && \
    yum clean all

WORKDIR /code

# 获取python安装包, 可以通过wget安装
COPY Python-3.6.5.tgz /code/Python-3.6.5.tgz

RUN set -ex \
    && tar -zxvf Python-3.6.5.tgz \ 
    && cd Python-3.6.5 \
    && ./configure prefix=/usr/local/python3 \
    && make \
    && make install \
    && make clean \
    && rm -rf /Python-3.6.5* \
    && yum install -y epel-release \ 
    && yum install -y python-pip

# 把python3设置为默认python
RUN set -ex \ 
    && mv /usr/bin/python /usr/bin/python27 \
    && mv /usr/bin/pip /usr/bin/pip-python2.7 \
    && ln -s /usr/local/python3/bin/python3.6.5 /usr/bin/python \
    && ln -s /usr/local/python3/bin/pip3 /usr/bin/pip

# 修复修改python默认版本，导致yum失效的问题
RUN set -ex \
    && sed -i "s#/usr/bin/python#/usr/bin/python2.7#" /usr/bin/yum \
    && sed -i "s#/usr/bin/python#/usr/bin/python2.7#" /usr/libexec/urlgrabber-ext-down \
    && yum install -y deltarpm

# 基础环境配置
RUN set -ex \
    && rm -rf /etc/localtime \ 
    && ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \ 
    && yum install -y vim \
    && yum -y install cronie

# 设置中文
RUN yum install kde-l10n-Chinese -y
RUN localedef -c -f UTF-8 -i zh_CN zh_CN.utf8

# 更新pip版本
RUN pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade pip
ENV LC_ALL zh_CN.UTF-8
ENV TZ Asia/Shanghai
ENV PATH /usr/local/python3/bin/:$PATH
RUN ln -sf /usr/share/zoneinfo/Asia/ShangHai /etc/localtime

WORKDIR /code/
ADD requirement.txt /code/requirement.txt


RUN set -ex \
    && yum install -y gcc-c++ cyrus-sasl cyrus-sasl-devel cyrus-sasl-lib geos-devel

RUN python3 -m pip install --no-cache-dir -r requirement.txt -i https://pypi.douban.com/simple/
