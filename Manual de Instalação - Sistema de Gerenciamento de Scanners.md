# Manual de Instalação - Sistema de Gerenciamento de Scanners

## Introdução

Este manual fornece instruções detalhadas para a instalação e configuração inicial do Sistema de Gerenciamento de Scanners em ambiente corporativo. O sistema foi projetado para funcionar em servidores Ubuntu Server 22.04 LTS, oferecendo máxima compatibilidade e estabilidade para ambientes empresariais.

## Pré-requisitos do Sistema

### Requisitos de Hardware

O Sistema de Gerenciamento de Scanners foi otimizado para funcionar eficientemente em diferentes configurações de hardware, desde ambientes de teste até implementações corporativas de grande escala.

**Configuração Mínima (até 50 usuários):**
- Processador: 2 cores x86_64 (2.0 GHz ou superior)
- Memória RAM: 4 GB
- Armazenamento: 50 GB de espaço livre
- Rede: Interface Ethernet 100 Mbps

**Configuração Recomendada (até 200 usuários):**
- Processador: 4 cores x86_64 (2.5 GHz ou superior)
- Memória RAM: 8 GB
- Armazenamento: 200 GB de espaço livre (SSD recomendado)
- Rede: Interface Ethernet 1 Gbps

**Configuração para Alta Disponibilidade (mais de 200 usuários):**
- Processador: 8 cores x86_64 (3.0 GHz ou superior)
- Memória RAM: 16 GB ou mais
- Armazenamento: 500 GB de espaço livre (SSD NVMe recomendado)
- Rede: Interface Ethernet 1 Gbps com redundância
- Backup: Sistema de backup automatizado

### Requisitos de Software

O sistema utiliza containerização Docker para garantir consistência e facilitar a implantação. Os seguintes componentes de software são necessários:

**Sistema Operacional:**
- Ubuntu Server 22.04 LTS (recomendado)
- Ubuntu Server 20.04 LTS (compatível)
- Debian 11 ou superior (compatível)
- CentOS 8 ou superior (compatível com adaptações)

**Software Base:**
- Docker Engine 20.10 ou superior
- Docker Compose 2.0 ou superior
- Git 2.25 ou superior
- Curl e Wget para downloads
- OpenSSL para certificados SSL

### Requisitos de Rede

O sistema requer conectividade adequada com os seguintes componentes da infraestrutura corporativa:

**Conectividade Essencial:**
- Acesso ao servidor Active Directory (portas 389/636 para LDAP/LDAPS)
- Conectividade com scanners de rede (portas 80, 443, 631, 9100, 8080)
- Acesso à internet para atualizações (opcional, mas recomendado)
- Conectividade com servidor de email SMTP para notificações

**Configuração de Firewall:**
- Porta 80 (HTTP) - Interface web
- Porta 443 (HTTPS) - Interface web segura
- Porta 5000 (API Backend) - Comunicação interna
- Porta 5432 (PostgreSQL) - Banco de dados (apenas interno)
- Porta 6379 (Redis) - Cache (apenas interno)

## Preparação do Ambiente

### Instalação do Sistema Operacional

Inicie com uma instalação limpa do Ubuntu Server 22.04 LTS. Durante a instalação, configure as seguintes opções:

1. **Particionamento**: Utilize LVM para facilitar expansão futura do armazenamento
2. **Usuário**: Crie um usuário administrativo com privilégios sudo
3. **SSH**: Habilite o servidor SSH para acesso remoto
4. **Atualizações**: Configure atualizações automáticas de segurança

Após a instalação, execute as atualizações iniciais:

```bash
sudo apt update && sudo apt upgrade -y
sudo reboot
```

### Configuração de Rede

Configure a interface de rede com IP estático para garantir conectividade consistente:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

Exemplo de configuração:

```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: false
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 192.168.1.10
          - 8.8.8.8
```

Aplique a configuração:

```bash
sudo netplan apply
```

### Instalação do Docker

O Docker é o componente fundamental para a execução do sistema. Instale a versão mais recente seguindo os passos oficiais:

```bash
# Remover versões antigas
sudo apt remove docker docker-engine docker.io containerd runc

# Instalar dependências
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release

# Adicionar chave GPG oficial do Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Configurar repositório
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker Engine
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Verificar instalação
sudo docker --version
sudo docker compose version
```

### Configuração do Docker

Configure o Docker para uso em produção:

```bash
# Adicionar usuário ao grupo docker
sudo usermod -aG docker $USER

# Configurar inicialização automática
sudo systemctl enable docker
sudo systemctl enable containerd

# Configurar logging
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json > /dev/null <<EOF
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "storage-driver": "overlay2"
}
EOF

# Reiniciar Docker
sudo systemctl restart docker

# Fazer logout e login novamente para aplicar grupo docker
```

## Instalação do Sistema

### Obtenção do Código Fonte

Clone o repositório do sistema para o servidor:

```bash
# Navegar para diretório apropriado
cd /opt

# Clonar repositório (substitua pela URL real)
sudo git clone https://github.com/empresa/scanner-management-system.git

# Definir permissões
sudo chown -R $USER:$USER scanner-management-system
cd scanner-management-system
```

### Configuração Inicial

O sistema utiliza variáveis de ambiente para configuração. Crie o arquivo de configuração baseado no template:

```bash
cp .env.example .env
```

Edite o arquivo `.env` com as configurações específicas do seu ambiente:

```bash
nano .env
```

### Configurações Essenciais

**Banco de Dados:**
```env
POSTGRES_DB=scanner_system
POSTGRES_USER=scanner_user
POSTGRES_PASSWORD=SuaSenhaSegura123!
DATABASE_URL=postgresql://scanner_user:SuaSenhaSegura123!@database:5432/scanner_system
```

**Aplicação Flask:**
```env
FLASK_ENV=production
SECRET_KEY=sua-chave-secreta-muito-longa-e-aleatoria-aqui
UPLOAD_FOLDER=/app/uploads
```

**Active Directory/LDAP:**
```env
LDAP_SERVER=ldap://seu-servidor-ad.empresa.com
LDAP_PORT=389
LDAP_USE_SSL=false
LDAP_BASE_DN=DC=empresa,DC=com
LDAP_USER_DN=OU=Users,DC=empresa,DC=com
LDAP_GROUP_DN=OU=Groups,DC=empresa,DC=com
LDAP_BIND_USER=CN=scanner-service,OU=Service Accounts,DC=empresa,DC=com
LDAP_BIND_PASSWORD=senha-do-usuario-de-bind
```

**Configurações de Email:**
```env
SMTP_SERVER=smtp.empresa.com
SMTP_PORT=587
SMTP_USE_TLS=true
SMTP_USERNAME=scanner-system@empresa.com
SMTP_PASSWORD=senha-do-email
```

**Configurações de Segurança:**
```env
JWT_SECRET_KEY=chave-jwt-muito-segura-e-aleatoria
JWT_ACCESS_TOKEN_EXPIRES=3600
JWT_REFRESH_TOKEN_EXPIRES=86400
```

### Configuração de Rede para Descoberta de Scanners

Configure o range de IPs onde o sistema deve procurar por scanners:

```env
NETWORK_DISCOVERY_RANGE=192.168.1.0/24
SCANNER_PORTS=80,443,631,9100,8080
MONITORING_INTERVAL=300
DISCOVERY_TIMEOUT=5
```

### Configuração de Cotas e Limites

Defina os limites padrão para usuários e departamentos:

```env
DEFAULT_USER_QUOTA=1000
DEFAULT_DEPARTMENT_QUOTA=10000
MAX_FILE_SIZE=104857600
ALLOWED_EXTENSIONS=pdf,jpg,jpeg,png,tiff,tif
DEFAULT_RETENTION_DAYS=365
LOG_RETENTION_DAYS=90
```

## Execução da Instalação

### Deploy Automatizado

O sistema inclui um script de deploy automatizado que facilita a instalação:

```bash
# Tornar o script executável
chmod +x deploy.sh

# Executar deploy
./deploy.sh
```

O script executará as seguintes operações:

1. Verificação de pré-requisitos
2. Validação do arquivo .env
3. Build das imagens Docker
4. Inicialização dos containers
5. Verificação de saúde dos serviços
6. Testes de conectividade

### Deploy Manual (Alternativo)

Se preferir controle manual sobre o processo:

```bash
# Build das imagens
docker compose build --no-cache

# Iniciar serviços
docker compose up -d

# Verificar status
docker compose ps

# Verificar logs
docker compose logs -f
```

### Verificação da Instalação

Após a execução do deploy, verifique se todos os serviços estão funcionando:

```bash
# Verificar containers em execução
docker compose ps

# Testar conectividade do backend
curl -f http://localhost:5000/api/health

# Testar interface web
curl -f http://localhost

# Verificar logs por possíveis erros
docker compose logs backend | grep ERROR
docker compose logs frontend | grep ERROR
```

## Configuração Pós-Instalação

### Criação do Usuário Administrador

Acesse o sistema pela primeira vez usando as credenciais padrão:

- **URL**: http://seu-servidor
- **Usuário**: admin
- **Senha**: admin123

**IMPORTANTE**: Altere a senha padrão imediatamente após o primeiro login.

### Configuração do Active Directory

Para integrar com Active Directory, siga estes passos:

1. **Teste de Conectividade LDAP:**
```bash
# Testar conexão LDAP
docker compose exec backend python -c "
import ldap3
server = ldap3.Server('seu-servidor-ad.empresa.com')
conn = ldap3.Connection(server, 'CN=scanner-service,OU=Service Accounts,DC=empresa,DC=com', 'senha')
print('Conexão LDAP:', conn.bind())
"
```

2. **Sincronização Inicial de Usuários:**
   - Acesse a interface administrativa
   - Navegue para "Configurações > Active Directory"
   - Execute "Sincronizar Usuários"
   - Verifique os logs para possíveis erros

3. **Configuração de Grupos:**
   - Mapeie grupos do AD para roles do sistema
   - Configure permissões por departamento
   - Teste login com usuários do AD

### Descoberta de Scanners

Configure a descoberta automática de scanners:

1. **Configuração de Rede:**
   - Verifique se o range de IPs está correto no .env
   - Confirme que o servidor tem acesso aos scanners
   - Teste conectividade manual com scanners conhecidos

2. **Execução da Descoberta:**
   - Acesse "Scanners > Descobrir Dispositivos"
   - Execute varredura da rede
   - Adicione scanners encontrados manualmente se necessário

3. **Configuração de Monitoramento:**
   - Configure intervalos de monitoramento
   - Defina alertas para dispositivos offline
   - Teste notificações por email

### Configuração de SSL/HTTPS

Para ambiente de produção, configure SSL:

1. **Obtenção de Certificados:**
```bash
# Usando Let's Encrypt (recomendado)
sudo apt install certbot
sudo certbot certonly --standalone -d seu-dominio.empresa.com
```

2. **Configuração do Nginx:**
```bash
# Criar configuração SSL
sudo mkdir -p scanner-management-system/nginx/conf.d
sudo nano scanner-management-system/nginx/conf.d/ssl.conf
```

Exemplo de configuração SSL:
```nginx
server {
    listen 443 ssl http2;
    server_name seu-dominio.empresa.com;
    
    ssl_certificate /etc/letsencrypt/live/seu-dominio.empresa.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/seu-dominio.empresa.com/privkey.pem;
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;
    
    location / {
        proxy_pass http://frontend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

3. **Ativação do Profile de Produção:**
```bash
# Editar docker-compose.yml para ativar nginx
docker compose --profile production up -d
```

## Configuração de Backup

### Backup Automático

Configure backups regulares dos dados críticos:

1. **Script de Backup:**
```bash
sudo nano /usr/local/bin/scanner-backup.sh
```

```bash
#!/bin/bash
BACKUP_DIR="/backup/scanner-system"
DATE=$(date +%Y%m%d_%H%M%S)

# Criar diretório de backup
mkdir -p $BACKUP_DIR

# Backup do banco de dados
docker compose exec -T database pg_dump -U scanner_user scanner_system > $BACKUP_DIR/database_$DATE.sql

# Backup de arquivos
tar -czf $BACKUP_DIR/uploads_$DATE.tar.gz -C /opt/scanner-management-system uploads/

# Backup de configurações
cp /opt/scanner-management-system/.env $BACKUP_DIR/config_$DATE.env

# Limpeza de backups antigos (manter 30 dias)
find $BACKUP_DIR -type f -mtime +30 -delete

echo "Backup concluído: $DATE"
```

2. **Agendamento com Cron:**
```bash
sudo chmod +x /usr/local/bin/scanner-backup.sh
sudo crontab -e

# Adicionar linha para backup diário às 2h
0 2 * * * /usr/local/bin/scanner-backup.sh >> /var/log/scanner-backup.log 2>&1
```

### Monitoramento de Backup

Configure alertas para falhas de backup:

```bash
# Script de verificação
sudo nano /usr/local/bin/check-backup.sh
```

```bash
#!/bin/bash
BACKUP_DIR="/backup/scanner-system"
ALERT_EMAIL="admin@empresa.com"

# Verificar se backup foi criado nas últimas 25 horas
if [ ! "$(find $BACKUP_DIR -name "database_*.sql" -mtime -1)" ]; then
    echo "ALERTA: Backup do banco de dados não foi criado nas últimas 24 horas" | mail -s "Falha no Backup - Scanner System" $ALERT_EMAIL
fi

if [ ! "$(find $BACKUP_DIR -name "uploads_*.tar.gz" -mtime -1)" ]; then
    echo "ALERTA: Backup de arquivos não foi criado nas últimas 24 horas" | mail -s "Falha no Backup - Scanner System" $ALERT_EMAIL
fi
```

## Monitoramento e Manutenção

### Configuração de Logs

Configure rotação de logs para evitar consumo excessivo de espaço:

```bash
sudo nano /etc/logrotate.d/scanner-system
```

```
/opt/scanner-management-system/logs/*.log {
    daily
    missingok
    rotate 30
    compress
    delaycompress
    notifempty
    create 644 root root
    postrotate
        docker compose restart backend
    endscript
}
```

### Monitoramento de Performance

Configure monitoramento básico do sistema:

1. **Script de Monitoramento:**
```bash
sudo nano /usr/local/bin/scanner-monitor.sh
```

```bash
#!/bin/bash
LOG_FILE="/var/log/scanner-monitor.log"
DATE=$(date '+%Y-%m-%d %H:%M:%S')

# Verificar uso de CPU e memória
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
MEM_USAGE=$(free | grep Mem | awk '{printf("%.2f", $3/$2 * 100.0)}')

# Verificar espaço em disco
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}' | cut -d'%' -f1)

# Verificar containers
CONTAINERS_UP=$(docker compose ps --services --filter "status=running" | wc -l)
CONTAINERS_TOTAL=$(docker compose ps --services | wc -l)

echo "$DATE - CPU: ${CPU_USAGE}%, MEM: ${MEM_USAGE}%, DISK: ${DISK_USAGE}%, Containers: $CONTAINERS_UP/$CONTAINERS_TOTAL" >> $LOG_FILE

# Alertas
if (( $(echo "$CPU_USAGE > 80" | bc -l) )); then
    echo "ALERTA: Uso de CPU alto: ${CPU_USAGE}%" | mail -s "Scanner System - CPU Alert" admin@empresa.com
fi

if (( $(echo "$MEM_USAGE > 80" | bc -l) )); then
    echo "ALERTA: Uso de memória alto: ${MEM_USAGE}%" | mail -s "Scanner System - Memory Alert" admin@empresa.com
fi

if [ "$DISK_USAGE" -gt 80 ]; then
    echo "ALERTA: Uso de disco alto: ${DISK_USAGE}%" | mail -s "Scanner System - Disk Alert" admin@empresa.com
fi
```

2. **Agendamento:**
```bash
sudo chmod +x /usr/local/bin/scanner-monitor.sh
sudo crontab -e

# Monitoramento a cada 15 minutos
*/15 * * * * /usr/local/bin/scanner-monitor.sh
```

## Solução de Problemas Comuns

### Problemas de Conectividade

**Sintoma**: Sistema não consegue descobrir scanners
**Solução**:
1. Verificar conectividade de rede: `ping IP_DO_SCANNER`
2. Testar portas: `nmap -p 80,443,631,9100 IP_DO_SCANNER`
3. Verificar configuração de firewall
4. Confirmar range de IPs no .env

**Sintoma**: Erro de autenticação LDAP
**Solução**:
1. Testar conectividade: `telnet servidor-ad.empresa.com 389`
2. Verificar credenciais de bind
3. Confirmar DN base e estrutura do AD
4. Verificar logs do backend: `docker compose logs backend | grep LDAP`

### Problemas de Performance

**Sintoma**: Sistema lento ou travando
**Solução**:
1. Verificar uso de recursos: `docker stats`
2. Analisar logs de erro: `docker compose logs | grep ERROR`
3. Verificar espaço em disco: `df -h`
4. Reiniciar serviços: `docker compose restart`

**Sintoma**: Banco de dados lento
**Solução**:
1. Verificar conexões ativas: `docker compose exec database psql -U scanner_user -c "SELECT count(*) FROM pg_stat_activity;"`
2. Analisar queries lentas nos logs
3. Considerar otimização de índices
4. Aumentar recursos do container se necessário

### Problemas de Armazenamento

**Sintoma**: Erro de espaço insuficiente
**Solução**:
1. Verificar uso de disco: `du -sh /opt/scanner-management-system/`
2. Limpar logs antigos: `docker system prune -f`
3. Arquivar arquivos antigos
4. Expandir armazenamento se necessário

## Atualizações do Sistema

### Processo de Atualização

Para atualizar o sistema para uma nova versão:

```bash
# Fazer backup antes da atualização
/usr/local/bin/scanner-backup.sh

# Parar serviços
docker compose down

# Atualizar código
git pull origin main

# Reconstruir imagens
docker compose build --no-cache

# Iniciar serviços
docker compose up -d

# Verificar funcionamento
docker compose ps
curl -f http://localhost/api/health
```

### Rollback em Caso de Problemas

Se houver problemas após atualização:

```bash
# Parar serviços
docker compose down

# Voltar para versão anterior
git checkout TAG_VERSAO_ANTERIOR

# Restaurar backup se necessário
docker compose exec -T database psql -U scanner_user scanner_system < /backup/scanner-system/database_TIMESTAMP.sql

# Reconstruir e iniciar
docker compose build --no-cache
docker compose up -d
```

## Conclusão

A instalação do Sistema de Gerenciamento de Scanners, quando seguida corretamente, resultará em uma solução robusta e escalável para gerenciamento de dispositivos de digitalização em ambiente corporativo. O sistema oferece alta disponibilidade, segurança adequada e facilidade de manutenção através da containerização Docker.

Para suporte adicional ou questões específicas sobre a instalação, consulte a documentação complementar ou entre em contato com a equipe de suporte técnico.

---

**Autor**: Manus AI  
**Data**: Agosto 2024  
**Versão do Manual**: 1.0

