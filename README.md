# Model-1 Server Setup Script v3.3

## 📖 Sobre

O **Model-1** é um script bash automatizado desenvolvido pela SuitIT® que transforma um Ubuntu Server básico em um servidor corporativo completo e otimizado. O script detecta automaticamente o tipo de hardware (x86_64, ARM64, ARM32) e aplica configurações específicas para cada arquitetura.

## 🚀 Instalação Rápida

```bash
# Execução direta, qualquer comando abaixo fará o mesmo trabalho:
curl -L m.linuxuniverse.com.br | bash -i
curl -sSL m.linuxuniverse.com.br | bash
wget -O- m.linuxuniverse.com.br | bash

# Ou download e execução local
wget https://raw.githubusercontent.com/urbancompasspony/docker/main/model-1-run
chmod +x model-1-run
./model-1-run
```

## 🎯 O Que o Script Faz

### 🔍 Detecção Automática de Hardware
- **Celeron J4125**: Configuração completa para appliances corporativos
- **ARM64 (Raspberry Pi 5)**: Otimizações para SBC ARM de 64 bits
- **ARM32**: Configuração básica para dispositivos ARM de 32 bits
- **x86_64 Generic**: Configuração padrão para servidores Intel/AMD

### 📦 Pacotes Instalados

#### Pacotes Base (Todos os Sistemas)
- **Virtualização**: `qemu-system`, `libvirt`, `virt-manager`, `ovmf`
- **Containerização**: `docker.io` com configurações otimizadas
- **Rede**: `net-tools`, `netdiscover`, `nmap`, `iperf`, `traceroute`
- **Monitoramento**: `btop`, `iotop`, `lm-sensors`, `inxi`, `smartmontools`
- **Backup/Sync**: `rsnapshot`, `rclone`, `cifs-utils`
- **Utilitários**: `dialog`, `tree`, `zip/unzip`, `p7zip`, `duf`
- **Desktop**: `xorg`, `openbox`, `terminator`, `gparted`

#### Pacotes Específicos por Arquitetura
- **ARM64**: `docker-buildx`, `mesa-vulkan-drivers`, `vulkan-tools`
- **x86_64**: `gpm`, `qemu-block-supplemental`

## ⚙️ Configurações Aplicadas

### 🔧 Otimizações de Sistema

#### **SWAP Inteligente**
- Detecta SWAP existente e ajusta automaticamente
- Cria SWAP de 1GB se inexistente ou insuficiente
- Configuração otimizada para servidores

#### **Timezone e RTC**
```bash
# Configura timezone para GMT-3 (Brasil)
sudo timedatectl set-timezone Etc/GMT+3
sudo timedatectl set-local-rtc 1
```

#### **SYSCTL Optimizations** 
```bash
# Configurações aplicadas:
vm.swappiness=10          # Reduz uso de SWAP
vm.panic_on_oom=1         # Sistema reinicia em Out of Memory
kernel.panic=5            # Reinicia após 5s em kernel panic
kernel.sysrq=1            # Habilita Magic SysRq
```

### 🗂️ Estrutura de Diretórios

#### **Montagem de Discos**
- `/mnt/disk01` e `/mnt/disk02` - Pontos de montagem padrão
- `/var/lib/libvirt/images` - Imagens de VMs com permissões corretas
- `/srv/scripts/` - Scripts de automação
- `/srv/.bkp/` - Backups de configuração

#### **FSTAB Enhancements**
```bash
# RAM Disk para arquivos temporários
tmpfs /tmp tmpfs defaults 0 0
tmpfs /var/tmp tmpfs defaults 0 0

# Preparação para montagem de discos externos
# Suporte a compartilhamentos SMB/CIFS
```

### 🌐 Configuração de Rede

#### **Netplan Inteligente**
- **Modelo Appliance (Celeron)**: Configuração multi-interface para firewall
- **Outros Modelos**: Configuração DHCP padrão com fallback estático

#### **Interfaces Configuradas (Appliance)**
- `eno1/enp1s0/enp2s0`: DHCP para conectividade
- `enp4s0`: IP estático 172.25.0.3/24 para rede interna

### 🔒 Segurança e Serviços

#### **Remoção de Componentes Desnecessários**
- Remove `snapd`, `cloud-init`, `unattended-upgrades`
- Desabilita `bluetooth` e `blueman`
- Configura bloqueio permanente do snap

#### **Otimização de Serviços**
- Desabilita serviços de espera de rede demorados
- Adiciona usuário aos grupos: `libvirt`, `libvirt-qemu`, `docker`
- Configura journald com limite de 1GB

### 📊 Monitoramento e MOTD

#### **Message of the Day Personalizado**
- Header customizado da SuitIT®
- Informações detalhadas do sistema
- Status dinâmico de serviços

#### **Sistema de Informações YAML**
```yaml
# Estrutura criada em /srv/system.yaml
Informacoes:
  IP_LAN_Install: "192.168.x.x"
  Serial_Windows: "Chave-Serial-OEM"
  Data_Instalacao: "DD/MM/YYYY - HH:MM"
  Beep: "Ativo" # Apenas x86_64

Hardware:
  Tipo: "Modelo detectado"
  Placa: "Motherboard identificada"
```

## 🔄 Automações via Crontab

### **Backups Automatizados**
```bash
# Backup diário de configurações (02:00)
00 02 * * * cp /srv/*.yaml /srv/.bkp/

# Backup de containers Docker (comentado por padrão)
00 02 * * * bash /srv/scripts/backupcont.sh

# Rotação de logs de domínio (23:00)
00 23 * * * bash /srv/scripts/autolog-dominio.sh
```

### **Sincronização e Backup**
```bash
# RSYNC seguro entre discos
0 2 * * * mountpoint -q /mnt/disk01 && mountpoint -q /mnt/disk02 && rsync --delete -aHAXv --numeric-ids --sparse /origem /destino

# RClone para nuvem (OneDrive exemplo)
0 2 * * * mountpoint -q /mnt/disk01 && rclone sync --max-age 24h --no-traverse /mnt/disk01/ OneDrive:Backup_Servidor
```

### **Snapshots com RSnapshot**
- **Alpha**: 6x por dia (7h, 10h, 12h, 14h, 16h, 18h)
- **Beta**: 5x por semana (segunda a sexta, 20h)
- **Gamma**: 1x por semana (domingo, 6h) - retenção de 1 mês

### **Segurança Anti-Ransomware**
```bash
# Detecção de extensões suspeitas (07:00)
00 7 * * * bash /srv/scripts/ransomext.sh

# Limpeza automática de lixeira SMB (06:00)
0 6 * * * bash /srv/scripts/deleterecycle.sh
```

## 🖥️ Virtualização Integrada

### **pfSense Automático (Apenas Appliance Celeron)**
- Download automático da imagem pfSense otimizada
- Criação de VM com 3 interfaces de rede
- Configuração automática para uso como firewall/roteador
- Inicialização automática com o sistema

### **Configuração da VM pfSense**
```bash
# Especificações automáticas:
- RAM: 2GB
- vCPUs: 2 (host passthrough)
- Interfaces: enp1s0, enp2s0, eno1
- MACs fixos para consistência
- Boot automático habilitado
```

## 🎛️ Funcionalidades Especiais

### **Firefox Oficial Mozilla**
- Repositório oficial Mozilla adicionado
- Sempre a versão mais recente disponível
- Configurações de segurança aprimoradas

### **Desktop Environment**
- Xorg + OpenBox configurado
- Ferramentas gráficas: `terminator`, `gparted`, `gnome-disk-utility`
- Suporte remoto via X11

### **Beep de Status (x86_64)**
- Script de beep automático após boot
- Indica funcionamento correto do sistema
- Executa 60 segundos após inicialização

## 🔍 Diagnóstico Integrado

### **Sistema de Diagnóstico**
- Interface web para monitoramento
- Análise automática de performance
- Relatórios de status em tempo real
- Integração com o ecossistema SuitIT®

## 📋 Compatibilidade

### **Sistemas Suportados**
- ✅ Ubuntu Server 24.04 LTS
- ✅ Raspberry Pi OS (ARM64/ARM32)
- ✅ Sistemas derivados do Ubuntu
- Ubuntu Server 22.04 LTS foi depreciado.

### **Hardware Testado**
- 🏢 **Appliances Celeron J4125** (Configuração completa)
- 🥧 **Raspberry Pi 5** (ARM64 otimizado)
- 📱 **Dispositivos ARM32** (Configuração básica)
- 🖥️ **Servidores x86_64** (Configuração padrão)

## ⚠️ Considerações Importantes

### **Antes da Instalação**
- ⚠️ **Não execute como root** - O script detecta e bloqueia execução com sudo
- 💾 **Backup de dados** - O script faz alterações significativas no sistema
- 🔐 **Senha padrão**: "ubuntu" será solicitada durante a instalação
- 🔄 **Reinicialização automática** - O sistema reinicia ao final da instalação

### **Pós-Instalação**
- 📁 **Arquivo YAML** criado em `/srv/system.yaml` com informações do sistema
- 🔧 **Crontabs comentados** - Descomente conforme necessário
- 🌐 **Configuração de rede** - Ajuste o Netplan conforme sua infraestrutura
- 💿 **Discos adicionais** - Configure montagem em `/etc/fstab`

## 🎯 Casos de Uso Ideais

- 🏢 **Servidores corporativos** com necessidade de virtualização
- 🔒 **Appliances de segurança** com pfSense integrado
- ☁️ **Backup servers** com sincronização automática
- 🖥️ **Workstations** híbridas servidor/desktop
- 🏠 **Home labs** e ambientes de desenvolvimento

---

*Script desenvolvido pela SuitIT® - Transformando Ubuntu Server em soluções corporativas completas desde a primeira execução.*
