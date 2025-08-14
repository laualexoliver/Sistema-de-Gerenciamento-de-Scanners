# Manual de Administração - Sistema de Gerenciamento de Scanners

## Introdução

Este manual destina-se aos administradores de sistema responsáveis pela operação, manutenção e configuração do Sistema de Gerenciamento de Scanners. Abrange desde tarefas rotineiras de administração até procedimentos avançados de otimização e solução de problemas.

## Visão Geral da Administração

O Sistema de Gerenciamento de Scanners oferece uma interface administrativa completa que permite controle total sobre todos os aspectos da solução. Como administrador, você terá acesso a funcionalidades avançadas de configuração, monitoramento e manutenção que garantem o funcionamento otimizado do sistema em ambiente corporativo.

### Responsabilidades do Administrador

As principais responsabilidades incluem gerenciamento de usuários e permissões, configuração e monitoramento de dispositivos scanners, controle de cotas e políticas de uso, manutenção da segurança do sistema, monitoramento de performance e disponibilidade, execução de backups e procedimentos de recuperação, aplicação de atualizações e patches de segurança, e geração de relatórios gerenciais e de auditoria.

## Acesso Administrativo

### Login como Administrador

Para acessar as funcionalidades administrativas, faça login no sistema usando uma conta com privilégios de administrador. O sistema identifica automaticamente usuários administrativos e exibe as opções avançadas na interface.

**Credenciais Padrão (alterar após primeira instalação):**
- Usuário: admin
- Senha: admin123

**IMPORTANTE**: Por questões de segurança, altere a senha padrão imediatamente após o primeiro acesso e configure autenticação via Active Directory sempre que possível.

### Interface Administrativa

A interface administrativa está organizada em seções principais que facilitam a navegação e execução de tarefas:

**Dashboard Administrativo**: Visão geral do sistema com métricas em tempo real, alertas ativos, status dos serviços e resumo de atividades recentes.

**Gerenciamento de Usuários**: Controle completo sobre contas de usuário, incluindo criação, edição, desativação e configuração de permissões individuais.

**Gerenciamento de Scanners**: Configuração e monitoramento de dispositivos, incluindo descoberta automática, configuração manual e definição de políticas de uso.

**Sistema de Cotas**: Configuração de limites de uso por usuário e departamento, monitoramento de consumo e configuração de alertas automáticos.

**Configurações do Sistema**: Parâmetros gerais de funcionamento, integração com Active Directory, configurações de email e políticas de segurança.

**Relatórios e Auditoria**: Geração de relatórios detalhados sobre uso do sistema, atividades de usuários e eventos de segurança.

## Gerenciamento de Usuários

### Integração com Active Directory

A integração com Active Directory é o método recomendado para gerenciamento de usuários em ambiente corporativo. Esta configuração permite autenticação centralizada e sincronização automática de informações de usuários.

**Configuração Inicial do LDAP:**

Para configurar a integração, acesse "Configurações > Active Directory" na interface administrativa. Os parâmetros essenciais incluem:

- **Servidor LDAP**: Endereço do controlador de domínio (exemplo: ldap://dc01.empresa.com)
- **Porta**: Geralmente 389 para LDAP ou 636 para LDAPS
- **Base DN**: Distinguished Name base (exemplo: DC=empresa,DC=com)
- **Usuário de Bind**: Conta de serviço para consultas LDAP
- **Senha de Bind**: Senha da conta de serviço

**Teste de Conectividade:**

Após configurar os parâmetros, execute o teste de conectividade para verificar se a comunicação com o Active Directory está funcionando corretamente. O sistema tentará estabelecer conexão e executar uma consulta básica para validar as credenciais.

**Sincronização de Usuários:**

A sincronização pode ser executada manualmente através da interface administrativa ou configurada para execução automática em intervalos regulares. Durante a sincronização, o sistema:

1. Consulta o Active Directory para obter lista de usuários ativos
2. Compara com usuários existentes no sistema
3. Cria novos usuários conforme necessário
4. Atualiza informações de usuários existentes
5. Desativa usuários que não existem mais no AD

**Mapeamento de Grupos:**

Configure o mapeamento entre grupos do Active Directory e roles do sistema. Isso permite atribuição automática de permissões baseada na estrutura organizacional existente:

- **Administradores**: Grupo com acesso total ao sistema
- **Usuários Avançados**: Acesso a funcionalidades estendidas
- **Usuários Padrão**: Acesso básico às funcionalidades de digitalização

### Gerenciamento Manual de Usuários

Para ambientes sem Active Directory ou para usuários específicos que requerem configuração manual, o sistema oferece ferramentas completas de gerenciamento de usuários.

**Criação de Usuários:**

Para criar um novo usuário manualmente:

1. Acesse "Usuários > Adicionar Usuário"
2. Preencha as informações básicas (nome, email, departamento)
3. Defina credenciais de acesso (usuário e senha)
4. Configure permissões e cotas específicas
5. Ative ou desative a conta conforme necessário

**Edição de Usuários:**

A edição permite modificar qualquer aspecto da conta do usuário, incluindo informações pessoais, credenciais, permissões e cotas. Todas as alterações são registradas no log de auditoria para rastreabilidade.

**Desativação e Exclusão:**

Usuários podem ser temporariamente desativados (mantendo histórico) ou permanentemente excluídos. A desativação é recomendada para preservar dados históricos e permitir reativação futura se necessário.

### Controle de Permissões

O sistema implementa um modelo de permissões baseado em roles que oferece controle granular sobre as funcionalidades disponíveis para cada usuário.

**Roles Padrão:**

- **Super Administrador**: Acesso completo a todas as funcionalidades
- **Administrador**: Acesso a configurações e gerenciamento, exceto configurações críticas do sistema
- **Supervisor**: Monitoramento e relatórios, sem capacidade de alteração
- **Usuário Avançado**: Funcionalidades estendidas de digitalização e compartilhamento
- **Usuário Padrão**: Funcionalidades básicas de digitalização

**Permissões Específicas:**

Cada role pode ser customizada com permissões específicas:

- Gerenciar usuários e grupos
- Configurar dispositivos scanners
- Acessar relatórios administrativos
- Modificar configurações do sistema
- Executar backups e restaurações
- Visualizar logs de auditoria

## Gerenciamento de Scanners

### Descoberta Automática de Dispositivos

O sistema oferece funcionalidade avançada de descoberta automática que identifica scanners compatíveis na rede corporativa.

**Configuração da Descoberta:**

Para configurar a descoberta automática, acesse "Scanners > Configurações de Descoberta" e defina:

- **Range de IPs**: Faixa de endereços a ser verificada (exemplo: 192.168.1.0/24)
- **Portas de Verificação**: Portas comuns de scanners (80, 443, 631, 9100, 8080)
- **Timeout de Conexão**: Tempo limite para cada tentativa de conexão
- **Intervalo de Varredura**: Frequência de execução da descoberta automática

**Execução da Descoberta:**

A descoberta pode ser executada manualmente através do botão "Descobrir Dispositivos" ou automaticamente conforme o intervalo configurado. Durante o processo, o sistema:

1. Varre o range de IPs especificado
2. Testa conectividade nas portas configuradas
3. Identifica dispositivos que respondem
4. Tenta determinar modelo e fabricante
5. Apresenta lista de dispositivos encontrados para aprovação

**Validação de Dispositivos:**

Dispositivos descobertos devem ser validados antes da adição ao sistema. A validação inclui:

- Verificação de compatibilidade com protocolos suportados
- Teste de funcionalidades básicas
- Configuração de parâmetros específicos do dispositivo
- Definição de localização física e responsável

### Configuração Manual de Scanners

Para dispositivos que não são descobertos automaticamente ou que requerem configuração específica, utilize o processo manual de adição.

**Adição Manual:**

1. Acesse "Scanners > Adicionar Scanner"
2. Informe endereço IP ou hostname do dispositivo
3. Selecione modelo/fabricante (se conhecido)
4. Configure protocolos de comunicação
5. Teste conectividade e funcionalidades
6. Defina configurações específicas (resolução, formatos, etc.)

**Configurações Avançadas:**

Para cada scanner, configure parâmetros avançados:

- **Protocolos Suportados**: HTTP, HTTPS, FTP, SMB, Email
- **Formatos de Arquivo**: PDF, TIFF, JPEG, PNG
- **Resoluções Disponíveis**: 150, 300, 600, 1200 DPI
- **Configurações de Cor**: Monocromático, Escala de Cinza, Colorido
- **Limites de Uso**: Páginas por dia, tamanho máximo de arquivo

### Monitoramento de Dispositivos

O sistema monitora continuamente o status e performance dos scanners registrados.

**Status em Tempo Real:**

O dashboard administrativo exibe status atual de todos os dispositivos:

- **Online**: Dispositivo respondendo normalmente
- **Offline**: Dispositivo não responde a tentativas de conexão
- **Erro**: Dispositivo responde mas reporta problemas
- **Manutenção**: Dispositivo temporariamente desabilitado

**Alertas Automáticos:**

Configure alertas para situações específicas:

- Dispositivo fica offline por período determinado
- Erro de comunicação persistente
- Uso excessivo ou inatividade prolongada
- Necessidade de manutenção preventiva

**Histórico de Atividades:**

Mantenha registro detalhado de todas as atividades:

- Tempo de atividade e inatividade
- Volume de digitalizações processadas
- Erros e problemas reportados
- Intervenções de manutenção realizadas

## Sistema de Cotas

### Configuração de Cotas por Usuário

O sistema de cotas permite controle preciso sobre o uso de recursos por usuário individual.

**Tipos de Cotas:**

- **Cota de Armazenamento**: Limite de espaço em disco por usuário
- **Cota de Digitalizações**: Número máximo de documentos por período
- **Cota de Páginas**: Limite de páginas digitalizadas por período
- **Cota de Compartilhamentos**: Número de links de compartilhamento ativos

**Configuração Individual:**

Para configurar cotas específicas para um usuário:

1. Acesse "Usuários > Gerenciar Usuários"
2. Selecione o usuário desejado
3. Clique em "Configurar Cotas"
4. Defina limites específicos para cada tipo de cota
5. Configure período de renovação (diário, semanal, mensal)
6. Ative alertas automáticos para limites próximos

**Cotas Padrão:**

Defina cotas padrão que serão aplicadas automaticamente a novos usuários:

- Usuários Padrão: 1GB armazenamento, 100 digitalizações/mês
- Usuários Avançados: 5GB armazenamento, 500 digitalizações/mês
- Supervisores: 10GB armazenamento, 1000 digitalizações/mês

### Configuração de Cotas por Departamento

Cotas departamentais permitem controle agregado sobre grupos de usuários.

**Estrutura Departamental:**

Configure a estrutura organizacional:

1. Crie departamentos na seção "Departamentos"
2. Associe usuários aos departamentos apropriados
3. Defina hierarquia departamental se necessário
4. Configure cotas específicas para cada departamento

**Cotas Compartilhadas:**

Departamentos podem ter cotas compartilhadas onde o limite total é dividido entre todos os usuários do departamento. Isso permite flexibilidade no uso enquanto mantém controle sobre o consumo total.

**Relatórios de Uso:**

Gere relatórios detalhados de uso por departamento:

- Consumo atual vs. limite estabelecido
- Usuários com maior consumo
- Tendências de uso ao longo do tempo
- Projeções de necessidade futura

### Alertas e Notificações

Configure sistema abrangente de alertas para monitoramento proativo de cotas.

**Tipos de Alertas:**

- **Alerta de Proximidade**: Quando uso atinge 80% da cota
- **Alerta de Limite**: Quando cota é totalmente consumida
- **Alerta de Excesso**: Quando uso excede cota (se permitido)
- **Alerta de Inatividade**: Quando usuário não utiliza sistema por período prolongado

**Canais de Notificação:**

- Email para usuário e administradores
- Notificações na interface web
- Integração com sistemas de monitoramento externos
- Logs detalhados para auditoria

## Configurações do Sistema

### Parâmetros Gerais

Configure aspectos fundamentais do funcionamento do sistema através da seção "Configurações Gerais".

**Configurações de Aplicação:**

- **Nome da Organização**: Exibido na interface e relatórios
- **Timezone**: Fuso horário para logs e relatórios
- **Idioma Padrão**: Português ou Inglês
- **Tema da Interface**: Claro, escuro ou automático
- **Logo Personalizado**: Upload de logo da empresa

**Configurações de Segurança:**

- **Política de Senhas**: Complexidade mínima, expiração, histórico
- **Timeout de Sessão**: Tempo de inatividade antes de logout automático
- **Tentativas de Login**: Número máximo antes de bloqueio
- **Auditoria**: Nível de detalhamento dos logs de segurança

**Configurações de Performance:**

- **Cache**: Configuração do sistema de cache Redis
- **Conexões de Banco**: Pool de conexões com PostgreSQL
- **Uploads**: Tamanho máximo de arquivo, tipos permitidos
- **Processamento**: Número de workers para processamento paralelo

### Configurações de Email

Configure sistema de email para notificações automáticas.

**Servidor SMTP:**

Configure conexão com servidor de email corporativo:

- **Servidor SMTP**: Endereço do servidor (exemplo: smtp.empresa.com)
- **Porta**: Geralmente 587 para TLS ou 465 para SSL
- **Segurança**: TLS, SSL ou sem criptografia
- **Autenticação**: Usuário e senha para autenticação

**Templates de Email:**

Personalize templates para diferentes tipos de notificação:

- **Alerta de Cota**: Notificação quando usuário se aproxima do limite
- **Dispositivo Offline**: Alerta quando scanner fica indisponível
- **Novo Compartilhamento**: Notificação de arquivo compartilhado
- **Relatório Periódico**: Resumo automático de atividades

### Políticas de Retenção

Configure políticas para gerenciamento automático de dados históricos.

**Retenção de Arquivos:**

- **Arquivos de Usuários**: Tempo de manutenção de arquivos digitalizados
- **Arquivos Compartilhados**: Prazo de validade de links de compartilhamento
- **Arquivos Temporários**: Limpeza automática de arquivos de processamento
- **Backups**: Período de manutenção de backups automáticos

**Retenção de Logs:**

- **Logs de Aplicação**: Período de manutenção de logs operacionais
- **Logs de Auditoria**: Tempo de retenção para conformidade
- **Logs de Acesso**: Histórico de acessos e atividades
- **Logs de Erro**: Registro de erros e problemas do sistema

## Monitoramento e Relatórios

### Dashboard de Monitoramento

O dashboard administrativo oferece visão consolidada do status do sistema.

**Métricas em Tempo Real:**

- **Status dos Serviços**: Backend, frontend, banco de dados, cache
- **Performance do Sistema**: CPU, memória, disco, rede
- **Atividade de Usuários**: Usuários online, digitalizações em andamento
- **Status dos Scanners**: Dispositivos online/offline, erros ativos

**Alertas Ativos:**

Visualize todos os alertas ativos com priorização por criticidade:

- **Críticos**: Falhas de sistema que impedem operação
- **Altos**: Problemas que afetam funcionalidades importantes
- **Médios**: Situações que requerem atenção mas não impedem uso
- **Baixos**: Informações e avisos gerais

### Relatórios Operacionais

Gere relatórios detalhados sobre operação do sistema.

**Relatório de Uso por Usuário:**

- Volume de digitalizações por usuário
- Consumo de armazenamento individual
- Padrões de uso ao longo do tempo
- Comparativo entre usuários e departamentos

**Relatório de Performance de Scanners:**

- Tempo de atividade de cada dispositivo
- Volume de documentos processados
- Frequência de erros e problemas
- Eficiência e utilização dos dispositivos

**Relatório de Sistema:**

- Performance geral da aplicação
- Uso de recursos computacionais
- Estatísticas de acesso e uso
- Tendências de crescimento

### Relatórios de Auditoria

Mantenha conformidade com requisitos de auditoria através de relatórios detalhados.

**Log de Atividades:**

- Todas as ações realizadas por usuários
- Modificações em configurações do sistema
- Acessos e tentativas de acesso
- Operações administrativas executadas

**Relatório de Segurança:**

- Tentativas de acesso não autorizadas
- Alterações em permissões e usuários
- Eventos de segurança detectados
- Conformidade com políticas estabelecidas

## Backup e Recuperação

### Estratégia de Backup

Implemente estratégia robusta de backup para proteção de dados críticos.

**Tipos de Backup:**

- **Backup Completo**: Cópia integral de todos os dados
- **Backup Incremental**: Apenas dados modificados desde último backup
- **Backup Diferencial**: Dados modificados desde último backup completo
- **Backup de Configuração**: Apenas arquivos de configuração e scripts

**Frequência Recomendada:**

- **Backup Completo**: Semanal
- **Backup Incremental**: Diário
- **Backup de Configuração**: Antes de qualquer alteração importante
- **Backup de Emergência**: Antes de atualizações do sistema

### Procedimentos de Backup

Execute backups através de scripts automatizados ou interface administrativa.

**Backup Automático:**

Configure execução automática através do cron:

```bash
# Backup diário às 2h da manhã
0 2 * * * /usr/local/bin/scanner-backup.sh
```

**Backup Manual:**

Para backup manual através da interface:

1. Acesse "Sistema > Backup e Recuperação"
2. Selecione tipo de backup desejado
3. Configure destino do backup (local ou remoto)
4. Execute backup e monitore progresso
5. Verifique integridade do backup criado

**Verificação de Integridade:**

Todos os backups devem ser verificados quanto à integridade:

- Verificação de checksums
- Teste de restauração parcial
- Validação de estrutura de dados
- Confirmação de completude

### Procedimentos de Recuperação

Estabeleça procedimentos claros para recuperação em diferentes cenários.

**Recuperação Completa:**

Para recuperação completa do sistema:

1. Instale sistema base conforme manual de instalação
2. Restaure arquivos de configuração
3. Restaure banco de dados a partir do backup
4. Restaure arquivos de usuários
5. Verifique funcionamento de todos os serviços
6. Execute testes de funcionalidade

**Recuperação Parcial:**

Para recuperação de componentes específicos:

- **Banco de Dados**: Restauração de dump SQL
- **Arquivos de Usuários**: Restauração de arquivos específicos
- **Configurações**: Restauração de arquivos .env e configurações
- **Logs**: Recuperação de histórico de atividades

## Manutenção Preventiva

### Rotinas de Manutenção

Estabeleça rotinas regulares de manutenção para garantir funcionamento otimizado.

**Manutenção Diária:**

- Verificação de logs de erro
- Monitoramento de uso de recursos
- Validação de backups automáticos
- Verificação de status dos scanners

**Manutenção Semanal:**

- Limpeza de arquivos temporários
- Otimização de banco de dados
- Verificação de atualizações de segurança
- Análise de relatórios de performance

**Manutenção Mensal:**

- Revisão de cotas e políticas
- Análise de tendências de uso
- Planejamento de capacidade
- Teste de procedimentos de recuperação

### Otimização de Performance

Mantenha sistema operando com performance otimizada.

**Otimização de Banco de Dados:**

- Reindexação de tabelas principais
- Análise e otimização de queries lentas
- Limpeza de dados históricos desnecessários
- Ajuste de parâmetros de configuração

**Otimização de Sistema:**

- Monitoramento de uso de memória
- Ajuste de parâmetros de cache
- Otimização de configurações de rede
- Balanceamento de carga se aplicável

## Solução de Problemas

### Diagnóstico de Problemas

Utilize ferramentas integradas para diagnóstico eficiente de problemas.

**Logs do Sistema:**

Analise logs para identificar problemas:

```bash
# Logs do backend
docker compose logs backend | grep ERROR

# Logs do frontend
docker compose logs frontend | grep ERROR

# Logs do banco de dados
docker compose logs database | grep ERROR
```

**Ferramentas de Diagnóstico:**

- **Health Check**: Verificação automática de saúde dos serviços
- **Status Dashboard**: Visão geral do status de todos os componentes
- **Métricas de Performance**: Monitoramento de recursos em tempo real
- **Teste de Conectividade**: Verificação de comunicação entre componentes

### Problemas Comuns e Soluções

**Sistema Lento ou Não Responsivo:**

1. Verificar uso de CPU e memória
2. Analisar logs para erros recorrentes
3. Verificar conectividade com banco de dados
4. Reiniciar serviços se necessário

**Erro de Autenticação LDAP:**

1. Testar conectividade com servidor AD
2. Verificar credenciais de bind
3. Validar configuração de DN base
4. Confirmar estrutura do Active Directory

**Scanner Não Descoberto:**

1. Verificar conectividade de rede
2. Testar portas do scanner manualmente
3. Confirmar configuração de firewall
4. Adicionar scanner manualmente se necessário

**Problemas de Cota:**

1. Verificar cálculo de uso atual
2. Validar configuração de limites
3. Analisar logs de atividade do usuário
4. Recalcular cotas se necessário

## Segurança e Conformidade

### Políticas de Segurança

Implemente políticas robustas de segurança para proteção do sistema.

**Controle de Acesso:**

- Princípio do menor privilégio
- Revisão periódica de permissões
- Autenticação multifator quando possível
- Monitoramento de atividades privilegiadas

**Proteção de Dados:**

- Criptografia de dados sensíveis
- Backup seguro e criptografado
- Controle de acesso a arquivos
- Políticas de retenção adequadas

### Auditoria e Conformidade

Mantenha conformidade com regulamentações aplicáveis.

**Logs de Auditoria:**

- Registro completo de todas as atividades
- Proteção contra alteração de logs
- Retenção conforme requisitos legais
- Relatórios de conformidade automáticos

**Revisões de Segurança:**

- Análise periódica de vulnerabilidades
- Teste de procedimentos de segurança
- Revisão de configurações de segurança
- Atualização de políticas conforme necessário

## Conclusão

A administração eficiente do Sistema de Gerenciamento de Scanners requer conhecimento detalhado de suas funcionalidades e implementação de práticas adequadas de manutenção e monitoramento. Este manual fornece as diretrizes necessárias para operação segura e eficiente do sistema em ambiente corporativo.

Para questões específicas ou situações não cobertas neste manual, consulte a documentação técnica adicional ou entre em contato com o suporte técnico especializado.

---

**Autor**: Manus AI  
**Data**: Agosto 2024  
**Versão do Manual**: 1.0

