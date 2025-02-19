# Configuració prèvia a tots els nodes (Master i Workers)
## Desactivar swap (obligatori per Kubernetes)
```bash
sudo sed -i '/swap/s/^\(.*\)$/#\1/g' /etc/fstab  # Desactiva swap permanentment
```
## Configurar sysctl i mòduls del kernel
sudo modprobe overlay
sudo modprobe br_netfilter

# Configurar paràmetres del kernel
```bash
cat <<EOF | sudo tee /etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
sudo sysctl --system
```
## Instal·lar Docker (container runtime)
```bash
sudo apt install -y docker.io
sudo systemctl enable docker --now
```
## Afegir repositori de Kubernetes i instal·lar kubeadm, kubelet i kubectl
```bash
sudo apt install -y apt-transport-https ca-certificates curl
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubeadm=1.28.* kubelet=1.28.* kubectl=1.28.*
sudo apt-mark hold kubeadm kubelet kubectl  # Evita actualitzacions automàtiques
```
