# 🚀 Linite - Automated Linux Server Deployment

> **Sistema automatizado de preparação e configuração de servidores Linux para ambientes corporativos e domésticos.**

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Platform](https://img.shields.io/badge/Platform-Linux-green.svg)](https://www.linux.org/)
[![Architecture](https://img.shields.io/badge/Architecture-x86__64%20%7C%20ARM64-orange.svg)](https://github.com/urbancompasspony/linite)

## 📋 Sobre o Projeto

O **Linite** é uma solução completa para automação de deploy e configuração de servidores Linux, projetado para simplificar a instalação e configuração de ambientes corporativos com foco em virtualização, containerização e serviços de rede.

## ✨ Características Principais

- **🔧 Configuração Automatizada** - Setup completo do sistema operacional
- **🐳 Containerização** - Suporte nativo ao Docker e orquestração
- **🖥️ Virtualização** - KVM/QEMU com libvirt pré-configurado
- **🌐 Networking** - Configuração avançada de rede com Netplan
- **📊 Monitoramento** - Ferramentas de sistema e performance
- **🔒 Segurança** - Configurações hardening e firewall
- **📱 Multi-Arquitetura** - Suporte x86_64, ARM64 e Android
- **🎯 Modelos Específicos** - Appliances e desktops corporativos

## 🏗️ Modelos Disponíveis

### 📟 **Model-1 (Appliance/Server)**
```bash
curl -L m.linuxuniverse.com.br | bash
```
**Características:**
- ✅ KVM/QEMU + Libvirt
- ✅ Docker + Network tools
- ✅ pfSense VM automática
- ✅ Netplan configurado
- ✅ Samba AD modelo
- ✅ Backup automático
- ✅ MOTD personalizado

**Hardware Suportado:**
- Intel Celeron J4125 (Appliance oficial)
- Sistemas genéricos x86_64

### 🦾 **Model-ARM (Raspberry Pi/ARM)**
```bash
curl -L arm.linuxuniverse.com.br | bash
```
**Características:**
- ✅ Docker otimizado para ARM
- ✅ Network tools completo
- ✅ Configuração WiFi/Ethernet
- ✅ GPIO e hardware específico
- ✅ Samba e OpenVPN

### 🖥️ **Model-Desk (Desktop Corporativo)**
```bash
wget -O- desk.linuxuniverse.com.br | bash
```
**Características:**
- ✅ Ambiente desktop completo
- ✅ Navegadores (Chrome, Firefox, Edge)
- ✅ OnlyOffice + LibreOffice
- ✅ Token A3 e certificados
- ✅ Active Directory integration
- ✅ Flatpak e Wine
- ✅ Ferramentas de produtividade

### 📱 **Model-Android (Linux Deploy)**
```bash
# Para Android com Linux Deploy
wget -O- https://raw.githubusercontent.com/urbancompasspony/linite/main/model-linux-deploy-android-run | bash
```

## 🚀 Instalação Rápida

### Detecção Automática de Hardware
```bash
# O script detecta automaticamente o hardware e aplica o modelo correto
curl -L m.linuxuniverse.com.br | bash
```

### Instalação Manual por Modelo
```bash
# Appliance/Server (x86_64)
curl -L m.linuxuniverse.com.br | bash

# ARM/Raspberry Pi
curl -L arm.linuxuniverse.com.br | bash

# Desktop Corporativo
wget -O- desk.linuxuniverse.com.br | bash
```

## 📦 Pacotes Instalados

### 🔧 **Ferramentas de Sistema**
```
- qemu-system, qemu-utils, qemu-kvm
- libvirt-clients, libvirt-daemon-system
- docker.io, bridge-utils
- net-tools, netdiscover, nmap
- iotop, btop, inxi, tree
```

### 🌐 **Networking**
```
- speedtest-cli, whois, traceroute
- openvpn, iperf, arp-scan
- samba, samba-dsdb-modules
- network-manager, dnsmasq
```

### 💾 **Storage & Backup**
```
- rsnapshot, rclone, cifs-utils
- btrfs-progs, guestfs-tools
- zip, unzip, p7zip-full, unrar
- smartmontools, gparted
```

### 🎨 **Desktop (Model-Desk apenas)**
```
- firefox, google-chrome, microsoft-edge
- libreoffice, onlyoffice-desktopeditors
- vlc, gparted, cpu-x
- flatpak, wine-stable
```

## ⚙️ Configurações Aplicadas

### 🐧 **Sistema Base**
- **Timezone**: `Etc/GMT+3` (Brasília)
- **Swap**: 1GB automático se não existir
- **Journal**: Limitado a 5GB, persistente
- **SYSCTL**: Otimizações de performance
- **MOTD**: Personalizado com informações do sistema

### 🌐 **Rede (Netplan)**
```yaml
# Appliance (Model-1)
network:
  ethernets:
    eno1: {dhcp4: true}        # WAN
    enp1s0: {dhcp4: true}      # LAN1  
    enp2s0: {dhcp4: true}      # LAN2
    enp4s0:                    # Management
      dhcp4: false
      addresses: [172.25.0.3/24]
```

### 🔒 **Segurança**
- Snap desabilitado e bloqueado
- Serviços desnecessários removidos
- Firewall configurado
- SSH habilitado e configurado

### 📊 **Monitoramento**
- Ferramentas de performance (btop, iotop)
- Sensors de hardware (lm-sensors)
- Disk usage tools (duf, tree)
- Network monitoring (netdiscover, nmap)

## 🐳 Virtualização e Containers

### KVM/QEMU (Model-1)
```bash
# pfSense VM criada automaticamente
- Name: pfSense
- Memory: 2GB
- vCPUs: 2
- Networks: 3 interfaces bridged
- Autostart: Enabled
```

### Docker
```bash
# Usuário adicionado ao grupo docker
sudo usermod -aG docker administrador

# Containers prontos para uso
docker run -d --name container_name image_name
```

## 📁 Estrutura de Arquivos

```
/srv/
├── system.yaml              # Configurações do sistema (YAML)
├── scripts/
│   ├── backupcont           # Script de backup containers
│   ├── rsnapshot            # Configuração rsnapshot
│   └── config/
│       └── SMB-AD-Model.conf # Modelo Samba AD
└── containers/              # Dados de containers

/var/lib/libvirt/images/     # Imagens de VMs
/etc/netplan/                # Configuração de rede
/etc/update-motd.d/          # MOTD personalizado
```

## 🔧 Configuração Pós-Instalação

### Verificar Status
```bash
# Verificar serviços
sudo systemctl status libvirtd docker

# Verificar VMs
sudo virsh list --all

# Verificar rede
ip addr show
sudo netplan status
```

### Configurar Samba AD
```bash
# Modelo disponível em:
sudo cat /srv/scripts/config/SMB-AD-Model.conf

# Configurar domínio
sudo samba-tool domain provision --use-rfc2307 --interactive
```

### Backup Automático
```bash
# Rsnapshot configurado automaticamente
sudo crontab -l

# Executar backup manual
sudo /usr/bin/rsnapshot -c /srv/scripts/rsnapshot alpha
```

## 🛠️ Customização

### Modificar Configurações
```bash
# Editar configurações do sistema
sudo nano /srv/system.yaml

# Aplicar mudanças de rede
sudo netplan apply

# Recarregar serviços
sudo systemctl daemon-reload
```

### Adicionar Pacotes
```bash
# Instalar pacotes adicionais
sudo apt install package_name

# Para ARM, usar pacotes específicos
sudo apt install package_name_arm64
```

## 📊 YAML de Configuração

O sistema utiliza um arquivo YAML central para configurações:

```yaml
# /srv/system.yaml
Informacoes:
  IP_LAN_Install: "192.168.1.100"
  Data_Instalacao: "07/07/2025 - 14:30"
  Data_Ultima_Reinstalacao: "Nunca foi reinstalado"
  Serial_Windows: "XXXXX-XXXXX-XXXXX"
  Beep: "Ativo"

Hardware:
  Tipo: "Desktop Computer"
  Placa: "Intel Corp. Board"

rsnapshots:
  - "#00 7,10,13,16,19,22 * * 1-5 /usr/bin/rsnapshot -c /srv/scripts/rsnapshot alpha"
  - "#00 23 * * 1-5 /usr/bin/rsnapshot -c /srv/scripts/rsnapshot beta"
  - "#00 7 * * 6 /usr/bin/rsnapshot -c /srv/scripts/rsnapshot gamma"
```

## 🔍 Troubleshooting

### Problemas Comuns

#### Script não executa
```bash
# Verificar permissões
chmod +x script_name

# Executar com bash explícito
bash script_name

# Verificar conectividade
ping -c 3 raw.githubusercontent.com
```

#### Rede não funciona
```bash
# Verificar interfaces
ip link show

# Reconfigurar netplan
sudo netplan --debug apply

# Verificar DNS
nslookup google.com
```

#### Docker não funciona
```bash
# Verificar status
sudo systemctl status docker

# Reiniciar serviço
sudo systemctl restart docker

# Verificar grupos do usuário
groups $USER
```

## 🚨 Requisitos

### Sistemas Suportados
- **Ubuntu 22.04 LTS** (Recomendado)
- **Ubuntu 24.04 LTS**
- **Debian 11/12**
- **Linux Mint 21/22**

### Hardware Mínimo
- **RAM**: 4GB (8GB recomendado)
- **Storage**: 20GB (50GB recomendado)
- **CPU**: x86_64 ou ARM64
- **Network**: Interface ethernet

### Especificações do Appliance
- **CPU**: Intel Celeron J4125
- **Interfaces**: 3x Ethernet (WAN + 2x LAN)
- **RAM**: 8GB
- **Storage**: 128GB SSD

### Estrutura de Desenvolvimento
```bash
linite/
├── model-1                  # Script modelo appliance
├── model-1-run             # Implementação modelo 1
├── model-arm               # Script modelo ARM
├── model-arm-run           # Implementação ARM
├── model-desk              # Script modelo desktop
├── model-desk-run          # Implementação desktop
└── model-linux-deploy-*    # Modelos especiais
```

<div align="center">

**🚀 Developed with ❤️ by SuitIT® for the Linux community**

[⭐ Star this repo](https://github.com/urbancompasspony/linite) | [🐛 Report Bug](https://github.com/urbancompasspony/linite/issues) | [💡 Request Feature](https://github.com/urbancompasspony/linite/issues)

</div>
