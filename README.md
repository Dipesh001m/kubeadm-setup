
# 🚀 Kubernetes Cluster Setup using Kubeadm (v1.20.0)

This guide helps you set up a Kubernetes cluster using `kubeadm` on **Ubuntu-based systems**. It includes steps for both **Master** and **Worker** nodes.

---

## 🧰 Prerequisites (Run on Both Master & Worker Nodes)

```bash
sudo su
apt update -y
apt install docker.io -y

systemctl start docker
systemctl enable docker

curl -fsSL "https://packages.cloud.google.com/apt/doc/apt-key.gpg" | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes-archive-keyring.gpg

echo 'deb https://packages.cloud.google.com/apt kubernetes-xenial main' > /etc/apt/sources.list.d/kubernetes.list

apt update -y
apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y
```

---

## 🧩 Master Node Setup

```bash
sudo su
kubeadm init
```

### 🔧 Configure kubectl Access

#### If you are a regular user:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### If you are root:

```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
```

---

### 🌐 Apply Network Plugin (Weave Net)

```bash
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

---

### 🔑 Generate Join Command

```bash
kubeadm token create --print-join-command
```

Copy the command and use it on each Worker Node.

---

## 🧮 Worker Node Setup

```bash
sudo su
kubeadm reset
```

### Paste the Join Command from Master Node

Append `--v=5` to the join command for verbose output:

```bash
kubeadm join <MASTER_IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash <HASH> --v=5
```

---

## ✅ Verify Nodes from Master

```bash
kubectl get nodes
```

---

## 🏷️ Optional: Label Worker Nodes

```bash
kubectl label node <NODE_NAME> node-role.kubernetes.io/worker=worker
kubectl label node <NODE_NAME> kubernetes.io/role=worker
```

---

## 📂 Optional: Set Default Namespace

```bash
kubectl config set-context $(kubectl config current-context) --namespace=dev
```

---

## 📌 Notes

- Replace `<MASTER_IP>`, `<TOKEN>`, and `<HASH>` with actual values from the join command.
- You can reset a node using `kubeadm reset` if needed.
