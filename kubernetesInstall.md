# Instal·larem Kubernetes
1. Configuració prèvia als 3 nodes
   1.1. Actualitzar el sistema
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
   1.2. Configurar noms d'host
   Executa en cada servidor el següent, canviant el nom segons correspongui:
   Master:
   ```bash
   sudo hostnamectl set-hostname master01b
   ```
   Worker 1:
   ```bash
   sudo hostnamectl set-hostname worker01b
   ```
   Worker 2:
   ```bash
   sudo hostnamectl set-hostname worker02b
   ```

   Afegir els noms d'host al fitxer /etc/hosts a tots els servidors:
   ```bash
   echo "192.168.0.120 master01b" | sudo tee -a /etc/hosts
   echo "192.168.0.121 worker01b" | sudo tee -a /etc/hosts
   echo "192.168.0.122 worker02b" | sudo tee -a /etc/hosts
   ```
   1.3. Desactivar Swap (si hi ha partició swap)
   ```bash
   sudo swapoff -a
   sudo sed -i '/ swap / s/^/#/' /etc/fstab
   ```

   1.4. Carregar mòduls del kernel necessaris
   ```bash
   cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
   overlay
   br_netfilter
   EOF

   sudo modprobe overlay
   sudo modprobe br_netfilter
   ```

   1.5. Configurar paràmetres del sistema
   ```bash
   cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
   net.bridge.bridge-nf-call-iptables = 1
   net.bridge.bridge-nf-call-ip6tables = 1
   net.ipv4.ip_forward = 1
   EOF

   sudo sysctl --system
   # Hauriem d'obtenir una sortida com la següent:
   worker01b@worker01B:~$ sudo sysctl --system
   * Applying /usr/lib/sysctl.d/50-pid-max.conf ...
   * Applying /usr/lib/sysctl.d/99-protect-links.conf ...
   * Applying /etc/sysctl.d/99-sysctl.conf ...
   * Applying /etc/sysctl.d/k8s.conf ...
   * Applying /etc/sysctl.conf ...
   kernel.pid_max = 4194304
   fs.protected_fifos = 1
   fs.protected_hardlinks = 1
   fs.protected_regular = 2
   fs.protected_symlinks = 1
   net.bridge.bridge-nf-call-iptables = 1
   net.bridge.bridge-nf-call-ip6tables = 1
   net.ipv4.ip_forward = 1
   ```

2. Instal·lar Kubernetes i Docker als 3 nodes
2.1. Instal·lar dependències
```bash
sudo apt install -y apt-transport-https ca-certificates curl
```

2.2. Afegir repositori de Kubernetes
```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo tee /etc/apt/keyrings/kubernetes-apt-keyring.asc

-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.4.5 (GNU/Linux)

mQENBGMHoXcBCADukGOEQyleViOgtkMVa7hKifP6POCTh+98xNW4TfHK/nBJN2sm
u4XaiUmtB9UuGt9jl8VxQg4hOMRf40coIwHsNwtSrc2R9v5Kgpvcv537QVIigVHH
WMNvXeoZkkoDIUljvbCEDWaEhS9R5OMYKd4AaJ+f1c8OELhEcV2dAQLLyjtnEaF/
qmREN+3Y9+5VcRZvQHeyBxCG+hdUGE740ixgnY2gSqZ/J4YeQntQ6pMUEhT6pbaE
10q2HUierj/im0V+ZUdCh46Lk/Rdfa5ZKlqYOiA2iN1coDPIdyqKavcdfPqSraKF
Lan2KLcZcgTxP+0+HfzKefvGEnZa11civbe9ABEBAAG0PmlzdjprdWJlcm5ldGVz
IE9CUyBQcm9qZWN0IDxpc3Y6a3ViZXJuZXRlc0BidWlsZC5vcGVuc3VzZS5vcmc+
iQE+BBMBCAAoBQJnFF34AhsDBQkIK2yBBgsJCAcDAgYVCAIJCgsEFgIDAQIeAQIX
gAAKCRAjRlTamilkNtOACACDK9dQ8CH2Ji9C3Q926nVMUiXdyJK1onCBrQSEBqdR
LJaT6hGx5pzxkQGgUDpS9p7LA0u920HKLwGb7yIAWtyE5TAj2CYprGgpq98sfsGC
+U5T9IrAdya/BaTAkkP6gNhfMjNaK3bOWsvuLRlluKMNch4ify+IwLqc1JLG40bj
2HnKBGYkC3m0VtQfUuPQMImSLta/NwRHJMPo8jfGyManqMMxp35/ecP2rXMfb/l1
WjFDY7h+6nqXay20ljMXkN23W8wFTdvC6lq45wwM5IBnKNR/TjNNYAIizZoHFWz1
c/ecMWWWCB2S7WbY4xI3JSCOD4XIff3ie7pc68/kgPytiQIcBBMBAgAGBQJjB6F3
AAoJEM8Lkoze1k873TQP/0t2F/jltLRQMG7VCLw7+ps5JCW5FIqu/S2i9gSdNA0E
42u+LyxjG3YxmVoVRMsxeu4kErxr8bLcA4p71W/nKeqwF9VLuXKirsBC7z2syFiL
Ndl0ARnC3ENwuMVlSCwJO0MM5NiJuLOqOGYyD1XzSfnCzkXN0JGA/bfPRS5mPfoW
0OHIRZFhqE7ED6wyWpHIKT8rXkESFwszUwW/D7o1HagX7+duLt8WkrohGbxTJ215
YanOKSqyKd+6YGzDNUoGuMNPZJ5wTrThOkTzEFZ4HjmQ16w5xmcUISnCZd4nhsbS
qN/UyV9Vu3lnkautS15E4CcjP1RRzSkT0jka62vPtAzw+PiGryM1F7svuRaEnJD5
GXzj9RCUaR6vtFVvqqo4fvbA99k4XXj+dFAXW0TRZ/g2QMePW9cdWielcr+vHF4Z
2EnsAmdvF7r5e2JCOU3N8OUodebU6ws4VgRVG9gptQgfMR0vciBbNDG2Xuk1WDk1
qtscbfm5FVL36o7dkjA0x+TYCtqZIr4x3mmfAYFUqzxpfyXbSHqUJR2CoWxlyz72
XnJ7UEo/0UbgzGzscxLPDyJHMM5Dn/Ni9FVTVKlALHnFOYYSTluoYACF1DMt7NJ3
oyA0MELL0JQzEinixqxpZ1taOmVR/8pQVrqstqwqsp3RABaeZ80JbigUC29zJUVf
=Eplj

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.asc] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list

deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.asc] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /
```

2.3. Instal·lar Docker
```bash
sudo apt install -y docker.io

sudo systemctl enable --now docker
```

2.4. Instal·lar kubeadm, kubelet i kubectl
```bash
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

3. Configurar el node Master
**⚠️Executa només en el servidor master01b (192.168.0.120)⚠️**

3.1. Inicialitzar el cluster Kubernetes
```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address=192.168.0.120
```
⚠️Apunta la comanda que apareixerà per unir els workers al cluster⚠️

3.2. Configurar kubectl per l'usuari actual
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

3.3. Instal·lar la xarxa de pod (Calico)
```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```
