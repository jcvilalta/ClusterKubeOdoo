# Configuració prèvia a tots els nodes (Master i Workers)
## Desactivar swap (obligatori per Kubernetes)
```bash
sudo sed -i '/swap/s/^\(.*\)$/#\1/g' /etc/fstab  # Desactiva swap permanentment
```
