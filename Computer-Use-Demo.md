# Computer Use Demo

# Ubuntu 22.04 服务器配置指南

## 1. 创建新的服务器实例
创建一台 Ubuntu Server 22.04 LTS (HVM), SSD Volume Type 实例

## 2. 安装图形界面和 Xrdp 远程桌面
首先，使用 SSH 连接到您的服务器，使用以下命令依次可以按照步骤：

```bash
# 更新系统
sudo apt update

# 安装桌面功能
export DEBIAN_FRONTEND=noninteractive
sudo -E apt-get update
sudo -E apt-get install -y ubuntu-desktop

# 安装 xfce 和 xfce-goodies 组件
sudo apt install xfce4 xfce4-goodies -y

# 安装 xrdp
sudo apt install xrdp -y

# 检查 xrdp 的状态
sudo systemctl status xrdp

# 如果 xrdp 的状态不是 running，可以手动启动服务
sudo systemctl start xrdp

# 修改 ubuntu 用户密码为 ubuntu
sudo passwd ubuntu 

# 在 /home/ubuntu 下创建一个 .xsession 文件，设置 xfce4-session 作为登录时使用的会话管理器
echo "xfce4-session" | tee .xsession

# 重启 xrdp 服务
sudo systemctl restart xrdp
```

配置安全组，允许在端口 3389 上的公网 IP 进行远程连接。
在 Windows 上测试 RDP 连接，在 cmd 中输入：`mstsc`

## 3. 安装 Docker
```bash
# 添加 Docker 官方 GPG 密钥
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# 添加 Docker 仓库配置
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 更新包索引
sudo apt-get update

# 安装 Docker
sudo apt install docker-ce docker-ce-cli containerd.io -y

# 验证安装
docker --version
```

## 4. 安装 AWS CLI
```bash
# 下载官方 AWS CLI v2 安装包
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# 解压安装包
unzip awscliv2.zip

# 安装 AWS CLI v2
sudo ./aws/install

# 验证安装
aws --version
```

## 5. 配置 AK 和 SK 信息
```bash
export AWS_PROFILE=demo
aws configure --profile demo
```

## 6. 安装浏览器
```bash
# 添加 Google 的 GPG 密钥和仓库
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
echo "deb [arch=amd64] https://dl.google.com/linux/chrome/deb/ stable main" | sudo tee /etc/apt/sources.list.d/google-chrome.list

# 更新包索引并安装 Chrome
sudo apt update
sudo apt install google-chrome-stable
```

## 7. 运行 AWS ComputerUse 容器
```bash
sudo docker run \
    -e API_PROVIDER=bedrock \
    -e AWS_PROFILE=$AWS_PROFILE \
    -e AWS_REGION=us-west-2 \
    -v $HOME/.aws/credentials:/home/computeruse/.aws/credentials \
    -v $HOME/.anthropic:/home/computeruse/.anthropic \
    -p 5900:5900 \
    -p 8501:8501 \
    -p 6080:6080 \
    -p 8080:8080 \
    -it ghcr.io/anthropics/anthropic-quickstarts:computer-use-demo-latest
```

## 8. 示例参考
按照以下步骤进行操作：
1. 在浏览器中输入网址：https://jimeng.jianying.com/ai-tool/home
2. 如果页面弹出任何窗口，先进行关闭，然后继续操作
3. 如果页面显示登录，则需要完成登录；如果已经登录，直接进入下一步操作
4. 在页面中，点击"图片生成"按钮，进入图片生成功能页面
5. 在图片生成区域输入："黄昏海滩"，然后点击生成
6. 等待所有图片完全生成（注意：每次图片下载地址不同）
7. 将生成的图片保存到本地 C 盘根目录下

## 9. 参考链接
https://blog.csdn.net/rralucard123/article/details/143190747

## 10. Ubuntu 22.04 中文输入法配置
```bash
# 安装桌面相关功能
export DEBIAN_FRONTEND=noninteractive
sudo -E apt-get update
sudo -E apt-get install -y ubuntu-desktop
```

配置完成后，点击右上角输入法的 configure，添加双拼输入法图标。
- 切换英文方法: control + space
- 切换中文方法: space
