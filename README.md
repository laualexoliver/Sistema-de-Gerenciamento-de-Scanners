# Sistema de Gerenciamento de Scanners

## Visão Geral

O Sistema de Gerenciamento de Scanners é uma solução corporativa completa desenvolvida para empresas que necessitam de controle centralizado sobre dispositivos de digitalização em rede. Este sistema oferece uma interface web moderna e intuitiva para gerenciar scanners, controlar cotas de uso, compartilhar arquivos digitalizados e monitorar atividades em tempo real.

### Características Principais

- **Interface Web Moderna**: Aplicação React responsiva com design profissional
- **Autenticação Integrada**: Suporte completo ao Active Directory via LDAP/Kerberos
- **Descoberta Automática**: Identificação automática de scanners na rede corporativa
- **Sistema de Cotas**: Controle granular de uso por usuário e departamento
- **Compartilhamento Seguro**: Links seguros com controle de acesso e expiração
- **Monitoramento em Tempo Real**: Status dos dispositivos e alertas automáticos
- **Containerização Docker**: Deploy simplificado e escalável
- **Logs Detalhados**: Auditoria completa de todas as atividades

### Arquitetura do Sistema

O sistema utiliza uma arquitetura moderna baseada em microserviços:

- **Backend**: Flask (Python) com API RESTful
- **Frontend**: React com Tailwind CSS
- **Banco de Dados**: SQLite (desenvolvimento) / PostgreSQL (produção)
- **Containerização**: Docker e Docker Compose
- **Proxy Reverso**: Nginx para produção
- **Cache**: Redis para sessões e cache

## Requisitos do Sistema

### Requisitos Mínimos

- **Sistema Operacional**: Ubuntu Server 22.04 LTS ou superior
- **Memória RAM**: 4GB mínimo, 8GB recomendado
- **Armazenamento**: 50GB mínimo, 200GB recomendado
- **Processador**: 2 cores mínimo, 4 cores recomendado
- **Rede**: Conectividade com Active Directory e scanners

### Software Necessário

- Docker 20.10 ou superior
- Docker Compose 2.0 ou superior
- Git para clonagem do repositório

## Instalação Rápida

### 1. Clonar o Repositório

```bash
git clone https://github.com/empresa/scanner-management-system.git
cd scanner-management-system
```

### 2. Configurar Variáveis de Ambiente

```bash
cp .env.example .env
nano .env
```

### 3. Executar Deploy

```bash
./deploy.sh
```

### 4. Acessar o Sistema

- **Interface Web**: http://localhost
- **API Backend**: http://localhost:5000
- **Credenciais Padrão**: admin / admin123

## Estrutura do Projeto

```
scanner-management-system/
├── backend/                    # Backend Flask
│   └── scanner_api/
│       ├── src/               # Código fonte
│       │   ├── models/        # Modelos de dados
│       │   ├── routes/        # Endpoints da API
│       │   ├── services/      # Lógica de negócio
│       │   └── main.py        # Aplicação principal
│       ├── Dockerfile         # Container backend
│       └── requirements.txt   # Dependências Python
├── frontend/                  # Frontend React
│   └── scanner-frontend/
│       ├── src/              # Código fonte React
│       ├── public/           # Arquivos públicos
│       ├── Dockerfile        # Container frontend
│       └── nginx.conf        # Configuração Nginx
├── database/                 # Scripts de banco
│   └── init.sql             # Inicialização PostgreSQL
├── docs/                    # Documentação
├── docker-compose.yml       # Orquestração containers
├── deploy.sh               # Script de deploy
├── .env.example           # Exemplo de configuração
└── README.md             # Este arquivo
```

## Configuração

### Active Directory / LDAP

Para integrar com Active Directory, configure as seguintes variáveis no arquivo `.env`:

```env
LDAP_SERVER=ldap://seu-servidor-ad.empresa.com
LDAP_BASE_DN=DC=empresa,DC=com
LDAP_BIND_USER=CN=scanner-service,OU=Service Accounts,DC=empresa,DC=com
LDAP_BIND_PASSWORD=sua-senha-ldap
```

### Configuração de Rede

O sistema pode descobrir scanners automaticamente na rede. Configure o range de IPs:

```env
NETWORK_DISCOVERY_RANGE=192.168.1.0/24
SCANNER_PORTS=80,443,631,9100,8080
```

### Cotas e Limites

Defina cotas padrão para usuários e departamentos:

```env
DEFAULT_USER_QUOTA=1000        # MB por usuário
DEFAULT_DEPARTMENT_QUOTA=10000 # MB por departamento
MAX_FILE_SIZE=104857600        # 100MB por arquivo
```

## Uso do Sistema

### Para Administradores

1. **Descoberta de Scanners**: Use a função de descoberta automática para identificar dispositivos na rede
2. **Gerenciamento de Usuários**: Sincronize com Active Directory ou gerencie usuários localmente
3. **Configuração de Cotas**: Defina limites por usuário e departamento
4. **Monitoramento**: Acompanhe o status dos dispositivos e uso do sistema
5. **Relatórios**: Gere relatórios de uso e atividade

### Para Usuários

1. **Login**: Acesse com credenciais do Active Directory
2. **Digitalização**: Faça upload de documentos digitalizados
3. **Compartilhamento**: Gere links seguros para compartilhar arquivos
4. **Histórico**: Visualize suas digitalizações anteriores
5. **Cotas**: Monitore seu uso de armazenamento

## API Documentation

A API RESTful oferece endpoints completos para todas as funcionalidades:

### Autenticação

- `POST /api/auth/login` - Login de usuário
- `POST /api/auth/logout` - Logout
- `POST /api/auth/refresh` - Renovar token

### Scanners

- `GET /api/scanners` - Listar scanners
- `POST /api/scanners` - Adicionar scanner
- `PUT /api/scanners/{id}` - Atualizar scanner
- `DELETE /api/scanners/{id}` - Remover scanner
- `POST /api/scanners/discover` - Descobrir scanners

### Digitalizações

- `GET /api/scan/jobs` - Listar trabalhos
- `POST /api/scan/jobs` - Criar trabalho
- `GET /api/scan/jobs/{id}` - Detalhes do trabalho
- `DELETE /api/scan/jobs/{id}` - Remover trabalho

### Compartilhamento

- `POST /api/share` - Criar compartilhamento
- `GET /api/share` - Listar compartilhamentos
- `PUT /api/share/{id}` - Atualizar compartilhamento
- `DELETE /api/share/{id}` - Remover compartilhamento

## Monitoramento e Logs

### Health Checks

O sistema oferece endpoints de monitoramento:

- `/api/health` - Status geral do sistema
- `/api/health/ready` - Verificação de prontidão
- `/api/health/live` - Verificação de vida

### Logs

Os logs são armazenados em diferentes níveis:

- **INFO**: Operações normais
- **WARNING**: Situações de atenção
- **ERROR**: Erros do sistema
- **DEBUG**: Informações detalhadas (desenvolvimento)

### Métricas

O sistema coleta métricas sobre:

- Uso de scanners
- Volume de digitalizações
- Consumo de cotas
- Performance do sistema

## Segurança

### Autenticação e Autorização

- Integração com Active Directory
- Tokens JWT com expiração
- Controle de acesso baseado em roles
- Sessões seguras

### Proteção de Dados

- Criptografia de arquivos sensíveis
- Links de compartilhamento com expiração
- Logs de auditoria completos
- Backup automático de dados

### Configurações de Segurança

- HTTPS obrigatório em produção
- Headers de segurança configurados
- Validação de entrada rigorosa
- Rate limiting para APIs

## Backup e Recuperação

### Backup Automático

Configure backups regulares dos dados:

```bash
# Backup do banco de dados
docker-compose exec database pg_dump -U scanner_user scanner_system > backup.sql

# Backup de arquivos
tar -czf uploads_backup.tar.gz uploads/
```

### Recuperação

Para restaurar dados:

```bash
# Restaurar banco
docker-compose exec database psql -U scanner_user scanner_system < backup.sql

# Restaurar arquivos
tar -xzf uploads_backup.tar.gz
```

## Troubleshooting

### Problemas Comuns

#### Scanner não é descoberto

1. Verifique conectividade de rede
2. Confirme portas abertas no scanner
3. Verifique configuração de firewall
4. Teste ping manual para o IP do scanner

#### Erro de autenticação LDAP

1. Verifique configurações LDAP no .env
2. Teste conectividade com servidor AD
3. Confirme credenciais de bind
4. Verifique logs do backend

#### Performance lenta

1. Monitore uso de recursos
2. Verifique logs de erro
3. Otimize consultas de banco
4. Considere scaling horizontal

### Comandos Úteis

```bash
# Ver logs em tempo real
docker-compose logs -f backend

# Reiniciar serviço específico
docker-compose restart backend

# Verificar status dos containers
docker-compose ps

# Executar comando no container
docker-compose exec backend bash

# Limpar dados (CUIDADO!)
docker-compose down -v
```

## Desenvolvimento

### Ambiente de Desenvolvimento

Para desenvolver o sistema:

```bash
# Backend
cd backend/scanner_api
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python src/main.py

# Frontend
cd frontend/scanner-frontend
pnpm install
pnpm run dev
```

### Testes

Execute os testes:

```bash
# Backend
pytest tests/

# Frontend
pnpm test
```

### Contribuição

1. Fork o repositório
2. Crie uma branch para sua feature
3. Faça commit das mudanças
4. Abra um Pull Request

## Suporte

### Documentação Adicional

- [Manual de Instalação](docs/installation.md)
- [Manual de Administração](docs/administration.md)
- [Manual do Usuário](docs/user-guide.md)
- [API Reference](docs/api-reference.md)

### Contato

- **Email**: suporte@empresa.com
- **Telefone**: (11) 1234-5678
- **Website**: https://empresa.com/suporte

## Licença

Este sistema é propriedade da empresa e está licenciado sob os termos do contrato de software corporativo.

## Changelog

### Versão 1.0.0 (2024-08-14)

- Lançamento inicial do sistema
- Interface web completa
- Integração com Active Directory
- Sistema de cotas implementado
- Containerização Docker
- Documentação completa

---

**Desenvolvido por**: Manus AI  
**Data**: Agosto 2024  
**Versão**: 1.0.0

