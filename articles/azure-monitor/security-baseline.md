---
title: Linha de segurança Azure para O Monitor Azure
description: A linha de base de segurança Azure Monitor fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bd3bb5e3060752d086f80f44a8f136cb861669c0
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078524"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Linha de segurança Azure para O Monitor Azure

Esta linha de base de segurança aplica orientações da [versão Azure Security Benchmark1.0](../security/benchmarks/overview-v1.md) ao Azure Monitor. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Monitor. **Foram excluídos os controlos** não aplicáveis ao Azure Monitor, ou para os quais a responsabilidade é da Microsoft.

Para ver como o Azure Monitor mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança Azure Monitor completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Ative o Azure Private Link para permitir o acesso aos Serviços Azure SaaS (por exemplo, Azure Monitor) e Azure acolheu serviços de cliente/parceiro ao longo de um Ponto Final Privado na sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública.

Para permitir que o tráfego chegue ao Azure Monitor, utilize as etiquetas de serviço "AzureMonitor" para permitir o tráfego de entrada e saída através de grupos de segurança de rede. Para permitir que o tráfego de teste de monitorização de disponibilidade chegue ao Monitor Azure, utilize a etiqueta de serviço "ApplicationInsightsAvailability" para todo o tráfego de entrada através de grupos de segurança de rede. Para permitir que as notificações de alerta cheguem aos pontos finais do cliente, utilize a etiqueta de serviço "ActionGroup" para permitir o tráfego de entrada através de grupos de segurança de rede.

As regras de rede virtual permitem ao Azure Monitor apenas aceitar comunicações que sejam enviadas a partir de sub-redes selecionadas dentro de uma rede virtual.

Utilize o gateway Do Log Analytics para enviar dados para um espaço de trabalho do Log Analytics no Azure Monitor em nome dos computadores que não conseguem ligar-se diretamente à internet impedindo a necessidade de computadores serem ligados à internet. 

- [Como configurar o Link Privado para o Azure Monitor](/azure/azure-monitor/platform/private-link-security)

- [Conecte computadores sem acesso à Internet utilizando o gateway Log Analytics no Azure Monitor](/azure/azure-monitor/platform/gateway)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: O Azure Monitor é um serviço central e não suporta a implantação diretamente numa rede virtual, a sua infraestrutura subjacente é tratada pela Microsoft. No entanto, para os recursos que fazem ligações de rede à oferta do Azure Monitor, garantam a sua rede com um grupo de segurança de rede. Ativar os registos de fluxo do grupo de segurança da rede e enviar registos numa Conta de Armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Ao utilizar o Azure Monitor com Private Link, obtém acesso ao registo de rede, como "Dados processados pelo Ponto Final Privado (IN/OUT)".

- [Requisitos de rede para agentes do Monitor Azure](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Conecte computadores sem acesso à Internet utilizando o gateway Log Analytics no Azure Monitor](/azure/azure-monitor/platform/gateway)

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para permitir que o tráfego chegue ao Azure Monitor, utilize as etiquetas de serviço "AzureMonitor" para permitir o tráfego de entrada e saída através de Grupos de Segurança de Rede. Para permitir que o tráfego de teste de monitorização da disponibilidade chegue ao Azure Monitor, utilize a etiqueta de serviço "ApplicationInsightsAvailability" para todo o tráfego de entrada através de grupos de segurança de rede. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Compreender e utilizar tags de serviço](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: O Azure Monitor faz parte dos serviços centrais do Azure e não pode ser implantado como um serviço separadamente. Os componentes do Azure Monitor, incluindo o Agente monitor Azure, e a Application Insights SDK podem ser implantados com os seus recursos, o que pode afetar a postura de segurança desses recursos.

- [Requisitos de rede para agentes do Monitor Azure](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Conecte computadores sem acesso à Internet utilizando o gateway Log Analytics no Azure Monitor](/azure/azure-monitor/platform/gateway) 

- [Ver a começar com a Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#get-started)

- [Como configurar testes web de disponibilidade](app/monitor-web-app-availability.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações dos recursos e detetar alterações nos recursos da sua rede relacionadas com o Azure Monitor. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações a esses recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: O Monitor azul usa registos de atividade para registar alterações nos seus recursos. Você pode exportar estes registos para Azure Storage, Event Hub ou um espaço de trabalho Log Analytics. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. Dentro do Azure Monitor, pode consultar e efetuar análises contra os dados, utilizar contas de armazenamento Azure para qualquer armazenamento de registos a longo prazo/arquivo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: O Monitor azul utiliza registos de atividade, o Registo de Atividades é automaticamente ativado e regista operações tomadas nos recursos do Azure Monitor, tais como: quem iniciou a operação, quando ocorreu a operação, o estado da operação e outras informações úteis de auditoria. 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Compreenda o registo e diferentes tipos de registo em Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, desa um ponto final no seu período de retenção do espaço de trabalho Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para qualquer armazenamento a longo prazo/arquivo dos seus registos.

- [Alterar o período de retenção de dados em Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados. Utilize o Azure Monitor e um espaço de trabalho log Analytics para rever registos e realizar consultas nos dados de registo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Começando com consultas log analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Como realizar consultas personalizadas no Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Centro de Segurança Azure com o log analytics espaço de trabalho para monitorizar e alertar sobre a atividade anómala encontrada em registos de segurança e eventos. Em alternativa, pode ativar e a bordo dados para a Azure Sentinel.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](/azure/azure-monitor/learn/tutorial-response)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O controlo de acesso baseado em funções (Azure RBAC) permite-lhe gerir o acesso aos recursos Azure através de atribuições de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas predefinidas para determinados recursos, que podem ser inventariadas ou consultadas com ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure.

- [Como obter um papel de diretório no Azure Ative Directory (Azure AD) com a PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Também pode ativar um Acesso Rápido / Justo-Suficiente utilizando o Azure Ative Directory (Azure AD) Funções privilegiadas de gestão de identidade privilegiada para serviços da Microsoft e gestor de recursos Azure.

- [Visão geral da gestão de identidade privilegiada da Azure AD](../active-directory/privileged-identity-management/pim-configure.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Sempre que possível, utilize o Azure Ative Directory (Azure AD) SSO em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactorA Azure Ative (Azure AD) e seguir as recomendações de identidade e acesso do Azure Security Center.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho segura gerida pelo Azure (também conhecida como Estação de Trabalho de Acesso Privilegiado, ou PAW) para tarefas administrativas que exijam privilégios elevados.

- [Compreenda estações de trabalho seguras e geridas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactorA Azure Ative (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança e monitorização do Azure Ative Directory (Azure AD) para detetar quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring/)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a Fontes de registo de registo de eventos de inscrição, de auditoria e de eventos de risco da Azure Ative, que lhe permitem integrar-se com qualquer ferramenta SIEM/Monitoring. Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize funcionalidades de Proteção de Risco e Identidade Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize tags sempre que possível para ajudar a rastrear os recursos do Azure Monitor que armazenam ou processam informações sensíveis como os espaços de trabalho do Log Analytics.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Gerir o acesso a dados de registo e áreas de trabalho no Azure Monitor](/azure/azure-monitor/platform/manage-access)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar o isolamento utilizando assinaturas e grupos de gestão separados para domínios de segurança individuais, tais como o tipo de ambiente e o nível de sensibilidade aos dados. Pode restringir o nível de acesso ao seu Azure Monitor e recursos relacionados que as suas aplicações e ambientes empresariais exigem. Pode controlar o acesso ao Azure Monitor através do controlo de acesso baseado em funções Azure (Azure RBAC).

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Azure Monitor negoceia TLS 1.2 por defeito. Certifique-se de que qualquer cliente que se conecte aos seus recursos Azure seja capaz de negociar TLS 1.2 ou superior. 

Os Insights de Aplicação e o Log Analytics continuam a permitir que os dados TLS 1.1 e TLS 1.0 sejam ingeridos. Os dados podem ser restringidos ao TLS 1.2 configurando do lado do cliente.

- [Como enviar dados de forma segura usando tLS 1.2](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Monitor Azure. Implementar solução de terceiros, se necessário para efeitos de conformidade.
Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (RBAC) para gerir o acesso ao Azure Monitor.

- [Funções, permissões e segurança no Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: O Azure Monitor garante que todos os dados e consultas guardadas são encriptados em repouso utilizando as teclas geridas pela Microsoft (MMK). O Azure Monitor também fornece uma opção de encriptação utilizando a sua própria chave que é armazenada no seu Cofre de Chave Azure e acedida por armazenamento utilizando a autenticação de identidade gerida atribuída pelo sistema. Esta chave gerida pelo cliente (CMK) pode estar protegida por software ou hardware-HSM.

- [Chaves geridas pelo cliente Azure Monitor](/azure/azure-monitor/platform/customer-managed-keys)

- [Log Analytics segurança de dados](/azure/azure-monitor/platform/data-security)

- [Recolha, retenção e armazenamento de dados no Application Insights](app/data-retention-privacy.md)

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações no Monitor Azure e recursos conexos.

- [Como criar alertas para eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, Sistema Comum de Pontuação de Vulnerabilidade) ou as classificações de risco padrão fornecidas pela sua ferramenta de digitalização de terceiros.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o CLI Azure para consultar e descobrir os recursos do Azure Monitor dentro das suas subscrições. Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

- [Azure Monitor CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest&amp;preserve-view=true)

- [Como ver as suas Subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

- [Funções, permissões e segurança no Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure Monitor, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos relacionados com o Azure Monitor. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as suas necessidades organizacionais.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições.

Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Conciliar o inventário regularmente e garantir que os recursos relacionados com o Azure Monitor não autorizados sejam eliminados da subscrição em tempo útil.  

- [Excluir espaço de trabalho Azure Log Analytics](/azure/azure-monitor/platform/delete-workspace)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para restringir os recursos relacionados com o Azure Monitor que pode obter no seu ambiente.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize definições personalizadas da Política Azure para auditar ou impor a configuração dos seus recursos relacionados com o Azure Monitor. Também pode utilizar definições de Política Azure incorporadas, conforme disponível.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo na Notação de Objetos JavaScript (JSON), que deve ser revisto para garantir que as configurações cumprem /excedem os requisitos de segurança para a sua organização.

Também pode utilizar recomendações do Azure Security Center como uma base de configuração segura para os seus recursos Azure.

Se utilizar as capacidades de streaming APM ao vivo, torne o canal seguro com uma chave API secreta para além da chave de instrumentação.

- [Fluxo de métricas ao vivo seguro APM](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a política Azure [negar] e [implementar se não existir] para impor definições seguras em todos os seus recursos relacionados com o Azure Monitor.  Além disso, pode utilizar modelos do Gestor de Recursos Azure para manter a configuração de segurança dos recursos relacionados com o Azure Monitor exigidos pela sua organização.

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas e modelos de Gestor de Recursos Azure. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Definir e implementar configurações de segurança padrão para recursos relacionados com o Azure Monitor utilizando a Política Azure. Utilize definições personalizadas da Política Azure para auditar ou impor a configuração de segurança dos seus recursos relacionados com o Azure Monitor. Você também pode fazer uso de definições políticas incorporadas relacionadas com os seus recursos específicos.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pseudónimos da Política Azure](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Centro de Segurança Azure para efetuar análises de base para os seus recursos relacionados com o Azure Monitor.  Além disso, utilize a Azure Policy para alertar e auditar as configurações de recursos do Azure.

- [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Use a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para recursos relacionados com monitores Azure suportados.

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Serviços que suportam identidades geridas para recursos da Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Como criar um Cofre-Chave](../key-vault/secrets/quick-create-portal.md)

- [Como fornecer a autenticação do Cofre-Chave com uma identidade gerida](/azure/key-vault/general/assign-access=policy-portal)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). As identidades geridas permitem-lhe autenticar qualquer serviço que suporte a autenticação Azure AD, incluindo recursos do Azure Monitor, sem quaisquer credenciais no seu código.

- [Como configurar identidades geridas para os recursos da Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

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

**Orientação**: O Microsoft Anti-malware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, recursos relacionados com o Azure Monitor), no entanto não funciona no seu conteúdo. 

Pré-digitalizar quaisquer ficheiros que sejam carregados para recursos relacionados com o Azure Monitor, tais como log analytics espaço de trabalho.

Utilize a deteção de ameaças do Azure Security Center para detetar malware enviado para contas de armazenamento. 

- [Compreenda o Anti-malware da Microsoft para serviços em nuvem azure e máquinas virtuais](../security/fundamentals/antimalware.md)

- [Compreenda a deteção de ameaças do Azure Security Center para serviços de dados](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Utilize o Gestor de Recursos Azure para exportar o Monitor Azure e recursos relacionados num modelo javaScript Object Notation (JSON) que pode ser usado como cópia de segurança para o Monitor Azure e configurações relacionadas.  Utilize a Azure Automation para executar automaticamente os scripts de reserva. 

- [Gerir o espaço de trabalho do Log Analytics utilizando modelos de Gestor de Recursos Azure](/azure/azure-monitor/samples/resource-manager-workspace)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Sobre a Azure Automation](../automation/automation-intro.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Utilize o Gestor de Recursos Azure para exportar o Monitor Azure e recursos relacionados num modelo javaScript Object Notation (JSON) que pode ser usado como cópia de segurança para o Monitor Azure e configurações relacionadas. Chaves geridas pelo cliente de backup dentro do Cofre de Chaves Azure se os recursos relacionados com o Azure Monitor estiverem a usar chaves geridas pelo cliente,

- [Gerir o espaço de trabalho do Log Analytics utilizando modelos de Gestor de Recursos Azure](/azure/azure-monitor/platform/template-workspace-configuration)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Como backup chaves chave cofre em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Certifique-se de que a restauração é executada periodicamente utilizando ficheiros de modelos apoiados pelo Azure Resource Manager. Teste de restauração de chaves geridas pelo cliente.

- [Gerir o espaço de trabalho do Log Analytics utilizando modelos de Gestor de Recursos Azure](/azure/azure-monitor/samples/resource-manager-workspace)

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure. Para proteger os recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se estiverem integrados com Azure DevOps ou Ative Directory se integrados com TFS.  Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para proteger as chaves geridas pelo cliente.

Além disso, Ative Soft-Delete e purpurar a proteção no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa. Se o Azure Storage for utilizado para armazenar cópias de segurança do modelo do Azure Resource Manager, ative a eliminação suave para guardar e recuperar os seus dados quando as bolhas ou as imagens blob forem eliminadas.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Como permitir a proteção de Soft-Delete e purga no Cofre de Chaves](../storage/blobs/soft-delete-blob-overview.md)

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Alavancar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
