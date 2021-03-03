---
title: Linha de segurança Azure para Azure Cosmos DB
description: A linha de base de segurança Azure Cosmos DB fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5f9cf6dba0a1e670e3a27cb3546de9237466e7ce
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659431"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Linha de segurança Azure para Azure Cosmos DB

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para Azure Cosmos DB. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Azure Cosmos DB. Foram excluídos **os controlos** não aplicáveis à Azure Cosmos DB.

 
Para ver como a Azure Cosmos DB mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de segurança Azure Cosmos DB](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Ao utilizar o Azure Private Link, pode ligar-se a uma conta Azure Cosmos através de um Ponto Final Privado. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Também pode reduzir o risco de exfiltração de dados configurando um conjunto rigoroso de regras de saída num grupo de segurança de rede (NSG) e associando esse NSG à sua sub-rede.

Também pode utilizar o Service Endpoints para proteger a sua conta Azure Cosmos. Ao ativar um Ponto Final de Serviço, pode configurar contas Azure Cosmos para permitir o acesso a partir de apenas uma sub-rede específica de uma rede virtual Azure. Uma vez ativado o Endpoint de Serviço DB Azure Cosmos, pode limitar o acesso a uma conta Azure Cosmos com ligações a partir de uma sub-rede numa rede virtual.

Também pode proteger os dados armazenados na sua conta Azure Cosmos utilizando firewalls IP. O Azure Cosmos DB suporta controlos de acesso baseados em IP para suporte a firewall de entrada. Pode configurar uma firewall IP na conta Azure Cosmos utilizando os modelos do portal Azure, do Gestor de Recursos Azure ou através do Azure CLI ou da Azure PowerShell.

- [Descrição Geral do Azure Private Link](../private-link/private-link-overview.md)

- [Como configurar um ponto final privado para a Azure Cosmos DB](how-to-configure-private-endpoints.md) 

- [Como criar um Grupo de Segurança de Rede com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como configurar firewall IP em Cosmos DB](how-to-configure-firewall.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Política Azure - Microsoft.DocumentDB**:

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Utilize o Azure Security Center e siga as recomendações de proteção da rede para ajudar a proteger os recursos de rede relacionados com a sua conta Azure Cosmos.

Quando as máquinas virtuais são implantadas na mesma rede virtual que a sua conta Azure Cosmos, pode utilizar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento Azure para auditorias de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Utilize a funcionalidade De Partilha de Recursos de Origem Cruzada (CORS) para permitir que uma aplicação web em execução sob um domínio aceda a recursos noutro domínio. Os navegadores da Web implementam uma restrição de segurança conhecida como política de mesma origem que impede uma página web de chamar APIs em um domínio diferente. No entanto, o CORS fornece uma forma segura de permitir que o domínio de origem chame APIs em outro domínio. Depois de ativar o suporte DO CORS para a sua conta Azure Cosmos, apenas são avaliados pedidos autenticados para determinar se são permitidos de acordo com as regras especificadas.

- [Configure a partilha de recursos de origem cruzada](how-to-configure-cross-origin-resource-sharing.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Utilize proteção contra ameaças avançadas (ATP) para Azure Cosmos DB. ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente prejudiciais de aceder ou explorar contas Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças e integrá-las com sistemas centrais de monitorização de segurança.

Ativar o DDoS Protection Standard nas Redes Virtuais associadas às suas instâncias DB do Azure Cosmos para se proteger contra ataques de DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

- [Como configurar a Azure Cosmos DB Advanced Threat Protection](cosmos-db-advanced-threat-protection.md)

- [Como configurar a proteção DDoS](/azure/virtual-network/manage-ddos-protection)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](/azure/security-center/security-center-alerts-service-layer)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Ativar os registos de fluxo do grupo de segurança da rede (NSG) e enviar registos para uma conta de armazenamento para auditoria de tráfego. Pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Utilize proteção contra ameaças avançadas (ATP) para Azure Cosmos DB. ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente prejudiciais de aceder ou explorar contas Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças e integrá-las com sistemas centrais de monitorização de segurança. 

- [Como configurar a Proteção Avançada de Ameaças da Cosmos DB](cosmos-db-advanced-threat-protection.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso à sua conta Azure Cosmos, utilize tags de serviço de Rede Virtual para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, AzureCosmosDB) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Para mais informações sobre a utilização de tags de serviço](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Definir e implementar configurações de segurança padrão para recursos de rede com a Política Azure. Use pseudónimos da Azure Policy nos espaços de nome "Microsoft.DocumentDB" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus exemplos DB do Azure Cosmos. Você também pode fazer uso de definições políticas incorporadas para Azure Cosmos DB, tais como:

- Implementar proteção avançada de ameaças para contas DB cosmos

- Cosmos DB deve usar um ponto final de serviço de rede virtual

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em funções Azure (Azure RBAC), e políticas numa única definição de planta. Pode aplicar facilmente o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos de rede associados à sua implantação DB Azure Cosmos de forma a organizá-las logicamente numa taxonomia.

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as suas instâncias DB do Azure Cosmos. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede. 

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados pela Azure Cosmos DB. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de armazenamento para armazenamento a longo prazo/arquivo. Em alternativa, pode embarcar dados para a Azure Sentinel ou para um relatório de segurança de terceiros e gestão de eventos (SIEM). 

- [Como ativar registos de diagnóstico para Azure Cosmos DB](/azure/cosmos-db/logging)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ative as definições de diagnóstico para Azure Cosmos DB e envie os registos para um espaço de trabalho log Analytics ou conta de armazenamento Azure. As definições de diagnóstico em Azure Cosmos DB são usadas para recolher registos de recursos. Estes registos são capturados por pedido e também são referidos como **registos de data plane**. Alguns exemplos das operações do plano de dados incluem eliminar, inserir e ler. 

Também pode ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar esses registos para o mesmo espaço de trabalho do Log Analytics que utiliza para registos DB do Azure Cosmos.

- [Como ativar as definições de diagnóstico para Azure Cosmos DB](/azure/cosmos-db/logging)

- [Como ativar as definições de diagnóstico para registo de atividades Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados aos seus casos de DB Azure Cosmos de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de registos](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Pode realizar consultas no Log Analytics um espaço de trabalho para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações baseadas nos registos DB do Azure Cosmos que recolheu.

- [Como realizar consultas para Azure Cosmos DB em Log Analytics Workspaces](monitor-cosmos-db.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: No Centro de Segurança Azure, permita a Proteção Avançada de Ameaças para a Azure Cosmos DB monitorizar atividades anómalas em registos de segurança e eventos. Ativar as definições de diagnóstico em Azure Cosmos DB e enviar registos para um espaço de trabalho log analytics.

 

Também pode embarcar no seu espaço de trabalho Log Analytics para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança. Além disso, pode criar alertas de registo personalizados no seu espaço de trabalho Log Analytics utilizando o Azure Monitor.

- [Lista de alertas de proteção de ameaças para Azure Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Criar, visualizar e gerir alertas de registo usando o Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Pode utilizar o painel de controlo de identidade e acesso (IAM) no portal Azure para configurar o controlo de acesso baseado em funções (RBAC) e manter o inventário nos recursos DB do Azure Cosmos. As funções são aplicadas aos utilizadores, grupos, diretores de serviços e identidades geridas no Azure Ative Directory (Azure AD). Você pode usar papéis incorporados ou papéis personalizados para indivíduos e grupos.

A Azure Cosmos DB fornece Azure RBAC incorporado para cenários comuns de gestão em Azure Cosmos DB. Um indivíduo que tenha um perfil em Azure AD pode atribuir estas funções Azure a utilizadores, grupos, diretores de serviços ou identidades geridas para conceder ou negar o acesso a recursos e operações em recursos DB da Azure Cosmos.

Também pode utilizar o módulo Azure AD PowerShell para realizar consultas de adhoc para descobrir contas que são membros de grupos administrativos.

Além disso, algumas ações em Azure Cosmos DB podem ser controladas com Azure AD e chaves master específicas de conta. Utilize a definição de conta 'DisableKeyBasedMetadataWriteAccess' para controlar o acesso à chave.

- [Compreender o controlo de acesso baseado em funções na Azure Cosmos DB](role-based-access-control.md)

- [Construa os seus próprios papéis personalizados utilizando Azure Cosmos DB Actions (Microsoft.Docespaço de nome umentDB)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb)

- [Criar um novo papel no AZure AD](../role-based-access-control/custom-roles.md)

- [Como obter um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Restringir o acesso do utilizador apenas às operações de dados](how-to-restrict-user-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O conceito de palavras-passe padrão ou em branco não existe em relação ao Azure Ative Directory (Azure AD) ou Azure Cosmos DB. Em vez disso, a Azure Cosmos DB utiliza dois tipos de chaves para autenticar os utilizadores e fornecer acesso aos seus dados e recursos; chaves principais e fichas de recursos. As chaves podem ser regeneradas a qualquer momento.

- [Compreender o acesso seguro aos dados em Azure Cosmos DB](secure-access-to-data.md)

- [Como regenerar Azure Cosmos DB Keys](https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys)

- [Como aceder programáticamente chaves usando Azure AD](certificate-based-authentication.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Não aplicável; A Azure Cosmos DB não suporta contas de administrador. Todo o acesso é integrado com O Diretório Ativo Azure (Azure AD) e o controlo de acesso baseado em funções Azure (Azure RBAC).

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: A Azure Cosmos DB utiliza dois tipos de chaves para autorizar os utilizadores e não suporta Sign-On (SSO) ao nível do plano de dados. O acesso ao plano de controlo da Cosmos DB está disponível através da REST API e suporta a SSO. Para autenticar, desacorda o cabeçalho de Autorização para os seus pedidos num Token Web JSON que obtenha do Azure Ative Directory (Azure AD).

- [Compreender a Base de Dados Azure para Cosmos DB REST API](/rest/api/cosmos-db/)

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multifactor configurada para iniciar sessão e configurar recursos Azure. 

- [Saiba mais sobre estações de trabalho de acesso privilegiado](/security/compass/privileged-access-devices)
 
- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize proteção contra ameaças avançadas (ATP) para Azure Cosmos DB. ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente prejudiciais de aceder ou explorar contas Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças e integrá-las com sistemas centrais de monitorização de segurança.

Além disso, poderá utilizar o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Configure a condição de localização de uma política de acesso condicional e gerencie as suas localizações nomeadas. Com localizações nomeadas, pode criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões. Você pode restringir o acesso a recursos sensíveis, como as suas instâncias DB Azure Cosmos, para os seus locais com nomes configurados.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar instâncias do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Como configurar e gerir a autenticação AD da Azure com o Azure SQL](/azure/sql-database/sql-database-aad-authentication-configure)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, você pode usar Azure Identity Access Reviews para gerir eficientemente membros do grupo, acesso a aplicações empresariais e atribuições de funções. O acesso do utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Pode criar definições de diagnóstico para contas de utilizador do Azure Ative (Azure AD), enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics onde pode configurar os alertas pretendidos.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize proteção contra ameaças avançadas (ATP) para Azure Cosmos DB. ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente prejudiciais de aceder ou explorar contas Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças e integrá-las com sistemas centrais de monitorização de segurança.

Também pode utilizar o Azure Ative Directory (Azure AD) Identity Protection e a funcionalidade de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores. Além disso, você pode ingerir troncos em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear instâncias DB da Azure Cosmos que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. As instâncias DB do Azure Cosmos são separadas por rede/sub-rede virtuais, marcadas adequadamente e protegidas dentro de um grupo de segurança de rede (NSG) ou Azure Firewall. As instâncias DB de Azure Cosmos que armazenam dados sensíveis devem ser isoladas. Ao utilizar o Azure Private Link, pode ligar-se a uma conta de exemplo de Azure Cosmos através de um ponto final privado. O ponto final privado é um conjunto de endereços IP privados numa sub-rede dentro da sua rede virtual. Pode então limitar o acesso aos endereços IP privados selecionados. 

- [Como criar subscrições adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar grupos de gestão](/azure/governance/management-groups/create)

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

- [Como configurar um ponto final privado para a Azure Cosmos DB](how-to-configure-private-endpoints.md)

- [Como criar um Grupo de Segurança de Rede com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Pode implementar a Advanced Threat Protection for Azure Cosmos DB, que irá detetar atividades anómalas que indiquem tentativas incomuns e potencialmente prejudiciais de aceder ou explorar bases de dados. Atualmente pode desencadear os seguintes alertas:

- Acesso a partir de locais incomuns

- Extração de dados incomuns

Além disso, ao utilizar máquinas virtuais para aceder às suas instâncias DB do Azure Cosmos, utilize o Private Link, Firewall, grupos de segurança de rede e tags de serviço para mitigar a possibilidade de exfiltração de dados. A Microsoft gere a infraestrutura subjacente à Azure Cosmos DB e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Como configurar a Proteção Avançada de Ameaças da Cosmos DB](cosmos-db-advanced-threat-protection.md)

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades automáticas de identificação, classificação e prevenção de perdas ainda não estão disponíveis para a Azure Cosmos DB. No entanto, pode utilizar a integração da Azure Cognitive Search para classificação e análise de dados. Também pode implementar uma solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Índice Azure Cosmos DB dados com pesquisa cognitiva Azure](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp;bc=/azure/cosmos-db/breadcrumb/toc.json)

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: A Azure Cosmos DB fornece o controlo de acesso baseado em funções incorporada a Azure (Azure RBAC) para cenários comuns de gestão em Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Ative Directory (Azure AD) pode atribuir estas funções Azure a utilizadores, grupos, diretores de serviços ou identidades geridas para conceder ou negar o acesso a recursos e operações em recursos DB da Azure Cosmos. As atribuições de funções são apenas para acesso de avião de controlo, o que inclui o acesso às contas da Azure Cosmos, bases de dados, contentores e ofertas (produção).

- [Como implementar o Azure RBAC em Azure Cosmos DB](role-based-access-control.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Todos os dados do utilizador armazenados em Cosmos DB são encriptados em repouso por padrão. Não há controlos para desligá-lo. A Azure Cosmos DB utiliza encriptação AES-256 em todas as regiões onde a conta está a funcionar.

Por predefinição, a Microsoft gere as chaves que são usadas para encriptar os dados na sua conta Azure Cosmos. Pode optar opcionalmente por adicionar uma segunda camada de encriptação com as suas próprias teclas.

- [Compreender a encriptação em repouso com Azure Cosmos DB](database-encryption-at-rest.md)

- [Compreender a gestão chave para a encriptação em repouso com Azure Cosmos DB](/azure/cosmos-db/cosmos-db-security-controls)

- [Como configurar chaves geridas pelo cliente para a sua conta DB Azure Cosmos](how-to-setup-cmk.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção do Azure Cosmos DB.

- [Como criar alertas para eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/alerts-activity-log)

- [Como criar alertas para eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center para as suas instâncias DB Azure Cosmos. 

A Microsoft executa a correção do sistema e a gestão de vulnerabilidades nos anfitriões subjacentes que suportam as suas instâncias DB Azure Cosmos. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Funcionalidades suportadas disponíveis no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o portal Azure ou o Azure Resource Graph para descobrir todos os recursos (não limitados à Azure Cosmos DB, mas também incluindo recursos como o cálculo, outros armazenamentos, rede, portas e protocolos, etc.) dentro da sua subscrição(s). Certifique-se de que tem permissões adequadas no seu inquilino e é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Compreender o controlo de acesso baseado em funções da Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nas suas instâncias DB do Azure Cosmos e recursos relacionados com metadados para organizá-los logicamente numa taxonomia.

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

- [Quais os recursos de recursos da Azure Cosmos DB tags](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e subscrições separadas, se for caso disso, para organizar e rastrear ativos, incluindo, mas não se limitando aos recursos DB da Azure Cosmos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](/azure/governance/management-groups/create)

- [Como criar e usar Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações aos recursos num ambiente de alta segurança.

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para as suas instâncias de DB cosmos com a Política Azure. Use pseudónimos da Azure Policy no **espaço de nomesMicrosoft.DocumentDB** para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de DB cosmos. Você também pode fazer uso de definições políticas incorporadas para Azure Cosmos DB, tais como:
- Implementar proteção avançada de ameaças para contas DB cosmos
- Cosmos DB deve usar um ponto final de serviço de rede virtual

- [Como ver pseudónimos disponíveis da Política Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se estiver a utilizar definições personalizadas da Política Azure para o seu Cosmos DB ou recursos relacionados, utilize o Azure Repos para armazenar e gerir as definições de política personalizada como código.

- [Criar fluxos de trabalho de Política como Código](../governance/policy/concepts/policy-as-code.md)

- [Documentação de Azure Repos](/azure/devops/repos/git/gitworkflow)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Use pseudónimos da Política Azure no espaço de nomes "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Use pseudónimos da Política Azure no espaço de nomes "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para as suas instâncias DB Azure Cosmos e recursos relacionados. 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder às suas instâncias DB Azure Cosmos, use a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger a gestão secreta da Azure Cosmos DB. Certifique-se de que o cofre da chave é excluído suave.

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Cofre-Chave](/azure/key-vault/quick-create-portal)

- [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder às suas instâncias DB Azure Cosmos, use a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger a gestão secreta da Azure Cosmos DB.

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como configurar identidades geridas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure CosmosDB), no entanto não funciona com conteúdo do cliente.

É da sua responsabilidade pré-digitalizar quaisquer ficheiros que sejam enviados para recursos Azure não computados, incluindo Azure Cosmos DB. A Microsoft não pode aceder aos dados dos clientes e, portanto, não pode realizar análises anti-malware do conteúdo do cliente em seu nome.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: A Azure Cosmos DB recolhe automaticamente cópias de segurança dos seus dados em intervalos regulares. Se a base de dados ou o contentor forem eliminados, pode arquivar um bilhete de suporte ou chamar o suporte do Azure para restaurar os dados a partir de cópias de segurança online automáticas. O suporte Azure está disponível para planos selecionados apenas como Standard, Developer, e planos superiores a eles. Para restaurar uma imagem específica da cópia de segurança, a Azure Cosmos DB requer que os dados estão disponíveis durante a duração do ciclo de backup para esse instantâneo. 

Se utilizar o Key Vault para armazenar credenciais para as suas instâncias de DB cosmos, certifique-se de cópias de segurança automáticas regulares das suas chaves.

- [Compreenda backups automatizados Azure Cosmos DB](online-backup-and-restore.md)

- [Como restaurar dados em Azure Cosmos DB](/azure/cosmos-db/how-to-backup-and-restore)

- [Como apoiar chaves do cofre](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Se a base de dados ou o contentor forem eliminados, pode arquivar um bilhete de suporte ou chamar o suporte do Azure para restaurar os dados a partir de cópias de segurança online automáticas. O suporte Azure está disponível para planos selecionados apenas como Standard, Developer, e planos superiores a eles. Para restaurar uma imagem específica da cópia de segurança, a Azure Cosmos DB requer que os dados estão disponíveis durante a duração do ciclo de backup para esse instantâneo.

Teste a restauração dos seus segredos armazenados no Cofre da Chave Azure usando o PowerShell. O Restore-AzureKeyVaultKey cmdlet cria uma chave no cofre de chaves especificado. Esta chave é uma réplica da chave de apoio no ficheiro de entrada e tem o mesmo nome que a chave original.

- [Compreenda backups automatizados Azure Cosmos DB](online-backup-and-restore.md)

- [Como restaurar dados em Azure Cosmos DB](/azure/cosmos-db/how-to-backup-and-restore)

- [Como restaurar os segredos do cofre da chave Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Como todos os dados do utilizador armazenados em Cosmos DB estão encriptados em repouso e no transporte, não é necessário tomar nenhuma ação. Outra forma de colocar isto é que a encriptação em repouso está "on" por padrão. Não há controlos para desligá-lo ou ligar. A Azure Cosmos DB utiliza encriptação AES-256 em todas as regiões onde a conta está a funcionar.

Ativar Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Compreender a encriptação de dados em Azure Cosmos DB](database-encryption-at-rest.md)

- [Como permitir Soft-Delete em Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Você também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está em encontrar ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
