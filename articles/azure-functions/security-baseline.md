---
title: Linha de segurança Azure para Funções Azure
description: A linha de base de segurança Azure Functions fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4fd53067309f83b284da25040f9f6534936cead9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704675"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Linha de segurança Azure para Funções Azure

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../security/benchmarks/overview-v1.md) às Funções Azure. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável às Funções Azure. Foram excluídos **os controlos** não aplicáveis às Funções Azure.

 
Para ver como as funções do Azure funcionam completamente para o Benchmark de Segurança Azure, consulte o ficheiro completo de [mapeamento de base de segurança Azure Functions](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Integre as suas aplicações Azure Functions com uma rede virtual Azure. As aplicações de função que executam no plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Azure App Service, que inclui a funcionalidade "Integração VNet".  As redes virtuais Azure permitem-lhe colocar muitos dos seus recursos Azure, como as Funções Azure, numa rede de encaminhamento não internet.

- [Como integrar funções com uma Rede Virtual Azure](functions-create-vnet.md)

- [Compreender a Integração da Vnet para funções Azure e o Serviço de Aplicações Azure](../app-service/web-sites-integrate-with-vnet.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Utilize o Azure Security Center e siga recomendações de proteção da rede para ajudar a garantir recursos de rede e configurações de rede relacionadas com as suas aplicações Azure Functions.

Se utilizar grupos de segurança de rede com a sua implementação de Funções Azure, ative os registos de fluxo dos grupos de segurança da rede e envie registos para uma Conta de Armazenamento Azure para auditorias de tráfego. Também pode enviar registos de fluxo de grupos de segurança de rede para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Para garantir totalmente os pontos finais das suas funções Azure na produção, deve considerar a implementação de uma das seguintes opções de segurança ao nível da aplicação da função:

- Ligue a autenticação ou autorização do Serviço de Aplicações para a sua aplicação de função

- Utilizar a Azure API Management (APIM) para autenticar pedidos

- Implemente a sua aplicação de função para um Ambiente de Serviço de Aplicações Azure.

Além disso, certifique-se de que a depuração remota foi desativada para a sua produção Azure Functions. Além disso, a Partilha de Recursos de Origem Cruzada (CORS) não deve permitir que todos os domínios acedam à sua aplicação de função em Azure. Permitir que apenas os domínios necessários interajam com a sua aplicação de função.

Considere a implementação do Azure Web Application Firewall (WAF) como parte da configuração de rede para inspeção adicional do tráfego de entrada. Ativar a Definição de Diagnóstico para WAF e ingerir registos numa conta de armazenamento, no Centro de Eventos ou no Espaço de Trabalho do Log Analytics. 

- [Como garantir pontos finais do Azure Functions na produção](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative o DDoS Protection Standard nas redes virtuais associadas às suas aplicações de função para proteger contra ataques DDoS. Use funcionalidades de Inteligência de Ameaças no Azure Security Center Integrado para negar comunicações com endereços IP públicos maliciosos ou não utilizados conhecidos.

Além disso, configurar um gateway frontal, como o Azure Web Application Firewall, para autenticar todos os pedidos de entrada e filtrar o tráfego malicioso. O Azure Web Application Firewall pode ajudar a proteger a sua aplicação de função inspecionando o tráfego web de entrada para bloquear injeções DE SQL, Scripting cross-Site, uploads de malware e ataques DDoS. A introdução de uma Firewall de Aplicação Web requer um Ambiente de Serviço de Aplicações ou a utilização de Pontos Finais Privados. Pode utilizar Os Pontos Finais Privados para as suas funções hospedadas nos planos Premium e App Service.

- [Opções de rede das Funções do Azure](functions-networking-options.md)

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Se utilizar o grupo de segurança da rede com a sua implementação de Funções Azure, ative os registos de fluxo do grupo de segurança da rede e envie registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo de grupos de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Configure um gateway frontal, como o Azure Web Application Firewall, para autenticar todos os pedidos de entrada e filtrar o tráfego malicioso. O Azure Web Application Firewall pode ajudar a proteger as suas aplicações de função inspecionando o tráfego web de entrada para bloquear injeções DE SQL, Scripting cross-Site, uploads de malware e ataques DDoS. A introdução de uma Firewall de Aplicação Web requer um Ambiente de Serviço de Aplicações ou a utilização de Pontos Finais Privados. Pode utilizar Os Pontos Finais Privados para as suas funções hospedadas nos planos Premium e App Service.

Em alternativa, existem várias opções de marketplace, como o Barracuda Web Application Firewall para Azure que estão disponíveis no Azure Marketplace, que incluem funcionalidades de deteção ou prevenção de intrusões.

- [Opções de rede das Funções do Azure](functions-networking-options.md)

- [Plano Premium de Funções Azure](functions-premium-plan.md)

- [Introdução aos Ambientes de Serviço de Aplicações](../app-service/environment/intro.md)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](../app-service/environment/network-info.md) 

- [Compreender firewall de aplicação web Azure](../web-application-firewall/overview.md)

- [Utilização de pontos finais privados para funções Azure](../app-service/networking/private-endpoint.md)

- [Compreender o Serviço de Nuvem WAF Barracuda](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Configure um gateway frontal para a sua rede, como o Azure Web Application Firewall com encriptação TLS de ponta a ponta. A introdução de uma Firewall de Aplicação Web requer um Ambiente de Serviço de Aplicações ou a utilização de Pontos Finais Privados. Pode utilizar Os Pontos Finais Privados para as suas funções hospedadas nos planos Premium e App Service.

- [Opções de rede das Funções do Azure](functions-networking-options.md)

- [Plano Premium de Funções Azure](functions-premium-plan.md)

- [Introdução aos Ambientes de Serviço de Aplicações](../app-service/environment/intro.md)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](../app-service/environment/network-info.md) 

- [Compreender firewall de aplicação web Azure](../web-application-firewall/overview.md)

- [Como configurar tLS de ponta a ponta utilizando o Gateway de Aplicação com o portal](../application-gateway/end-to-end-ssl-portal.md)

- [Utilização de pontos finais privados para funções Azure](../app-service/networking/private-endpoint.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtual para definir controlos de acesso à rede em grupo de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, AzureAppService) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Para mais informações sobre a utilização de tags de serviço](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para definições de rede relacionadas com as suas Funções Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.Web" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede das suas Funções Azure. Também pode utilizar definições políticas incorporadas para funções Azure, tais como:

- O CORS não deve permitir que todos os recursos acedam às suas apps de função

- A aplicação de função só deve estar acessível através do HTTPS

- A versão TLS mais recente deve ser usada na sua aplicação de função

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em funções Azure (Azure RBAC), e políticas numa única definição de planta. Pode aplicar facilmente o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Se utilizar o grupo de segurança da rede com a sua implementação de Funções Azure, utilize etiquetas para os NSGs e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais do grupo de segurança da rede, utilize o campo "Descrição" para especificar a necessidade do negócio e/ou duração e assim por diante, para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de política incorporadas do Azure relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para as definições de rede e recursos relacionados com as suas implementações de Funções Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nas definições ou recursos críticos da rede. 

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Para controlar a verificação do plano, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

A Azure Functions também oferece integração incorporada com a Azure Application Insights para monitorizar funções. O Application Insights recolhe dados de registo, desempenho e erro. Deteta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e a compreender como as suas funções são usadas.

Se tiver um registo de segurança/auditoria personalizado incorporado dentro da sua aplicação de função, ative a definição de diagnóstico "FunctionAppLogs" e envie os registos para um espaço de trabalho do Log Analytics, centro de eventos Azure ou conta de armazenamento Azure para arquivo. 

Opcionalmente, pode ativar e a bordo dados para a Azure Sentinel ou uma solução de informação de sistema de terceiros e gestão de eventos. 

- [Como ativar definições de diagnóstico para registo de atividades azure](/azure/azure-monitor/platform/activity-log)

- [Como configurar funções Azure com Insights de Aplicação Azure](functions-monitoring.md)

- [Como ativar definições de diagnóstico (registos gerados pelo utilizador) para funções Azure](functions-monitor-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Para controlar a verificação do plano, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Se tiver um registo de segurança/auditoria personalizado incorporado dentro da sua aplicação de função, ative a definição de diagnóstico "FunctionAppLogs" e envie os registos para um espaço de trabalho do Log Analytics, centro de eventos Azure ou conta de armazenamento Azure para arquivo. 

- [Como ativar definições de diagnóstico para registo de atividades azure](/azure/azure-monitor/platform/activity-log)

- [Como ativar definições de diagnóstico (registos gerados pelo utilizador) para funções Azure](functions-monitor-log-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação IaaS.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados às aplicações de função de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de registos](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure, bem como as definições de diagnóstico da sua aplicação de função e enviar os registos para um espaço de trabalho do Log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos.

Ative o Application Insights para as suas aplicações de função para recolher dados de registo, desempenho e erro. Pode ver os dados de telemetria recolhidos pela Application Insights dentro do portal Azure.

Se tiver um registo de segurança/auditoria personalizado incorporado dentro da sua aplicação de função, ative a definição de diagnóstico "FunctionAppLogs" e envie os registos para um espaço de trabalho do Log Analytics, centro de eventos Azure ou conta de armazenamento Azure para arquivo. 

Opcionalmente, pode ativar e a bordo dados para a Azure Sentinel ou uma solução de informação de sistema de terceiros e gestão de eventos.

- [Como ativar as definições de diagnóstico para registo de atividades Azure](/azure/azure-monitor/platform/activity-log)

- [Como ativar as definições de diagnóstico para funções Azure](functions-monitor-log-analytics.md)

- [Como configurar funções Azure com Insights de Aplicação Azure e ver os dados da telemetria](functions-monitoring.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure, bem como as definições de diagnóstico da sua aplicação de função e enviar os registos para um espaço de trabalho do Log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos. Pode criar alertas com base nas suas consultas de espaço de trabalho Log Analytics.

Ative o Application Insights para as suas aplicações de função para recolher dados de registo, desempenho e erro. Pode ver os dados de telemetria recolhidos pela Application Insights e criar alertas dentro do portal Azure.

Opcionalmente, pode ativar e a bordo dados para a Azure Sentinel ou uma solução de informação de sistema de terceiros e gestão de eventos.

- [Como ativar as definições de diagnóstico para registo de atividades Azure](/azure/azure-monitor/platform/activity-log)

- [Como ativar as definições de diagnóstico para funções Azure](functions-monitor-log-analytics.md)

- [Como ativar insights de aplicação para funções Azure](https://docs.microsoft.com/azure/azure-functions/configure-monitoring#enable-application-insights-integration)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O acesso do avião ao Azure Functions é controlado através do Azure Ative Directory (Azure AD). A Azure AD não tem o conceito de senhas padrão.

O acesso a um avião de dados pode ser controlado através de vários meios, incluindo chaves de autorização, restrições de rede e validação de uma identidade AD AZure. As chaves de autorização são utilizadas pelos clientes que se ligam aos seus pontos finais Azure Functions HTTP e podem ser regeneradas a qualquer momento. Estas teclas são geradas para novos pontos finais HTTP por padrão.

Vários métodos de implementação estão disponíveis para funcionar apps, alguns dos quais podem alavancar um conjunto de credenciais geradas. Reveja os métodos de implementação que serão utilizados para a sua aplicação.

- [Proteja um ponto final HTTP](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Como obter e regenerar chaves de autorização](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#obtaining-keys)

- [Tecnologias de implantação em Funções Azure](functions-deployment-technologies.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição

- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição

- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Informações adicionais estão disponíveis nos links referenciados.

- [Como usar o Centro de Segurança Azure para monitorizar a identidade e o acesso ](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Sempre que possível, utilize o Azure Ative Directory (Azure AD) SSO em vez de configurar credenciais individuais autónomas para acesso a dados à sua aplicação de funções. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure. Implemente um único sinal de inscrição para as suas funções através da função De autenticação /Autorização do Serviço de Aplicação.

- [Compreender a autenticação e autorização em Funções Azure](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

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

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso ao portal Azure a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização das suas aplicações de função. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como configurar a sua app de função para usar o login AZure AD](../app-service/configure-authentication-provider-aad.md)

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring/)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização das suas aplicações de função. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Tem acesso a atividade de login AD, fontes de registo de auditoria e eventos de risco, que lhe permitem integrar-se com a Azure Sentinel ou com um SIEM de terceiros.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo pretendidos no Log Analytics.

- [Como configurar a sua app de função para usar o login AZure AD](../app-service/configure-authentication-provider-aad.md)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização das suas aplicações de função. Para desvio de comportamento de login de conta no plano de controlo (o portal Azure), utilize recursos de Proteção de Identidade AD AZure e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

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

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. As aplicações de função devem ser separadas por rede virtual (VNet)/sub-rede e marcadas adequadamente.

Também pode utilizar Pontos Finais Privados para realizar o isolamento da rede. Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço (por exemplo, app de função HTTPs endpoint) alimentado pela Azure Private Link. O Ponto Final Privado utiliza um endereço IP privado na VNet e leva de forma eficaz o serviço até à VNet. Pode utilizar Os Pontos Finais Privados para as suas funções hospedadas nos planos Premium e App Service.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Opções de rede das Funções do Azure](functions-networking-options.md)

- [Plano Premium de Funções Azure](functions-premium-plan.md)

- [Compreender o Ponto Final Privado](../private-link/private-endpoint-overview.md)

- [Utilização de pontos finais privados para funções Azure](../app-service/networking/private-endpoint.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: No portal Azure para as suas aplicações de função, em "Funcionalidades da Plataforma: Networking: SSL", ativar a definição "HTTPs Only" e definir a versão mínima TLS para 1.2.

- [Requer HTTPS em aplicações de função](https://docs.microsoft.com/azure/azure-functions/security-concepts#require-https)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Não disponível atualmente; as funcionalidades de identificação, classificação e prevenção de perdas não estão atualmente disponíveis para as Funções Azure. Tag Function apps que podem estar a processar informações sensíveis como tal e implementar solução de terceiros se necessário para fins de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) para gerir o acesso ao plano de controlo de aplicações de função (o portal Azure).

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

A Microsoft gere a infraestrutura subjacente às Funções Azure e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Ao criar uma aplicação de função, deve criar ou ligar para uma conta de Armazenamento Azure de uso geral que suporte o armazenamento de Blob, Queue e Table. Isto porque as funções dependem do Azure Storage para operações como a gestão de gatilhos e execuções de funções de registo. O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft. Para controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para encriptação de bolhas e dados de ficheiros. Estas teclas devem estar presentes no Cofre da Chave Azure para que a aplicação de função possa aceder à conta de armazenamento.

- [Compreender considerações de armazenamento para funções Azure](storage-considerations.md)

- [Compreenda a encriptação de armazenamento Azure para os dados em repouso](../storage/common/storage-service-encryption.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de Atividade Azure para criar alertas para quando ocorrerem alterações nas aplicações de função de produção, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Adote uma prática de DevSecOps para garantir que as suas aplicações de função estão seguras e permanecem o mais seguras possível durante todo o seu ciclo de vida. A DevSecOps incorpora a equipa de segurança da sua organização e as suas capacidades nas suas práticas de DevOps, tornando a segurança uma responsabilidade de todos na equipa.

Além disso, siga as recomendações do Azure Security Center para ajudar a proteger as suas aplicações de função.

- [Como adicionar validação contínua de segurança ao seu pipeline CI/CD](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&amp;preserve-view=true)

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: A Microsoft executa uma gestão de vulnerabilidades nos sistemas subjacentes que suportam as Funções Azure, no entanto poderá utilizar a gravidade das recomendações dentro do Azure Security Center, bem como a Pontuação Segura para medir o risco dentro do seu ambiente. A sua Pontuação Segura baseia-se na quantidade de recomendações do Centro de Segurança que atenuou. Para priorizar as recomendações para resolver primeiro, considere a gravidade de cada um.

- [Guia de referência de recomendações de segurança](../security-center/recommendations-reference.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s). Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a política da Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Informações adicionais estão disponíveis nos links referenciados.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Defina recursos Azure aprovados e software aprovado para recursos de computação.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição. 

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados. 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

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

**Orientação**: Defina e implemente configurações de segurança padrão para a sua aplicação de função com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Web" para criar políticas personalizadas para auditar ou impor a configuração das suas aplicações de funções. Pode também utilizar definições políticas incorporadas, tais como:

- A identidade gerida deve ser usada na sua aplicação de função

- Depuragem remota deve ser desligada para aplicações de função

- app de função só deve ser acessível através de HTTPS

Informações adicionais estão disponíveis nos links referenciados.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Armazenar e gerir modelos ARM e definições de política Azure personalizadas de forma segura no controlo de fontes.

- [O que é infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code)

- [Política de design como fluxos de trabalho de código](../governance/policy/concepts/policy-as-code.md)

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentação de Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize identidades geridas em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem. Identidades geridas permite que a sua aplicação de função autente a qualquer serviço que suporte a autenticação do Azure Ative Directory (Azure AD), incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como criar um Cofre-Chave](../key-vault/secrets/quick-create-portal.md)

- [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](../app-service/overview-managed-identity.md)

- [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

- [Utilize referências de Cofre de Chaves para Serviço de Aplicações e Funções Azure](../app-service/app-service-key-vault-references.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer à sua aplicação de função uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](../app-service/overview-managed-identity.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Utilize a função de cópia de segurança e restauro para agendar cópias de segurança regulares da sua aplicação. As aplicações de função que executam no plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Azure App Service, que inclui a funcionalidade "Backup and Restore".

Utilize também uma solução de controlo de origem, como a Azure Repos e a Azure DevOps, para armazenar e gerir o seu código de forma segura. Os Serviços de DevOps do Azure tiram partido das muitas funcionalidades de armazenamento do Azure para garantir a disponibilidade de dados no caso de falhas de hardware, interrupções de serviço ou desastre na região. Adicionalmente, a equipa do Azure DevOps segue procedimentos para proteger dados contra eliminação acidental ou maliciosa.

- [Efetuar cópia de segurança da sua aplicação no Azure](../app-service/manage-backup.md)

- [Compreender a disponibilidade de dados em Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability&amp;preserve-view=true)

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentação de Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Utilize a função de cópia de segurança e restauro para agendar cópias de segurança regulares da sua aplicação. As aplicações de função que executam no plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Azure App Service, que inclui a funcionalidade "Backup and Restore". Chaves geridas pelo cliente de reserva dentro do Cofre da Chave Azure.

Utilize também uma solução de controlo de origem, como a Azure Repos e a Azure DevOps, para armazenar e gerir o seu código de forma segura. Os Serviços de DevOps do Azure tiram partido das muitas funcionalidades de armazenamento do Azure para garantir a disponibilidade de dados no caso de falhas de hardware, interrupções de serviço ou desastre na região. Adicionalmente, a equipa do Azure DevOps segue procedimentos para proteger dados contra eliminação acidental ou maliciosa.

- [Efetuar cópia de segurança da sua aplicação no Azure](../app-service/manage-backup.md)

- [Como backup chaves chave cofre em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Compreender a disponibilidade de dados em Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability&amp;preserve-view=true)

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentação de Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Certifique-se de que a restauração é periódica a partir da função de backup e restauro. Se utilizar outra localização offline para fazer backup do seu código, certifique-se periodicamente de capacidade para efetuar restauros completos. Teste de restauração de chaves geridas pelo cliente.

- [Restaurar uma aplicação em Azure a partir de uma cópia de segurança](../app-service/web-sites-restore.md)

- [Restaurar uma aplicação em Azure a partir de um instantâneo](../app-service/app-service-web-restore-snapshots.md)

- [Como restaurar chaves chave do cofre em Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Cópias de segurança da funcionalidade Backup e Restore utilizam uma conta de Armazenamento Azure na sua subscrição. O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft. Para controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para encriptação de dados de armazenamento.

Se estiver a utilizar chaves geridas pelo cliente, certifique-se de Soft-Delete no Cofre de Chaves está ativado para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Encriptação do Armazenamento do Azure em repouso](../storage/common/storage-service-encryption.md)

- [Como permitir Soft-Delete em Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Guia de manuseamento de incidentes de segurança informática do NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Responsabilidade**: Partilhada

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

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas a alertas de segurança e recomendações com apps lógicas.

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
