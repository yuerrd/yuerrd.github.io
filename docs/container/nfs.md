#NFS安装

1. 服务端安装

   ```shell
   sudo yum -y install nfs-utils rpcbind
   
   sudo systemctl start rpcbind nfs 
   sudo systemctl enable rpcbind nfs 
   
   sudo chown -R nfsnobody.nfsnobody /opt/nfsdata
   ```

2. 服务配置

   ```shell
   cat <<EOF> /etc/exports
   /opt/nfsdata *(rw,no_root_squash,sync)
   EOF
   
   sudo systemctl reload nfs
   # 查看nfs是否注册到rpc
   sudo rpcinfo -p
   # 查看可挂载路径
   sudo showmount -e 10.0.24.5
   
   sudo mount -t nfs 10.0.24.5:/opt/nfsdata
   ```

3. 基于nfs创建StorageClass

   k8s: v1.173

   - nfs-client.yaml

     ```shell
     cat <<EOF > nfs-client.yaml
     kind: Deployment
     apiVersion: apps/v1
     metadata:
       name: nfs-client-provisioner
     spec:
       selector:
         matchLabels:
           app: nfs-client-provisioner
       replicas: 1
       strategy:
         type: Recreate
       template:
         metadata:
           labels:
             app: nfs-client-provisioner
         spec:
           serviceAccountName: nfs-client-provisioner
           containers:
             - name: nfs-client-provisioner
               # k8s 1.2.0 需要换镜像
               # easzlab/nfs-subdir-external-provisioner:v4.0.2
               image: quay.io/external_storage/nfs-client-provisioner:latest
               volumeMounts:
                 - name: nfs-client-root
                   mountPath: /persistentvolumes
               env:
                 - name: PROVISIONER_NAME
                   value: fuseim.pri/ifs
                 - name: NFS_SERVER
                   value: 10.0.24.5
                 - name: NFS_PATH
                   value: /opt/nfsdata
           volumes:
             - name: nfs-client-root
               nfs:
                 server: 10.0.24.5
                 path: /opt/nfsdata
     EOF
     ```
     
   - nfs-client-sa.yaml

     ```shell
     cat <<EOF > nfs-client-sa.yaml
     apiVersion: v1
     kind: ServiceAccount
     metadata:
       name: nfs-client-provisioner
     
     ---
     kind: ClusterRole
     apiVersion: rbac.authorization.k8s.io/v1
     metadata:
       name: nfs-client-provisioner-runner
     rules:
       - apiGroups: [""]
         resources: ["persistentvolumes"]
         verbs: ["get", "list", "watch", "create", "delete"]
       - apiGroups: [""]
         resources: ["persistentvolumeclaims"]
         verbs: ["get", "list", "watch", "update"]
       - apiGroups: ["storage.k8s.io"]
         resources: ["storageclasses"]
         verbs: ["get", "list", "watch"]
       - apiGroups: [""]
         resources: ["events"]
         verbs: ["list", "watch", "create", "update", "patch"]
       - apiGroups: [""]
         resources: ["endpoints"]
         verbs: ["create", "delete", "get", "list", "watch", "patch", "update"]
     
     ---
     kind: ClusterRoleBinding
     apiVersion: rbac.authorization.k8s.io/v1
     metadata:
       name: run-nfs-client-provisioner
     subjects:
       - kind: ServiceAccount
         name: nfs-client-provisioner
         namespace: default
     roleRef:
       kind: ClusterRole
       name: nfs-client-provisioner-runner
       apiGroup: rbac.authorization.k8s.io
     EOF
     ```
     
   - nfs-client-storageclass.yaml

     ```shell
     cat <<EOF > nfs-client-storageclass.yaml
     apiVersion: storage.k8s.io/v1
     kind: StorageClass
     metadata:
       annotations:
         storageclass.kubernetes.io/is-default-class: "true"
       name: nfs
     provisioner: fuseim.pri/ifs # or choose another name, must match deployment's env PROVISIONER_NAME'
     EOF
     ```
     
   - 启动

     ```shell
     kubectl apply -f .
     kubectl get pod,sc
     ```

   

