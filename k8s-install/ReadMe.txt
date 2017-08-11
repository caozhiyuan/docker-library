sudo su

------------------------

apt update

apt install docker.io

images=(
    kube-controller-manager-amd64:v1.6.0
    kube-apiserver-amd64:v1.6.0
    kube-scheduler-amd64:v1.6.0
    kube-proxy-amd64:v1.6.0
    kubernetes-dashboard-amd64:v1.6.0
    k8s-dns-sidecar-amd64:1.14.1
    k8s-dns-kube-dns-amd64:1.14.1
    k8s-dns-dnsmasq-nanny-amd64:1.14.1
    etcd-amd64:3.0.17
    pause-amd64:3.0
)

for imageName in ${images[@]} ; do
    docker pull caozhiyuan/$imageName
    docker tag caozhiyuan/$imageName gcr.io/google_containers/$imageName
    docker rmi caozhiyuan/$imageName
done

wget http://192.168.100.6:9999/v1.6.1/kubernetes-server-linux-amd64.tar.gz

tar xvfz kubernetes-server-linux-amd64.tar.gz

cd kubernetes/server/bin/

cp kubectl /usr/bin/kubectl

chmod -R 777 /usr/bin/kubectl

cp kubelet /usr/bin/kubelet

chmod -R 777 /usr/bin/kubelet

cp kubeadm /usr/bin/kubeadm

chmod -R 777 /usr/bin/kubeadm

wget http://192.168.100.6:9999/v1.6.1/cni.tar.gz

mkdir cni

tar xvfz cni.tar.gz -C cni

mkdir -p /opt/cni/bin

cp cni/bin/* /opt/cni/bin/

wget http://192.168.100.6:9999/v1.6.1/conf.tar

tar xvf conf.tar

mkdir /etc/systemd/system/kubelet.service.d

cp conf/10-kubeadm.conf /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

cp conf/kubelet.service /lib/systemd/system/kubelet.service 

systemctl daemon-reload

systemctl enable kubelet.service

kubeadm init

kubectl create -f https://github.com/weaveworks/weave/releases/download/v2.0.1/weave-daemonset-k8s-1.6.yaml


tee /etc/docker/daemon.json <<-'EOF'
{
    "insecure-registries": ["10.1.62.5:5000"],
    "registry-mirrors": ["https://u5528225.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

for imageName in ${images[@]} ; do
    docker pull 10.1.62.5:5000/$imageName
    docker tag 10.1.62.5:5000/$imageName gcr.io/google_containers/$imageName
    docker rmi 10.1.62.5:5000/$imageName
done

for imageName in ${images[@]} ; do
    docker tag gcr.io/google_containers/$imageName 10.1.62.5:5000/$imageName 
    docker push 10.1.62.5:5000/$imageName
    docker rmi 10.1.62.5:5000/$imageName
done


/etc/resolvconf/resolv.conf.d/tail
nameserver 192.168.139.2

/etc/init.d/resolvconf restart 


images=(
    nginx-ingress-controller:0.9.0-beta.11
    defaultbackend:1.0
	echoserver:1.8
)

for imageName in ${images[@]} ; do
    docker pull caozhiyuan/$imageName
    docker tag caozhiyuan/$imageName gcr.io/google_containers/$imageName
    docker rmi caozhiyuan/$imageName
done


images=(
   hyperkube-amd64:v1.7.3
)