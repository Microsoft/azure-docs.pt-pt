---
title: Linha de base de segurança Azure para funções azure
description: Linha de base de segurança Azure para funções azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 040eeda3edc8aa1165915a157cb7e1bdd1594740
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796502"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Linha de base de segurança Azure para funções azure

A Linha de Base de Segurança Azure para funções Azure contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a visão geral das linhas de [segurança do Azure.](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

## <a name="network-security"></a>Segurança da rede

*Para mais informações, consulte Controlo de [Segurança: Segurança da rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: Integre as suas aplicações De Funções Azure com uma rede virtual Azure. As aplicações de função que executam o plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Serviço de Aplicações Azure, que inclui a funcionalidade "Integração VNet".  As redes virtuais Azure permitem colocar muitos dos seus recursos Azure, como as Funções Azure, numa rede não-internet de saída.

- [Como integrar funções com uma Rede Virtual Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-vnet)

- [Compreender a Integração Vnet para funções Azure e serviço de aplicações Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: Utilize o Azure Security Center e siga as recomendações de proteção da rede para ajudar a proteger os recursos de rede e as configurações de rede relacionadas com as suas aplicações de Funções Azure.

Se utilizar os grupos de Segurança da Rede (NSGs) com a implementação das suas Funções Azure, ative os registos de fluxo do NSG e envie registos numa Conta de Armazenamento Azure para auditorias de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Como ativar os registos de fluxo nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Como ativar e utilizar o Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Para assegurar totalmente os pontos finais da Função Azure em produção, deve considerar a implementação de uma das seguintes opções de segurança ao nível da aplicação:
- Ligue a autenticação do serviço de aplicações / autorização para a sua aplicação de funções,
- Utilizar a Azure API Management (APIM) para autenticar pedidos, ou
- Implemente a sua aplicação de função para um Ambiente de Serviço de Aplicações Azure.

Além disso, certifique-se de que a depuração remota foi desativada para a sua produção Funções Azure. Além disso, a Partilha de Recursos De Origem Cruzada (CORS) não deve permitir que todos os domínios acedam à sua aplicação Função Azure. Permita que apenas os domínios necessários interajam com a sua aplicação Função Azure.

Considere a implementação do Firewall de Aplicação Web Azure (WAF) como parte da configuração de rede para uma inspeção adicional do tráfego de entrada. Ative a definição de diagnóstico para WAF e ingere os registos numa Conta de Armazenamento, Hub de Eventos ou log analytics workspace. 

- [Como garantir pontos finais da Função Azure na produção](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Como implantar o Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a Norma de Proteção DDoS nas Redes Virtuais associadas às suas aplicações de funções para proteger contra ataques DDoS. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP públicos conhecidos ou não utilizados.
Além disso, configure um gateway frontal, como o Azure Web Application Firewall, para autenticar todos os pedidos de entrada e filtrar o tráfego malicioso. O Firewall de aplicação web azure pode ajudar a proteger as suas aplicações De Função Azure inspecionando o tráfego web de entrada para bloquear injeções SQL, Scripting Cross-Site, uploads de malware e ataques DDoS. A introdução de um WAF requer um ambiente de serviço de aplicação ou a utilização de pontos finais privados (Pré-visualização). Certifique-se de que os pontos finais privados já não estão em (Pré-visualização) antes de os utilizar com cargas de trabalho de produção.

- [Opções de rede das Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Plano Premium funções azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Introdução aos Ambientes de Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Como configurar a proteção DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Como implantar o Firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Compreender o Centro de Segurança Azure Inteligência Integrada de Ameaças](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Compreender o Centro de Segurança Azure Adaptive Network Hardening](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Compreender o Centro de Segurança Azure apenas no controlo de acesso à rede de tempo](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Utilização de pontos finais privados para funções azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Se utilizar os grupos de Segurança da Rede (NSGs) com a implementação das suas Funções Azure, ative os registos de fluxo do Grupo de Segurança da Rede e envie registos numa conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar os registos de fluxo nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Como ativar e utilizar o Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Como ativar o Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Configure um portal frontal como o Firewall de aplicação web azure para autenticar todos os pedidos de entrada e filtrar o tráfego malicioso. O Firewall de aplicação web azure pode ajudar a proteger as suas aplicações de função inspecionando o tráfego web de entrada para bloquear injeções SQL, Scripting Cross-Site, uploads de malware e ataques DDoS. A introdução de um WAF requer um ambiente de serviço de aplicação ou a utilização de pontos finais privados (Pré-visualização). Certifique-se de que os pontos finais privados já não estão em (Pré-visualização) antes de os utilizar com cargas de trabalho de produção.

Em alternativa, existem múltiplas opções de marketplace como a Barracuda WAF para o Azure que estão disponíveis no Mercado Azure que incluem funcionalidades IDS/IPS.

- [Opções de rede das Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Plano Premium funções azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Introdução aos Ambientes de Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Compreender firewall de aplicação web Azure](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [Utilização de pontos finais privados para funções azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

- [Compreender o Serviço de Nuvem Barracuda WAF](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Configure um portal frontal para a sua rede, como o Firewall de aplicação web Azure com encriptação TLS de ponta a ponta. A introdução de um WAF requer um ambiente de serviço de aplicação ou a utilização de pontos finais privados (Pré-visualização). Certifique-se de que os pontos finais privados já não estão em (Pré-visualização) antes de os utilizar com cargas de trabalho de produção.

- [Opções de rede das Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Plano Premium funções azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Introdução aos Ambientes de Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Compreender firewall de aplicação web Azure](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [Como configurar TLS de ponta a ponta utilizando o Gateway de Aplicação com o portal](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

- [Utilização de pontos finais privados para funções azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtual para definir controlos de acesso à rede em Grupos de Segurança da Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, AzureAppService) no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Para mais informações sobre a utilização de etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para configurações de rede relacionadas com as suas Funções Azure. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.Web" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração da rede das suas Funções Azure. Também pode utilizar definições políticas incorporadas para funções Azure, tais como:
- O CORS não deve permitir que todos os recursos acedam às suas Aplicações de Função
- App de funções só deve ser acessível através de HTTPS
- A versão TLS mais recente deve ser usada na sua App de Funções

Também pode utilizar plantas Azure para simplificar as implementações azure em larga escala através da embalagem de artefactos ambientais chave, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em papéis (RBAC) e políticas numa única definição de blueprint. Você pode facilmente aplicar o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como criar uma Planta Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Se utilizar grupos de Segurança de Rede (NSGs) com a implementação das funções Azure, utilize etiquetas para os NSGs e outros recursos relacionados com a segurança da rede e fluxo de tráfego. Para regras nsg individuais, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições políticas do Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e notificá-lo de recursos não marcados existentes.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou realizar ações em recursos baseados nas suas etiquetas.

- [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos de rede e detetar alterações para as definições de rede e recursos relacionados com as implementações das suas Funções Azure. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações às definições ou recursos críticos da rede. 

- [Como visualizar e recuperar eventos de Registo de Atividade sinuosa do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para mais informações, consulte Controlo de [Segurança: Registo e monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para recursos Azure, tais como funções Azure para selos temporais nos registos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Para controlar a exploração de registos de auditoria de planos, ative as definições de diagnóstico do Azure Activity Log e envie os registos para um espaço de trabalho de Log Analytics, hub de eventos Azure ou conta de armazenamento Azure para arquivo. Utilizando dados do Registo de Atividade sinuoso do Azure, pode determinar o "quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

A Azure Functions também oferece integração integrada com insights de aplicação Azure para monitorizar funções. Os Insights de Aplicação recolhem dados de registo, desempenho e erro. Deteta automaticamente anomalias de desempenho e inclui ferramentas de análise poderosas para o ajudar a diagnosticar problemas e a compreender como as suas funções são usadas.

Se tiver registos de segurança/auditoria personalizados incorporados dentro da sua aplicação Azure Function, ative os diagnósticos que definem "FunctionAppLogs" e envie os registos para um espaço de trabalho de Log Analytics, centro de eventos Azure ou conta de armazenamento Azure para arquivo. 

Opcionalmente, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como ativar as Definições de Diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como configurar funções azure com insights de aplicação Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Como ativar as Definições de Diagnóstico (registos gerados pelo utilizador) para funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Para controlar a exploração de registos de auditoria de planos, ative as definições de diagnóstico do Azure Activity Log e envie os registos para um espaço de trabalho de Log Analytics, hub de eventos Azure ou conta de armazenamento Azure para arquivo. Utilizando dados do Registo de Atividade sinuoso do Azure, pode determinar o "quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Se tiver registos de segurança/auditoria personalizados incorporados dentro da sua aplicação Azure Function, ative os diagnósticos que definem "FunctionAppLogs" e envie os registos para um espaço de trabalho de Log Analytics, centro de eventos Azure ou conta de armazenamento Azure para arquivo. 

- [Como ativar as Definições de Diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como ativar as Definições de Diagnóstico (registos gerados pelo utilizador) para funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se aos recursos computacionais iaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: No Monitor Azure, delineie o período de retenção de registos para os espaços de trabalho do Log Analytics associados às suas aplicações De funções Azure de acordo com as regras de conformidade da sua organização.

- [Como definir parâmetros de retenção de troncos](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: Ative as definições de diagnóstico do Registo de Atividade si, bem como as definições de diagnóstico da aplicação Funções Azure e envie os registos para um espaço de trabalho de Log Analytics. Execute consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados recolhidos.

Ative os Insights de Aplicação das suas aplicações De Funções Azure para recolher dados de registo, desempenho e erro. Pode visualizar os dados de telemetria recolhidos pela Application Insights dentro do portal Azure.

Se tiver registos de segurança/auditoria personalizados incorporados dentro da sua aplicação Azure Function, ative os diagnósticos que definem "FunctionAppLogs" e envie os registos para um espaço de trabalho de Log Analytics, centro de eventos Azure ou conta de armazenamento Azure para arquivo. 

Opcionalmente, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como ativar as definições de diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como ativar as definições de diagnóstico para funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Como configurar funções azure com insights de aplicação Azure e ver os dados de telemetria](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: Ative as definições de diagnóstico do Registo de Atividade si, bem como as definições de diagnóstico da aplicação Funções Azure e envie os registos para um espaço de trabalho de Log Analytics. Execute consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados recolhidos. Pode criar alertas com base nas consultas no espaço de trabalho do Log Analytics.

Ative os Insights de Aplicação das suas aplicações De Funções Azure para recolher dados de registo, desempenho e erro. Pode ver os dados de telemetria recolhidos pela Application Insights e criar alertas dentro do portal Azure.

Opcionalmente, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como ativar as definições de diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como ativar as definições de diagnóstico para funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Como ativar insights de aplicação para funções azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#enable-application-insights-integration)

- [Como criar alertas dentro do Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável; As aplicações Azure Functions não processam ou produzem registos relacionados com anti-malware.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável; As aplicações Azure Functions não processam ou produzem registos relacionados com dNS acessíveis ao utilizador.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Não aplicável; esta orientação destina-se aos recursos computacionais iaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte Controlo de [Segurança: Controlo de identidade e acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Diretório Ativo azure (AD) tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos. 

- [Como obter um papel de diretório em Azure AD com powerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: Controle o acesso do avião às Funções Azure é controlado através do Diretório Ativo Azure (AD). A Azure AD não tem o conceito de senhas padrão.

O acesso ao avião de dados pode ser controlado através de vários meios, incluindo chaves de autorização, restrições de rede e validação de uma identidade AAD. As chaves de autorização são utilizadas pelos clientes que ligam os pontos finais do Azure Functions HTTP e podem ser regeneradas a qualquer momento. Estas teclas são geradas para novos pontos finais http por padrão.

Vários métodos de implementação estão disponíveis para aplicações de função, algumas das quais podem alavancar um conjunto de credenciais geradas. Reveja os métodos de implementação que serão utilizados para a sua aplicação.

- [Fixe um ponto final http](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Como obter e regenerar chaves de autorização](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#obtaining-keys)

- [Tecnologias de implantação em Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Identidade e Gestão de Acesso do Azure Security Center para monitorizar o número de contas administrativas.

Além disso, para o ajudar a acompanhar as contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou das Políticas Azure incorporadas, tais como: Deve haver mais de um proprietário atribuído à sua subscrição Contas Deprecated com permissões do proprietário devem ser removidas da sua subscrição As contas externas com permissões do proprietário devem ser removidas da sua subscrição

- [Como utilizar o Azure Security Center para monitorizar a identidade e o acesso (Pré-visualização)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Como usar a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas para acesso a dados à sua aplicação de função. Utilize recomendações de Identidade e Gestão de Acesso do Centro de Segurança Azure. Implemente um único início de sessão para as suas aplicações De Funções Azure utilizando a funcionalidade de autenticação/autorização do serviço de aplicações.

- [Compreender a autenticação e autorização em Funções Azure](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

- [Compreender sSO com Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Ativar o Diretório Ativo Azure (AD) Autenticação multi-factor (MFA) e seguir as recomendações de Identidade e Gestão de Acesso do Azure Security Center.

- [Como ativar o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Como monitorizar a identidade e o acesso dentro do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho privilegiadas de acesso (PAW) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre postos de trabalho de acesso privilegiados](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Como ativar o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (AD) Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente.

Além disso, utilize deteções de risco azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como implementar a Gestão de Identidade Privilegiada (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Compreender as deteções de risco da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Utilizar locais de acesso condicional nomeados para permitir o acesso ao portal Azure a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (AD) como sistema central de autenticação e autorização para as suas aplicações De Funções Azure. A Azure AD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A Azure AD também sais, hashes e armazena de forma segura credenciais de utilizador.

- [Como configurar a sua aplicação Funções Azure para utilizar o login Azure AD](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Como criar e configurar uma instância AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: O Diretório Ativo Azure (AD) fornece registos para ajudá-lo a descobrir contas velhas. Além disso, utilize as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. 

- [Compreender relatórios da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Como utilizar avaliações de acesso à identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Utilize o Diretório Ativo Azure (AD) como sistema central de autenticação e autorização para as suas aplicações Azure Function. A Azure AD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A Azure AD também sais, hashes e armazena de forma segura credenciais de utilizador.

Tem acesso a fontes de registo de registo de eventos de acesso à Azure AD, auditoria e registo de eventos de risco, que lhe permitem integrar-se com o Azure Sentinel ou com um SIEM de terceiros.

Pode simplificar este processo criando definições de diagnóstico para contas de utilizadores da AD Azure e enviando os registos de auditoria e registos de log-in para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo desejados dentro do Log Analytics.

- [Como configurar a sua aplicação Funções Azure para utilizar o login Azure AD](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Como integrar registos de atividade do Azure no Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize o Diretório Ativo Azure (AD) como sistema central de autenticação e autorização para as suas aplicações De Funções Azure. Para desvio de comportamento de login de conta no plano de controlo (portal Azure), utilize o Azure Ative Directory (AD) Identity Protection and risk detection features para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Também pode ingerir dados no Azure Sentinel para mais investigação.

- [Como ver os sign-ins de risco da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e ativar políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Não disponível atualmente; O Bloqueio do Cliente não é suportado atualmente para funções Azure.

- [Lista de serviços apoiados pelo Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para mais informações, consulte Controlo de [Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. As aplicações Azure Function devem ser separadas por rede virtual (VNet)/subnet e marcadas adequadamente.

Também pode utilizar pontos finais privados para realizar o isolamento da rede. Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço (por exemplo: Azure Functions app HTTPs endpoint) alimentado por Azure Private Link. O Private Endpoint utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Os pontos finais privados estão em (Pré-visualização) para aplicações de funções em execução no plano Premium. Certifique-se de que os pontos finais privados já não estão em (Pré-visualização) antes de os utilizar com cargas de trabalho de produção.

- [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Opções de rede das Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Plano Premium funções azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Compreender o Ponto Final Privado](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

- [Utilização de pontos finais privados para funções azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Implementar uma ferramenta automatizada em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie essas transferências enquanto alerta os profissionais de segurança da informação. 

A Microsoft gere a infraestrutura subjacente às Funções Azure e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Não aplicável

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: No portal Azure para as suas aplicações Azure Function, em "Funcionalidades da Plataforma: Networking: SSL", ativar a definição "Apenas HTTPs" e definir a versão TLS mínima para 1.2.

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Não disponível atualmente; As características de identificação, classificação e prevenção de perdas de dados não estão atualmente disponíveis para funções azure. Aplicações tag Function que podem estar a processar informações sensíveis como tal e implementar uma solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções azure Ative (AD) para controlar o acesso ao plano de controlo da Função Azure (portal Azure). 

- [Como configurar o RBAC em Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos da IaaS.

A Microsoft gere a infraestrutura subjacente às Funções Azure e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: Ao criar uma aplicação de função, deve criar ou ligar-se a uma conta de Armazenamento Azure de uso geral que suporte o armazenamento de Blob, Queue e Mesa. Isto porque as Funções dependem do Armazenamento Azure para operações como a gestão de gatilhos e execuções de funções de exploração madeireira. O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por padrão, os dados são encriptados com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para encriptação de dados blob e ficheiros. Estas chaves devem estar presentes no Cofre de Chaves Azure para que a aplicação de funções possa aceder à conta de armazenamento.

- [Compreender considerações de armazenamento para funções azure](https://docs.microsoft.com/azure/azure-functions/storage-considerations)

- [Compreender encriptação de armazenamento Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo da Atividade Azure para criar alertas para quando as alterações ocorrerem na produção de aplicações da Função Azure, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Log de Atividade do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para mais informações, consulte Controlo de [Segurança: Gestão de vulnerabilidades.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Adote uma prática DevSecOps para garantir que as aplicações das funções Azure estão seguras e permanecem o mais seguras possível durante toda a duração do seu ciclo de vida. A DevSecOps incorpora a equipa de segurança da sua organização e as suas capacidades nas práticas dos DevOps, tornando a segurança uma responsabilidade de todos na equipa.

Além disso, siga as recomendações do Azure Security Center para ajudar a proteger as suas aplicações Azure Function.

- [Como adicionar validação contínua de segurança ao seu pipeline CI/CD](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5.2: Implementar uma solução automatizada de gestão de patch do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos da IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos da IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos da IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: A Microsoft realiza uma gestão de vulnerabilidade nos sistemas subjacentes que suportam as Funções Azure, no entanto pode utilizar a gravidade das recomendações dentro do Azure Security Center, bem como a Pontuação Segura para medir o risco dentro do seu ambiente. O seu Secure Score baseia-se em quantas recomendações do Centro de Segurança atenuou. Para priorizar as recomendações para resolver primeiro, considere a gravidade de cada um.

- [Guia de referência de recomendações de segurança](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte Controlo de [Segurança: Inventário e gestão de ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua subscrição(s).  Certifique-se de permissões (ler) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos do Gestor de Recursos Azure para avançar.

- [Como criar consultas com o Gráfico de Recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Como visualizar as suas Assinaturas Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Compreender o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure, dando metadados para logicamente organizá-los numa taxonomia.

- [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Além disso, utilize a política do Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas: Tipos de recursos não autorizados

- [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Definir recursos azure aprovados e software aprovado para recursos computacionais.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na sua subscrição. 

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados. 

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos da IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos da IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos da IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas: Tipos de recursos não autorizados

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos da IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts

**Orientação**: Configure O Acesso Condicional azure para limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure configurando o "Block access" para a App "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos da IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

**Orientação**: Para aplicações de função Azure sensíveis ou de alto risco, implemente subscrições separadas e/ou grupos de gestão para fornecer isolamento.

Implemente aplicações de função Azure de alto risco na sua própria Rede Virtual (VNet). A segurança do perímetro nas Funções Azure é alcançada através de VNets. As funções em funcionamento no plano Premium ou no App Service Environment (ASE) podem ser integradas com VNets. Escolha a melhor arquitetura para o seu caso de uso.

- [Opções de rede das Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Plano Premium funções azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Como criar uma ASE externa](https://docs.microsoft.com/azure/app-service/environment/create-external-ase)

Como criar uma ASE interna:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Como criar um NSG com um config de segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para mais informações, consulte [O controlo de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Defina e implemente configurações de segurança padrão para a sua aplicação Função Azure com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.Web" para criar políticas personalizadas para auditar ou impor a configuração das suas aplicações De Funções Azure. Também pode utilizar definições políticas incorporadas, tais como:
- A identidade gerida deve ser utilizada na sua App de Funções
- Depuração remota deve ser desligada para aplicações de função
- App de funções só deve ser acessível através de HTTPS

- [Como ver os Aliases políticos do Azure disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se aos recursos computacionais iaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação:** Utilize a política azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Compreender efeitos políticos do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; embora seja possível implementar funções no local, esta orientação destina-se aos recursos computacionais iaaS. Ao implantar nas funções do local, é responsável pela configuração segura do seu ambiente.

- [Compreender as funções no local](https://docs.microsoft.com/azure/azure-functions/functions-runtime-install)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Armazenar e gerir modelos ARM e definições políticas personalizadas do Azure de forma segura no controlo de fonte.

- [O que é infraestrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

- [Política de design como fluxos de código](https://docs.microsoft.com/azure/governance/policy/concepts/policy-as-code)

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta orientação destina-se aos recursos computacionais iaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize definições de política azure incorporadas, bem como pseudónimos da Política Azure no espaço de nome "Microsoft.Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se aos recursos computacionais iaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Utilize definições de política azure incorporadas, bem como pseudónimos da Política Azure no espaço de nome "Microsoft.Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Utilize a política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente as configurações dos seus recursos Azure.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se aos recursos computacionais iaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize identidades geridas em conjunto com o Cofre chave Azure para simplificar e proteger a gestão secreta para as suas aplicações na nuvem. Identidades Geridas permite que a sua aplicação de funções autentique qualquer serviço que suporte a autenticação AD Azure, incluindo o Key Vault, sem qualquer credencial no seu código.

- [Como criar um Cofre chave](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

- [Como fornecer a autenticação do Cofre Chave com uma identidade gerida](https://docs.microsoft.com/azure/key-vault/managed-identity)

- [Utilize referências chave vault para serviço de aplicações e funções azure](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer à sua aplicação Função Azure uma identidade gerida automaticamente em Azure AD. Identidades Geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AD Azure, incluindo o Key Vault, sem qualquer credencial no seu código.

- [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte Controlo de [Segurança: Defesa de Malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta orientação destina-se aos recursos computacionais iaaS.

O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, funções Azure), no entanto não funciona com conteúdo de clientes.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: Não aplicável; esta recomendação destina-se a recursos não calculados destinados a armazenar dados.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos não calculados destinados a armazenar dados.

O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, funções Azure), no entanto não funciona com conteúdo de clientes.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para mais informações, consulte Controlo de [Segurança: Recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Utilize a função 'Backup and Restore' para agendar cópias de segurança regulares da sua aplicação. As aplicações de função que executam o plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Serviço de Aplicações Azure, que inclui a funcionalidade "Backup and Restore".

Utilize também uma solução de controlo de fontes, como a Azure Repos e a Azure DevOps, para armazenar e gerir de forma segura o seu código. A Azure DevOps Services aproveita muitas das funcionalidades de armazenamento do Azure para garantir a disponibilidade de dados em caso de falha de hardware, perturbação do serviço ou desastre da região. Além disso, a equipa azure DevOps segue os procedimentos para proteger os dados de eliminação acidental ou maliciosa.

- [Efetuar cópia de segurança da sua aplicação no Azure](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Compreender a disponibilidade de dados em Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: Utilize a função 'Backup and Restore' para agendar cópias de segurança regulares da sua aplicação. As aplicações de função que executam o plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Serviço de Aplicações Azure, que inclui a funcionalidade "Backup and Restore". O cliente de reserva geria as chaves dentro do Cofre de Chaves Azure.

Utilize também uma solução de controlo de fontes, como a Azure Repos e a Azure DevOps, para armazenar e gerir de forma segura o seu código. A Azure DevOps Services aproveita muitas das funcionalidades de armazenamento do Azure para garantir a disponibilidade de dados em caso de falha de hardware, perturbação do serviço ou desastre da região. Além disso, a equipa azure DevOps segue os procedimentos para proteger os dados de eliminação acidental ou maliciosa.

- [Efetuar cópia de segurança da sua aplicação no Azure](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Como apoiar as chaves do cofre chave em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Compreender a disponibilidade de dados em Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Garantir a capacidade de realizar periodicamente a restauração a partir da função De backup e restauro. Se utilizar outro local offline para fazer backup do seu código, certifique-se periodicamente da capacidade de realizar restauros completos. Teste a restauração das chaves geridas pelo cliente.

- [Restaurar uma aplicação em Azure a partir de uma cópia de segurança](https://docs.microsoft.com/azure/app-service/web-sites-restore)

- [Restaurar uma aplicação em Azure a partir de um instantâneo](https://docs.microsoft.com/azure/app-service/app-service-web-restore-snapshots)

- [Como restaurar as chaves do cofre chave em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: As cópias de segurança da função 'Backup and Restore' utilizam uma conta de Armazenamento Azure na sua subscrição. O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por padrão, os dados são encriptados com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para encriptação de dados de armazenamento.

Se estiver a utilizar chaves geridas pelo cliente, certifique-se de que o Soft-Delete no Cofre de Chaves está ativado para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Encriptação do Armazenamento do Azure em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [Como ativar o Soft-Delete no Cofre de Chaves](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte Controlo de [Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.

- [Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

- [Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure.

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

- [Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas para o Azure Sentinel.

- [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Como transmitir alertas para o Sentinela Azure](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para acionar automaticamente respostas a alertas de segurança e recomendações com Aplicações Lógicas.

- [Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para mais informações, consulte Controlo de [Segurança: Testes de penetração e exercícios vermelhos da equipa.](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Utilize a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft.

- [Regras de teste de penetração de envolvimento](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte o referencial de [segurança azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
