### WSL2配置

#### 安装docker

```sh
# install docker
curl -fsSL get.docker.com -o get-docker.sh
sh get-docker.sh

if [ ! $(getent group docker) ];
then
    sudo groupadd docker;
else
    echo "docker user group already exists"
fi

sudo gpasswd -a $USER docker
sudo service docker restart

rm -rf get-docker.sh#
```

#### 安装zsh

```shell
sudo apt install zsh
```

[ohmyzsh](https://github.com/ohmyzsh/ohmyzsh) [zsh-autosuggestions插件](https://github.com/zsh-users/zsh-autosuggestions)

```shell
vim ~/.zshrc
```

添加自动提示插件

```properties
plugins=(
        git
        zsh-autosuggestions
        )
```

#### 设置网络代理

```shell
vim ~/.zshr
```

```properties
export hostip=$(cat /etc/resolv.conf |grep -oP '(?<=nameserver\ ).*')
alias setss='export https_proxy="http://${hostip}:10811";export http_proxy="http://${hostip}:10811";export all_proxy="socks5://${hostip}:10810";'
alias unsetss='unset all_proxy'
```

#### Python环境

```shell
sudo apt install python3-pip
sduo apt install python3-venv
python3 -m venv env
source env/bin/activate
```

