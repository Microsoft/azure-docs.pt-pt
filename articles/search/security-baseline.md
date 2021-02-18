---
title: Linha de segurança Azure para Azure Cognitive Search
description: A linha de base de segurança Azure Cognitive Search fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0618e4445629893fbe623df4bc937698bd307c7c
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096561"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Linha de segurança Azure para Azure Cognitive Search

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para Azure Cognitive Search. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Azure Cognitive Search. **Os controlos** não aplicáveis à Azure Cognitive Search, ou o cliente foram excluídos.

Para ver como a Azure Cognitive Search mapeia completamente para o Benchmark de Segurança Azure, consulte o ficheiro completo de [mapeamento de base de segurança de segurança Azure Cognitive Search](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Certifique-se de que todas as implementações da sub-rede da Rede Virtual Microsoft Azure têm um grupo de segurança de rede aplicado com regras para implementar um sistema de acesso "menos privilegiado". Permitir o acesso apenas às portas fidedignas da sua aplicação e aos intervalos de endereços IP. Implemente a Azure Cognitive Search com um ponto final privado Azure, sempre que possível, para permitir o acesso privado aos seus serviços a partir da sua rede virtual.

A Cognitive Search também suporta funcionalidades adicionais de segurança da rede para gerir listas de controlo de acesso à rede. Configure o seu serviço de pesquisa para permitir apenas a comunicação com fontes fidedignas, restringindo o acesso a intervalos específicos de endereços IP públicos utilizando a sua capacidade de firewall.

- [Como configurar pontos finais privados para a pesquisa cognitiva do Azure](./service-create-private-endpoint.md)

- [Como configurar a firewall de pesquisa cognitiva Azure](./service-configure-firewall.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: A Procura Cognitiva não pode ser implantada diretamente numa rede virtual. No entanto, se a aplicação do seu cliente ou as fontes de dados estiverem numa rede virtual, pode monitorizar e registar o tráfego para os componentes da rede, incluindo pedidos enviados para um serviço de pesquisa na nuvem. As recomendações padrão incluem permitir um registo de fluxo de grupo de segurança de rede e enviar registos para o Azure Storage ou um espaço de trabalho Log Analytics. Pode utilizar opcionalmente o Traffic Analytics para obter informações sobre os padrões de tráfego.

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a segurança da rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: A Pesquisa Cognitiva não fornece uma característica específica para combater um ataque de negação de serviço distribuído, mas pode ativar o DDoS Protection Standard nas redes virtuais associadas ao seu serviço de Pesquisa Cognitiva para proteção geral.

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Ative os registos de fluxo do grupo de segurança da rede para os grupos de segurança da rede que protegem as Máquinas Virtuais Azure (VM) que estarão a ligar-se ao seu serviço de Pesquisa Cognitiva. Envie registos para uma conta de Armazenamento Azure para auditoria de tráfego. 

Ativar a captura de pacotes do Monitor de Rede, se necessário para investigar atividades anómalas.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: A Pesquisa Cognitiva não suporta a deteção de intrusões na rede, mas como mitigação de intrusões, pode configurar regras de firewall para especificar os endereços IP aceites pelo serviço de Pesquisa Cognitiva. Configurar um ponto final privado para manter o tráfego de pesquisa longe da internet pública.

- [Como configurar chaves geridas pelo cliente para encriptação de dados](./search-security-manage-encryption-keys.md)

- [Como obter informações-chave geridas pelo cliente a partir de índices e mapas de sinónimos](./search-security-get-encryption-keys.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço, se estiver a alavancar indexadores e skillsets na Pesquisa Cognitiva, para representar uma série de endereços IP que têm permissão para se ligar a recursos externos. 

Permitir ou negar o tráfego aos recursos especificando o nome da etiqueta de serviço (por exemplo, AzureCognitiveSearch) no campo de origem ou destino apropriado de uma regra. 

- [Tags de serviço de rede virtual](../virtual-network/service-tags-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: A Pesquisa Cognitiva não tem nem depende dos recursos da rede por design. As aplicações de clientes e fontes de dados relacionadas com a sua aplicação de pesquisa podem estar numa rede virtual, mas o serviço de pesquisa não está por si só implantado na rede. 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Pode configurar a Pesquisa Cognitiva com um ponto final privado Azure para integrar o seu serviço de pesquisa com uma rede virtual.  Utilize etiquetas de recursos para grupos de segurança de rede e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais do grupo de segurança da rede, utilize o campo "Descrição" para documentar as regras que permitem o tráfego de/para uma rede. 

Utilize qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, tais como efeitos de "Exigir etiqueta e seu valor", para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados. 

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas. 

- [Como criar um ponto final privado para a Pesquisa Cognitiva](./service-create-private-endpoint.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual Azure](../virtual-network/quick-create-portal.md)

- [Como filtrar o tráfego da rede com regras do grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: A Pesquisa Cognitiva não tem ou depende de quaisquer componentes de rede, pelo que as configurações destes recursos não podem ser monitorizadas.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Pesquisa Cognitiva não suporta configurar as suas próprias fontes de sincronização de tempo. O serviço de pesquisa baseia-se em fontes de sincronização de tempo da Microsoft e não está exposto aos clientes para configuração.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos relacionados com a Pesquisa Cognitiva via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.
Em alternativa, pode ativar e embarcar estes dados para a Azure Sentinel ou para um SIEM de terceiros.

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Os registos de diagnóstico e operacionais fornecem informações detalhadas sobre as operações detalhadas da Pesquisa Cognitiva e são úteis para monitorizar o serviço e para as cargas de trabalho que acedem ao seu serviço.  Para capturar dados de diagnóstico, ative o registo, especificando onde as informações de registo são armazenadas.

- [Como recolher e analisar dados de registo para a Pesquisa Cognitiva do Azure](./search-monitor-logs.md)

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Os dados históricos que se alimentam em métricas de diagnóstico são preservados pela Pesquisa Cognitiva durante 30 dias por padrão. Para uma retenção mais longa, certifique-se de que ativa a definição que especifica uma opção de armazenamento para eventos e métricas registados persistentes.

No Azure Monitor, desapasse o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize as contas de armazenamento Azure para armazenamento a longo prazo e arquivo. 

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Analise e monitorize registos do seu serviço de Pesquisa Cognitiva para um comportamento anómalo. Utilize o Azure Monitor's Log Analytics para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como recolher e analisar dados de registo para pesquisa cognitiva](./search-monitor-logs.md)

- [Como visualizar dados de registo de pesquisa no Power BI](./search-monitor-logs-powerbi.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Saiba mais sobre o Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Centro de Segurança com log analytics espaço de trabalho para monitorizar e alertar sobre a atividade anómala encontrada em registos de segurança e eventos. Em alternativa, pode ativar e a bordo dados para a Azure Sentinel.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável à Pesquisa Cognitiva. A Microsoft gere a solução anti-malware para a plataforma subjacente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável à Pesquisa Cognitiva. Não produz nem consome registos DNS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável à Pesquisa Cognitiva. A auditoria da linha de comando não está disponível para pesquisa cognitiva.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O controlo de acesso baseado em funções (Azure RBAC) permite-lhe gerir o acesso aos recursos Azure através de atribuições de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas predefinidas para determinados recursos, que podem ser inventariadas ou consultadas com ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure.

As funções de Pesquisa Cognitiva estão associadas a permissões que suportam tarefas de gestão de nível de serviço.  Estas funções não concedem acesso ao ponto final de serviço. O acesso às operações contra o ponto final (como a gestão de índices, a população indexada e as consultas sobre dados de pesquisa), utiliza as chaves API para autenticar o pedido.

- [Definir funções para acesso administrativo à Pesquisa Cognitiva de Azure](./search-security-rbac.md)

- [Criar e gerir chaves api para um serviço de Pesquisa Cognitiva Azure](./search-security-api-keys.md)

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)
- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Não aplicável à pesquisa cognitiva. Não tem um conceito de senhas padrão.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: A Pesquisa Cognitiva não tem o conceito de contas de administrador de nível local ou Azure Ative (Azure AD) que possam ser usadas para gerir índices e operações. 

Utilize as funções incorporadas AZURE AD que devem ser explicitamente atribuídas para operações de gestão. Invoque o módulo Azure AD PowerShell para realizar consultas ad-hoc para descobrir contas que são membros de grupos administrativos.

- [Como usar funções para acesso administrativo em Pesquisa Cognitiva](./search-security-rbac.md)

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Utilize a autenticação SSO com o Azure Ative Directory (Azure AD) para aceder às informações do serviço de pesquisa para operações de gestão suportadas através do Azure Resource Manager. 

Estabeleça um processo para reduzir o número de identidades e credenciais, permitindo sSO para o serviço com as identidades pré-existentes da sua organização.

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ative Directory (Azure AD) Multi-Factor Authentication (MFA) e seguir as recomendações de Identidade e Acesso do Centro de Segurança.

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiada (PAW) com autenticação multi-factor (MFA) configurada para iniciar sessão e aceder aos recursos do Azure.

- [Compreenda estações de trabalho seguras e geridas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

- [Como ativar O Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança e monitorização do Azure Ative Directory (Azure AD) para detetar quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gerir os recursos do Azure apenas a partir de locais aprovados

**Orientação**: Não aplicável à Pesquisa Cognitiva. Não suporta a utilização da localização aprovada como condição para o acesso.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização para tarefas de gestão de nível de serviço na Pesquisa Cognitiva Azure. As identidades da AD AZure não concedem acesso ao ponto final do serviço de pesquisa.  O acesso a operações como a gestão de índices, a população indexada e as consultas sobre dados de pesquisa estão disponíveis através de chaves API.

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Criar e gerir chaves api para um serviço de Pesquisa Cognitiva Azure](./search-security-api-keys.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Use a identidade da Azure E aceda a avaliações de acesso para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado. 

Reveja os registos de diagnóstico da Pesquisa Cognitiva para a atividade no ponto final do serviço de pesquisa, tais como gestão de índices, população de índices e consultas.

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Monitorizar operações e atividade da Azure Cognitive Search](./search-monitor-usage.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Acesso à atividade de login, auditoria e registo de eventos de risco do Azure Ative, permite-lhe integrar-se com qualquer SIEM ou ferramenta de monitorização.

Agilize este processo criando definições de diagnóstico para contas de utilizadores AZure AD e enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Configure os alertas desejados dentro do espaço de trabalho do Log Analytics.

- [Como integrar registos de atividades do Azure com o Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de Proteção de Identidade Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Ingerir dados em Azure Sentinel para mais investigação, conforme necessário.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não aplicável à Pesquisa Cognitiva. O Lockbox do cliente não suporta a Pesquisa Cognitiva.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os recursos devem ser separados por rede/sub-rede virtuais, marcados adequadamente e protegidos dentro de um grupo de segurança de rede ou Azure Firewall. Os recursos que armazenam ou processam dados sensíveis devem ser isolados. Utilize o Link Privado para configurar um ponto final privado para a Pesquisa Cognitiva.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar um ponto final privado para a Pesquisa Cognitiva](./service-create-private-endpoint.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Utilize uma solução de terceiros do Azure Marketplace nos perímetros de rede para monitorizar a transferência não autorizada de informações sensíveis e bloquear tais transferências enquanto alerta os profissionais de segurança da informação.

A Microsoft gere a plataforma subjacente e trata todos os conteúdos do cliente como sensíveis e protege contra a perda e exposição de dados do cliente. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: A Pesquisa Cognitiva encripta os dados em trânsito com a Segurança da Camada de Transporte 1.2 e impõe a encriptação (SSL/TLS) em todos os momentos para todas as ligações. Isto garante que todos os dados são encriptados "em trânsito" entre o cliente e o serviço.

- [Compreender a encriptação em trânsito com Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para pesquisa cognitiva. Implementar uma solução de terceiros, se necessário para efeitos de conformidade. 

A Microsoft gere a plataforma subjacente e trata todos os conteúdos do cliente como sensíveis e protege contra a perda e exposição de dados do cliente. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Para administração de serviços, utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para gerir o acesso às chaves e configuração. Para operações de conteúdo, tais como indexação e consultas, a Cognitive Search utiliza chaves em vez de um modelo de controlo de acesso baseado na identidade. Utilize o Azure RBAC para controlar o acesso às chaves.
- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md) 

 
- [Como usar papéis para acesso administrativo à Pesquisa Cognitiva](./search-security-rbac.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta orientação destina-se a recursos computacional. 

A Microsoft gere a infraestrutura subjacente à Pesquisa Cognitiva e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: A Pesquisa Cognitiva encripta automaticamente o conteúdo indexado em repouso com as teclas geridas pela Microsoft. Se for necessária mais proteção, pode complementar a encriptação padrão com uma segunda camada de encriptação utilizando as teclas que cria e gere no Cofre da Chave Azure.

- [Configure as chaves geridas pelo cliente para encriptação de dados na Pesquisa Cognitiva Azure](./search-security-manage-encryption-keys.md)

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações em casos de produção de Pesquisa Cognitiva e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Como criar alertas para atividades de Pesquisa Cognitiva](./search-monitor-logs.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte o [Azure Security Benchmark: Gestão de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Atualmente não está disponível para pesquisa cognitiva.  Para os clusters que armazenam o conteúdo do serviço de pesquisa, a Microsoft é responsável pela gestão de vulnerabilidades desses clusters.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar uma solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável à Pesquisa Cognitiva. A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam os serviços de Pesquisa Cognitiva.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Não aplicável à Pesquisa Cognitiva. Não tem nenhum sistema padrão de classificação de risco ou pontuação para obter resultados de verificação de vulnerabilidades.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (tais como computação, armazenamento, rede, portas, protocolos, e assim por diante) nas suas subscrições.  

Certifique-se de permissões (leia) adequadas no seu inquilino e enumere todas as subscrições do Azure, bem como os recursos nas suas subscrições.  

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Como ver as suas subscrições Azure](/powershell/module/az.accounts/get-azsubscription) 

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas nos recursos Azure com metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.
- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md) 

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Defina uma lista de recursos Azure aprovados relacionados com a indexação e processamento de skillset na Pesquisa Cognitiva.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Recomenda-se que defina um inventário dos recursos Azure que tenham sido aprovados para utilização de acordo com as suas políticas e padrões organizacionais anteriores, em seguida, monitorize para recursos Azure não aprovados com Azure Policy, ou Azure Resource Graph.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta orientação destina-se a recursos computacional.

Recomenda-se que tenha um inventário de aplicações de software que tenham sido consideradas aprovadas de acordo com as suas políticas organizacionais e padrões de segurança, e monitorize quaisquer títulos de software não aprovados instalados nos seus recursos de computação Azure.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável à Pesquisa Cognitiva. Não expõe quaisquer recursos de computação nem permite a instalação de aplicações de software em nenhum dos seus recursos.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar ou descobrir recursos dentro da sua subscrição( s). Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md) 

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a aplicações em execução de recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Para a gestão do serviço, utilize o Acesso Condicionado Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". 

Controle o acesso às chaves utilizadas para autenticar pedidos para todas as outras operações, nomeadamente as relacionadas com o conteúdo com a Pesquisa Cognitiva.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts em recursos computacional

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.Search" para criar políticas personalizadas para auditar ou impor a configuração dos seus recursos de Pesquisa Cognitiva Azure. Também pode utilizar definições de Política Azure incorporadas para serviços de Pesquisa Cognitiva, tais como:

- Permitir a exploração de auditoria para recursos Azure

O Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem os requisitos de segurança para a sua organização. 

Também pode utilizar as recomendações do Azure Security Center como uma base de configuração segura para os seus recursos Azure. 

- [Controlos de conformidade regulamentar da política Azure para a pesquisa cognitiva do Azure](./security-controls-policy.md)

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] efeitos, para impor definições seguras em todos os seus recursos de serviço de Pesquisa Cognitiva. 

Os modelos do Azure Resource Manager podem ser utilizados para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)

- [Controlos de conformidade regulamentar da política Azure para a pesquisa cognitiva do Azure](./security-controls-policy.md)

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentação de Azure Repos](/azure/devops/repos/index)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os seus recursos de serviço de Pesquisa Cognitiva utilizando a Política Azure. 

Use pseudónimos para criar políticas personalizadas para auditar ou fazer cumprir as configurações da rede. Também pode utilizar definições políticas incorporadas relacionadas com os seus recursos específicos. 

Além disso, pode utilizar a Azure Automation para implementar alterações de configuração e gerir exceções de política. 

- [Controlos de conformidade regulamentar da política Azure para a pesquisa cognitiva do Azure](./security-controls-policy.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Centro de Segurança para realizar análises de base dos seus recursos de serviço de Pesquisa Cognitiva.  Além disso, utilize a Política Azure para alertar e auditar as configurações dos seus recursos. 

- [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

- [Controlos de conformidade regulamentar da política Azure para a pesquisa cognitiva do Azure](./security-controls-policy.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize identidades geridas azure em conjunto com o Azure Key Vault para simplificar a gestão secreta das suas aplicações em nuvem.
- [Como utilizar identidades geridas para recursos Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [Como criar um Cofre-Chave](../key-vault/general/quick-create-portal.md) 

- [Como fornecer a autenticação do Cofre-Chave com uma identidade gerida](../key-vault/general/assign-access-policy-portal.md) 

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize uma Identidade Gerida Azure para dar acesso à Pesquisa Cognitiva a outros serviços Azure, tais como Key Vault e fontes de dados indexantes utilizando uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). As identidades geridas permitem-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Azure Key Vault, sem quaisquer credenciais no seu código. 

- [Configurar uma ligação indexante a uma fonte de dados utilizando uma identidade gerida](./search-howto-managed-identities-data-sources.md)

- [Configure as chaves geridas pelo cliente para encriptação de dados usando uma identidade gerida](./search-security-manage-encryption-keys.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Não aplicável à Pesquisa Cognitiva. Não acolhe código e não tem credenciais para identificar.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte o [Azure Security Benchmark: Malware defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Utilizar software antimalware gerido centralmente

**Orientação**: Não aplicável à Pesquisa Cognitiva. Esta recomendação destina-se a recursos computacional.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Cognitive Search), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, tais como Pesquisa Cognitiva, Armazenamento de Blob, Base de Dados Azure SQL, e assim por diante. 

É da sua responsabilidade pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados. A Microsoft não pode aceder aos dados dos clientes e, portanto, não pode realizar análises anti-malware do conteúdo do cliente em seu nome.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Assegurar que o software e assinaturas antimalware sejam atualizados

**Orientação**: Não aplicável à Pesquisa Cognitiva. Não permite a instalação de soluções anti-malware nos seus recursos. Para a plataforma subjacente, a Microsoft lida com a atualização de qualquer software e assinaturas anti-malware. 

Para quaisquer recursos de computação que sejam propriedade da sua organização e utilizados na sua solução de pesquisa, siga recomendações no Security Center, Compute &amp; Apps para garantir que todos os pontos finais estão atualizados com as assinaturas mais recentes. Para o Linux, utilize uma solução anti-malware de terceiros.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: O conteúdo armazenado num serviço de pesquisa não pode ser apoiado através do Azure Backup ou de qualquer outro mecanismo incorporado, mas pode reconstruir um índice a partir do código fonte de aplicação e fontes de dados primárias, ou construir uma ferramenta personalizada para recuperar e armazenar conteúdo indexado.

- [Amostra de restauro do índice GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: ATualmente, a Pesquisa Cognitiva não suporta cópias de segurança automatizadas para dados num serviço de pesquisa e deve ser apoiada através de um processo manual.  Também pode fazer backup nas chaves geridas pelo cliente no Cofre da Chave Azure. 

- [Recue e restaure um índice de Pesquisa Cognitiva Azure](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Como apoiar chaves do Cofre chave em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: ATualmente, a Pesquisa Cognitiva não suporta cópias de segurança automatizadas para dados num serviço de pesquisa e deve ser apoiada e restaurada através de um processo manual.  Execute periodicamente a restauração de dados de conteúdos que tenha feito o backup manual para garantir a integridade de ponta a ponta do seu processo de backup.

- [Recue e restaure um índice de Pesquisa Cognitiva Azure](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Como restaurar chaves do Cofre chave em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: ATualmente, a Pesquisa Cognitiva não suporta cópias de segurança automatizadas para dados num serviço de pesquisa e deve ser apoiada através de um processo manual.  Também pode fazer backup nas chaves geridas pelo cliente no Cofre da Chave Azure. 

Ativar a proteção de eliminação e purga suave no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa. Se o Azure Storage for utilizado para armazenar cópias de segurança manuais, ative a eliminação suave para guardar e recuperar os seus dados quando as bolhas ou as imagens blob forem eliminadas. 

- [Recue e restaure um índice de Pesquisa Cognitiva Azure](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Como ativar a eliminação recuperável e a proteção contra remoção no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Excluir suave para armazenamento Azure Blob](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Desenvolva um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções do pessoal, bem como as fases de tratamento e gestão de incidentes, desde a deteção até à revisão pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque subscrições usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis. É sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde o incidente ocorreu.

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST, "Guide to Test, Training and Exercise Programs for IT Plans and Capabilities"](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Seu Centro de Segurança utilizando a função Exportação Contínua. A Exportação Contínua permite exportar alertas e recomendações manualmente ou contínuas. Pode utilizar o conector de dados do Centro de Segurança para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.
- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)