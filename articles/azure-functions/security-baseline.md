---
title: Linha de segurança Azure para Funções Azure
description: Linha de segurança Azure para Funções Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5b38da5539cb80110b2a769a219213a5c74e1506
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98198560"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Linha de segurança Azure para Funções Azure

A Linha de Base de Segurança Azure para Funções Azure contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte a visão geral das [linhas de base de segurança Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](../security/benchmarks/security-control-network-security.md)*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: Integre as suas aplicações Azure Functions com uma rede virtual Azure. As aplicações de função que executam no plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Azure App Service, que inclui a funcionalidade "Integração VNet".  As redes virtuais Azure permitem-lhe colocar muitos dos seus recursos Azure, como as Funções Azure, numa rede de encaminhamento não internet.

- [Como integrar funções com uma Rede Virtual Azure](./functions-create-vnet.md)

- [Compreender a Integração da Vnet para funções Azure e o Serviço de Aplicações Azure](../app-service/web-sites-integrate-with-vnet.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICs

**Orientação**: Utilize o Azure Security Center e siga recomendações de proteção da rede para ajudar a garantir recursos de rede e configurações de rede relacionadas com as suas aplicações Azure Functions.

Se utilizar grupos de Segurança de Rede (NSGs) com a sua implementação de Funções Azure, ative os registos de fluxo NSG e envie registos para uma conta de armazenamento Azure para auditorias de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Para garantir totalmente os pontos finais das suas funções Azure na produção, deve considerar a implementação de uma das seguintes opções de segurança ao nível da aplicação da função:
- Ligue a autenticação /Autorização do Serviço de Aplicações para a sua aplicação de função,
- Utilizar a Azure API Management (APIM) para autenticar pedidos, ou
- Implemente a sua aplicação de função para um Ambiente de Serviço de Aplicações Azure.

Além disso, certifique-se de que a depuração remota foi desativada para a sua produção Azure Functions. Além disso, a Partilha de Recursos de Origem Cruzada (CORS) não deve permitir que todos os domínios acedam à sua aplicação de função em Azure. Permitir que apenas os domínios necessários interajam com a sua aplicação de função.

Considere a implementação do Azure Web Application Firewall (WAF) como parte da configuração de rede para inspeção adicional do tráfego de entrada. Ativar a Definição de Diagnóstico para WAF e ingerir registos numa conta de armazenamento, no Centro de Eventos ou no Espaço de Trabalho do Log Analytics. 

- [Como garantir pontos finais do Azure Functions na produção](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative o DDoS Protection Standard nas redes virtuais associadas às suas aplicações de funções para proteger contra ataques DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP públicos maliciosos ou não utilizados conhecidos.
Além disso, configurar um gateway frontal, como o Azure Web Application Firewall, para autenticar todos os pedidos de entrada e filtrar o tráfego malicioso. O Azure Web Application Firewall pode ajudar a proteger a sua aplicação de função inspecionando o tráfego web de entrada para bloquear injeções DE SQL, Scripting cross-Site, uploads de malware e ataques DDoS. A introdução de um WAF requer um Ambiente de Serviço de Aplicações ou a utilização de Pontos Finais Privados (Pré-visualização). Certifique-se de que os pontos finais privados já não estão em (Pré-visualização) antes de os utilizar com cargas de trabalho de produção.

- [Opções de rede das Funções do Azure](./functions-networking-options.md)

- [Plano Premium de Funções Azure](./functions-premium-plan.md)

- [Introdução aos Ambientes de Serviço de Aplicações](../app-service/environment/intro.md)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](../app-service/environment/network-info.md)

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

- [Compreender o Hardenive de Rede Adaptável do Centro de Segurança Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Compreender o Centro de Segurança Azure Mesmo no Tempo Controlo de Acesso à Rede](../security-center/security-center-just-in-time.md)

- [Utilização de pontos finais privados para funções Azure](../app-service/networking/private-endpoint.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Se utilizar grupos de Segurança de Rede (NSGs) com a sua implementação de Funções Azure, ative os registos de fluxo do Grupo de Segurança da Rede e envie registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Configure um gateway frontal, como o Azure Web Application Firewall, para autenticar todos os pedidos de entrada e filtrar o tráfego malicioso. O Azure Web Application Firewall pode ajudar a proteger as suas aplicações de função inspecionando o tráfego web de entrada para bloquear injeções DE SQL, Scripting cross-Site, uploads de malware e ataques DDoS. A introdução de um WAF requer um Ambiente de Serviço de Aplicações ou a utilização de Pontos Finais Privados (Pré-visualização). Certifique-se de que os pontos finais privados já não estão em (Pré-visualização) antes de os utilizar com cargas de trabalho de produção.

Em alternativa, existem várias opções de mercado como o Barracuda WAF para Azure que estão disponíveis no Azure Marketplace que incluem funcionalidades IDS/IPS.

- [Opções de rede das Funções do Azure](./functions-networking-options.md)

- [Plano Premium de Funções Azure](./functions-premium-plan.md)

- [Introdução aos Ambientes de Serviço de Aplicações](../app-service/environment/intro.md)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](../app-service/environment/network-info.md)

- [Compreender firewall de aplicação web Azure](../web-application-firewall/index.yml)

- [Utilização de pontos finais privados para funções Azure](../app-service/networking/private-endpoint.md)

- [Compreender o Serviço de Nuvem WAF Barracuda](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Configure um gateway frontal para a sua rede, como o Azure Web Application Firewall com encriptação TLS de ponta a ponta. A introdução de um WAF requer um Ambiente de Serviço de Aplicações ou a utilização de Pontos Finais Privados (Pré-visualização). Certifique-se de que os pontos finais privados já não estão em (Pré-visualização) antes de os utilizar com cargas de trabalho de produção.

- [Opções de rede das Funções do Azure](./functions-networking-options.md)

- [Plano Premium de Funções Azure](./functions-premium-plan.md)

- [Introdução aos Ambientes de Serviço de Aplicações](../app-service/environment/intro.md)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](../app-service/environment/network-info.md)

- [Compreender firewall de aplicação web Azure](../web-application-firewall/index.yml)

- [Como configurar tLS de ponta a ponta utilizando o Gateway de Aplicação com o portal](../application-gateway/end-to-end-ssl-portal.md)

- [Utilização de pontos finais privados para funções Azure](../app-service/networking/private-endpoint.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtual para definir controlos de acesso à rede em Grupos de Segurança de Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, AzureAppService) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Para mais informações sobre a utilização de tags de serviço](../virtual-network/service-tags-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para definições de rede relacionadas com as suas Funções Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.Web" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede das suas Funções Azure. Também pode utilizar definições políticas incorporadas para funções Azure, tais como:
- O CORS não deve permitir que todos os recursos acedam às suas apps de função
- A aplicação de função só deve estar acessível através do HTTPS
- A versão TLS mais recente deve ser usada na sua aplicação de função

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em funções Azure (Azure RBAC), e políticas numa única definição de planta. Pode aplicar facilmente o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Se utilizar grupos de Segurança de Rede (NSGs) com a sua implementação de Funções Azure, utilize etiquetas para os NSGs e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de política incorporadas do Azure relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para as definições de rede e recursos relacionados com as suas implementações de Funções Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nas definições ou recursos críticos da rede. 

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como as Funções Azure para os relógios nos registos.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Para controlar a verificação do plano, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

A Azure Functions também oferece integração incorporada com a Azure Application Insights para monitorizar funções. O Application Insights recolhe dados de registo, desempenho e erro. Deteta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e a compreender como as suas funções são usadas.

Se tiver um registo de segurança/auditoria personalizado incorporado dentro da sua aplicação de função, ative a definição de diagnóstico "FunctionAppLogs" e envie os registos para um espaço de trabalho do Log Analytics, centro de eventos Azure ou conta de armazenamento Azure para arquivo. 

Opcionalmente, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/platform/activity-log.md)

- [Como configurar funções Azure com Insights de Aplicação Azure](./functions-monitoring.md)

- [Como ativar definições de diagnóstico (registos gerados pelo utilizador) para funções Azure](./functions-monitor-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Para controlar a verificação do plano, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Se tiver um registo de segurança/auditoria personalizado incorporado dentro da sua aplicação de função, ative a definição de diagnóstico "FunctionAppLogs" e envie os registos para um espaço de trabalho do Log Analytics, centro de eventos Azure ou conta de armazenamento Azure para arquivo. 

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/platform/activity-log.md)

- [Como ativar definições de diagnóstico (registos gerados pelo utilizador) para funções Azure](./functions-monitor-log-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados às aplicações de função de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de registos](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure, bem como as definições de diagnóstico da sua aplicação de função e enviar os registos para um espaço de trabalho do Log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos.

Ative o Application Insights para as suas aplicações de função para recolher dados de registo, desempenho e erro. Pode ver os dados de telemetria recolhidos pela Application Insights dentro do portal Azure.

Se tiver um registo de segurança/auditoria personalizado incorporado dentro da sua aplicação de função, ative a definição de diagnóstico "FunctionAppLogs" e envie os registos para um espaço de trabalho do Log Analytics, centro de eventos Azure ou conta de armazenamento Azure para arquivo. 

Opcionalmente, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/platform/activity-log.md)

- [Como ativar as definições de diagnóstico para funções Azure](./functions-monitor-log-analytics.md)

- [Como configurar funções Azure com Insights de Aplicação Azure e ver os dados da telemetria](./functions-monitoring.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure, bem como as definições de diagnóstico da sua aplicação de função e enviar os registos para um espaço de trabalho do Log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos. Pode criar alertas com base nas suas consultas de espaço de trabalho Log Analytics.

Ative o Application Insights para as suas aplicações de função para recolher dados de registo, desempenho e erro. Pode ver os dados de telemetria recolhidos pela Application Insights e criar alertas dentro do portal Azure.

Opcionalmente, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/platform/activity-log.md)

- [Como ativar as definições de diagnóstico para funções Azure](./functions-monitor-log-analytics.md)

- [Como ativar insights de aplicação para funções Azure](./configure-monitoring.md#enable-application-insights-integration)

- [Como criar alertas dentro do Azure](../azure-monitor/learn/tutorial-response.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável; as aplicações de função não processam ou produzem registos relacionados com anti-malware.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; as aplicações de função não processam ou produzem registos DNS acessíveis ao utilizador.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos. 

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O acesso do avião ao Azure Functions é controlado através do Azure Ative Directory (AD). A Azure AD não tem o conceito de senhas padrão.

O acesso a um avião de dados pode ser controlado através de vários meios, incluindo chaves de autorização, restrições de rede e validação de uma identidade AD AZure. As chaves de autorização são utilizadas pelos clientes que se ligam aos seus pontos finais Azure Functions HTTP e podem ser regeneradas a qualquer momento. Estas teclas são geradas para novos pontos finais HTTP por padrão.

Vários métodos de implementação estão disponíveis para funcionar apps, alguns dos quais podem alavancar um conjunto de credenciais geradas. Reveja os métodos de implementação que serão utilizados para a sua aplicação.

- [Proteja um ponto final HTTP](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Como obter e regenerar chaves de autorização](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Tecnologias de implantação em Funções Azure](./functions-deployment-technologies.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou das Políticas Azure incorporadas, tais como: Deve haver mais de um proprietário atribuído à sua subscrição Contas depreifadas com permissões de proprietário devem ser removidas da sua subscrição Contas externas com permissões do proprietário devem ser removidas da sua subscrição

- [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas para acesso de dados à sua aplicação de função. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure. Implemente um único sinal de inscrição para as suas aplicações de função utilizando a função De autenticação /Autorização do Serviço de Aplicação.

- [Compreender a autenticação e autorização em Funções Azure](../app-service/overview-authentication-authorization.md#identity-providers)

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar o Azure Ative Directory (AD) Autenticação multi-factor (MFA) e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Além disso, utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso ao portal Azure a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AD) como o sistema central de autenticação e autorização para as suas aplicações de função. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como configurar a sua app de função para usar o login AZure AD](../app-service/configure-authentication-provider-aad.md)

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (AD) fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. 

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação**: Utilize o Azure Ative Directory (AD) como o sistema central de autenticação e autorização para as suas aplicações de função. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Tem acesso a atividade de login AD, fontes de registo de auditoria e eventos de risco, que lhe permitem integrar-se com a Azure Sentinel ou com um SIEM de terceiros.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo pretendidos no Log Analytics.

- [Como configurar a sua app de função para usar o login AZure AD](../app-service/configure-authentication-provider-aad.md)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize o Azure Ative Directory (AD) como o sistema central de autenticação e autorização para as suas aplicações de função. Para desvio de comportamento de login de conta no plano de controlo (o portal Azure), utilize o Azure Ative Directory (AD) Identity Protection e funcionalidades de deteção de riscos para configurar respostas automatizadas para ações suspeitas detetadas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não disponível atualmente; O Lockbox do cliente não é suportado atualmente para funções Azure.

- [Lista de serviços suportados pelo Cliente Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. as aplicações de função devem ser separadas por rede virtual (VNet)/sub-rede e marcadas adequadamente.

Também pode utilizar Pontos Finais Privados para realizar o isolamento da rede. Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço (por exemplo, app de função HTTPs endpoint) alimentado pela Azure Private Link. O Ponto Final Privado utiliza um endereço IP privado na VNet e leva de forma eficaz o serviço até à VNet. Os pontos finais privados estão em (Pré-visualização) para aplicações de função em execução no plano Premium. Certifique-se de que os pontos finais privados já não estão em (Pré-visualização) antes de os utilizar com cargas de trabalho de produção.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Opções de rede das Funções do Azure](./functions-networking-options.md)

- [Plano Premium de Funções Azure](./functions-premium-plan.md)

- [Compreender o Ponto Final Privado](../private-link/private-endpoint-overview.md)

- [Utilização de pontos finais privados para funções Azure](../app-service/networking/private-endpoint.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Implemente uma ferramenta automatizada em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie tais transferências enquanto alerta os profissionais de segurança da informação. 

A Microsoft gere a infraestrutura subjacente às Funções Azure e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Não aplicável

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: No portal Azure para as suas aplicações de função, em "Funcionalidades da Plataforma: Networking: SSL", ativar a definição "HTTPs Only" e definir a versão mínima TLS para 1.2.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Não disponível atualmente; as funcionalidades de identificação, classificação e prevenção de perdas não estão atualmente disponíveis para as Funções Azure. Tag Function apps que podem estar a processar informações sensíveis como tal e implementar solução de terceiros se necessário para fins de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) para controlar o acesso ao plano de controlo de aplicações de função (o portal Azure). 

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

A Microsoft gere a infraestrutura subjacente às Funções Azure e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Ao criar uma aplicação de função, deve criar ou ligar para uma conta de Armazenamento Azure de uso geral que suporte o armazenamento de Blob, Queue e Table. Isto porque as funções dependem do Azure Storage para operações como a gestão de gatilhos e execuções de funções de registo. O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft. Para controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para encriptação de bolhas e dados de ficheiros. Estas teclas devem estar presentes no Cofre da Chave Azure para que a aplicação de função possa aceder à conta de armazenamento.

- [Compreender considerações de armazenamento para funções Azure](./storage-considerations.md)

- [Compreenda a encriptação de armazenamento Azure para os dados em repouso](../storage/common/storage-service-encryption.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de Atividade Azure para criar alertas para quando ocorrerem alterações nas aplicações de função de produção, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Adote uma prática de DevSecOps para garantir que as suas aplicações de função estão seguras e permanecem o mais seguras possível durante todo o seu ciclo de vida. A DevSecOps incorpora a equipa de segurança da sua organização e as suas capacidades nas suas práticas de DevOps, tornando a segurança uma responsabilidade de todos na equipa.

Além disso, siga as recomendações do Azure Security Center para ajudar a proteger as suas aplicações de função.

- [Como adicionar validação contínua de segurança ao seu pipeline CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5.2: Implementar uma solução automatizada de gestão de patchs de sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: A Microsoft executa uma gestão de vulnerabilidades nos sistemas subjacentes que suportam as Funções Azure, no entanto poderá utilizar a gravidade das recomendações dentro do Azure Security Center, bem como a Pontuação Segura para medir o risco dentro do seu ambiente. A sua Pontuação Segura baseia-se na quantidade de recomendações do Centro de Segurança que atenuou. Para priorizar as recomendações para resolver primeiro, considere a gravidade de cada um.

- [Guia de referência de recomendações de segurança](../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s).  Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada: Tipos de recursos não permitidos Tipos de recursos permitidos

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos aprovados da Azure e dos títulos de software

**Orientação**: Defina recursos Azure aprovados e software aprovado para recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição. 

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados. 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de políticas incorporadas: Tipos de recursos não permitidos Tipos de recursos permitidos

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure através de scripts

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Para aplicações sensíveis ou de alto risco, implemente subscrições separadas e/ou grupos de gestão para proporcionar isolamento.

Implemente aplicações de função de alto risco na sua própria Rede Virtual (VNet). A segurança do perímetro das aplicações de funções é conseguida através de VNets. As funções em execução no plano Premium ou no Ambiente de Serviço de Aplicações (ASE) podem ser integradas com VNets. Escolha a melhor arquitetura para o seu caso de uso.

- [Opções de rede das Funções do Azure](./functions-networking-options.md)

- [Plano Premium de Funções Azure](./functions-premium-plan.md)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](../app-service/environment/network-info.md)

- [Como criar um ASE externo](../app-service/environment/create-external-ase.md)

Como criar um ASE interno:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um config de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para a sua aplicação de função com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Web" para criar políticas personalizadas para auditar ou impor a configuração das suas aplicações de função. Pode também utilizar definições políticas incorporadas, tais como:
- A identidade gerida deve ser usada na sua aplicação de função
- Depuragem remota deve ser desligada para aplicações de função
- A aplicação de função só deve estar acessível através do HTTPS

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; enquanto é possível implementar funções no local, esta orientação destina-se a recursos de computação IaaS. Ao implementar nas instalações, é responsável pela configuração segura do seu ambiente.

- [Compreender as funções no local](./functions-runtime-install.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Armazenar e gerir modelos ARM e definições de política Azure personalizadas de forma segura no controlo de fontes.

- [O que é infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code)

- [Política de design como fluxos de trabalho de código](../governance/policy/concepts/policy-as-code.md)

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize identidades geridas em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem. Identidades geridas permitem que a sua aplicação de função autente a qualquer serviço que suporte a autenticação AZure AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como criar um Cofre-Chave](../key-vault/secrets/quick-create-portal.md)

- [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](../app-service/overview-managed-identity.md)

* [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)

* [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

- [Utilize referências de Cofre de Chaves para Serviço de Aplicações e Funções Azure](../app-service/app-service-key-vault-references.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer à sua aplicação de função uma identidade gerida automaticamente em Azure AD. Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](../app-service/overview-managed-identity.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta orientação destina-se aos recursos de computação IaaS.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Functions), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos não computacional destinados a armazenar dados.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos não computacional destinados a armazenar dados.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Functions), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Utilize a função de cópia de segurança e restauro para agendar cópias de segurança regulares da sua aplicação. As aplicações de função que executam no plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Azure App Service, que inclui a funcionalidade "Backup and Restore".

Utilize também uma solução de controlo de origem, como a Azure Repos e a Azure DevOps, para armazenar e gerir o seu código de forma segura. Os Serviços de DevOps do Azure tiram partido das muitas funcionalidades de armazenamento do Azure para garantir a disponibilidade de dados no caso de falhas de hardware, interrupções de serviço ou desastre na região. Adicionalmente, a equipa do Azure DevOps segue procedimentos para proteger dados contra eliminação acidental ou maliciosa.

- [Efetuar cópia de segurança da sua aplicação no Azure](../app-service/manage-backup.md)

- [Compreender a disponibilidade de dados em Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Utilize a função de cópia de segurança e restauro para agendar cópias de segurança regulares da sua aplicação. As aplicações de função que executam no plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Azure App Service, que inclui a funcionalidade "Backup and Restore". O cliente de reserva geriu as chaves dentro do Cofre da Chave Azure.

Utilize também uma solução de controlo de origem, como a Azure Repos e a Azure DevOps, para armazenar e gerir o seu código de forma segura. Os Serviços de DevOps do Azure tiram partido das muitas funcionalidades de armazenamento do Azure para garantir a disponibilidade de dados no caso de falhas de hardware, interrupções de serviço ou desastre na região. Adicionalmente, a equipa do Azure DevOps segue procedimentos para proteger dados contra eliminação acidental ou maliciosa.

- [Efetuar cópia de segurança da sua aplicação no Azure](../app-service/manage-backup.md)

- [Como backup chaves chave cofre em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Compreender a disponibilidade de dados em Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Certifique-se de que a restauração é periódica a partir da função de backup e restauro. Se utilizar outra localização offline para fazer backup do seu código, certifique-se periodicamente de capacidade para efetuar restauros completos. Teste de restauração de chaves geridas pelo cliente.

- [Restaurar uma aplicação em Azure a partir de uma cópia de segurança](../app-service/web-sites-restore.md)

- [Restaurar uma aplicação em Azure a partir de um instantâneo](../app-service/app-service-web-restore-snapshots.md)

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Cópias de segurança da funcionalidade Backup e Restore utilizam uma conta de Armazenamento Azure na sua subscrição. O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft. Para controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para encriptação de dados de armazenamento.

Se estiver a utilizar chaves geridas pelo cliente, certifique-se de Soft-Delete no Cofre de Chaves está ativado para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Encriptação do Armazenamento do Azure em repouso](../storage/common/storage-service-encryption.md)

- [Como permitir Soft-Delete em Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas a alertas de segurança e recomendações com apps lógicas.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
