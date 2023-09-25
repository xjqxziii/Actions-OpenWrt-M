# Ubuntu C++ 编译环境
# 参考
# https://www.binss.me/blog/learn-docker-with-me-about-building-compiler-environment/
# https://blog.phpgao.com/zsh_in_docker.html

# docker build -t make_openwrt .
# docker run -d -p 2022:22  --name myop -v ./worker_dir:/work_dir make_openwrt

FROM ubuntu:20.04
LABEL maintainer="xjqxziii"

# 换源
RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    apt clean && \
    apt update && \
    apt install --fix-missing

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 编译工具
RUN apt install -y build-essential
RUN apt install -y cmake
RUN apt install -y gdb gdbserver
RUN apt install -y rsync # Clion 需要 rsync 同步数据
RUN apt install -y vim # vim
RUN apt install -y git # git
RUN apt install -y qemu # qemu
RUN apt install -y nasm # nasm
RUN apt install -y ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential
RUN apt install -y bzip2 ccache clang cmake cpio curl device-tree-compiler ecj fastjar flex gawk gettext gcc-multilib
RUN apt install -y g++-multilib git gnutls-dev gperf haveged help2man intltool lib32gcc-s1 libc6-dev-i386 libelf-dev
RUN apt install -y libglib2.0-dev libgmp3-dev libltdl-dev libmpc-dev libmpfr-dev libncurses5-dev libncursesw5
RUN apt install -y libncursesw5-dev libpython3-dev libreadline-dev libssl-dev libtool lld llvm lrzsz mkisofs msmtp
RUN apt install -y nano ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 python3-pip python3-ply
RUN apt install -y python-docutils python3-pyelftools qemu-utils re2c rsync scons squashfs-tools subversion swig
RUN apt install -y texinfo uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev

# ssh服务器
# 参考 https://github.com/rastasheep/ubuntu-sshd/blob/ed6fffcaf5a49eccdf821af31c1594e3c3061010/18.04/Dockerfile
RUN apt install -y openssh-server
RUN mkdir /var/run/sshd && \
    mkdir /root/.ssh
# 修改 root 的密码为 qqq123
RUN echo 'root:qqq123' | chpasswd
RUN sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config
# SSH login fix. Otherwise user is kicked off after login
RUN sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

# 删除 apt update 产生的缓存文件
# 因为 docker 的文件系统是层文件系统，上一个层中缓存有apt-get update的结果，
# 那么下次 Dockerfile 运行时就会直接使用之前的缓存，
# 这样 docker 中的 apt 软件源就不是最新的软件列表了，将会带来缓存过期的问题。
# 并且这些缓存将占用不少空间，导致最终生成的image非常庞大，
# 而这些垃圾文件是我们最终的image中无需使用到的东西，我们应当在Docker构建过程中予以删除。
RUN apt clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
