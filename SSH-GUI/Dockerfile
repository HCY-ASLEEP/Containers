# 在 ssh -Y 的情况下可以使用GUI
# 使用 Ubuntu 的最新 LTS 版本作为基础镜像
FROM ubuntu:latest

# 设置环境变量 DEBIAN_FRONTEND=noninteractive 以非交互模式运行 apt-get
ENV DEBIAN_FRONTEND=noninteractive

# 更新包索引并安装所需的软件包
RUN apt update && apt upgrade -y \
    && apt install -y dbus-x11 fcitx5 fcitx5-chinese-addons locales mousepad openssh-server vim wget curl fonts-wqy-microhei fonts-wqy-zenhei xfonts-wqy sudo tmux iproute2

# 配置用户名和其密码
RUN USERNAME="test" \
    && NEW_PASSWORD="1" \
    && useradd -m ${USERNAME} \
    && chsh -s /bin/bash ${USERNAME} \
    && echo "${USERNAME} ALL=(ALL:ALL) ALL" >> /etc/sudoers \
    && echo "${USERNAME}:${NEW_PASSWORD}" | sudo chpasswd
    
# 配置 SSH 允许 root 登录
RUN SSHD_CONFIG="/etc/ssh/sshd_config" \
    && sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' "$SSHD_CONFIG"

# 创建启动脚本并定义环境变量
RUN SYSTEM_START_DIR="/opt/system-init/" \
    && SYSTEM_START_SCRIPT=${SYSTEM_START_DIR}"start.sh" \
    && mkdir -p $SYSTEM_START_DIR \
    && echo '#!/bin/bash' > $SYSTEM_START_SCRIPT \
    && echo '/etc/init.d/ssh start >/dev/null 2>&1' >> $SYSTEM_START_SCRIPT \
    && echo '/etc/init.d/dbus start >/dev/null 2>&1' >> $SYSTEM_START_SCRIPT \
    && echo 'nohup fcitx5 restart > /dev/null 2>&1 &' >> $SYSTEM_START_SCRIPT \
    && chmod a+x $SYSTEM_START_SCRIPT \
    && echo "${SYSTEM_START_SCRIPT}" >> /etc/bash.bashrc

# 启动 fcitx5 启动脚本
RUN FCITX5_LAUNCH_DIR="/opt/fcitx5/" \
    && FCITX5_LAUNCH_SCRIPT=${FCITX5_LAUNCH_DIR}"fcitx5-launch.sh" \
    && mkdir -p $FCITX5_LAUNCH_DIR \
    && echo 'nohup fcitx5-configtool >/dev/null 2>&1 &' > $FCITX5_LAUNCH_SCRIPT \
    && chmod a+x $FCITX5_LAUNCH_SCRIPT \
    && echo 'export PATH="$PATH":'$FCITX5_LAUNCH_DIR >> /etc/bash.bashrc
