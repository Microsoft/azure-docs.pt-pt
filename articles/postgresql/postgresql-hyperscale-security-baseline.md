---
title: Linha de base de segurança Azure para Azure Database for PostgreSQL - Hyperscale (Citus)
description: A base de dados Azure para postgresql - hiperescala (Citus) fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cef787cbf8ae38b5b80b63594710fcc21e124c5a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92481079"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale-citus"></a>Linha de base de segurança Azure para Azure Database for PostgreSQL - Hyperscale (Citus)

A Base de Segurança Azure para Base de Dados de Azure para PostgreSQL - Hiperescala (Citus) contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](../security/benchmarks/security-control-network-security.md)*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: A azure Database for PostgreSQL server firewall impede todo o acesso ao seu nó coordenador de Hiperescala (Citus) até especificar quais os computadores que têm permissão. A firewall concede acesso ao servidor com base no endereço IP originário de cada pedido. Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

- [Como configurar as regras de Firewall na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](./concepts-hyperscale-firewall-rules.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados à sua Base de Dados Azure para instâncias PostgreSQL com Azure Policy. Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede da sua Base de Dados Azure para instâncias PostgreSQL.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Para controlar a verificação do plano, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Além disso, ingere troncos via Azure Monitor para agregar dados de segurança gerados pela Hyperscale (Citus). Dentro do Azure Monitor, utilize o log analytics workspace(s) para consultar e realizar análises, e use contas de armazenamento para armazenamento de longo prazo/arquivo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um terceiro Security Incident and Event Management (SIEM). 

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/platform/activity-log.md)

- [Métricas em Hiperescala (Citus)](./concepts-hyperscale-monitoring.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação:** A hiperescala (Citus) fornece métricas para cada nó num grupo de servidores. As métricas dão uma visão do comportamento de apoio aos recursos. Cada métrica é emitida com uma frequência de um minuto, e tem até 30 dias de história.

Para controlar a verificação de registos de auditorias de planos, ative as definições de diagnóstico do Registo de Atividades Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Além disso, ingere troncos via Azure Monitor para agregar dados de segurança gerados pela Hyperscale (Citus). Dentro do Azure Monitor, utilize o log analytics workspace(s) para consultar e realizar análises, e use contas de armazenamento para armazenamento de longo prazo/arquivo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um terceiro Security Incident and Event Management (SIEM). 

- [Métricas em Hiperescala (Citus)](./concepts-hyperscale-monitoring.md)

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/platform/activity-log.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Monitor Azure, para o espaço de trabalho Log Analytics que está a ser utilizado para manter os seus registos de Hiperescala (Citus), desagure o período de retenção de acordo com as normas de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Como definir parâmetros de retenção de registos para log analytics workspaces](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Armazenar registos de recursos numa conta de armazenamento Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos das suas instâncias de Hiperescala (Citus) para comportamento anómalo. Utilize o Azure Monitor's Log Analytics para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Para mais informações sobre o Log Analytics](../azure-monitor/log-query/get-started-portal.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Pode ativar as definições de diagnóstico para Hiperescala (Citus) e enviar registos para um espaço de trabalho log analytics. Pode configurar e receber um alerta baseado em métricas de monitorização dos seus serviços Azure. Utilize o Azure Monitor's Log Analytics para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

A bordo do seu espaço de trabalho Log Analytics para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança.

- [Métricas em Hiperescala (Citus)](./howto-hyperscale-alert-on-metric.md)

- [Como configurar definições de diagnóstico para o registo de atividades azure](../azure-monitor/platform/activity-log.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha um inventário das contas de utilizador que tenham acesso administrativo ao plano de controlo (por exemplo, portal Azure) das suas instâncias de Hiperescala (Citus). Além disso, mantenha um inventário das contas administrativas que tenham acesso ao plano de dados (dentro da própria base de dados) dos seus casos de Hiperescala (Citus).

A Hiperescala (Citus) não suporta o controlo de acesso baseado em funções incorporada, mas pode criar funções personalizadas baseadas em operações específicas de fornecedores de recursos.

Além disso, o motor PostgreSQL utiliza funções para controlar o acesso a objetos de base de dados, e um grupo de servidor hiperescala (Citus) recém-criado vem com várias funções pré-definidas. Para modificar os privilégios do utilizador, utilize comandos PostgreSQL padrão, utilizando uma ferramenta como o PgAdmin ou o PSQL.

- [Compreenda as funções personalizadas para a subscrição do Azure](../role-based-access-control/custom-roles.md) 

- [Compreender a base de dados Azure para operações de fornecedor de recursos postgresQL](../role-based-access-control/resource-provider-operations.md#microsoftdbforpostgresql) 

- [Compreender a gestão de acessos para a Base de Dados Azure para PostgreSQL](./concepts-security.md#access-management)

- [Como criar utilizadores na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](./howto-hyperscale-create-users.md)

- [Como ligar-se ao PostgreSQL - Hiperescala (Citus) usando psql](./quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: A Azure AD não tem o conceito de senhas padrão. Outros recursos Azure que requerem uma palavra-passe forçam a criação de uma palavra-passe com requisitos de complexidade e um comprimento mínimo de senha, que difere consoante o serviço. É responsável por aplicações de terceiros e serviços de marketplace que possam utilizar senhas padrão.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que sejam utilizadas para aceder às suas instâncias de Hiperescala (Citus). As contas de administração para a gestão do recurso Azure estão ligadas ao Azure Ative Directory, existem também contas de administração de servidores locais que existem dentro do grupo de servidores Hyperscale (Citus) para gerir permissões de acesso à base de dados. Utilize a identidade do Centro de Segurança Azure e a gestão de acessos para monitorizar o número de contas administrativas dentro do Diretório Ativo Azure.

- [Compreender a identidade e o acesso do Centro de Segurança Azure](../security-center/security-center-identity-access.md) 

- [Como criar utilizadores na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](./howto-hyperscale-create-users.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Para acesso ao portal Azure, permita a autenticação multi-factor do Diretório Ativo (MFA) e siga as recomendações do Centro de Segurança Azure identity and Access Management.

- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)


**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAWs) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-alert-on-account-login-behavior-deviation"></a>3.7: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize o Azure Ative Directory (AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](../active-directory/identity-protection/overview-identity-protection.md)


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso do portal e do Gestor de Recursos Azure a partir de apenas agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AD) como o sistema central de autenticação e autorização para gerir os recursos PostgreSQL. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Os utilizadores dentro de um grupo de servidores Hyperscale (Citus) não podem ser ligados diretamente às contas do Azure Ative Directory. Para modificar os privilégios do utilizador para o acesso a objetos de base de dados utilize comandos PostgreSQL padrão com ferramentas como PgAdmin ou psql.

- [Modificar os privilégios para funções de utilizador](./howto-hyperscale-create-users.md#how-to-modify-privileges-for-user-role)

- [Como criar e configurar um caso AAD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)



**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Rever e conciliar o acesso a ambos os utilizadores que tenham acesso à base de dados local, bem como através do Azure Ative Directory para gerir os recursos postgreSQL.

Para utilizadores com acesso a gerir recursos de base de dados Azure, reveja os registos do Azure Ative Directory (AD) para ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais que possam ser usadas para aceder à Hyperscale (Citus) e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente, como a cada 90 dias, para garantir que apenas os utilizadores certos têm acesso continuado.

- [Rever utilizadores postgreSQL e funções atribuídas](https://www.postgresql.org/docs/current/database-roles.html)

- [Compreenda a Azure Ad Reporting](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Dentro do Azure Ative Directory (AD), tem acesso a fontes de registo de registo de eventos de registo Azure AD, que lhe permitem integrar-se com qualquer ferramenta SIEM/Monitoring. 

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores do Azure Ative Directory e enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace. 

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize as funcionalidades de Proteção de Identidade do Azure Ative Directory e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas ao nível do Diretório Ativo (AD) do Azure. Pode permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

Você também pode ingerir troncos em Azure Sentinel para mais investigação.

- [Visão geral da Proteção de Identidade AD AZure](../active-directory/identity-protection/overview-identity-protection.md)

- [Como ver a Azure AD a entrar em risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Atualmente não disponível; O Lockbox do cliente ainda não está suportado para Hyperscale (Citus).

- [Lista de serviços suportados pelo Bloqueio do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear casos de Hiperescala (Citus) ou recursos relacionados que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Utilize uma combinação de funções administrativas e regras de firewall para isolar e limitar o acesso à rede à sua Base de Dados Azure para instâncias PostgreSQL.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Compreenda as regras de firewall na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](./concepts-hyperscale-firewall-rules.md)

- [Compreender os papéis em Hiperescala (Citus)](./howto-hyperscale-create-users.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: As ligações de aplicação do cliente ao nó coordenador de Hiperescala (Citus) requerem Segurança da Camada de Transporte (TLS) 1.2. A aplicação das ligações TLS entre o servidor de base de dados e as aplicações do seu cliente ajuda a proteger contra ataques "man-in-the-middle", encriptando o fluxo de dados entre o servidor e a sua aplicação.

Para todas as bases de dados Azure para servidores PostgreSQL a provisionadas através do portal Azure, a aplicação das ligações TLS é ativada por padrão.

Em alguns casos, os pedidos de terceiros requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) para se ligar em segurança.

- [Como configurar o TLS na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](./concepts-hyperscale-ssl-connection-security.md)

- [Aplicações que requerem verificação de certificados para conectividade TLS](./concepts-hyperscale-ssl-connection-security.md)



**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) para controlar o acesso ao plano de controlo Hyperscale (Citus) (por exemplo, portal Azure). O Azure RBAC não afeta as permissões do utilizador dentro da base de dados.

Para modificar os privilégios do utilizador ao nível da base de dados, utilize comandos PostgreSQL padrão, utilizando uma ferramenta como o PgAdmin ou o PSQL.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Como configurar o acesso do utilizador com o SQL para Azure Database for PostgreSQL](./howto-hyperscale-create-users.md)


**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação:**  
Pelo menos uma vez por dia, a Base de Dados Azure para a Hiperescala Pós-SQL (Citus) faz cópias de segurança instantâneas de ficheiros de dados e do registo de transações de bases de dados. As cópias de segurança permitem restaurar um servidor a qualquer ponto no tempo dentro do período de retenção. (O período de retenção é atualmente de 35 dias para todos os clusters.) Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits. A oferta de Hiperescala PostgreSQL (Citus) utiliza chaves geridas pela Microsoft para encriptação.

- [Compreenda a encriptação para backups Azure PostgreSQL - Hyperscale (Citus)](./concepts-hyperscale-backup.md)



**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações em casos de produção de Hiperescala (Citus) e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Atualmente não disponível; O Centro de Segurança Azure ainda não suporta a avaliação de vulnerabilidade para a Base de Dados Azure para PostgreSQL - Hyperscale (Citus).

- [Cobertura de recursos para serviços Azure PaaS no Azure Security Center](../security-center/features-paas.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descubra todos os recursos (incluindo instâncias de Hiperescala (Citus) dentro da sua subscrição( s). Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar tags em instâncias de Hiperescala (Citus) e outros recursos relacionados que dão metadados para organizá-los logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e acompanhar casos de Hiperescala (Citus) e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Utilize a política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s) subscrição.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s) subscrição.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações de recursos dentro de um ambiente de alta segurança, como casos de Hiperescala (Citus) que contêm informações sensíveis.

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para as suas instâncias de Hiperescala (Citus) com a Política Azure. Utilize a Política Azure para criar políticas personalizadas para auditar ou impor a configuração de rede da sua Base de Dados Azure para instâncias PostgreSQL.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo na Notação de Objetos JavaScript (JSON), que deve ser revisto para garantir que as configurações cumprem /excedem os requisitos de segurança para a sua organização. 

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md) 



**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.  Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)



**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições de política Azure personalizadas para as suas instâncias de Hiperescala (Citus) e recursos relacionados, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.  Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)



**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.DBforPostgreSQL" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Utilize a política do Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para a sua Base de Dados Azure para instâncias postgreSQL e recursos relacionados.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: A base de dados Azure para PostgreSQL - Hiperescala (Citus) atualmente não suporta diretamente identidades geridas. Ao criar a Base de Dados Azure para o servidor PostgreSQL, tem de fornecer credenciais para um utilizador administrador. Pode criar funções de utilizador adicionais na interface do portal Azure.

- [Criar uma base de dados Azure para PostgreSQL - Hiperescala (Citus)](./quickstart-create-hyperscale-portal.md#create-an-azure-database-for-postgresql---hyperscale-citus)

- [Criar funções de utilizador adicionais](./howto-hyperscale-create-users.md#how-to-create-additional-user-roles)


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure -- por exemplo, Hyperscale (Citus) -- no entanto, não funciona com conteúdo do cliente.

Pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, etc. A Microsoft não pode aceder aos seus dados nestes casos.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Base de Dados Azure para PostgreSQL – Hyperscale (Citus) cria automaticamente cópias de segurança de cada nó e armazena-as num armazenamento localmente redundante. As cópias de segurança podem ser utilizadas para restaurar o cluster Hyperscale (Citus) num tempo especificado.

- [Como fazer backup e restaurar na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](./concepts-hyperscale-backup.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Pelo menos uma vez por dia, a Azure Database for PostgreSQL retira cópias de segurança instantâneas dos ficheiros de dados e do registo de transações de bases de dados. As cópias de segurança permitem restaurar um servidor a qualquer ponto no tempo dentro do período de retenção. O período de retenção é atualmente de 35 dias para todos os clusters. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits.

Nas regiões de Azure que suportam zonas de disponibilidade, os instantâneos de backup são armazenados em três zonas de disponibilidade. Enquanto pelo menos uma zona de disponibilidade estiver on-line, o cluster Hyperscale (Citus) é ressaltável.

- [Como fazer backup e restaurar na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](./concepts-hyperscale-backup.md)


**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Na Base de Dados Azure para PostgreSQL, restaurar um cluster de Hiperescala (Citus) cria um novo cluster a partir das cópias de segurança originais dos nós. Pode restaurar um aglomerado a qualquer ponto no tempo nos últimos 35 dias. O processo de restauro cria um novo cluster na mesma região de Azure, subscrição e grupo de recursos como o original. A nova configuração do cluster é a mesma que a configuração original do cluster: o mesmo número de nós, número de vCores, tamanho de armazenamento e funções de utilizador.

As definições de firewall e os parâmetros do servidor PostgreSQL não são preservados do grupo de servidor original; são repostos para valores predefinidos. A firewall evitará todas as ligações. Terá de ajustar manualmente estas definições após a restauração.

- [Como fazer backup e restaurar na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](./concepts-hyperscale-backup.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação:** Os aglomerados de hiperescala (Citus) eliminados não podem ser restaurados. Se eliminar o cluster, todos os nós que pertencem ao cluster são apagados e não podem ser recuperados. Para proteger os recursos de cluster pós-implantação de eliminação acidental ou alterações inesperadas, os administradores podem alavancar os bloqueios de gestão.

- [Como fazer backup e restaurar na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](./concepts-hyperscale-backup.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Crie um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente. 

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md) 

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário. 

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas. 

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança. 

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre [as linhas de base de segurança da Azure](../security/benchmarks/security-baselines-overview.md)