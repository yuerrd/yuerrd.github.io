# K8S安装

1. 安装信息

   - Centos7
   - k8s 1.17.3

2. 修改hostname

   ```shell
   hostnamectl set-hostname node
   ```

3. 添加hosts

   ```shell
   cat <<EOF >> /etc/hosts  
   10.0.24.5   node1
   10.0.24.6   node2
   10.0.24.7   node3
   EOF
   ```

4. 关闭防火墙

   ```shell
   # 查看防火墙状态
   firewall-cmd --state
   # 临时停止防火墙
   systemctl stop firewalld.service
   # 禁止防火墙开机启动
   systemctl disable firewalld.service
   ```

5. 关闭selinux

   ```shell
   # 查看selinux状态
   getenforce
   # 临时关闭selinux
   setenforce 0
   # 永久关闭selinux
   sed -i 's/^ *SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
   ```

6. 关闭swap

   ```shell
   # 临时关闭swap
   swapoff -a
   # 永久关闭swap
   sed -i.bak '/swap/s/^/#/' /etc/fstab
   # 查看
   free -g
   ```

7. 启用br_netfilter内核模块

   ```shell
   modprobe br_netfilter
   echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
   echo '1' > /proc/sys/net/bridge/bridge-nf-call-ip6tables
   echo '1' > /proc/sys/net/ipv4/ip_forward
   # 在 /etc/sysctl.conf添加这两行
   net.bridge.bridge-nf-call-iptables = 1
   net.bridge.bridge-nf-call-ip6tables = 1
   net.ipv4.ip_forward = 1
   # 执行此命令，使其生效
   sysctl -p
   ```

8. 安装docker

   ```shell
   sudo yum install -y yum-utils
   # defalut
   sudo yum-config-manager \
       --add-repo \
       https://download.docker.com/linux/centos/docker-ce.repo
   # aliyun
   sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   
   sudo yum install -y docker-ce docker-ce-cli containerd.io
   
   mkdir /etc/docker
   
   cat > /etc/docker/daemon.json <<EOF
   {
     "registry-mirrors":["https://ustc-edu-cn.mirror.aliyuncs.com","https://mirror.ccs.tencentyun.com"],
     "exec-opts": ["native.cgroupdriver=systemd"],
     "log-driver": "json-file",
     "log-opts": {
       "max-size": "100m"
     },
     "storage-driver": "overlay2",
     "storage-opts": ["overlay2.override_kernel_check=true"]
   }
   EOF
   
   # 开机启动
   sudo systemctl enable docker
   
   # 加载配置
   sudo systemctl daemon-reload
   
   # 重启docker服务
   sudo systemctl restart docker  
   ```

9. 安装k8s

   k8s 默认源

   ```shell
   # set repo
   cat <<EOF > /etc/yum.repos.d/kubernetes.repo
   [kubernetes]
   name=Kubernetes
   baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
   enabled=1
   gpgcheck=1
   repo_gpgcheck=1
   gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
   EOF
   ```

   aliyun 源

   ```shell
   #set aliyun
   cat <<EOF > /etc/yum.repos.d/kubernetes.repo
   [kubernetes]
   name=Kubernetes
   baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
   enabled=1
   gpgcheck=1
   repo_gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
   EOF
   # or
   cat <<EOF > /etc/yum.repos.d/kubernetes.repo
   [kubernetes] 
   name=Kubernetes 
   baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64 
   enabled=1 
   gpgcheck=0 
   repo_gpgcheck=0 
   EOF
   ```

   ```shell
   yum install -y kubelet-1.17.3 kubeadm-1.17.3 kubectl-1.17.3 --disableexcludes=kubernetes
   
   # 启动 kubernetes
   sudo systemctl start kubelet
   
   # 开机启动
   systemctl enable kubelet
   ```

10. 安装

    ```shell
    
    kubeadm init \
    --apiserver-advertise-address=10.0.24.5 \
    --kubernetes-version v1.17.3 \
    --pod-network-cidr=10.244.0.0/16  \
    --image-repository registry.aliyuncs.com/google_containers \
    --ignore-preflight-errors=Swap
    
    kubeadm join 10.0.24.5:6443 --token wn73pc.x0rrxf3ykrpm00gr \
        --discovery-token-ca-cert-hash sha256:4c9369e8724e0b53218c777d394ac3dc59746b40d75252599f8e83470d3a2f36
    
    # 当token失效是，可以用下面命令重新生成新token
    kubeadm token create --print-join-command
    ```

    ```shell
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    
    kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
    
    ```

11. k8s 重置

    ```shell
    kubeadm reset
    rm -fr  $HOME/.kube/config
    ```

12. helm install

    ```shell
    curl -O  https://get.helm.sh/helm-v3.8.2-linux-amd64.tar.gz
    tar -zxvf helm-v3.8.2-linux-amd64.tar.gz
    mv linux-amd64/helm /usr/local/bin/helm
    ```

    [Helm安装]: https://helm.sh/zh/docs/intro/install/

    ```shell
    # add repo
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm repo add stable http://mirror.azure.cn/kubernetes/charts/
    helm repo add incubator https://charts.helm.sh/incubator
    ```

13. 

