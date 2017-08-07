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

cp /cni/bin/* /opt/cni/bin/

kubeadm init

kubectl create -f http://192.168.100.6:9999/v1.6.1/weave-daemonset-k8s-1.6.yaml



