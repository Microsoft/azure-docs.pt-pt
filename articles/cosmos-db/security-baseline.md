---
title: Linha de Base de Segurança Azure para Cosmos DB
description: Linha de Base de Segurança Azure para Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd2706b6a6880d7c9454619e1315b2b5b5404561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244278"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Linha de Base de Segurança Azure para Cosmos DB

A Linha de Base de Segurança Azure para cosmos DB contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: Utilizando o Link Privado Azure, pode ligar-se a uma conta Azure Cosmos através de um Ponto Final Privado. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Também pode reduzir o risco de exfiltração de dados configurando um conjunto rigoroso de regras de saída num grupo de segurança de rede (NSG) e associando esse NSG à sua sub-rede.

Também pode utilizar pontos finais de serviço para garantir a sua conta Azure Cosmos. Ao ativar um Ponto Final de Serviço, pode configurar contas Azure Cosmos para permitir o acesso a partir de apenas uma subnet específica de uma rede virtual Azure. Uma vez ativado o Ponto final do Serviço Db Azure Cosmos, pode limitar o acesso a uma conta Azure Cosmos com ligações a partir de uma subnet numa rede virtual.

Também pode proteger os dados armazenados na sua conta Azure Cosmos utilizando firewalls IP. A Azure Cosmos DB suporta controlos de acesso baseados em IP para suporte de firewall de entrada. Pode configurar uma firewall IP na conta Azure Cosmos utilizando o portal Azure, modelos do Gestor de Recursos Azure, ou através do Azure CLI ou Azure PowerShell.

Visão geral do link privado azure:https://docs.microsoft.com/azure/private-link/private-link-overview

Como configurar um ponto final privado para o Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Como criar um Grupo de Segurança de Rede com um Config de Segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como configurar firewall IP em Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: Utilize o Azure Security Center e siga as recomendações de proteção da rede para ajudar a proteger os recursos de rede relacionados com a sua conta Azure Cosmos.

Quando as máquinas virtuais são implantadas na mesma rede virtual que a sua conta Azure Cosmos, pode utilizar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ative os registos de fluxo nsg e envie registos numa Conta de Armazenamento Azure para auditorias de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Compreender a Segurança da Rede fornecida pelo Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Como ativar os registos de fluxo nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e utilizar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Utilize a função Cross-Origin Resource Sharing (CORS) para permitir que uma aplicação web que funciona sob um domínio aceda a recursos noutro domínio. Os navegadores da Web implementam uma restrição de segurança conhecida como política de origem mesma que impede uma página web de chamar APIs num domínio diferente. No entanto, o CORS fornece uma forma segura de permitir que o domínio de origem chame APIs em outro domínio. Depois de ativar o suporte cors para a sua conta Azure Cosmos, apenas são avaliados pedidos autenticados para determinar se são permitidos de acordo com as regras que especificou.

Configure partilha de recursos de origem cruzada:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Utilize proteção avançada contra ameaças (ATP) para o Azure Cosmos DB . O ATP para o Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas da Azure Cosmos. Esta camada de proteção permite-lhe lidar com ameaças e integrá-las com sistemas centrais de monitorização de segurança.

Ative o Padrão de Proteção DDoS nas Redes Virtuais associadas aos seus casos De Db Azure Cosmos para se proteger contra ataques DDoS. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.

Como configurar a Proteção avançada de ameaças avançadas do Cosmos Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Como configurar a proteção DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Compreender o Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Ativar os registos de fluxo do grupo de segurança da rede (NSG) e enviar registos para uma conta de armazenamento para auditoria de tráfego. Pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar os registos de fluxo nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e utilizar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Utilize proteção avançada contra ameaças (ATP) para o Azure Cosmos DB. O ATP para o Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas da Azure Cosmos. Esta camada de proteção permite-lhe lidar com ameaças e integrá-las com sistemas centrais de monitorização de segurança. 

Como configurar a Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso à sua conta Azure Cosmos, utilize etiquetas de serviço de Rede Virtual para definir controlos de acesso à rede em seguranças de redeGrupos ou Firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, AzureCosmosDB) no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Para mais informações sobre a utilização de etiquetas de serviço:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede com a Política Azure. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.DocumentDB" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração da rede dos seus casos de DB Do Cosmos do Azure. Você também pode fazer uso de definições políticas incorporadas para Azure Cosmos DB, tais como:

- Implementar Proteção Avançada de Ameaças para Contas DB cosmos

- Cosmos DB deve usar um ponto final de serviço de rede virtual

Também pode utilizar plantas Azure para simplificar as implementações azure em larga escala através da embalagem de artefactos ambientais chave, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em papéis (RBAC) e políticas numa única definição de blueprint. Você pode facilmente aplicar o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos de rede associados à sua implantação do Azure Cosmos DB de forma a organizá-las logicamente numa taxonomia.

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as instâncias do Seu Azure Cosmos DB. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede. 

Como visualizar e recuperar eventos de registo de atividade sinuosa:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para recursos Azure, como o Azure Cosmos DB, para os selos temporais nos registos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Ingerir logs via Azure Monitor para agregar dados de segurança gerados pela Azure Cosmos DB. Dentro do Monitor Azure, utilize o (s) espaço de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de armazenamento para armazenamento a longo prazo/arquivo. Em alternativa, pode ativar e a bordo dados para o Azure Sentinel ou para um incidente de segurança e gestão de eventos de terceiros (SIEM). 

Como ativar registos de diagnóstico para O Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Ative as definições de diagnóstico do Azure Cosmos DB e envie os registos para um espaço de trabalho ou conta de armazenamento de Log Analytics. As definições de diagnóstico em Azure Cosmos DB são usadas para recolher registos de recursos. Estes registos são capturados por pedido e também são referidos como "registos de planos de dados". Alguns exemplos das operações do avião de dados incluem apagar, inserir e ler. Também pode ativar as Definições de Diagnóstico de Registo de Atividade do Azure e enviá-las para o mesmo espaço de trabalho de Log Analytics.

Como ativar as Definições de Diagnóstico para O Cosmos DB do Azure:https://docs.microsoft.com/azure/cosmos-db/logging

Como ativar as Definições de Diagnóstico para o Registo de Atividade seletiva do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: No Monitor Azure, detete o período de retenção de registos para os espaços de trabalho do Log Analytics associados aos seus casos De DB Do Seu Azure Cosmos, de acordo com as regras de conformidade da sua organização.

Como definir parâmetros de retenção de troncos:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação:** Você pode realizar consultas no Log Analytics um espaço de trabalho para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos registos do Azure Cosmos DB que você reuniu.

Como realizar consultas para Azure Cosmos DB em espaços de trabalho de Log Analytics:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: No Centro de Segurança Azure, permitir a Proteção avançada de Ameaças para o Azure Cosmos DB monitorize atividades anómalas em registos e eventos de segurança. Ative as definições de diagnóstico em Azure Cosmos DB e envie registos para um espaço de trabalho de Log Analytics.

 

Também pode embarcar no seu espaço de trabalho Log Analytics para o Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar e utilizar os livros de jogadas (soluções automatizadas) para remediar problemas de segurança. Além disso, pode criar alertas de registo personalizados no seu espaço de trabalho Log Analytics utilizando o Monitor Azure.

Lista de alertas de proteção contra ameaças para o Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Criar, visualizar e gerir alertas de registo utilizando o Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável; A Azure Cosmos DB não processa nem produz registos relacionados com anti-malware.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável; A Azure Cosmos DB não processa nem produz registos relacionados com DNS.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação:** Pode utilizar o painel de controlo de identidade e acesso (IAM) no portal Azure para configurar o controlo de acesso baseado em funções (RBAC) e manter o inventário nos recursos DB da Azure Cosmos. As funções são aplicadas a utilizadores, grupos, diretores de serviços e identidades geridas no Diretório Ativo. Você pode usar papéis incorporados ou papéis personalizados para indivíduos e grupos.

A Azure Cosmos DB fornece RBAC incorporado para cenários de gestão comuns em Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Ative Directory (AD) pode atribuir estas funções rBAC a utilizadores, grupos, diretores de serviços ou identidades geridas para conceder ou negar o acesso a recursos e operações em recursos DB da Azure Cosmos.

Também pode utilizar o módulo Azure AD PowerShell para realizar consultas adhoc para descobrir contas que são membros de grupos administrativos. 

Além disso, algumas ações no Azure Cosmos DB podem ser controladas com o Azure Ative Directory e chaves master específicas da conta.  Utilize a definição da conta 'desactivarKeyBasedMetadataWriteAccess' para controlar o acesso à chave.

Compreender o controlo de acesso baseado em papéis em Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Construa as suas próprias funções personalizadas utilizando as ações db do Azure Cosmos (Microsoft.DocumentDB namespace):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Criar um novo papel no Diretório Ativo do Azure:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Como obter um papel de diretório no Azure Ative Directory com a PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório no Azure Ative Directory com powerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Restringir o acesso dos utilizadores a operações de dados apenas:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: O conceito de senhas padrão ou em branco não existe em relação ao Azure AD ou ao Azure Cosmos DB. Em vez disso, a Azure Cosmos DB utiliza dois tipos de chaves para autenticar os utilizadores e fornecer acesso aos seus dados e recursos; chaves principais e fichas de recursos. As chaves podem ser regeneradas a qualquer momento.

Compreender o acesso seguro aos dados em Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Como regenerar as Chaves DB da Azure Cosmos:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Como aceder programáticamente às teclas utilizando o Diretório Ativo Azure:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Não aplicável; A Azure Cosmos DB não suporta contas de administrador.  Todos os acessos estão integrados com o Azure Ative Directory e o Azure role-based access control (RBAC).



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: O Azure Cosmos DB utiliza dois tipos de teclas para autorizar os utilizadores e não suporta o Single Sign-On (SSO) ao nível do plano de dados. O acesso ao plano de controlo para cosmos DB está disponível via REST API e suporta SSO. Para autenticar, detete tede o cabeçalho de Autorização para os seus pedidos a um Token Web JSON que obtenha do Diretório Ativo Azure.

Compreender a Base de Dados Azure para cosmos DB REST API:https://docs.microsoft.com/rest/api/cosmos-db/

Compreenda o SSO com o Diretório Ativo Azure:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Permitir a autenticação multi-factor do Diretório Ativo azure e seguir as recomendações de Identidade e Gestão de Acesso do Azure Security Center.

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize postos de trabalho de acesso privilegiados (PAW) com autenticação multi-factor configurada para iniciar sessão e configurar os recursos Azure.

Saiba mais sobre postos de trabalho de acesso privilegiados:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize proteção avançada contra ameaças (ATP) para o Azure Cosmos DB. O ATP para o Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas da Azure Cosmos. Esta camada de proteção permite-lhe lidar com ameaças e integrá-las com sistemas centrais de monitorização de segurança. 

Além disso, pode utilizar o Azure Ative Directory (AD) Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente.

Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

Como implementar a Gestão de Identidade Privilegiada (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Compreender as deteções de risco da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Configure o estado de localização de uma política de acesso condicional e gerencie os seus locais nomeados. Com localizações nomeadas, pode criar agrupamentos lógicos de gamas de endereços IP ou países e regiões. Pode restringir o acesso a recursos sensíveis, como os seus casos De Db Do Cosmos, aos seus locais nomeados configurados.

Como configurar localizações nomeadas em Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A Azure AD também sais, hashes e armazena de forma segura credenciais de utilizador.

Como criar e configurar uma instância de Diretório Ativo Azure:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Como configurar e gerir a autenticação do Diretório Ativo Azure com o Azure SQL:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: O Diretório Ativo Azure fornece registos para ajudar a descobrir contas velhas. Além disso, pode utilizar as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso do utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

Como utilizar comentários de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Pode criar definições de diagnóstico para contas de utilizadores do Diretório Ativo do Azure, enviando os registos de auditoria e os registos de log-in para um espaço de trabalho do Log Analytics onde pode configurar os alertas desejados.

Como integrar registos de atividade do Azure no Monitor Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize proteção avançada contra ameaças (ATP) para o Azure Cosmos DB. O ATP para o Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas da Azure Cosmos. Esta camada de proteção permite-lhe lidar com ameaças e integrá-las com sistemas centrais de monitorização de segurança. 

Também pode utilizar a funcionalidade de Proteção de Identidade AD Azure e deteção de riscos para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Além disso, pode ingerir logs no Azure Sentinel para mais investigação.

Como ver os sign-ins de risco do Azure Ative Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Atualmente não disponível; O Customer Lockbox ainda não foi suportado para a Base de Dados Azure para o Cosmos DB.

Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear as instâncias do Azure Cosmos DB que armazenam ou processam informações sensíveis.

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. As instâncias DE BB da Azure Cosmos são separadas por rede virtual/subnet, marcadas adequadamente e protegidas dentro de um grupo de segurança de rede (NSG) ou Azure Firewall. As instâncias da Azure Cosmos DB que armazenam dados sensíveis devem ser isoladas. Ao utilizar o Azure Private Link, pode ligar-se a uma conta de instância Azure Cosmos DB através de um ponto final privado. O ponto final privado é um conjunto de endereços IP privados numa subnet dentro da sua rede virtual. Em seguida, pode limitar o acesso aos endereços IP privados selecionados. 

Como criar subscrições adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar grupos de gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como configurar um ponto final privado para o Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Como criar um Grupo de Segurança de Rede com um Config de Segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Pode implementar proteção avançada de ameaças para o Azure Cosmos DB, que irá detetar atividades anómalas que indiquem tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. Atualmente pode desencadear os seguintes alertas:

- Acesso a partir de locais incomuns

- Extração invulgar de dados

Além disso, ao utilizar máquinas virtuais para aceder às instâncias do Seu Azure Cosmos DB, faça uso de Private Link, Firewall, grupos de segurança de rede e etiquetas de serviço para mitigar a possibilidade de exfiltração de dados. A Microsoft gere a infraestrutura subjacente ao Azure Cosmos DB e implementou controlos rigorosos para evitar a perda ou exposição de dados dos clientes.

Como configurar a Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Todas as ligações ao suporte DO Azure Cosmos DB HTTPS. A Azure Cosmos DB também suporta o TLS1.2. É possível impor um servidor de servidor de versão TLS mínimo. Para isso, por [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)favor contacte.

Visão geral da Cosmos DB Security:https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades automáticas de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Cosmos DB. No entanto, pode utilizar a integração de Pesquisa Cognitiva Azure para classificação e análise de dados. Também pode implementar uma solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Dados do Index Azure Cosmos DB https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampcom Pesquisa Cognitiva Azure: .bc=/azure/cosmos-db/breadcrumb/toc.json

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: O Azure Cosmos DB fornece um controlo de acesso baseado em funções (RBAC) para cenários comuns de gestão em Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Ative Directory pode atribuir estas funções rBAC a utilizadores, grupos, diretores de serviços ou identidades geridas para conceder ou negar o acesso a recursos e operações em recursos DB da Azure Cosmos. As atribuições de funções são orientadas apenas para o acesso ao plano de controlo, que inclui acesso a contas, bases de dados, contentores e ofertas da Azure Cosmos (entrada).

Como implementar o RBAC em Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

A Microsoft gere a infraestrutura subjacente à Cosmos DB e implementou controlos rigorosos para evitar a perda ou exposição de dados dos clientes.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: Todos os dados do utilizador armazenados no Cosmos DB são encriptados em repouso por padrão. Não há controlos para desligá-lo. A Azure Cosmos DB utiliza encriptação AES-256 em todas as regiões onde a conta está em execução.

Por padrão, a Microsoft gere as chaves que são usadas para encriptar os dados na sua conta Azure Cosmos. Pode optar opcionalmente por adicionar uma segunda camada de encriptação com as suas próprias chaves.

Compreender a encriptação em repouso com o Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Compreender a gestão chave para encriptação em repouso com o Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Como configurar as chaves geridas pelo cliente para a sua conta Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividade seleções Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção do Azure Cosmos DB.

Como criar alertas para eventos de Registo de Atividade seleções do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Como criar alertas para eventos de Registo de Atividade seleções do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center para os seus casos Azure Cosmos DB. 

A Microsoft realiza a gestão de patching de sistemas e vulnerabilidades nos anfitriões subjacentes que suportam as suas instâncias De DB Do Azure Cosmos. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Funcionalidades suportadas disponíveis no Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o portal Azure ou o Azure Resource Graph para descobrir todos os recursos (não limitados ao Azure Cosmos DB, mas também recursos como a computação, outro armazenamento, rede, portas e protocolos, etc.) no âmbito da sua subscrição.  Certifique-se de que tem permissões adequadas no seu inquilino e é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos do Gestor de Recursos Azure para avançar.

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como visualizar as suas Assinaturas Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Compreender o controlo de acesso baseado em funções do Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nas instâncias do Seu Azure Cosmos DB e recursos relacionados com metadados para logicamente organizá-las numa taxonomia.

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Que etiquetas de suporte de recursos da Azure Cosmos DB:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear ativos, incluindo, mas não limitados, aos recursos DB da Azure Cosmos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Como criar subscrições adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação e ao Azure como um todo.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s subscrição)."

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Não aplicável; esta linha de base destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação e ao Azure como um todo.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos 

- Tipos de recursos permitidos

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos AzureResources através de scripts

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure configurando o "Block access" para a App "Microsoft Azure Management". Isto pode impedir a criação e alterações nos recursos num ambiente de alta segurança.

Como configurar o Acesso Condicional ao acesso ao portal Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta orientação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para mais informações, consulte [Controlo de Segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os seus casos Cosmos DB com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.DocumentDB" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias Cosmos DB. Você também pode fazer uso de definições políticas incorporadas para Azure Cosmos DB, tais como:

- Implementar Proteção Avançada de Ameaças para Contas DB cosmos

- Cosmos DB deve usar um ponto final de serviço de rede virtual

Como visualizar os aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação:** Utilize a política azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender efeitos políticos do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições políticas personalizadas do Azure para o seu Cosmos DB ou recursos conexos, utilize o Azure Repos para armazenar e gerir de forma segura o seu código.

Documentação Azure https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops Repos:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para as suas instâncias de DB Do Cosmos do Azure e recursos conexos. 

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em funcionamento no Azure App Service que estão a ser utilizados para aceder aos seus casos De DB Do Azure Cosmos, utilize a Identidade de Serviço Gerida em conjunto com o Azure Key Vault para simplificar e proteger a gestão secreta do Azure Cosmos DB. Certifique-se de que o Soft Delete do cofre de chaves está ativado.

Como integrar-se com identidades geridas pelo Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer a autenticação do Cofre Chave com uma identidade gerida:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Para máquinas virtuais Azure ou aplicações web em funcionamento no Azure App Service que estão a ser utilizados para aceder aos seus casos De DB Do Azure Cosmos, utilize a Identidade de Serviço Gerida em conjunto com o Azure Key Vault para simplificar e proteger a gestão secreta do Azure Cosmos DB.

Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Diretório Ativo Azure (AD). Identidades Geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AD Azure, incluindo o Key Vault, sem qualquer credencial no seu código.

Como configurar identidades geridas:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como integrar-se com identidades geridas pelo Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure.

Como configurar o Scanner Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação. O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, o Azure App Service), no entanto não funciona com conteúdo de clientes.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O Microsoft Antimalware está ativado no hospedeiro subjacente que suporta os serviços Azure (por exemplo, O Serviço de Aplicações Azure), no entanto não funciona com conteúdo do cliente.

É da sua responsabilidade fazer a pré-digitalização de quaisquer ficheiros que sejam enviados para recursos não computacionais do Azure, incluindo o Azure Cosmos DB. A Microsoft não pode aceder aos dados dos clientes e, portanto, não pode realizar digitalizações anti-malware de conteúdos de clientes em seu nome.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Não aplicável; o benchmark destina-se aos recursos de computação. O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Do Azure, no entanto não funciona com conteúdo de clientes.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Azure Cosmos DB tira fotos dos seus dados a cada quatro horas. Todos os backups são armazenados separadamente num serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. A qualquer momento, apenas as duas últimas fotos são mantidas. No entanto, se o contentor ou a base de dados forem eliminados, o Azure Cosmos DB reterá as cópias sombra existentes de um dado contentor ou base de dados durante 30 dias. Contacte o Suporte Azure para restaurar a partir de uma cópia de segurança.

Compreender as cópias de segurança automatizadas da Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: O Azure Cosmos DB recebe automaticamente cópias de segurança dos seus dados em intervalos regulares. Se a base de dados ou o recipiente foreliminado, pode arquivar um bilhete de suporte ou ligar para o suporte do Azure para restaurar os dados de cópias de segurança online automáticas. O suporte azure está disponível para planos selecionados apenas como Standard, Developer e planos superiores aos mesmos. Para restaurar uma imagem específica da cópia de segurança, o Azure Cosmos DB requer que os dados estão disponíveis durante a duração do ciclo de backup para esse instantâneo. 

Se utilizar o Key Vault para armazenar credenciais para as suas instâncias Cosmos DB, certifique-se de cópias de segurança automáticas regulares das suas chaves.

Compreenda as cópias de segurança automatizadas da Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Como restaurar dados em Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Como fazer backup das chaves do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Se a base de dados ou o recipiente foreliminado, pode arquivar um bilhete de apoio ou ligar para o suporte do Azure para restaurar os dados de cópias de segurança online automáticas. O suporte azure está disponível para planos selecionados apenas como Standard, Developer e planos superiores aos mesmos. Para restaurar uma imagem específica da cópia de segurança, o Azure Cosmos DB requer que os dados estão disponíveis durante a duração do ciclo de backup para esse instantâneo.

Teste a restauração dos seus segredos armazenados no Cofre de Chaves Azure usando powerShell. O cmdlet Restore-AzureKeyKey cria uma chave no cofre de chaves especificado. Esta chave é uma réplica da chave de back-up no ficheiro de entrada e tem o mesmo nome que a chave original.

Compreenda as cópias de segurança automatizadas da Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Como restaurar dados em Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Como restaurar os segredos do cofre de chaves Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Como todos os dados do utilizador armazenados em Cosmos DB estão encriptados em repouso e em transporte, você não precisa tomar qualquer ação. Outra forma de colocar isto é que a encriptação em repouso é "on" por padrão. Não há controlos para desligá-lo ou ligar. A Azure Cosmos DB utiliza encriptação AES-256 em todas as regiões onde a conta está em execução.

Ative o Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

Compreender a encriptação de dados em Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Como ativar o Soft-Delete no Cofre de Chaves:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.

Também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está em encontrar ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

Como definir o Contacto de Segurança do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas Sentinel.

Como configurar a exportação contínua:https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para O Sentinela Azul:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
