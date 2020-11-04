---
title: Linha de base de segurança Azure para Synapse Analytics
description: A linha de base de segurança Synapse Analytics fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 37017ce7dc71b21119765a8146899c0e5ca2e03d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317734"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Linha de base de segurança Azure para Synapse Analytics

A Linha de Base de Segurança Azure para Synapse Analytics contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](/azure/security/benchmarks/security-control-network-security)*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação** : Fixe o seu Azure SQL Server a uma rede virtual via Private Link. O Azure Private Link permite-lhe aceder aos serviços do Azure PaaS sobre um ponto final privado na sua rede virtual. O tráfego entre a sua rede virtual e o serviço percorre a rede de espinha dorsal da Microsoft.

Em alternativa, ao ligar-se à sua piscina Synapse SQL, reduza o âmbito da ligação de saída à base de dados SQL utilizando um grupo de segurança de rede. Desative todo o tráfego de serviço Azure para a base de dados SQL através do ponto final público, definindo Allow Azure Services to OFF. Certifique-se de que não são permitidos endereços IP públicos nas regras de firewall.

* [Compreenda a ligação privada Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [Compreender Link Privado para Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Como criar um NSG com uma configuração de segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação** : Ao ligar-se à sua piscina de SQL dedicada, e tiver ativado registos de fluxo do grupo de segurança da rede (NSG), os registos são enviados para uma Conta de Armazenamento Azure para auditoria de tráfego.

Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

* [Como ativar registos de fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Compreender a Segurança da Rede fornecida pelo Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Como Ativar e utilizar a Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Compreender a Segurança da Rede fornecida pelo Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação** : Não aplicável; esta recomendação destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação** : Utilize proteção contra ameaças avançadas (ATP) para O SQL Azure Synapse. O ATP deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados e pode desencadear vários alertas, tais como" potencial injeção de SQL", e "Acesso a partir de localização invulgar". O ATP faz parte da oferta avançada de segurança de dados (ADS) e pode ser acedido e gerido através do portal central de ADS SQL.

Ativar a Norma de Proteção DDoS nas Redes Virtuais associadas ao Azure Synapse SQL para proteção contra ataques de negação de serviço distribuídos. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

* [Compreenda ATP para Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Como permitir a Segurança Avançada de Dados para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Visão geral do ADS](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Como configurar a proteção DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação** : Ao ligar-se à sua piscina de SQL dedicada, e tiver ativado registos de fluxo do grupo de segurança da rede (NSG), envie registos para uma Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo para um espaço de trabalho do Log Analytics ou transmiti-los para Os Centros de Eventos. Se necessário para investigar a atividade anómala, ative a captura de pacotes do Observador de Rede.

* [Como ativar registos de fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como ativar o Observador de Redes](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação** : Utilize proteção contra ameaças avançadas (ATP) para O SQL Azure Synapse. O ATP deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados e pode desencadear vários alertas, tais como" potencial injeção de SQL", e "Acesso a partir de localização invulgar". O ATP faz parte da oferta avançada de segurança de dados (ADS) e pode ser acedido e gerido através do portal central de ADS SQL. O ATP integra também alertas com o Azure Security Center.

* [Compreenda ATP para Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação** : Não aplicável; esta recomendação destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação** : Utilize etiquetas de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Ao utilizar um ponto final de serviço para o seu pool DE SQL dedicado, é necessário um acesso à base de dados Azure SQL Endereços IP públicos: Os Grupos de Segurança da Rede (NSGs) devem ser abertos ao Azure SQL Database IPs para permitir a conectividade. Pode fazê-lo utilizando etiquetas de serviço NSG para Azure SQL Database.

* [Compreender tags de serviço com pontos finais de serviço para Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

* [Compreender e usar etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação** : Defina e implemente configurações de segurança de rede para recursos relacionados com o seu pool DE SQL dedicado com Azure Policy. Pode utilizar o espaço de nome "Microsoft.Sql" para definir definições de política personalizadas ou utilizar qualquer uma das definições de política incorporadas concebidas para a proteção da base de dados/rede de servidores Azure SQL. Um exemplo de uma política de segurança de rede incorporada aplicável para o servidor Azure SQL Database seria: "O SQL Server deve usar um ponto final de serviço de rede virtual".

Utilize plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de gestão de recursos Azure, controlo de acesso baseado em funções Azure (Azure RBAC), e políticas, numa única definição de planta. Aplique facilmente o projeto em novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar uma Planta Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação** : Utilize etiquetas para grupos de segurança de rede (NSG) e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

* [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação** : Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com o seu pool DE SQL dedicado. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

* [Como visualizar e recuperar eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Como criar alertas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação** : A Microsoft mantém fontes de tempo para os recursos Azure. Pode atualizar a sincronização de tempo para as suas implementações de cálculo.

* [Como configurar a sincronização temporal para os recursos computativos do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação** : Uma política de auditoria pode ser definida para uma base de dados específica ou como uma política de servidor padrão em Azure (que acolhe Azure Synapse). Uma política de servidor aplica-se a todas as bases de dados existentes e recém-criadas no servidor.

Se a auditoria do servidor estiver ativada, aplica-se sempre à base de dados. A base de dados será auditada, independentemente das definições de auditoria da base de dados.

Quando ativa a auditoria, pode escrevê-las num registo de auditoria na sua Conta de Armazenamento Azure, no espaço de trabalho do Log Analytics ou no Event Hubs.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

* [Como configurar a auditoria para os seus recursos Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação** : Ative a auditoria ao nível do servidor Azure SQL para a sua piscina DE SQL dedicada e escolha um local de armazenamento para os registos de auditoria (Azure Storage, Log Analytics ou Event Hubs).

A auditoria pode ser ativada tanto na base de dados como no nível do servidor, e sugere-se que seja ativada apenas ao nível do servidor, a menos que exija configurar um lavatório de dados separado ou retenção para uma base de dados específica.

* [Como permitir a auditoria para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)

* [Como ativar a auditoria para o seu servidor](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

* [Diferenças nas políticas de auditoria ao nível do servidor vs. nível de base de dados](https://docs.microsoft.com/azure/sql-database/sql-database-auditing#server-vs-database-level)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação** : Não aplicável; este referencial destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação** : Ao armazenar registos relacionados com a sua piscina DE SQL dedicada numa Conta de Armazenamento, Log Analytics ou centros de eventos, desempate o período de retenção de registos de acordo com os regulamentos de conformidade da sua organização.

* [Gerir o ciclo de vida do Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

* [Como definir parâmetros de retenção de registo num espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Capture eventos de streaming em Centros de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação** : Analise e monitorize registos para comportamentos anómalos e reveja regularmente os resultados. Utilize proteção de ameaças avançadas para a base de dados Azure SQL em conjunto com o Azure Security Center para alertar sobre atividades incomuns relacionadas com a sua base de dados SQL. Em alternativa, configurar alertas com base em valores métricos ou entradas de Registo de Atividade Azure relacionadas com a sua base de dados SQL.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

* [Compreender a Proteção Avançada de Ameaças e alertar para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Como permitir a Segurança Avançada de Dados para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Como configurar alertas personalizados para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0&preserve-view=true)

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação** : Utilize proteção de ameaças avançadas (ATP) para a Base de Dados Azure SQL em conjunto com o Azure Security Center para monitorizar e alertar sobre a atividade anómala. O ATP faz parte da oferta avançada de segurança de dados (ADS) e pode ser acedido e gerido através do SQL ADS central no portal. A ADS inclui funcionalidades para descobrir e classificar dados sensíveis, surgir e mitigar potenciais vulnerabilidades de bases de dados e detetar atividades anómalas que possam indicar uma ameaça à sua base de dados.

Em alternativa, pode ativar e a bordo dados para a Azure Sentinel.

* [Compreender a Proteção Avançada de Ameaças e alertar para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Como permitir a Segurança Avançada de Dados para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Como gerir alertas no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação** : Não aplicável; para recursos relacionados com o seu pool DE SQL dedicado, a solução anti-malware é gerida pela Microsoft na plataforma subjacente.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação** : Não aplicável; nenhum registo DNS é produzido por recursos relacionados com a sua piscina SQL dedicada.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação** : Não aplicável; a auditoria da linha de comando não é aplicável ao SQL Azure Synapse.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](/azure/security/benchmarks/security-control-identity-access-control)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação** : Os utilizadores são autenticados com o Azure Ative Directy ou com a Autenticação SQL.

Quando implementar pela primeira vez o Azure SQL, especifique um login de administração e uma palavra-passe associada para esse login. Esta conta administrativa chama-se administrador do Servidor. Pode identificar as contas do administrador de uma base de dados abrindo o portal Azure e navegando no separador de propriedades do seu servidor ou caso gerido. Também pode configurar uma conta administrada Azure AD com permissões administrativas completas, isto é necessário se quiser ativar a autenticação do Azure Ative Directory.

Para as operações de gestão, utilize as funções incorporadas do Azure que devem ser explicitamente atribuídas. Utilize o módulo Azure AD PowerShell para realizar consultas ad-hoc para descobrir contas que são membros de grupos administrativos.

* [Autenticação para Base de Dados SQL](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

* [Criar contas para utilizadores não administrativos](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

* [Utilize uma conta Azure Ative Directory para autenticação](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

* [Como obter um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

* [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

* [Como gerir logins e contas de administração existentes no Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Funções incorporadas do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação** : O Azure Ative Directory não tem o conceito de palavras-passe padrão. Ao a provisionar uma piscina SQL dedicada, recomenda-se que opte por integrar a autenticação com o Azure Ative Directory. Com este método de autenticação, o utilizador submete um nome de conta de utilizador e solicita que o serviço utilize as informações credenciais armazenadas no Diretório Azure Ative (Azure AD).

* [Como configurar e gerir a autenticação do Azure Ative Directory com a Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#active-directory-password-authentication)

* [Compreender a autenticação em Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação** : Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas que assinam através do Diretório Ativo Azure.

Para identificar as contas do administrador de uma base de dados, abra o portal Azure e navegue no separador Propriedades do seu servidor ou caso gerido.

* [Compreender a identidade e o acesso do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Como gerir logins e contas de administração existentes no Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação** : Utilize um registo de aplicações Azure (principal serviço) para recuperar um token que pode ser usado para interagir com o seu armazém de dados no plano de controlo (portal Azure) através de chamadas API.

* [Como chamar Azure REST APIs](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Como registar a sua aplicação ao cliente (principal serviço) com a Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informações da Azure Synapse REST API](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação** : Ativar o Azure Ative Directory (AD) Autenticação multi-factor (MFA) e seguir as recomendações do Azure Security Center Identity and Access Management.

* [Como permitir o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Compreender mFA em Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/authentication-mfa-ssms-overview)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação** : Utilize uma estação de trabalho de acesso privilegiada (PAW) com autenticação multi-factor (MFA) configurada para iniciar sessão e configurar recursos Azure.

* [Saiba mais sobre estações de trabalho de acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Como permitir o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação** : Utilize relatórios de segurança do Azure Ative Directory para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Utilize a Proteção Avançada de Ameaças para a Base de Dados Azure SQL em conjunto com o Azure Security Center para detetar e alertar sobre atividades anómalas que indiquem tentativas incomuns e potencialmente prejudiciais de aceder ou explorar bases de dados.

A auditoria do SQL Server permite criar auditorias de servidores, que podem conter especificações de auditoria de servidores para eventos de nível de servidor e especificações de auditoria de bases de dados para eventos de nível de base de dados. Os eventos auditados podem ser escritos nos registos do evento ou para auditar ficheiros.

* [Como identificar utilizadores de AD Azure sinalizados para atividade de risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Reveja a Proteção Avançada de Ameaças e potenciais alertas](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts)

* [Compreender logins e contas de utilizador no Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

* [Compreender a auditoria do servidor SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver15&preserve-view=true)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação** : Utilize locais nomeados de acesso condicional para permitir o acesso ao Portal e à Gestão de Recursos Azure a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação** : Crie um administrador Azure Ative Directory (AD) para o servidor Azure SQL Database na sua piscina DE SQL dedicada.

* [Como configurar e gerir a autenticação AD da Azure com o Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Como criar e configurar uma instância AD Azure](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação** : O Azure Ative Directory fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize avaliações de acesso ao Azure Ative Directory para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso dos utilizadores pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

Ao utilizar a autenticação SQL, crie utilizadores de bases de dados contidos na base de dados. Certifique-se de que coloca um ou mais utilizadores de bases de dados numa função de base de dados personalizada com permissões específicas adequadas a esse grupo de utilizadores.

* [Como utilizar comentários de acesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Compreender logins e contas de utilizador no Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação** : Configurar a autenticação do Azure Ative Directory (AD) com o Azure SQL e permitir configurações de diagnóstico para contas de utilizador do Azure Ative Directory, enviando os registos de auditoria e registos de inscrição para um espaço de trabalho log Analytics. Configure os alertas desejados dentro do Log Analytics.

Ao utilizar a autenticação SQL, crie utilizadores de bases de dados contidos na base de dados. Certifique-se de que coloca um ou mais utilizadores de bases de dados numa função de base de dados personalizada com permissões específicas adequadas a esse grupo de utilizadores.

* [Como utilizar comentários de acesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Como configurar e gerir a autenticação AZure AD com base de dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Como integrar os Registos de Atividades do Azure no Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Compreender logins e contas de utilizador no Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação** : Utilize o Azure Ative Directory (Azure AD) Proteção de Identidade e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Além disso, você pode a bordo e ingerir dados em Azure Sentinel para mais investigação.

Ao utilizar a autenticação SQL, crie utilizadores de bases de dados contidos na base de dados. Certifique-se de que coloca um ou mais utilizadores de bases de dados numa função de base de dados personalizada com permissões específicas adequadas a esse grupo de utilizadores.

* [Como ver os sign-ins de risco Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Como configurar e permitir políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-data-sources)

* [Compreender logins e contas de utilizador no Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação** : Em cenários de suporte em que a Microsoft precisa de aceder a dados relacionados com a Base de Dados Azure SQL no seu pool de SQL dedicado, o Azure Customer Lockbox fornece uma interface para que você reveja e aprove ou rejeite pedidos de acesso a dados.

* [Compreender o bloqueio do cliente](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação** : Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

A &amp; Classificação da Descoberta de Dados é incorporada no Azure Synapse SQL. Fornece capacidades avançadas para descobrir, classificar, rotular e reportar os dados sensíveis nas suas bases de dados.

* [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Compreender classificação de descoberta de dados &amp;](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação** : Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os recursos devem ser separados por rede/sub-rede virtuais, marcados adequadamente e protegidos dentro de um grupo de segurança de rede ou Azure Firewall. Os recursos que armazenam ou processam dados sensíveis devem ser isolados. Utilizar Link Privado; implementar o seu Azure SQL Server dentro de uma rede virtual e ligar-se de forma segura usando o Link Privado.

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Como configurar o Link Privado para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação** : Para qualquer Base de Dados Azure SQL na sua piscina DE SQL dedicada armazenando ou processando informações sensíveis, marque a base de dados e recursos relacionados como sensíveis usando tags. Configure o Private Link em conjunto com as etiquetas de serviço do Grupo de Segurança da Rede (NSG) nas suas instâncias de Base de Dados Azure SQL para impedir a exfiltração de informações sensíveis.

Adicionalmente, a Advanced Threat Protection for Azure SQL Database, Azure SQL Managed Instance e Azure Synapse deteta atividades anómalas que indiquem tentativas incomuns e potencialmente prejudiciais de aceder ou explorar bases de dados.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

* [Como configurar o Private Link e os NSGs para evitar a exfiltração de dados nas suas instâncias da Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Compreender a Proteção Avançada de Ameaças para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview)

* [Compreender a proteção de dados do cliente em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação** : A Azure SQL Database protege os seus dados encriptando dados em movimento com a Segurança da Camada de Transporte. O SQL Server aplica sempre a encriptação (SSL/TLS) para todas as ligações. Isto garante que todos os dados são encriptados "em trânsito" entre o cliente e o servidor, independentemente da definição de Encrypt ou TrustServerCertificate na cadeia de ligação.

* [Compreender a encriptação Azure SQL em Trânsito](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação** : Utilize a função Azure Synapse SQL Data Discovery &amp; Classification. A Data Discovery &amp; Classification fornece capacidades avançadas incorporadas na Base de Dados Azure SQL para descobrir, classificar, rotular &amp; protegendo os dados sensíveis nas suas bases de dados.

A Data Discovery &amp; Classification faz parte da oferta avançada de Segurança de Dados, que é um pacote unificado para capacidades avançadas de segurança SQL. A classificação da descoberta &amp; de dados pode ser acedida e gerida através do portal central SQL ADS.

Além disso, pode configurar uma política dinâmica de mascaramento de dados (DDM) no portal Azure. As recomendações do DDM sinalizam certos campos da sua base de dados como campos potencialmente sensíveis, que podem ser bons candidatos para mascarar.

* [Como utilizar a descoberta e classificação de dados para o Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

* [Compreenda a máscara dinâmica de dados para o Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação** : Utilize o controlo de acesso baseado em funções (Azure RBAC) para gerir o acesso às bases de dados Azure SQL na sua piscina de SQL dedicada.

A autorização é controlada pelos membros da função de base de dados da sua conta de utilizador e permissões de nível de objeto. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários.

* [Como integrar o Azure SQL Server com o Azure Ative Directory para autenticação](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)

* [Como controlar o acesso no Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)

* [Compreender a autorização e a autenticação em Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação** : Não aplicável; A Microsoft gere a infraestrutura subjacente ao Azure Synapse SQL e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

* [Compreender a proteção de dados do cliente em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação** : A encriptação de dados transparente (TDE) ajuda a proteger o SQL do Azure Synapse contra a ameaça de atividade offline maliciosa, encriptando dados em repouso. Realiza a encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações inativos e não carece de alterações à aplicação. Em Azure, a definição padrão para TDE é que o DEK está protegido por um certificado de servidor incorporado. Em alternativa, pode utilizar o suporte TDE gerido pelo cliente, também referido como Suporte Bring Your Own Key (BYOK) para o TDE. Neste cenário, o TDE Protetor que encripta o DEK é uma chave assimétrica gerida pelo cliente, que é armazenada num cofre de chaves Azure (sistema de gestão de chaves externo baseado na nuvem do Azure) e nunca sai do cofre chave.

* [Compreender encriptação de dados transparentes gerida pelo serviço](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal)

* [Compreender encriptação de dados transparentes gerida pelo cliente](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [Como ligar o TDE usando a sua própria chave](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação** : Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção de piscinas SQL da Synapse e outros recursos críticos ou relacionados.

Além disso, pode configurar alertas para bases de dados na sua piscina SQL Synapse utilizando o portal Azure. Os alertas podem enviar-lhe um e-mail ou chamar um gancho web quando alguma métrica (por exemplo, tamanho da base de dados ou utilização do CPU) atinge o limiar.

* [Como criar alertas para eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Como criar alertas para a Azure SQL Synapse](https://docs.microsoft.com/azure/azure-sql/database/alerts-insights-configure-portal)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação** : Ativar a Segurança Avançada de Dados e seguir recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nas suas bases de dados Azure SQL.

* [Como executar avaliações de vulnerabilidade nas suas bases de dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [Como permitir a Segurança Avançada de Dados](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação** : Avaliação de Vulnerabilidade é um serviço de digitalização incorporado no Azure Synapse SQL. O serviço emprega uma base de conhecimento de regras que sinalizam vulnerabilidades de segurança. Destaca desvios das melhores práticas, tais como configurações erradas, permissões excessivas e dados sensíveis desprotegidos. A Avaliação de Vulnerabilidades pode ser acedida e gerida através do portal central sql Advanced Data Security (ADS).

* [Gestão e avaliação da vulnerabilidade das exportações no portal SQL ADS](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação** : Utilize as classificações de risco predefinidas (Pontuação Segura) fornecidas pelo Azure Security Center.

A &amp; Classificação da Descoberta de Dados é incorporada no Azure Synapse SQL. Fornece capacidades avançadas para descobrir, classificar, rotular e reportar os dados sensíveis nas suas bases de dados.

* [Compreenda a pontuação segura do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

* [Compreender classificação de descoberta de dados &amp;](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação** : Utilize o Azure Resource Graph para consultar e descubra todos os recursos relacionados com a sua piscina SQL dedicada dentro da sua subscrição. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

* [Como criar consultas com gráfico de recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Como ver as suas subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true)

* [Compreender Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação** : Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

* [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação** : Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação** : Defina uma lista de recursos Azure aprovados relacionados com a sua piscina SQL dedicada.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação** : Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro das suas subscrições. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar consultas com gráfico de recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação** : Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.s. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação** : Não aplicável; esta recomendação destina-se a aplicações em execução de recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação** : Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação** : Qualquer recurso relacionado com o seu pool DE SQL dedicado que seja necessário para operações comerciais, mas que possa incorrer em maior risco para a organização, deve ser isolado dentro da sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um Azure Firewall ou Grupo de Segurança de Rede.

* [Como criar uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Como criar um NSG com um config de segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação** : Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Sql" para criar políticas personalizadas para auditar ou impor a configuração de recursos relacionados com o seu pool de SQL dedicado. Também pode utilizar definições de política incorporadas para Azure Database/Server, tais como:
- Implementar deteção de ameaças em servidores SQL
- O SQL Server deve utilizar um ponto final de serviço de rede virtual

* [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true)

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação** : Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender efeitos da política do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação** : Se utilizar definições personalizadas da Política Azure, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

* [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

* [Documentação de Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação** : Não aplicável; O Azure Synapse SQL não tem configurações de segurança configuráveis.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação** : Aproveite o Centro de Segurança Azure para realizar pesquisas de base para quaisquer recursos relacionados com a sua piscina SQL dedicada.

* [Como remediar recomendações no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação** : A Encriptação de Dados Transparente (TDE) com chaves geridas pelo cliente no Cofre de Chaves Azure permite encriptar a Chave de Encriptação de Base de Dados gerada automaticamente (DEK) com uma chave assimétrica gerida pelo cliente chamada TDE Protetor. Isto também é geralmente referido como suporte Bring Your Own Key (BYOK) para encriptação de dados transparente. No cenário BYOK, o TDE Protetor é armazenado num Cofre chave Azure propriedade do cliente e gerido. Além disso, certifique-se de que a eliminação suave está ativada no Cofre da Chave Azure.

* [Como ativar o TDE com a chave gerida pelo cliente a partir do Cofre da Chave Azure](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure?tabs=azure-powershell)

* [Como permitir a eliminação suave no Cofre da Chave Azure](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação** : Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo Azure Key Vault, sem quaisquer credenciais no seu código.

* [Tutorial: Utilizar uma identidade gerida atribuída pelo sistema numa VM do Windows para aceder ao SQL do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql)

* [Como configurar identidades geridas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação** : Implementar o Scanner credencial para identificar credenciais dentro do seu código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](/azure/security/benchmarks/security-control-malware-defense)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação** : O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Synapse SQL); no entanto, não funciona com conteúdo do cliente.

Pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, Azure SQL Server, etc. A Microsoft não pode aceder aos seus dados nestes casos.

* [Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](/azure/security/benchmarks/security-control-data-recovery)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação** : As fotos da sua piscina DEDICADA SQL são tomadas automaticamente ao longo do dia criando pontos de restauração que estão disponíveis durante sete dias. Este período de retenção não pode ser alterado. A piscina dedicada SQL suporta um objetivo de oito horas de ponto de recuperação (RPO). Pode restaurar o seu armazém de dados na região primária a partir de qualquer um dos instantâneos tirados nos últimos sete dias. Tenha em atenção que também pode ativar manualmente as imagens, se necessário.

* [Backup e restauro em piscina SQL dedicada](/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação** : As imagens do seu armazém de dados são automaticamente tomadas ao longo do dia criando pontos de restauro que estão disponíveis durante sete dias. Este período de retenção não pode ser alterado. A piscina dedicada SQL suporta um objetivo de oito horas de ponto de recuperação (RPO). Pode restaurar o seu armazém de dados na região primária a partir de qualquer um dos instantâneos tirados nos últimos sete dias. Tenha em atenção que também pode ativar manualmente as imagens, se necessário.

Se estiver a utilizar uma chave gerida pelo cliente para encriptar a chave de encriptação da base de dados, certifique-se de que a sua chave está a ser apoiada.

* [Backup e restauro em piscina SQL dedicada](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Como apoiar as teclas do Cofre da Chave Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Compartilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação:** Teste periodicamente os seus pontos de restauro para garantir que as suas imagens são válidas. Para restaurar uma piscina SQL dedicada existente a partir de um ponto de restauração, você pode usar o portal Azure ou PowerShell. Teste de restauração de chaves geridas pelo cliente.

* [Como restaurar as teclas do Cofre da Chave Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true)

* [Backup e restauro em piscina SQL dedicada](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Como restaurar uma piscina SQL dedicada existente](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação** : Na Base de Dados Azure SQL, pode configurar uma base de dados única ou agimiutada com uma política de retenção de backup de longo prazo (LTR) para reter automaticamente as cópias de segurança da base de dados em recipientes de armazenamento separados Azure Blob por um período máximo de 10 anos. Os dados no Azure Storage são encriptados e desencriptados de forma transparente utilizando encriptação AES de 256 bits, uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2.

Por predefinição, os dados numa conta de armazenamento são encriptados com as teclas geridas pela Microsoft. Pode confiar nas teclas geridas pela Microsoft para a encriptação dos seus dados, ou pode gerir a encriptação com as suas próprias chaves. Se estiver a gerir as suas próprias chaves com o Key Vault, certifique-se de que a eliminação suave está ativada.

* [Gerir a Azure SQL Database retenção de backup a longo prazo](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure)

* [Azure Storage encryption for data at rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (Encriptação do Armazenamento do Azure para dados inativos)

* [Como permitir a eliminação suave no Cofre de Chaves](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure** : N/A

**Responsabilidade** : Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação** : Certifique-se de que existem planos escritos de resposta a incidentes que definem funções de pessoal, bem como fases de tratamento/gestão de incidentes.

* [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação** : O Centro de Segurança atribui uma gravidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que quando um recurso estiver comprometido, possa chegar imediatamente ao mesmo. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

* [Alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação** : Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

* [Pode consultar a publicação do NIST: Guide to Test, Training e Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação** : As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação** : Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Sentinel.

* [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Como transmitir alertas para o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação** : Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

* [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação** : * [Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridos pela Microsoft em nuvem, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança da Azure](/azure/security/benchmarks/security-baselines-overview)
