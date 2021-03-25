---
title: Linha de segurança Azure para Serviços Cognitivos
description: A linha de base de segurança dos Serviços Cognitivos fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 218810183f547d4e90043364a318615a204df9d8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044860"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Linha de segurança Azure para Serviços Cognitivos

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../security/benchmarks/overview-v1.md) aos Serviços Cognitivos. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável aos Serviços Cognitivos. Foram excluídos **os controlos** não aplicáveis aos Serviços Cognitivos.

 
Para ver como os Serviços Cognitivos mapeiam completamente o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança dos Serviços Cognitivos completo.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: A Microsoft Azure Cognitive Services fornece um modelo de segurança em camadas. Este modelo permite-lhe proteger as suas contas dos Serviços Cognitivos para um subconjunto específico de redes. Quando as regras de rede são configuradas, apenas as aplicações que pedem dados sobre o conjunto especificado de redes podem aceder à conta. Pode limitar o acesso aos seus recursos com a filtragem de pedidos, permitindo apenas pedidos originários de endereços IP especificados, intervalos IP ou de uma lista de sub-redes em Redes Virtuais Azure.

O suporte de rede virtual e de ponto final de serviço para serviços cognitivos está limitado a um conjunto específico de regiões.

- [Como configurar redes virtuais de Serviços Cognitivos Azure](./cognitive-services-virtual-networks.md?tabs=portal)

- [Visão geral das Redes Virtuais Azure](../virtual-network/virtual-networks-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Quando as Máquinas Virtuais são implantadas na mesma rede virtual que o seu contentor de Serviços Cognitivos, pode utilizar grupos de segurança de rede para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo dos grupos de segurança da rede e enviar registos para uma conta de armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo de grupos de segurança de rede para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Se estiver a utilizar serviços cognitivos dentro de um recipiente, pode aumentar a sua implantação do contentor com uma solução frontal de firewall de aplicação web que filtra tráfego malicioso e suporta encriptação TLS de ponta a ponta, mantendo o ponto final do contentor privado e seguro. 

Tenha em mente que os recipientes dos Serviços Cognitivos são obrigados a submeter informações de medição para efeitos de faturação. A única exceção é os contentores offline, uma vez que seguem uma metodologia de faturação diferente. A não identificação de vários canais de rede em que os contentores dos Serviços Cognitivos dependem impedirá o funcionamento do contentor. O anfitrião deve permitir a lista da porta 443 e os seguintes domínios:

- *.cognitive.microsoft.com
- *.cognitiveservices.azure.com

Note também que deve desativar a inspeção de pacotes profundos para a sua solução de firewall nos canais seguros que os contentores dos Serviços Cognitivos criam para os servidores da Microsoft. Se não o fizer, o recipiente funcione corretamente.

- [Compreenda a segurança do contentor dos Serviços Cognitivos Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Quando as máquinas virtuais forem implantadas na mesma rede virtual que o seu contentor de Serviços Cognitivos, defina e implemente configurações de segurança padrão para recursos de rede relacionados com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.CognitiveServices" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu Cache Azure para instâncias Redis. Pode também utilizar definições políticas incorporadas, tais como:

- A Norma de Proteção DDoS deve ser ativada

Use plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de gestor de recursos Azure, controlo de acesso baseado em funções Azure (Azure RBAC), e políticas, numa única definição de planta. Aplique facilmente o projeto em novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

Se estiver a utilizar serviços cognitivos dentro de um contentor, pode aumentar a sua implantação do contentor com uma solução de firewall de aplicação web virada para a frente que filtra tráfego malicioso e suporta encriptação TLS de ponta a ponta, mantendo o ponto final do contentor privado e seguro. 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

- [Compreenda a segurança do contentor dos Serviços Cognitivos Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Quando as máquinas virtuais são implantadas na mesma rede virtual que o seu contentor serviços cognitivos, pode utilizar grupos de segurança de rede para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo dos grupos de segurança da rede e enviar registos para uma conta de armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo de grupos de segurança de rede para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Se utilizar serviços cognitivos dentro de um recipiente, pode aumentar a sua implantação do contentor com uma solução frontal de firewall de aplicação web que filtra tráfego malicioso e suporta encriptação TLS de ponta a ponta, mantendo o ponto final do contentor privado e seguro.  Pode selecionar uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com a capacidade de desativar a inspeção de carga útil.

Tenha em mente que os recipientes dos Serviços Cognitivos são obrigados a submeter informações de medição para efeitos de faturação. A única exceção são os contentores offline, uma vez que seguem uma metodologia de faturação diferente. A não identificação de vários canais de rede em que os contentores dos Serviços Cognitivos dependem impedirá o funcionamento do contentor. O anfitrião deve permitir a lista da porta 443 e os seguintes domínios:

- *.cognitive.microsoft.com
- *.cognitiveservices.azure.com

Note também que deve desativar a inspeção de pacotes profundos para a sua solução de firewall nos canais seguros que os contentores dos Serviços Cognitivos criam para os servidores da Microsoft. Se não o fizer, o recipiente funcione corretamente.

- [Compreenda a segurança do contentor dos Serviços Cognitivos Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Se utilizar serviços cognitivos dentro de um recipiente, pode aumentar a sua implantação do contentor com uma solução frontal de firewall de aplicação web que filtra tráfego malicioso e suporta encriptação TLS de ponta a ponta, mantendo o ponto final do contentor privado e seguro. 

Tenha em mente que os recipientes dos Serviços Cognitivos são obrigados a submeter informações de medição para efeitos de faturação. A única exceção é os contentores offline, uma vez que seguem uma metodologia de faturação diferente. A não identificação de vários canais de rede em que os contentores dos Serviços Cognitivos dependem impedirá o funcionamento do contentor. O anfitrião deve permitir a lista da porta 443 e os seguintes domínios:

- *.cognitive.microsoft.com
- *.cognitiveservices.azure.com

Note também que deve desativar a inspeção de pacotes profundos para a sua solução de firewall nos canais seguros que os contentores dos Serviços Cognitivos criam para os servidores da Microsoft. Se não o fizer, o recipiente funcione corretamente.

- [Compreenda a segurança do contentor dos Serviços Cognitivos Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Também pode utilizar grupos de segurança de aplicações para ajudar a simplificar a configuração complexa de segurança. Os grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural da estrutura de uma aplicação, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos.

- [Tags de serviço de rede virtual](../virtual-network/service-tags-overview.md)

- [Grupos de Segurança de Aplicações](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede relacionados com o seu contentor de Serviços Cognitivos com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.CognitiveServices" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu Cache Azure para instâncias Redis.

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em funções (Azure RBAC), e políticas, numa única definição de planta. Aplique facilmente o projeto em novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos de rede associados ao seu contentor de Serviços Cognitivos para os organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o registo de Atividade Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com o seu contentor de Serviços Cognitivos. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho log Analytics, centro de eventos Azure ou conta de armazenamento Azure para o arquivo. Os registos de atividade fornecem informações sobre as operações que foram realizadas no seu contentor de Serviços Cognitivos ao nível do plano de controlo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para a sua Cache Azure para instâncias Redis.

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Para controlar a verificação do plano, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Adicionalmente, os Serviços Cognitivos enviam eventos de diagnóstico que podem ser recolhidos e utilizados para efeitos de análise, alerta e reporte. Pode configurar as definições de diagnóstico de um recipiente de Serviços Cognitivos através do portal Azure. Pode enviar um ou mais eventos de diagnóstico para uma conta de armazenamento, centro de eventos ou um espaço de trabalho Log Analytics.

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/essentials/activity-log.md)

- [Utilização de configurações de diagnóstico para serviços cognitivos Azure](diagnostic-logging.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desa um ponto final no período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize as contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Como definir parâmetros de retenção de registos para log analytics workspaces](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho do Log Analytics. Estes registos fornecem dados ricos e frequentes sobre o funcionamento de um recurso que são utilizados para identificar e depurar. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights baseados nos Dados de Registo de Atividade que possam ter sido recolhidos para os Serviços Cognitivos Azure.

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/essentials/activity-log.md)

- [Como recolher e analisar registos de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Pode elevar alertas sobre métricas suportadas nos Serviços Cognitivos indo para a secção alertas e métricas no Monitor Azure.

Configure as definições de diagnóstico para o seu recipiente de Serviços Cognitivos e envie registos para um espaço de trabalho log analytics. Dentro do seu espaço de trabalho Log Analytics, os alertas de configuração devem ocorrer para quando um conjunto de condições pré-definidos ocorrer. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Criar, visualizar e gerir alertas de registo usando o Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?amp;preserve-view=true&view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?amp;preserve-view=true&view=azureadps-2.0)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O acesso do avião aos Serviços Cognitivos é controlado através do Azure Ative Directory (Azure AD). A Azure AD não tem o conceito de senhas padrão.

O acesso de um plano de dados aos Serviços Cognitivos é controlado através de chaves de acesso. Estas chaves são utilizadas pelos clientes que se ligam à sua cache e podem ser regeneradas a qualquer momento.

Não é recomendável que construa senhas padrão na sua aplicação. Em vez disso, pode armazenar as suas palavras-passe no Cofre da Chave Azure e depois utilizar o Azure AD para as recuperar.

- [Como regenerar a Cache Azure para chaves de acesso Redis](../azure-cache-for-redis/cache-configure.md#settings)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize o Centro de Segurança Gestão de Identidade e Acesso para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

- [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Os Serviços Cognitivos utilizam as teclas de acesso para autenticar os utilizadores e não suportam uma única sintenção (SSO) ao nível do plano de dados. O acesso ao plano de controlo dos Serviços Cognitivos está disponível através da REST API e suporta a SSO. Para autenticar, detenha o cabeçalho de Autorização para os seus pedidos a um Token Web JSON (JavaScript Object Notation) que obtém do Azure Ative Directory (Azure AD).

- [Compreenda os Serviços Cognitivos Azure REST API](/rest/api/cognitiveservices/)

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Permitir a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações de Gestão de Identidade e Acesso do Centro de Segurança.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multifactor configurada para iniciar sessão e configurar recursos Azure. 

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Além disso, utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Configurar localizações nomeadas no Azure Ative Directory (Azure AD) Acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países e regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. O Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito e também sais, hashes e armazena de forma segura as credenciais dos utilizadores. Se o seu caso de utilização suportar a autenticação AD, utilize a Azure AD para autenticar pedidos à API dos Serviços Cognitivos. 

Atualmente, apenas a API de Visão De Computador, API facial, API de Análise de Texto, Leitor Imersivo, Reconhecedor de Formulários, Detetor de Anomalias e todos os serviços de Bing, exceto a autenticação de suporte de pesquisa personalizada Bing usando Azure AD.

- [Como autenticar pedidos aos Serviços Cognitivos](./authentication.md#authenticate-with-azure-active-directory)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, o cliente utilizar a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso dos utilizadores pode ser revisto regularmente para garantir que apenas os utilizadores ativos tenham acesso continuado.

Cliente para manter o inventário das contas de utilizador da API Management, conciliar o acesso conforme necessário. Na Gestão da API, os desenvolvedores são os utilizadores das APIs que expõe usando a API Management. Por padrão, as contas de programadores recém-criadas são Ativas e associadas ao grupo Developers. As contas de programadores que se encontrem num estado ativo podem ser usadas para aceder a todas as APIs para as quais têm subscrições.

- [Como gerir contas de utilizador na Gestão de API do Azure](../api-management/api-management-howto-create-or-invite-developers.md)

- [Como obter a lista de utilizadores da API Management](/powershell/module/az.apimanagement/get-azapimanagementuser?amp;preserve-view=true&view=azps-4.8.0)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a atividade de login, auditoria e log de eventos de risco da Azure Ative, que lhe permitem integrar-se com o Azure Sentinel ou com um SIEM de terceiros.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo pretendidos no Log Analytics.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Para desvio de comportamento de login de conta no plano de controlo, utilize o Azure Ative Directory (Azure AD) Proteção de identidade e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não disponível para Serviços Cognitivos. O Lockbox do cliente ainda não é suportado para serviços cognitivos.

- [Lista de serviços suportados pelo Cliente Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar assinaturas ou grupos de gestão separados para desenvolvimento, teste e produção. Os recursos devem ser separados por Redes Virtuais ou sub-redes, marcados adequadamente e protegidos por um grupo de segurança de rede ou firewall Azure. Os recursos que armazenam ou processam dados sensíveis devem ser suficientemente isolados. Para máquinas virtuais armazenar ou processar dados sensíveis, implementar políticas e procedimentos para desligá-los quando não estiverem a ser utilizados.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alerta ou alerta e negar com a Azure Firewall](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Ainda não disponível. As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os Serviços Cognitivos.

A Microsoft gere a infraestrutura subjacente aos Serviços Cognitivos e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Todos os pontos finais dos Serviços Cognitivos são expostos sobre HTTP executar TLS 1.2. Com um protocolo de segurança forçado, os consumidores que tentem chamar um ponto final dos Serviços Cognitivos devem aderir a estas orientações:

- O Sistema Operativo cliente (OS) precisa de suportar o TLS 1.2.
- O idioma (e plataforma) utilizado para fazer a chamada HTTP precisa de especificar tLS 1.2 como parte do pedido. Dependendo do idioma e da plataforma, especificar o TLS é feito implicitamente ou explicitamente.

- [Compreender a segurança da camada de transporte para os serviços cognitivos Azure](cognitive-services-security.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os Serviços Cognitivos. Identificar instâncias que contenham informações sensíveis como tal e implementar solução de terceiros, se necessário para efeitos de conformidade.

A Microsoft gere a plataforma subjacente e trata todos os conteúdos do cliente como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos 

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso ao plano de controlo dos Serviços Cognitivos (ou seja, portal Azure). 

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: A encriptação em repouso para serviços cognitivos depende do serviço específico que está a ser utilizado. Na maioria dos casos, os dados são encriptados e desencriptados utilizando encriptação AES de 140-2 conforme fips 140-2. A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para os clientes pela Microsoft. Os dados do cliente são seguros por padrão e não precisam de modificar o seu código ou aplicações para tirar partido da encriptação.

Também pode utilizar o Azure Key Vault para armazenar as suas chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves.

- [Lista de serviços que encriptam informação em repouso](./encryption/cognitive-services-encryption-keys-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações em casos de produção de Serviços Cognitivos e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar ou descobrir todos os recursos (tais como computação, armazenamento, rede, portas, protocolos e assim por diante) dentro das suas subscrições. Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?amp;preserve-view=true&view=azps-4.8.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear a Cache Azure para instâncias redis e recursos relacionados. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, utilize o Azure Resource Graph para consultar ou descobrir recursos dentro das subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para o seu recipiente de Serviços Cognitivos com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.CognitiveServices" para criar políticas personalizadas para auditar ou impor a configuração da sua Cache Azure para instâncias Redis.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?amp;preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] efeitos, para impor configurações seguras através dos seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se estiver a utilizar definições personalizadas de Política Azure ou modelos de Gestor de Recursos Azure para os seus recipientes de Serviços Cognitivos e recursos relacionados, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/?amp;preserve-view=true&view=azure-devops)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.Cache" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Use pseudónimos da Política Azure no espaço de nomes "Microsoft.CognitiveServices" para criar definições personalizadas de Política Azure para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] efeitos, para impor automaticamente configurações para o seu Azure Cache para instâncias Redis e recursos relacionados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder à sua API de Serviços Cognitivos, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger a gestão de chaves dos Serviços Cognitivos. Certifique-se de que a eliminação suave do Cofre da Chave está ativada.

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Cofre-Chave](../key-vault/secrets/quick-create-portal.md)

- [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder à sua API de Serviços Cognitivos, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger a gestão de chaves dos Serviços Cognitivos. Certifique-se de que o cofre da chave é excluído suave.

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo Azure Key Vault, sem quaisquer credenciais no seu código.

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

**Orientação**: O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Cache para Redis), no entanto não funciona com conteúdo do cliente.

Pré-digitalize qualquer conteúdo que seja enviado para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, e assim por diante. A Microsoft não pode aceder aos seus dados nestes casos.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Os dados na sua conta de armazenamento Microsoft Azure são sempre replicados automaticamente para garantir a durabilidade e alta disponibilidade. O Azure Storage copia os seus dados de modo a que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. Pode optar por replicar os seus dados dentro do mesmo centro de dados, através de centros de dados zonais dentro da mesma região, ou em regiões geograficamente separadas. 

Também pode utilizar a funcionalidade de gestão do ciclo de vida para fazer o back up dados para o nível Archive. Além disso, ative a eliminação suave para as suas cópias de segurança armazenadas na conta de Armazenamento.

- [Compreender os acordos de redundância e Service-Level de armazenamento da Azure](../storage/common/storage-redundancy.md)

- [Gerir o ciclo de vida do Armazenamento de Blobs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Utilize o Gestor de Recursos Azure para implantar serviços cognitivos e recursos conexos. O Azure Resource Manager fornece a capacidade de exportar modelos, o que lhe permite redistribuir a sua solução ao longo do ciclo de vida do desenvolvimento e ter confiança que os seus recursos são implantados num estado consistente. Utilize a Azure Automation para chamar regularmente o modelo de Azure Resource Manager para exportar API. Faça o reforço das chaves pré-partilhadas dentro do Cofre da Chave Azure.

- [Descrição geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Como criar um recurso de Serviços Cognitivos usando um modelo de Gestor de Recursos Azure](./create-account-resource-manager-template.md?tabs=portal)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos - Modelo de exportação](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à Azure Automation](../automation/automation-intro.md)

- [Como backup chaves chave cofre em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Garantir a capacidade de executar periodicamente a implementação de modelos do Gestor de Recursos Azure regularmente para uma subscrição isolada, se necessário. Teste de restauração de chaves pré-partilhadas.

- [Implementar recursos com modelos ARM e portal Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura os seus modelos de Gestor de Recursos Azure. Para proteger os recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se estiverem integrados com Azure DevOps ou Ative Directory se integrados com o Team Foundation Server.

Utilize o controlo de acesso baseado em funções Azure para proteger as chaves geridas pelo cliente. Ativar Soft-Delete e limpar a proteção no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa. 

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Você também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

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

**Orientação**: Exporte os alertas e recomendações do Seu Centro de Segurança utilizando a função Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança para ativar automaticamente respostas através de "Aplicações lógicas" em alertas e recomendações de segurança.

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

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)