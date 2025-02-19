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
