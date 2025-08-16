#!/usr/bin/env bash

# ======================================================================================================================================================== #
# Script de Verificação Pós-Linite v1.0
# Verifica se todas as configurações do Linuxuniverse foram aplicadas corretamente
# ======================================================================================================================================================== #

export LANG=C
export LC_ALL=C

# Cores para output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Contadores
PASSED=0
FAILED=0
WARNINGS=0

# Função para imprimir status
print_status() {
    local status=$1
    local message=$2
    case $status in
        "PASS")
            echo -e "${GREEN}✅ PASS${NC} - $message"
            ((PASSED++))
            ;;
        "FAIL")
            echo -e "${RED}❌ FAIL${NC} - $message"
            ((FAILED++))
            ;;
        "WARN")
            echo -e "${YELLOW}⚠️  WARN${NC} - $message"
            ((WARNINGS++))
            ;;
        "INFO")
            echo -e "${BLUE}ℹ️  INFO${NC} - $message"
            ;;
    esac
}

# Função para executar comando e capturar saída
run_check() {
    local cmd="$1"
    local expected="$2"
    local description="$3"
    
    if eval "$cmd" &>/dev/null; then
        print_status "PASS" "$description"
        return 0
    else
        print_status "FAIL" "$description"
        return 1
    fi
}

# Header
clear
echo -e "${BLUE}================================================================${NC}"
echo -e "${BLUE}    VERIFICADOR PÓS-LINITE - Linuxuniverse v3.3${NC}"
echo -e "${BLUE}================================================================${NC}"
echo ""

# ======================================================================================================================================================== #
# VERIFICAÇÃO INICIAL RÁPIDA
# ======================================================================================================================================================== #

echo -e "${YELLOW}🔍 VERIFICAÇÃO INICIAL RÁPIDA${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

print_status "INFO" "Sistema: $(whoami)@$(hostname) - $(date)"

# Verificar se é um sistema Linite
if [ -f "/srv/system.yaml" ]; then
    print_status "PASS" "Arquivo /srv/system.yaml existe - Sistema foi configurado pelo Linite"
else
    print_status "FAIL" "Arquivo /srv/system.yaml NÃO existe - Sistema NÃO foi configurado pelo Linite"
    echo -e "${RED}❌ SISTEMA NÃO CONFIGURADO PELO LINITE - Abortando verificação${NC}"
    exit 1
fi

# Verificar serviços principais
systemctl is-active docker &>/dev/null && print_status "PASS" "Docker ativo" || print_status "FAIL" "Docker inativo"
systemctl is-active libvirtd &>/dev/null && print_status "PASS" "Libvirtd ativo" || print_status "FAIL" "Libvirtd inativo"

echo ""

# ======================================================================================================================================================== #
# VERIFICAÇÃO DE DIRETÓRIOS E ARQUIVOS (/srv)
# ======================================================================================================================================================== #

echo -e "${YELLOW}📁 VERIFICAÇÃO DE DIRETÓRIOS E ARQUIVOS (/srv)${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Estrutura principal
[ -d "/srv/scripts" ] && print_status "PASS" "Diretório /srv/scripts existe" || print_status "FAIL" "Diretório /srv/scripts não existe"
[ -d "/srv/.bkp" ] && print_status "PASS" "Diretório /srv/.bkp existe" || print_status "FAIL" "Diretório /srv/.bkp não existe"
[ -d "/var/lib/libvirt/images" ] && print_status "PASS" "Diretório /var/lib/libvirt/images existe" || print_status "FAIL" "Diretório /var/lib/libvirt/images não existe"

# Verificar scripts em /srv/scripts/
scripts_count=$(find /srv/scripts/ -name "*.sh" 2>/dev/null | wc -l)
if [ "$scripts_count" -ge 5 ]; then
    print_status "PASS" "Scripts em /srv/scripts: $scripts_count encontrados"
else
    print_status "WARN" "Scripts em /srv/scripts: apenas $scripts_count encontrados (esperado ≥5)"
fi

echo ""

# ======================================================================================================================================================== #
# CONFIGURAÇÕES DO SISTEMA (/etc)
# ======================================================================================================================================================== #

echo -e "${YELLOW}⚙️ CONFIGURAÇÕES DO SISTEMA (/etc)${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Configurações sysctl
[ -f "/etc/sysctl.d/10-magic-sysrq.conf" ] && print_status "PASS" "SYSCTL magic-sysrq configurado" || print_status "FAIL" "SYSCTL magic-sysrq não configurado"
[ -f "/etc/sysctl.d/99-sysctl.conf" ] && print_status "PASS" "SYSCTL principal configurado" || print_status "FAIL" "SYSCTL principal não configurado"

# Journal
if grep -q "SystemMaxUse=1G" /etc/systemd/journald.conf 2>/dev/null; then
    print_status "PASS" "Journal configurado (1G limit)"
else
    print_status "FAIL" "Journal não configurado corretamente"
fi

# Políticas APT
[ -f "/etc/apt/preferences.d/nosnap.pref" ] && print_status "PASS" "Política nosnap configurada" || print_status "FAIL" "Política nosnap não configurada"
[ -f "/etc/apt/preferences.d/mozilla" ] && print_status "PASS" "Repositório Mozilla configurado" || print_status "FAIL" "Repositório Mozilla não configurado"

# MOTD
motd_files=$(ls /etc/update-motd.d/ 2>/dev/null | wc -l)
if [ "$motd_files" -ge 3 ]; then
    print_status "PASS" "MOTD personalizado ($motd_files arquivos)"
else
    print_status "FAIL" "MOTD não personalizado ($motd_files arquivos)"
fi

echo ""

# ======================================================================================================================================================== #
# CONFIGURAÇÃO DE REDE
# ======================================================================================================================================================== #

echo -e "${YELLOW}🌐 CONFIGURAÇÃO DE REDE${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Netplan
[ -f "/etc/netplan/00-installer-config.yaml" ] && print_status "PASS" "Netplan configurado" || print_status "FAIL" "Netplan não configurado"

# Conectividade
if ping -c 2 1.1.1.1 &>/dev/null; then
    print_status "PASS" "Conectividade com internet"
else
    print_status "FAIL" "Sem conectividade com internet"
fi

# Verificar se é appliance Celeron
if grep -q "Celeron(R) J4125" /proc/cpuinfo 2>/dev/null; then
    print_status "INFO" "Sistema Appliance Celeron J4125 detectado"
    # Verificar IP estático para appliance
    if ip a | grep -q "172.25.0.3" 2>/dev/null; then
        print_status "PASS" "IP estático 172.25.0.3 configurado (Appliance)"
    else
        print_status "WARN" "IP estático 172.25.0.3 não encontrado (esperado para Appliance)"
    fi
else
    print_status "INFO" "Sistema padrão detectado (não-appliance)"
fi

echo ""

# ======================================================================================================================================================== #
# VERIFICAÇÃO DE SWAP E MONTAGENS
# ======================================================================================================================================================== #

echo -e "${YELLOW}💾 VERIFICAÇÃO DE SWAP E MONTAGENS${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Swap
swap_size=$(free -m | awk '/Swap:/ {print $2}')
if [ "$swap_size" -ge 1000 ]; then
    print_status "PASS" "Swap ativo: ${swap_size}MB"
else
    print_status "FAIL" "Swap insuficiente: ${swap_size}MB (esperado ≥1000MB)"
fi

# Tmpfs
if mount | grep -q "tmpfs.*tmp" 2>/dev/null; then
    print_status "PASS" "Tmpfs configurado para /tmp"
else
    print_status "FAIL" "Tmpfs não configurado para /tmp"
fi

# Diretórios de montagem
[ -d "/mnt/disk01" ] && print_status "PASS" "Diretório /mnt/disk01 existe" || print_status "FAIL" "Diretório /mnt/disk01 não existe"
[ -d "/mnt/disk02" ] && print_status "PASS" "Diretório /mnt/disk02 existe" || print_status "FAIL" "Diretório /mnt/disk02 não existe"

echo ""

# ======================================================================================================================================================== #
# USUÁRIOS E GRUPOS
# ======================================================================================================================================================== #

echo -e "${YELLOW}👥 USUÁRIOS E GRUPOS${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Verificar grupos do administrador
if id administrador &>/dev/null; then
    groups_admin=$(groups administrador 2>/dev/null | tr ' ' '\n' | grep -E "(libvirt|docker)" | wc -l)
    if [ "$groups_admin" -ge 2 ]; then
        print_status "PASS" "Usuário administrador nos grupos corretos (libvirt, docker)"
    else
        print_status "FAIL" "Usuário administrador não está em todos os grupos necessários"
    fi
else
    print_status "WARN" "Usuário administrador não existe"
fi

echo ""

# ======================================================================================================================================================== #
# VERIFICAÇÃO DE SERVIÇOS
# ======================================================================================================================================================== #

echo -e "${YELLOW}🔄 VERIFICAÇÃO DE SERVIÇOS${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Serviços habilitados
systemctl is-enabled docker &>/dev/null && print_status "PASS" "Docker habilitado" || print_status "FAIL" "Docker não habilitado"
systemctl is-enabled libvirtd &>/dev/null && print_status "PASS" "Libvirtd habilitado" || print_status "FAIL" "Libvirtd não habilitado"

# Serviços mascarados (wait-online)
systemctl is-masked NetworkManager-wait-online &>/dev/null && print_status "PASS" "NetworkManager-wait-online mascarado" || print_status "WARN" "NetworkManager-wait-online não mascarado"
systemctl is-masked systemd-networkd-wait-online &>/dev/null && print_status "PASS" "systemd-networkd-wait-online mascarado" || print_status "WARN" "systemd-networkd-wait-online não mascarado"

# Verificar VMs (se aplicável)
if command -v virsh &>/dev/null; then
    vm_count=$(sudo virsh list --all 2>/dev/null | grep -c "running\|shut off" || echo "0")
    if [ "$vm_count" -gt 0 ]; then
        print_status "PASS" "VMs configuradas: $vm_count"
        # Verificar pfSense especificamente
        if sudo virsh list --all 2>/dev/null | grep -q "pfSense"; then
            print_status "PASS" "VM pfSense encontrada"
        fi
    else
        print_status "INFO" "Nenhuma VM configurada"
    fi
fi

echo ""

# ======================================================================================================================================================== #
# VERIFICAÇÃO COMPLETA DO CRONTAB
# ======================================================================================================================================================== #

echo -e "${YELLOW}📅 VERIFICAÇÃO COMPLETA DO CRONTAB${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

crontab_lines=$(sudo crontab -l 2>/dev/null | grep -v "^#" | grep -v "^$" | wc -l)
if [ "$crontab_lines" -ge 1 ]; then
    print_status "PASS" "Crontab configurado: $crontab_lines entradas ativas"
else
    print_status "FAIL" "Crontab não configurado adequadamente"
fi

# Verificar entradas específicas do crontab
if sudo crontab -l 2>/dev/null | grep -q "backupcont.sh"; then
    print_status "PASS" "Script de backup containers no crontab"
else
    print_status "WARN" "Script de backup containers não encontrado no crontab"
fi

echo ""

# ======================================================================================================================================================== #
# VERIFICAÇÕES ESPECÍFICAS (x86_64)
# ======================================================================================================================================================== #

echo -e "${YELLOW}🔊 VERIFICAÇÕES ESPECÍFICAS ($HOSTTYPE)${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

if [ "$HOSTTYPE" = "x86_64" ]; then
    # BEEP para x86_64
    if [ -f "/home/administrador/.beep.sh" ]; then
        print_status "PASS" "Script BEEP configurado"
    else
        print_status "WARN" "Script BEEP não encontrado"
    fi
    
    if sudo crontab -l 2>/dev/null | grep -q "beep"; then
        print_status "PASS" "BEEP no crontab configurado"
    else
        print_status "WARN" "BEEP não encontrado no crontab"
    fi
else
    print_status "INFO" "Arquitetura $HOSTTYPE - verificações específicas de BEEP puladas"
fi

echo ""

# ======================================================================================================================================================== #
# VERIFICAÇÃO DE PACOTES CRÍTICOS
# ======================================================================================================================================================== #

echo -e "${YELLOW}📦 VERIFICAÇÃO DE PACOTES CRÍTICOS${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Verificar pacotes essenciais
command -v yq &>/dev/null && print_status "PASS" "yq instalado" || print_status "FAIL" "yq não instalado"
command -v docker &>/dev/null && print_status "PASS" "docker instalado" || print_status "FAIL" "docker não instalado"
command -v firefox &>/dev/null && print_status "PASS" "firefox instalado" || print_status "FAIL" "firefox não instalado"

# Verificar pacotes indesejados
unwanted_count=$(dpkg -l 2>/dev/null | grep -E "(snapd|bluetooth|cloud-init)" | wc -l)
if [ "$unwanted_count" -eq 0 ]; then
    print_status "PASS" "Pacotes indesejados removidos"
else
    print_status "WARN" "Pacotes indesejados ainda presentes: $unwanted_count"
fi

echo ""

# ======================================================================================================================================================== #
# VERIFICAÇÃO FINAL - HARDWARE ESPECÍFICO
# ======================================================================================================================================================== #

echo -e "${YELLOW}🏁 VERIFICAÇÃO FINAL - HARDWARE ESPECÍFICO${NC}"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Informações do sistema no YAML
if command -v yq &>/dev/null && [ -f "/srv/system.yaml" ]; then
    install_date=$(yq '.Informacoes.Data_Instalacao' /srv/system.yaml 2>/dev/null)
    if [ "$install_date" != "null" ] && [ -n "$install_date" ]; then
        print_status "PASS" "Data de instalação registrada: $install_date"
    else
        print_status "WARN" "Data de instalação não registrada no YAML"
    fi
    
    ip_install=$(yq '.Informacoes.IP_LAN_Install' /srv/system.yaml 2>/dev/null)
    if [ "$ip_install" != "null" ] && [ -n "$ip_install" ]; then
        print_status "PASS" "IP de instalação registrado: $ip_install"
    else
        print_status "WARN" "IP de instalação não registrado no YAML"
    fi
fi

# Hardware específico
hardware_info=$(cat /proc/cpuinfo | grep "model name" | head -1 | cut -d: -f2 | xargs)
print_status "INFO" "Hardware: $hardware_info"
print_status "INFO" "Arquitetura: $HOSTTYPE"

echo ""

# ======================================================================================================================================================== #
# RESUMO FINAL
# ======================================================================================================================================================== #

echo -e "${BLUE}================================================================${NC}"
echo -e "${BLUE}                        RESUMO FINAL${NC}"
echo -e "${BLUE}================================================================${NC}"

total_checks=$((PASSED + FAILED + WARNINGS))

echo -e "${GREEN}✅ PASSOU: $PASSED${NC}"
echo -e "${RED}❌ FALHOU: $FAILED${NC}"
echo -e "${YELLOW}⚠️  AVISOS: $WARNINGS${NC}"
echo -e "${BLUE}📊 TOTAL: $total_checks verificações${NC}"

echo ""

# Calcular percentual de sucesso
if [ "$total_checks" -gt 0 ]; then
    success_rate=$(( (PASSED * 100) / total_checks ))
    echo -e "${BLUE}📈 Taxa de Sucesso: $success_rate%${NC}"
    
    if [ "$success_rate" -ge 90 ]; then
        echo -e "${GREEN}🎉 EXCELENTE! Sistema configurado corretamente pelo Linite${NC}"
    elif [ "$success_rate" -ge 75 ]; then
        echo -e "${YELLOW}👍 BOM! Algumas verificações precisam de atenção${NC}"
    else
        echo -e "${RED}⚠️  ATENÇÃO! Várias configurações precisam de correção${NC}"
    fi
fi

echo ""

# Comando de verificação ultra-rápida
echo -e "${BLUE}💡 Comando de verificação rápida:${NC}"
echo "Sistema: $([ -f /srv/system.yaml ] && echo "✅ Configurado" || echo "❌ Não configurado")"
echo "Crontab: $(sudo crontab -l 2>/dev/null | wc -l) entradas"
echo "Swap: $(free -h | awk '/Swap/ {print $2}')"
echo "Docker: $(systemctl is-active docker 2>/dev/null)"

echo ""
echo -e "${BLUE}🔍 Para mais detalhes, execute comandos específicos das seções que falharam.${NC}"
echo -e "${BLUE}================================================================${NC}"

exit 0
