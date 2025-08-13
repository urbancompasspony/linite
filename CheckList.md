# Checklist de Padrão de Servidores - Script Linuxuniverse v3.3

## 🔧 Sistema Base
- [ ] Arquivo `/srv/system.yaml` existe
- [ ] Diretório `/var/lib/libvirt/images` criado com permissões 777
- [ ] Configuração BASH personalizada instalada
- [ ] Timezone `Etc/GMT+3` e RTC local configurados

## 💾 Swap e Memória
- [ ] Swap de pelo menos 1GB ativo (`/swapfile` ou `/swap.img`)
- [ ] Entrada tmpfs no fstab para `/tmp` e `/var/tmp`

## 📦 Pacotes e Limpeza
- [ ] Pacotes indesejados removidos (snapd, bluetooth, cloud-init, etc.)
- [ ] Política nosnap configurada em `/etc/apt/preferences.d/nosnap.pref`
- [ ] Pacotes base instalados (qemu, libvirt, docker, ferramentas de rede/sistema)
- [ ] yq instalado em `/usr/local/bin/yq`
- [ ] Firefox instalado via repositório Mozilla

## 👥 Usuários e Serviços
- [ ] Usuário `administrador` nos grupos: libvirt, libvirt-qemu, docker
- [ ] Serviços wait-online desabilitados e mascarados

## ⚙️ Configurações do Sistema
- [ ] SYSCTL configurado: magic-sysrq, swappiness=10, panic settings
- [ ] MOTD personalizado instalado (00-header, 20-sysinfo, 90-dynamic-motd)
- [ ] Journal limitado: 1G max, 5G free, persistent storage
- [ ] Diretórios `/mnt/disk01` e `/mnt/disk02` criados

## 🌐 Rede (específico por hardware)
### Appliance Celeron J4125:
- [ ] Netplan configurado com IP estático 172.25.0.3/24 na enp4s0
- [ ] pfSense VM criada e configurada para autostart

### Outros sistemas:
- [ ] Netplan com configurações DHCP padrão

## 📁 Dados e Configurações
- [ ] Informações de hardware e IP coletadas no YAML
- [ ] Serial Windows coletado (se existir)
- [ ] Data de instalação registrada
- [ ] Painel de diagnóstico instalado

## 🔊 BEEP (apenas x86_64)
- [ ] Script beep configurado no crontab para boot

## 📅 Crontabs Configurados
- [ ] Backup CDN diário às 02:00
- [ ] Scripts prontos (comentados): rsync, rclone, backup containers, autolog, ransomext, delete trash
- [ ] RSnapshots configurado (alpha/beta/gamma)
- [ ] Permissões lixeira Syncthing
