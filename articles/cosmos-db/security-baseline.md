---
title: Linha de Base de Segurança Azure para Cosmos DB
description: Linha de Base de Segurança Azure para Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b475e9c6b1a186d72a37d38633c500ee62e95ff6
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805652"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Linha de Base de Segurança Azure para Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Linha de Base de Segurança Azure para a Cosmos DB contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, consulte [Controlo de Segurança: Segurança da Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: Ao utilizar o Azure Private Link, pode ligar-se a uma conta Azure Cosmos através de um Ponto Final Privado. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Também pode reduzir o risco de exfiltração de dados configurando um conjunto rigoroso de regras de saída num grupo de segurança de rede (NSG) e associando esse NSG à sua sub-rede.

Também pode utilizar o Service Endpoints para proteger a sua conta Azure Cosmos. Ao ativar um Ponto Final de Serviço, pode configurar contas Azure Cosmos para permitir o acesso a partir de apenas uma sub-rede específica de uma rede virtual Azure. Uma vez ativado o Endpoint de Serviço DB Azure Cosmos, pode limitar o acesso a uma conta Azure Cosmos com ligações a partir de uma sub-rede numa rede virtual.

Também pode proteger os dados armazenados na sua conta Azure Cosmos utilizando firewalls IP. O Azure Cosmos DB suporta controlos de acesso baseados em IP para suporte a firewall de entrada. Pode configurar uma firewall IP na conta Azure Cosmos utilizando os modelos do portal Azure, do Gestor de Recursos Azure ou através do Azure CLI ou da Azure PowerShell.

Visão geral do Link Privado Azure: https://docs.microsoft.com/azure/private-link/private-link-overview

Como configurar um ponto final privado para a Azure Cosmos DB:  https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Como criar um Grupo de Segurança de Rede com um Config de Segurança:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como configurar firewall IP em Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICs

**Orientação**: Utilize o Azure Security Center e siga as recomendações de proteção da rede para ajudar a proteger os recursos de rede relacionados com a sua conta Azure Cosmos.

Quando as máquinas virtuais são implantadas na mesma rede virtual que a sua conta Azure Cosmos, pode utilizar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento Azure para auditorias de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Compreender a Segurança da Rede fornecida pelo Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Como ativar os registos de fluxo NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como Ativar e utilizar a Análise de Tráfego: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Utilize a funcionalidade De Partilha de Recursos de Origem Cruzada (CORS) para permitir que uma aplicação web em execução sob um domínio aceda a recursos noutro domínio. Os navegadores da Web implementam uma restrição de segurança conhecida como política de mesma origem que impede uma página web de chamar APIs em um domínio diferente. No entanto, o CORS fornece uma forma segura de permitir que o domínio de origem chame APIs em outro domínio. Depois de ativar o suporte DO CORS para a sua conta Azure Cosmos, apenas são avaliados pedidos autenticados para determinar se são permitidos de acordo com as regras especificadas.

Configure a partilha de recursos de origem cruzada: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Utilize proteção contra ameaças avançadas (ATP) para Azure Cosmos DB . ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente prejudiciais de aceder ou explorar contas Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças e integrá-las com sistemas centrais de monitorização de segurança.

Ativar o DDoS Protection Standard nas Redes Virtuais associadas às suas instâncias DB do Azure Cosmos para se proteger contra ataques de DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

Como configurar a Azure Cosmos DB Advanced Threat Protection: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Como configurar a proteção DDoS: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Compreenda a Inteligência Integrada de Ameaças do Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Ativar os registos de fluxo do grupo de segurança da rede (NSG) e enviar registos para uma conta de armazenamento para auditoria de tráfego. Pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar os registos de fluxo NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como Ativar e utilizar a Análise de Tráfego: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Utilize proteção contra ameaças avançadas (ATP) para Azure Cosmos DB. ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente prejudiciais de aceder ou explorar contas Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças e integrá-las com sistemas centrais de monitorização de segurança. 

Como configurar a Proteção Avançada de Ameaças da Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; a recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso à sua conta Azure Cosmos, utilize tags de serviço de Rede Virtual para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, AzureCosmosDB) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Para mais informações sobre a utilização de etiquetas de serviço: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Definir e implementar configurações de segurança padrão para recursos de rede com a Política Azure. Use pseudónimos da Azure Policy nos espaços de nome "Microsoft.DocumentDB" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus exemplos DB do Azure Cosmos. Você também pode fazer uso de definições políticas incorporadas para Azure Cosmos DB, tais como:

- Implementar proteção avançada de ameaças para contas DB cosmos

- Cosmos DB deve usar um ponto final de serviço de rede virtual

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em funções Azure (Azure RBAC), e políticas numa única definição de planta. Pode aplicar facilmente o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar uma Planta Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos de rede associados à sua implantação DB Azure Cosmos de forma a organizá-las logicamente numa taxonomia.

Como criar e utilizar tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as suas instâncias DB do Azure Cosmos. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede. 

Como visualizar e recuperar eventos de Registo de Atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como o Azure Cosmos DB para os timetamps nos registos.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados pela Azure Cosmos DB. Dentro do Azure Monitor, utilize o log analytics workspace(s) para consultar e realizar análises, e use contas de armazenamento para armazenamento de longo prazo/arquivo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um terceiro Security Incident and Event Management (SIEM). 

Como ativar registos de diagnóstico para Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Como embarcar Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ative as definições de diagnóstico para Azure Cosmos DB e envie os registos para um espaço de trabalho ou conta de armazenamento do Log Analytics. As definições de diagnóstico em Azure Cosmos DB são usadas para recolher registos de recursos. Estes registos são capturados por pedido e também são referidos como "registos de data plane". Alguns exemplos das operações do plano de dados incluem eliminar, inserir e ler. Também pode ativar as Definições de Diagnóstico do Registo de Atividade do Azure e enviá-las para o mesmo espaço de trabalho do Log Analytics.

Como ativar as definições de diagnóstico para Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Como ativar as definições de diagnóstico para o registo de atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados aos seus casos de DB Azure Cosmos de acordo com os regulamentos de conformidade da sua organização.

Como definir parâmetros de retenção de registos: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Pode realizar consultas no Log Analytics um espaço de trabalho para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações baseadas nos registos DB do Azure Cosmos que recolheu.

Como realizar consultas para Azure Cosmos DB em Log Analytics Workspaces: https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: No Centro de Segurança Azure, permita a Proteção Avançada de Ameaças para a Azure Cosmos DB monitorizar atividades anómalas em registos de segurança e eventos. Ativar as definições de diagnóstico em Azure Cosmos DB e enviar registos para um espaço de trabalho log analytics.

 

Também pode embarcar no seu espaço de trabalho Log Analytics para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança. Além disso, pode criar alertas de registo personalizados no seu espaço de trabalho Log Analytics utilizando o Azure Monitor.

Lista de alertas de proteção contra ameaças para a Azure Cosmos DB: https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Como embarcar Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Criar, visualizar e gerir alertas de registo utilizando o Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável; A Azure Cosmos DB não processa nem produz registos relacionados com anti-malware.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; A Azure Cosmos DB não processa nem produz registos relacionados com DNS.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Pode utilizar o painel de controlo de identidade e acesso (IAM) no portal Azure para configurar o controlo de acesso baseado em funções Azure (Azure RBAC) e manter o inventário nos recursos DB do Azure Cosmos. As funções são aplicadas aos utilizadores, grupos, diretores de serviços e identidades geridas no Ative Directory. Você pode usar papéis incorporados ou papéis personalizados para indivíduos e grupos.

A Azure Cosmos DB fornece Azure RBAC incorporado para cenários comuns de gestão em Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Ative Directory (AD) pode atribuir estas funções Azure a utilizadores, grupos, diretores de serviços ou identidades geridas para conceder ou negar o acesso a recursos e operações em recursos DB da Azure Cosmos.

Também pode utilizar o módulo Azure AD PowerShell para realizar consultas de adhoc para descobrir contas que são membros de grupos administrativos. 

Além disso, algumas ações em Azure Cosmos DB podem ser controladas com O Diretório Ativo Azure e chaves primárias específicas para a conta.  Utilize a definição de conta 'DisableKeyBasedMetadataWriteAccess' para controlar o acesso à chave.

Compreenda o controlo de acesso baseado em funções da Azure em Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Construa os seus próprios papéis personalizados utilizando ações de DB Azure Cosmos (Microsoft.Docespaço de nome umentDB): https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Criar um novo papel no Azure Ative Directory: https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Como obter um papel de diretório no Azure Ative Directory com a PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true

Como obter membros de um papel de diretório no Azure Ative Directory com a PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true

Restringir o acesso do utilizador apenas às operações de dados: https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O conceito de palavras-passe padrão ou em branco não existe em relação a Azure AD ou Azure Cosmos DB. Em vez disso, a Azure Cosmos DB utiliza dois tipos de chaves para autenticar os utilizadores e fornecer acesso aos seus dados e recursos; chaves primárias e fichas de recursos. As chaves podem ser regeneradas a qualquer momento.

Compreensão do acesso seguro aos dados em Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Como regenerar Azure Cosmos DB Keys: https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Como aceder programáticamente às teclas utilizando o Azure Ative Directory: https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Não aplicável; A Azure Cosmos DB não suporta contas de administrador.  Todo o acesso é integrado com O Diretório Ativo Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: A Azure Cosmos DB utiliza dois tipos de chaves para autorizar os utilizadores e não suporta Sign-On (SSO) ao nível do plano de dados. O acesso ao plano de controlo da Cosmos DB está disponível através da REST API e suporta a SSO. Para autenticar, desacorda o cabeçalho de Autorização para os seus pedidos num Token Web JSON que obtenha do Azure Ative Directory.

Compreenda a Base de Dados Azure para cosmos DB REST API: https://docs.microsoft.com/rest/api/cosmos-db/

Compreender SSO com diretório ativo Azure: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar a autenticação multi-factor do Diretório Ativo Azure e seguir as recomendações do Azure Security Center Identity and Access Management.

Como permitir o MFA em Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multi-factor configurada para iniciar sessão e configurar recursos Azure.

Saiba mais sobre estações de acesso privilegiadas: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Como permitir o MFA em Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize proteção contra ameaças avançadas (ATP) para Azure Cosmos DB. ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente prejudiciais de aceder ou explorar contas Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças e integrá-las com sistemas centrais de monitorização de segurança. 

Além disso, pode utilizar o Azure Ative Directory (AD) Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

Como implementar a Gestão de Identidade Privilegiada (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Compreenda as deteções de risco da AD Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Configure a condição de localização de uma política de acesso condicional e gerencie as suas localizações nomeadas. Com localizações nomeadas, pode criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões. Você pode restringir o acesso a recursos sensíveis, como as suas instâncias DB Azure Cosmos, para os seus locais com nomes configurados.

Como configurar localizações nomeadas em Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Como criar e configurar um exemplo do Azure Ative Directory: https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Como configurar e gerir a autenticação do Azure Ative Directory com a Azure SQL: https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory fornece registos para ajudar a descobrir contas velhas. Além disso, você pode usar Azure Identity Access Reviews para gerir eficientemente membros do grupo, acesso a aplicações empresariais e atribuições de funções. O acesso do utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

Como utilizar comentários sobre o acesso à identidade do Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação**: Pode criar definições de diagnóstico para contas de utilizadores do Azure Ative Directory, enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics onde pode configurar os alertas pretendidos.

Como integrar os Registos de Atividade do Azure no Monitor Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize proteção contra ameaças avançadas (ATP) para Azure Cosmos DB. ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente prejudiciais de aceder ou explorar contas Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças e integrá-las com sistemas centrais de monitorização de segurança. 

Também pode utilizar a funcionalidade de Proteção de Identidade AD AZure e deteções de risco para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Além disso, você pode ingerir troncos em Azure Sentinel para mais investigação.

Como visualizar as entradas de risco do Azure Ative Directory: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e permitir políticas de risco de proteção de identidade: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: O bloqueio do cliente não é aplicável para a Cosmos DB.  Os colaboradores da Microsoft não conseguem aceder aos recursos de dados mesmo com permissões do cliente.

Lista de serviços suportados pelo Customer Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Proteção de Dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear instâncias DB da Azure Cosmos que armazenam ou processam informações sensíveis.

Como criar e utilizar tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. As instâncias DB do Azure Cosmos são separadas por rede/sub-rede virtuais, marcadas adequadamente e protegidas dentro de um grupo de segurança de rede (NSG) ou Azure Firewall. As instâncias DB de Azure Cosmos que armazenam dados sensíveis devem ser isoladas. Ao utilizar o Azure Private Link, pode ligar-se a uma conta de exemplo de Azure Cosmos através de um ponto final privado. O ponto final privado é um conjunto de endereços IP privados numa sub-rede dentro da sua rede virtual. Pode então limitar o acesso aos endereços IP privados selecionados. 

Como criar subscrições adicionais do Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar grupos de gestão: https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como configurar um ponto final privado para a Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Como criar um Grupo de Segurança de Rede com um Config de Segurança: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Pode implementar a Advanced Threat Protection for Azure Cosmos DB, que irá detetar atividades anómalas que indiquem tentativas incomuns e potencialmente prejudiciais de aceder ou explorar bases de dados. Atualmente pode desencadear os seguintes alertas:

- Acesso a partir de locais incomuns

- Extração de dados incomuns

Além disso, ao utilizar máquinas virtuais para aceder às suas instâncias DB do Azure Cosmos, utilize o Private Link, Firewall, grupos de segurança de rede e tags de serviço para mitigar a possibilidade de exfiltração de dados. A Microsoft gere a infraestrutura subjacente à Azure Cosmos DB e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

Como configurar a Proteção Avançada de Ameaças da Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Compreender a proteção de dados do cliente em Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Todas as ligações com o suporte DB da Azure Cosmos HTTPS. AZure Cosmos DB também suporta TLS1.2. É possível impor um mínimo de versão TLS lado do servidor. Para tal, [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) contacte.

Visão geral da Segurança DB da Cosmos: https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades automáticas de identificação, classificação e prevenção de perdas ainda não estão disponíveis para a Azure Cosmos DB. No entanto, pode utilizar a integração da Azure Cognitive Search para classificação e análise de dados. Também pode implementar uma solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Índice Azure Cosmos DB dados com Azure Cognitive Search: https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp ;bc=/azure/cosmos-db/breadcrumb/toc.json

Compreender a proteção de dados do cliente em Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: A Azure Cosmos DB fornece o controlo de acesso baseado em funções incorporada a Azure (Azure RBAC) para cenários comuns de gestão em Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Ative Directory pode atribuir estas funções Azure a utilizadores, grupos, diretores de serviços ou identidades geridas para conceder ou negar o acesso a recursos e operações em recursos DB da Azure Cosmos. As atribuições de funções são apenas para acesso de avião de controlo, o que inclui o acesso às contas da Azure Cosmos, bases de dados, contentores e ofertas (produção).

Como implementar o Azure RBAC em Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

A Microsoft gere a infraestrutura subjacente à Cosmos DB e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

Compreender a proteção de dados do cliente em Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Todos os dados do utilizador armazenados em Cosmos DB são encriptados em repouso por padrão. Não há controlos para desligá-lo. A Azure Cosmos DB utiliza encriptação AES-256 em todas as regiões onde a conta está a funcionar.

Por predefinição, a Microsoft gere as chaves que são usadas para encriptar os dados na sua conta Azure Cosmos. Pode optar opcionalmente por adicionar uma segunda camada de encriptação com as suas próprias teclas.

Compreender a encriptação em repouso com Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Compreender a gestão chave para a encriptação em repouso com Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Como configurar chaves geridas pelo cliente para a sua conta DB Azure Cosmos: https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção do Azure Cosmos DB.

Como criar alertas para eventos de Registo de Atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Como criar alertas para eventos de Registo de Atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para obter mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center para as suas instâncias DB Azure Cosmos. 

A Microsoft executa a correção do sistema e a gestão de vulnerabilidades nos anfitriões subjacentes que suportam as suas instâncias DB Azure Cosmos. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Funcionalidades suportadas disponíveis no Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o portal Azure ou o Azure Resource Graph para descobrir todos os recursos (não limitados à Azure Cosmos DB, mas também incluindo recursos como o cálculo, outros armazenamentos, rede, portas e protocolos, etc.) dentro da sua subscrição(s).  Certifique-se de que tem permissões adequadas no seu inquilino e é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

Como criar consultas com gráfico de recursos Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver as suas Subscrições Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true

Compreender o controlo de acesso baseado em funções da Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nas suas instâncias DB do Azure Cosmos e recursos relacionados com metadados para organizá-los logicamente numa taxonomia.

Como criar e utilizar tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Que Azure Cosmos DB recursos suporte tags: https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e subscrições separadas, se for caso disso, para organizar e rastrear ativos, incluindo, mas não se limitando aos recursos DB da Azure Cosmos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Como criar subscrições adicionais do Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão: https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos aprovados da Azure e dos títulos de software

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional e a Azure como um todo.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s) subscrição.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com O Gráfico Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; esta linha de base destina-se a recursos de computação.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional e a Azure como um todo.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos 

- Tipos de recursos permitidos

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azureresources através de scripts

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações aos recursos num ambiente de alta segurança.

Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta orientação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte [Controlo de Segurança: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para as suas instâncias de DB cosmos com a Política Azure. Use pseudónimos da Política Azure no espaço de nomes "Microsoft.DocumentDB" para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de Cosmos DB. Você também pode fazer uso de definições políticas incorporadas para Azure Cosmos DB, tais como:

- Implementar proteção avançada de ameaças para contas DB cosmos

- Cosmos DB deve usar um ponto final de serviço de rede virtual

Como visualizar pseudónimos disponíveis da Política Azure: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreenda os efeitos da política do Azure: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure para o seu Cosmos DB ou recursos relacionados, utilize a Azure Repos para armazenar e gerir o seu código de forma segura.

Documentação Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=truehttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Use pseudónimos da Política Azure no espaço de nomes "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação**: Use pseudónimos da Política Azure no espaço de nomes "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para as suas instâncias DB Azure Cosmos e recursos relacionados. 

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder às suas instâncias DB Azure Cosmos, use a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger a gestão secreta da Azure Cosmos DB. Certifique-se de que o cofre da chave é excluído suave.

Como integrar-se com identidades geridas aZure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Como autenticar o Cofre de Chaves: https://docs.microsoft.com/azure/key-vault/general/authentication

Como atribuir uma política de acesso ao Cofre chave: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder às suas instâncias DB Azure Cosmos, use a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger a gestão secreta da Azure Cosmos DB.

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente no Azure Ative Directory (AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

Como configurar identidades geridas: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como integrar-se com identidades geridas aZure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

Como configurar o Scanner Credencial: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Controlo de Segurança: Defesa de Malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional. O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure App Service), no entanto não funciona com conteúdo do cliente.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure App Service), no entanto não funciona com conteúdo do cliente.

É da sua responsabilidade pré-digitalizar quaisquer ficheiros que sejam enviados para recursos Azure não computados, incluindo Azure Cosmos DB. A Microsoft não pode aceder aos dados dos clientes e, portanto, não pode realizar análises anti-malware do conteúdo do cliente em seu nome.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; referência destina-se a recursos de computação. O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure, no entanto não funciona com conteúdo do cliente.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Azure Cosmos DB tira fotos dos seus dados a cada quatro horas. Todos os backups são armazenados separadamente num serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. A qualquer momento, apenas os dois últimos instantâneos são mantidos. No entanto, se o contentor ou a base de dados forem eliminados, o Azure Cosmos DB reterá as cópias sombra existentes de um dado contentor ou base de dados durante 30 dias. Contacte o Suporte Azure para restaurar a partir de uma cópia de segurança.

Compreensão das cópias de segurança automatizadas da Azure Cosmos: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: A Azure Cosmos DB recolhe automaticamente cópias de segurança dos seus dados em intervalos regulares. Se a base de dados ou o contentor forem eliminados, pode arquivar um bilhete de suporte ou chamar o suporte do Azure para restaurar os dados a partir de cópias de segurança online automáticas. O suporte Azure está disponível para planos selecionados apenas como Standard, Developer, e planos superiores a eles. Para restaurar uma imagem específica da cópia de segurança, a Azure Cosmos DB requer que os dados estão disponíveis durante a duração do ciclo de backup para esse instantâneo. 

Se utilizar o Key Vault para armazenar credenciais para as suas instâncias de DB cosmos, certifique-se de cópias de segurança automáticas regulares das suas chaves.

Compreenda as cópias de segurança automatizadas da Azure Cosmos: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Como restaurar os dados em Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Como apoiar chaves do cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Se a base de dados ou o contentor forem eliminados, pode arquivar um bilhete de suporte ou chamar o suporte do Azure para restaurar os dados a partir de cópias de segurança online automáticas. O suporte Azure está disponível para planos selecionados apenas como Standard, Developer, e planos superiores a eles. Para restaurar uma imagem específica da cópia de segurança, a Azure Cosmos DB requer que os dados estão disponíveis durante a duração do ciclo de backup para esse instantâneo.

Teste a restauração dos seus segredos armazenados no Cofre da Chave Azure usando o PowerShell. O Restore-AzureKeyVaultKey cmdlet cria uma chave no cofre de chaves especificado. Esta chave é uma réplica da chave de apoio no ficheiro de entrada e tem o mesmo nome que a chave original.

Compreenda as cópias de segurança automatizadas da Azure Cosmos:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Como restaurar os dados em Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Como restaurar os segredos do cofre da chave Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Como todos os dados do utilizador armazenados em Cosmos DB estão encriptados em repouso e no transporte, não é necessário tomar nenhuma ação. Outra forma de colocar isto é que a encriptação em repouso está "on" por padrão. Não há controlos para desligá-lo ou ligar. A Azure Cosmos DB utiliza encriptação AES-256 em todas as regiões onde a conta está a funcionar.

Ativar Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

Compreenda a encriptação de dados em Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Como permitir Soft-Delete no Cofre de Chaves: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, consulte [Controlo de Segurança: Resposta a incidentes.](../security/benchmarks/security-control-incident-response.md)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

Pode também aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação para a construção do seu próprio processo de resposta a incidentes de segurança: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está em encontrar ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

Consulte a publicação do NIST: Guide to Test, Training e Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

Como definir o Contacto de Segurança do Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

Como configurar a exportação contínua: https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a automatização do fluxo de trabalho e as aplicações lógicas: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte [Controlo de Segurança: Testes de penetração e exercícios da equipa vermelha.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft em nuvem, aqui:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre [as Linhas de Base de Segurança Azure](../security/benchmarks/security-baselines-overview.md)
