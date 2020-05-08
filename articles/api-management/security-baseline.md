---
title: Linha de base de segurança Azure para Gestão de API
description: Linha de base de segurança Azure para Gestão de API
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aa59a37f3a4413c92a483746ed4a08c363ab8457
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796528"
---
# <a name="azure-security-baseline-for-api-management"></a>Linha de base de segurança Azure para Gestão de API

A Linha de Base de Segurança Azure para gestão da API contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a visão geral das linhas de [segurança do Azure.](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

## <a name="network-security"></a>Segurança da rede

*Para mais informações, consulte Controlo de [Segurança: Segurança da rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: A Azure API Management pode ser implantada dentro de uma Rede Virtual Azure (Vnet), para que possa aceder a serviços de backend dentro da rede. O portal de desenvolvimento e o portal de gestão da API podem ser configurados para serem acessíveis a partir da Internet (Externa) ou apenas dentro do Vnet (Interno).
- Externo: o portal de gestão da API e o portal de desenvolvimento estão acessíveis a partir da internet pública através de um equilibrador de carga externa. O portal pode aceder a recursos dentro da rede virtual.
- Interna: o portal de gestão da API e o portal de desenvolvimento só estão acessíveis a partir da rede virtual através de um equilibrador de carga interna. O portal pode aceder a recursos dentro da rede virtual.

O tráfego de entrada e saída na subrede em que a API Management é implantada pode ser controlado através do Network Security Group.

* [Como utilizar a Gestão de API do Azure com redes virtuais](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Utilizar o serviço Gestão de API do Azure com uma rede virtual interna](https://docs.microsoft.com/azure/api-management/api-management-using-with-internal-vnet)

* [Integrar a Gestão da API num VNET interno com Gateway de Aplicação](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: O tráfego de entrada e saída na subrede em que a API Management é implantada pode ser controlado utilizando grupos de Segurança da Rede (NSGs). Desloque um NSG para a sua subnet a API Management e ative os registos de fluxo do NSG e envie registos para uma conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Atenção: Ao configurar um NSG na sub-rede de Gestão API, existem um conjunto de portas que são necessárias para serem abertas. Se alguma destas portas não estiver disponível, a API Management pode não funcionar corretamente e pode tornar-se inacessível.

* [Compreender as configurações do NSG para a Gestão da API Azure](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [Como ativar os registos de fluxo nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como ativar e utilizar o Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Para proteger a Web/HTTP APIs críticas configurar a Gestão aPI dentro de uma Rede Virtual (Vnet) em modo interno e configurar um Gateway de Aplicação Azure. Application Gateway é um serviço PaaS. Atua como um proxy inverso e fornece equilíbrio de carga L7, encaminhamento, firewall de aplicação web (WAF) e outros serviços.

Combinar gestão de API provisionado num Vnet interno com o frontend do Gateway de aplicação permite os seguintes cenários:
- Utilize um único recurso de Gestão API para expor todas as APIs tanto aos consumidores internos como aos consumidores externos.
- Utilize um único recurso de Gestão API para expor um subconjunto de APIs a consumidores externos.
- Providenciar uma forma de mudar o acesso à Gestão api a partir da Internet pública dentro e fora.

Nota: Esta funcionalidade está disponível nos níveis Premium e Developer da API Management.

* [Como integrar a Gestão api num VNET interno com Gateway de Aplicação](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Compreender o Gateway de Aplicações Azure](https://docs.microsoft.com/azure/application-gateway/)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Configure a Gestão da API dentro de uma Rede Virtual (Vnet) em modo interno e configure um Gateway de Aplicação Azure. Application Gateway é um serviço PaaS. Atua como um proxy inverso e fornece equilíbrio de carga L7, encaminhamento, firewall de aplicação web (WAF) e outros serviços.

Combinar gestão de API provisionado num Vnet interno com o frontend do Gateway de aplicação permite os seguintes cenários:
- Utilize um único recurso de Gestão API para expor todas as APIs tanto aos consumidores internos como aos consumidores externos.
- Utilize um único recurso de Gestão API para expor um subconjunto de APIs a consumidores externos.
- Providenciar uma forma de mudar o acesso à Gestão api a partir da Internet pública dentro e fora.

Nota: Esta funcionalidade está disponível nos níveis Premium e Developer da API Management.

Ative a Norma de Proteção Azure DDoS na Vnet associada à sua implementação de Gestão API para proteger contra ataques de negação de serviço distribuídos (DDoS).

Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.

* [Como integrar a Gestão api num VNET interno com Gateway de Aplicação](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Compreender o Gateway de Aplicações Azure](https://docs.microsoft.com/azure/application-gateway/)

* [Como configurar a Norma de Proteção DDoS Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Compreender o Centro de Segurança Azure Inteligência Integrada de Ameaças](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: O tráfego de entrada e saída na subrede em que a API Management é implantada pode ser controlado utilizando grupos de segurança de rede (NSG). Desloque um NSG para a sua subnet a API Management e ative os registos de fluxo do NSG e envie registos para uma conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Atenção: Ao configurar um NSG na sub-rede de Gestão API, existem um conjunto de portas que são necessárias para serem abertas. Se alguma destas portas não estiver disponível, a API Management pode não funcionar corretamente e pode tornar-se inacessível.

* [Compreender as configurações do NSG para a Gestão da API Azure](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [Como ativar os registos de fluxo nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como ativar e utilizar o Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Configure a Gestão da API dentro de uma Rede Virtual (Vnet) em modo interno e configure um Gateway de Aplicação Azure. Application Gateway é um serviço PaaS. Atua como um proxy inverso e fornece equilíbrio de carga L7, encaminhamento, firewall de aplicação web (WAF) e outros serviços. Aplicação Gateway WAF fornece proteção contra explorações e vulnerabilidades comuns de segurança e pode ser executado nos dois modos seguintes:
- Modo de deteção: Monitores e regista todos os alertas de ameaça. Pode ligar os diagnósticos de registo para o Gateway de Aplicação na secção de Diagnósticos. Deve certificar-se de que o registo WAF é selecionado e ligado. A firewall da aplicação web não bloqueia os pedidos de entrada quando está a funcionar no modo de Deteção.
- Modo de prevenção: Bloqueia intrusões e ataques que as regras detetam. O agressor recebe uma exceção de "403 acessos não autorizados" e a ligação está fechada. O modo de prevenção regista tais ataques nos registos waf.

Combinar gestão de API provisionado num Vnet interno com o frontend do Gateway de aplicação permite os seguintes cenários:
- Utilize um único recurso de Gestão API para expor todas as APIs tanto aos consumidores internos como aos consumidores externos.
- Utilize um único recurso de Gestão API para expor um subconjunto de APIs a consumidores externos.
- Providenciar uma forma de mudar o acesso à Gestão api a partir da Internet pública dentro e fora.

Nota: Esta funcionalidade está disponível nos níveis Premium e Developer da API Management.

* [Como integrar a Gestão api num VNET interno com Gateway de Aplicação](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Compreender a aplicação azure Gateway WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Para gerir o fluxo de tráfego para web/HTTP APIs implementar Gestão API para uma Rede Virtual (Vnet) associada ao Ambiente de Serviço de Aplicações em modo externo ou interno.

Em modo interno, configure um Gateway de Aplicação Azure em frente à Gestão API. Application Gateway é um serviço PaaS. Atua como um proxy inverso e fornece equilíbrio de carga L7, encaminhamento, firewall de aplicação web (WAF) e outros serviços. Aplicação Gateway WAF fornece proteção contra explorações e vulnerabilidades comuns de segurança.

Combinar gestão de API provisionado num Vnet interno com o frontend do Gateway de aplicação permite os seguintes cenários:
- Utilize um único recurso de Gestão API para expor todas as APIs tanto aos consumidores internos como aos consumidores externos.
- Utilize um único recurso de Gestão API para expor um subconjunto de APIs a consumidores externos.
- Providenciar uma forma de mudar o acesso à Gestão api a partir da Internet pública dentro e fora.

Nota: Esta funcionalidade está disponível nos níveis Premium e Developer da API Management.

* [Como expor APIs privadas a consumidores externos](https://docs.microsoft.com/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [Como utilizar a Gestão API dentro de um Vnet](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Firewall de aplicação web azure no gateway de aplicação azure](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Compreender o Gateway de Aplicações Azure](https://docs.microsoft.com/azure/application-gateway/overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtual (Vnet) para definir controlos de acesso à rede em Grupos de Segurança da Rede (NSGs) utilizados nas suas subredes de Gestão API. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Atenção: Ao configurar um NSG na sub-rede de Gestão API, existem um conjunto de portas que são necessárias para serem abertas. Se alguma destas portas não estiver disponível, a API Management pode não funcionar corretamente e pode tornar-se inacessível.

* [Compreensão e utilização de etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Portos necessários para gestão da API](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para configurações de rede relacionadas com as suas implementações de Gestão API Azure. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.ApiManagement" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração da rede das suas implementações de Gestão API Azure e recursos conexos. Também pode utilizar definições políticas incorporadas para redes virtuais Azure, tais como:
- Norma de proteção DDoS deve ser ativada

Também pode utilizar plantas Azure para simplificar as implementações azure em larga escala através da embalagem de artefactos ambientais chave, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em papéis (RBAC) e políticas numa única definição de blueprint. Você pode facilmente aplicar o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar uma Planta Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Etiquetas de utilização para grupos de segurança de rede (NSGs) e outros recursos relacionados com a segurança da rede e fluxo de tráfego. Para regras nsg individuais, pode utilizar o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Como criar um NSG com um Config de Segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seletiva para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede associados às suas implementações de Gestão API Azure. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede.

* [Como visualizar e recuperar eventos de Registo de Atividade sinuosa do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Como criar alertas no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para mais informações, consulte Controlo de [Segurança: Registo e monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para a Gestão da API Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Dentro do Monitor Azure, utilize o (s) espaço de trabalho do Log Analytics para consultar e realizar análises, enviar registos para o Armazenamento Azure para armazenamento a longo prazo/arquivo ou análise offline, ou registos de exportação para outra solução de análise em Azure e em outros lugares usando Hubs de Eventos Azure. A Azure API Management produz registos e métricas para o Monitor Azure por padrão. A verbosidade da exploração madeireira pode ser configurada numa base de serviço e por API.

Além do Azure Monitor, a Azure API Management pode ser integrada com um ou vários serviços azure Application Insights. As definições de registo de Insights de Aplicação podem ser configuradas por serviço ou por API.

Opcionalmente, ativar e embarcar dados para O Sentinel ou um incidente de segurança e gestão de eventos de terceiros (SIEM).

* [Como configurar as definições de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Como começar com o Azure Monitor e a integração siem de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Como criar o pipeline personalizado de exploração madeireira e analítica](https://docs.microsoft.com/azure/api-management/api-management-howto-log-event-hubs)

* [Como integrar-se com insights de aplicação azure](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Para controlar a exploração de registos de auditoria de planos, permitir as definições de diagnóstico do Azure Activity Log e enviar registos de atividade para um espaço de trabalho de Log Analytics para reporte e análise, para o Armazenamento Azure para manutenção a longo prazo, para Os Hubs de Eventos Azure para exportação em outras soluções de análise em Azure e em outros lugares. Utilizando dados do Registo de Atividade sinuoso do Azure, pode determinar o "quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para o seu serviço de Gestão API Azure.

Para a exploração de registos de auditoria de aviões de dados, os registos de diagnóstico fornecem informações ricas sobre operações e erros que são importantes para a auditoria, bem como para fins de resolução de problemas. Os registos de diagnóstico diferem dos registos de atividades. Os registos de atividades fornecem informações aprofundadas sobre as operações executadas nos recursos do Azure. Os registos de diagnóstico fornecem informações aprofundadas sobre as operações executadas pelo recurso.

* [Como ativar as Definições de Diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como ativar as Definições de Diagnóstico para a Gestão da API Azure](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: Dentro do Monitor Azure, detete o período de retenção do espaço de trabalho de Log Analytics de acordo com as regras de conformidade da sua organização. Utilize as contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

* [Como definir parâmetros de retenção de registos para espaços de trabalho de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Como arquivar registos numa conta de Armazenamento Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: A Azure API Management emite continuamente registos e métricas para o Monitor Azure, dando-lhe uma visibilidade quase em tempo real no estado e saúde das suas APIs. Com o Space(s do Monitor Azure e do Log Analytics), pode rever, consultar, visualizar, rota, arquivar, configurar alertas e tomar ações sobre métricas e registos provenientes da Gestão de API e recursos conexos. Analise e monitorize os registos para comportamentos anómalos e reveja regularmente os resultados.

Opcionalmente, integre a Gestão da API com insights de aplicação Azure e use-a como ferramenta de monitorização primária ou secundária, rastreio, reporte e alerta.

* [Como monitorizar e rever os registos para a Gestão da API azure](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor)

* [Como realizar consultas personalizadas no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Compreender o espaço de trabalho de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Como integrar-se com insights de aplicação azure](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: Ative as definições de diagnóstico do Registo de Atividade si, bem como as definições de diagnóstico das suas instâncias de Gestão API Azure e envie os registos para um espaço de trabalho de Log Analytics. Execute consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados recolhidos. Pode criar alertas com base nas consultas no espaço de trabalho do Log Analytics.

Crie alertas métricos para que saiba quando algo inesperado está acontecendo. Por exemplo, obtenha notificações quando a sua instância de Gestão API Azure tiver excedido a sua capacidade máxima esperada durante um determinado período de tempo ou se tiver havido um certo número de pedidos ou erros não autorizados durante um período de tempo predefinido.

Opcionalmente, integre a Gestão da API com insights de aplicação Azure e use-a como ferramenta de monitorização primária ou secundária, rastreio, reporte e alerta.

Opcionalmente, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros.

* [Como ativar as definições de diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como ativar as definições de diagnóstico para a Gestão da API Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

* [Como configurar uma regra de alerta para pedido não autorizado](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

* [Como visualizar métricas de capacidade de uma instância de gestão da API Azure](https://docs.microsoft.com/azure/api-management/api-management-capacity)

* [Como integrar-se com insights de aplicação azure](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

* [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável; A Azure API Management não processa nem produz registos relacionados com anti-malware.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável; A Azure API Management não processa nem produz registos relacionados com dNS acessíveis ao utilizador.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte Controlo de [Segurança: Controlo de identidade e acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Manter um inventário de contas que tenham acesso administrativo ao plano de controlo de gestão da API Azure (portal Azure).

O Azure Ative Directory (AD) tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. A API Management conta com estas funções e no Controlo de Acesso baseado em Funções para permitir a gestão de acesso sinuoso para serviços e entidades de Gestão aPI.

Além disso, a API Management contém um grupo de Administradores incorporados no sistema de utilizadores da API Management. Os grupos da API Management controlam a visibilidade das APIs no portal de desenvolvimento e os membros do grupo Administradores podem ver todas as APIs.

Siga as recomendações do Azure Security Center para a gestão e manutenção de contas administrativas.

* [Como utilizar o Controlo de Acesso Baseado em Funções na Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

* [Como obter a lista de utilizadores sob uma Instância de Gestão API Azure](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Como obter uma lista de utilizadores atribuídos a um papel de diretório em Azure AD com powerShell](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [Como obter uma definição de papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [Compreender as recomendações de identidade e acesso do Azure Security Center](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-identity)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: A Azure API Management não tem o conceito de senhas/chave predefinidas.

As subscrições da Azure API Management, que são um dos meios de garantir o acesso às APIs, vêm, no entanto, com um par de chaves de subscrição geradas. Os clientes podem regenerar estas chaves de subscrição a qualquer momento.

* [Compreender as assinaturas de gestão da API Azure](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Identidade e Gestão de Acesso do Azure Security Center para monitorizar o número de contas administrativas.

Além disso, para o ajudar a acompanhar as contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou das Políticas Azure incorporadas, tais como:
- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

* [Como utilizar o Azure Security Center para monitorizar a identidade e o acesso (Pré-visualização)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Como usar a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: A Azure API Management pode ser configurada para alavancar o Azure Ative Directory como fornecedor de identidade para autenticar utilizadores no Portal do Desenvolvimento de forma a beneficiar das capacidades SSO oferecidas pela Azure AD. Uma vez configurados, os novos utilizadores do Portal do Desenvolvimento podem optar por seguir o processo de inscrição fora da caixa, autenticando primeiro através do Azure AD e, em seguida, completando o processo de inscrição no portal uma vez autenticado.

* [Autorizar as contas de programador ao utilizar o Azure Active Directory na Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

Em alternativa, o processo de inscrição/inscrição pode ser ainda personalizado através da delegação. A delegação permite-lhe utilizar o seu website existente para lidar com o acesso ao programador, inscreveu/inscreva-se e subscreva produtos, em oposição à utilização da funcionalidade incorporada no portal do desenvolvedor. Permite ao seu website possuir os dados do utilizador e realizar a validação destes passos de forma personalizada.

* [Como delegar o registo do utilizador e a subscrição do produto](https://docs.microsoft.com/azure/api-management/api-management-howto-setup-delegation)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Ativar o Diretório Ativo Azure (AD) Autenticação multi-factor (MFA) e seguir as recomendações de Identidade e Gestão de Acesso do Azure Security Center.

* [Como ativar o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Como monitorizar a identidade e o acesso dentro do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho privilegiadas de acesso (PAW) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos Azure.

* [Saiba mais sobre postos de trabalho de acesso privilegiados](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Como ativar o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (AD) Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente.

Além disso, utilize deteções de risco azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

* [Como implementar a Gestão de Identidade Privilegiada (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Compreender as deteções de risco da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Utilizar locais de acesso condicional nomeados para permitir o acesso ao portal Azure a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Sempre que possível, utilize o Azure AD como sistema central de autenticação e autorização. A AAD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A Azure AD também sais, hashes e armazena de forma segura credenciais de utilizador.

Configure o portal de desenvolvimento de gestão da API Azure para autenticar contas de desenvolvimento utilizando o Diretório Ativo Azure.

Configure a sua instância de Gestão API Azure para proteger as suas APIs utilizando o protocolo OAuth 2.0 com o Azure Ative Directory (AD).

* [Como autorizar contas de desenvolvimento utilizando o Diretório Ativo Azure na Gestão da API Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [Como proteger uma API utilizando o OAuth 2.0 com o Diretório Ativo Azure e a Gestão da API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Como criar e configurar uma instância AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: O Diretório Ativo Azure fornece registos para ajudar a descobrir contas velhas. Os clientes podem utilizar as Avaliações de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos continuem a ter acesso adequado.

Os clientes podem manter o inventário das contas de utilizadores da API Management e conciliar o acesso conforme necessário. Na API Management, os desenvolvedores são os consumidores das APIs que se expuseram com a API Management. Por padrão, as contas de desenvolvedores recém-criadas são Ativas e associadas ao grupo Developers. As contas de desenvolvedores que se encontram em estado ativo podem ser usadas para aceder a todas as APIs para as quais têm subscrições.

Os administradores podem criar grupos personalizados ou alavancar grupos externos em inquilinos associados do Azure Ative Directory. É possível utilizar grupos personalizados e externos em conjunto com grupos de sistema para dar aos programadores acesso e visibilidade a produtos de API.

* [Como gerir contas de utilizador na Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-create-or-invite-developers)

* [Como obter a lista de utilizadores da API Management](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Como criar e utilizar grupos para gerir contas de programador na Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)

* [Como utilizar avaliações de acesso à identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Configure a sua instância de Gestão API Azure para autenticar contas de desenvolvimento utilizando o Azure Ative Directory como fornecedor de identidade na Gestão da API Azure.

Configure a sua instância de Gestão API Azure para proteger as suas APIs utilizando o protocolo OAuth 2.0 com o Azure Ative Directory (AD).

Configure a política de validação jWT para os pedidos de API que chegam para ajudar a impor a existência e validade de um token válido.

Crie definições de diagnóstico para contas de utilizadores da AD Azure e envie os registos de auditoria e os registos de log-in para um espaço de trabalho do Log Analytics. Configure os alertas desejados dentro do Log Analytics. Além disso, pode embarcar no espaço de trabalho do Log Analytics para o Azure Sentinel ou um SIEM de terceiros.

Configure a monitorização avançada com `log-to-eventhub` a API Management utilizando a política, capture quaisquer informações adicionais de contexto necessárias para análise de segurança, e envie para O Azure Sentinel ou SIEM de terceiros.

* [Como autorizar contas de desenvolvimento utilizando o Diretório Ativo Azure na Gestão da API Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [Como proteger uma API utilizando o OAuth 2.0 com o Diretório Ativo Azure e a Gestão da API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Políticas de restrição de acesso à Gestão de API](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies)

* [Como integrar registos de Anúncios Azure no Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Monitorização avançada das APIs](https://docs.microsoft.com/azure/api-management/api-management-log-to-eventhub-sample)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Para desvio de comportamento de registo de conta no plano de controlo (portal Azure), utilize funcionalidades de Proteção de Identidade do Diretório Ativo azure (AD) e deteção de riscos para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Também pode ingerir dados no Azure Sentinel para mais investigação.

* [Como ver os sign-ins de risco da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Como configurar e ativar políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Não disponível atualmente; O Bloqueio de Clientes não é atualmente suportado para a Gestão da API Azure.

* [Lista de serviços apoiados pelo Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para mais informações, consulte Controlo de [Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.

* [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. As instâncias de gestão da API Azure devem ser separadas por rede virtual (VNet)/subnet e marcadas adequadamente.

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Como utilizar a Gestão de API do Azure com redes virtuais](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Não disponível atualmente; As funcionalidades de identificação, classificação e prevenção de perdas de dados não estão atualmente disponíveis para a Gestão da API azure.

A Microsoft gere a infraestrutura subjacente à Azure API Management e implementou controlos rigorosos para evitar a perda ou exposição de dados dos clientes.

* [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: As chamadas de avião de gestão são feitas através do Azure Resource Manager sobre o TLS. É necessário um token web JSON válido (JWT). As chamadas de avião de dados podem ser asseguradas com TLS e um dos mecanismos de autenticação suportados (por exemplo, certificado de cliente ou JWT).

* [Compreender a proteção de dados na Gestão da API azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

* [Gerir as definições de TLS na Gestão API Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-manage-protocols-ciphers)

* [Proteja as APIs na Gestão da API Azure com o Diretório Ativo Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Proteja as APIs na Gestão da API Azure com o Diretório Ativo Azure B2C](https://docs.microsoft.com/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Não disponível atualmente; As funcionalidades de identificação, classificação e prevenção de perdas de dados não estão atualmente disponíveis para a Gestão da API azure. Serviços de Gestão API tag Azure que podem estar a processar informações sensíveis como tal e implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

* [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções para controlar o acesso à Gestão api. A Azure API Management conta com o Azure Role-Based Access Control (RBAC) para permitir a gestão de acesso sinuoso para serviços e entidades de Gestão de API (por exemplo, APIs e políticas).

* [Como utilizar o Controlo de Acesso Baseado em Funções na Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

A Microsoft gere a infraestrutura subjacente à Azure API Management e implementou controlos rigorosos para evitar a perda ou exposição de dados dos clientes.

* [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: Dados sensíveis, tais como certificados, chaves e valores nomeados secretos, são encriptados com chaves geridas pelo serviço, por chave de instância de serviço. Todas as chaves de encriptação são por instância de serviço e são geridas pelo serviço.

* [Compreender a proteção de dados/encriptação em repouso com a Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo da Atividade Azure para criar alertas para quando as alterações ocorrerem na produção de aplicações de Funções Azure, bem como outros recursos críticos ou relacionados.

* [Como criar alertas para eventos de Log de Atividade do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Como utilizar o Azure Monitor e o Azure Activity Log na Gestão da API Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para mais informações, consulte Controlo de [Segurança: Gestão de vulnerabilidades.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Não disponível atualmente; A avaliação de vulnerabilidade no Azure Security Center não está atualmente disponível para a Gestão da API Azure.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Reveja os controlos de segurança disponíveis para reduzir as vulnerabilidades relacionadas com a configuração do serviço.

* [Compreender os controlos de segurança disponíveis para a Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Não disponível atualmente; A avaliação de vulnerabilidade no Azure Security Center não está atualmente disponível para a Gestão da API Azure.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Cliente para rever os controlos de segurança disponíveis para reduzir vulnerabilidades relacionadas com a configuração do serviço.

* [Compreender os controlos de segurança disponíveis para a Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte Controlo de [Segurança: Inventário e gestão de ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua subscrição(s). Certifique-se de permissões (ler) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos do Gestor de Recursos Azure para avançar.

* [Como criar consultas com o Gráfico de Recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Como visualizar as suas Assinaturas Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Compreender o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure, dando metadados para logicamente organizá-los numa taxonomia.

* [Como criar e utilizar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Além disso, utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na sua subscrição utilizando as seguintes definições políticas incorporadas:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts

**Orientação**: Configure O Acesso Condicional azure para limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure configurando o "Block access" para a App "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

* [Controlo de acesso baseado em funções na Gestão da API azure](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para mais informações, consulte [O controlo de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Defina e implemente configurações de segurança padrão para o seu serviço de Gestão API Azure com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração dos seus serviços de Gestão API Azure.

* [Como ver os Aliases políticos do Azure disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os seus serviços de Gestão API Azure com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de instâncias de Gestão API Azure. Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender efeitos políticos do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições políticas personalizadas do Azure, utilize o Azure DevOps ou o Azure Repos para armazenar e gerir de forma segura a configuração do seu serviço de Gestão API Azure.

* [Como armazenar ficheiros em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

* [Compreenda o Kit de Recursos DevOps de Gestão apição azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Defina e implemente configurações de segurança padrão para os seus serviços de Gestão API Azure com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de instâncias de Gestão API Azure. Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender efeitos políticos do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Utilize o Kit de Recursos DevOps de Gestão API Azure para realizar a gestão de configuração para a Gestão da API Azure.

Além disso, defina e implemente configurações de segurança padrão para os seus serviços de Gestão API Azure com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de instâncias de Gestão API Azure. Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Compreenda o Kit de Recursos DevOps de Gestão apição azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender efeitos políticos do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize o Cofre chave para gerir os certificados e desemprece-os para autorotate. Se utilizar o Cofre de Chaves Azure para gerir o certificado SSL de domínio personalizado, certifique-se de que o certificado está inserido no Key Vault como certificado, e não como segredo.

* [Como definir nomes de domínio personalizados com orientação para a rotação da chave do cofre chave](https://docs.microsoft.com/azure/api-management/configure-custom-domain)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize a Identidade de Serviço Gerida gerada pelo Azure Ative Directory (AD) para permitir que a sua instância de Gestão API aceda de forma fácil e segura a outros recursos protegidos pela Azure AD, como o Azure Key Vault.

* [Como criar uma identidade gerida para uma instância de Gestão API](https://docs.microsoft.com/azure/api-management/api-management-howto-use-managed-service-identity)

* [Política de autenticação com identidade gerida](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte Controlo de [Segurança: Defesa de Malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Do Azure (por exemplo, a Azure API Management), no entanto não funciona com conteúdo de clientes.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: Não aplicável; esta recomendação destina-se a recursos não calculados destinados a armazenar dados.

O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Do Azure (por exemplo, a Azure API Management), no entanto não funciona com conteúdo de clientes.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos não calculados destinados a armazenar dados.

O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Do Azure (por exemplo, a Azure API Management), no entanto não funciona com conteúdo de clientes.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para mais informações, consulte Controlo de [Segurança: Recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Ao publicar e gerir as suas APIs através da Azure API Management, está a aproveitar a tolerância a falhas e as capacidades de infraestrutura que de outra forma desenharia, implementaria e geriria manualmente. A API Management apoia a implantação em várias regiões, o que torna o avião de dados impermeável a falhas regionais sem adicionar qualquer sobrecarga operacional.

O backup de serviço e restauro de funcionalidades da API Management fornecem os blocos de construção necessários para implementar uma estratégia de recuperação de desastres. As operações de backup e restauro podem ser realizadas manualmente ou automatizadas.

* [Como implantar o avião de dados da API Management para várias regiões](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

* [Como implementar a recuperação após desastre através do serviço de cópia de segurança e restauro na Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Como chamar a operação de backup da API Management](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)

* [Como chamar a Operação de Restauro de Gestão aPi](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: As operações de backup e restauro fornecidas pela Azure API Management realizam a cópia de segurança e restauro do sistema completo.

As identidades geridas podem ser usadas para obter certificados do Cofre chave azure para nomes de domínio personalizados de Gestão API. Faça a cópia de segurança de quaisquer certificados armazenados no Cofre de Chaves Azure.

* [Como implementar a recuperação após desastre através do serviço de cópia de segurança e restauro na Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Como fazer backup dos certificados do Cofre de Chaves Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Validar cópias de segurança através da realização de um teste de restauro do serviço e dos certificados de cópias de segurança.

* [Como chamar a Operação de Restauro de Gestão aPi](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

* [Como restaurar os certificados do Cofre de Chaves Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: A Azure API Management escreve cópias de segurança para contas de armazenamento azure detidas por clientes. Siga as recomendações de segurança do Azure Storage para proteger o seu backup.

* [Como implementar a recuperação após desastre através do serviço de cópia de segurança e restauro na Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Recomendação de segurança para armazenamento de bolhas](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)

Ative o Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

* [Como ativar o Soft-Delete no Cofre de Chaves](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte Controlo de [Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.

* [Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Aproveite o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (para ex. produção, não prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis. É da sua responsabilidade priorizar a remediação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

* [Alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Use tags para organizar os seus recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

* [Publicação do NIST - Guia para Programas de Teste, Formação e Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exportar os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua para ajudar a identificar riscos para os recursos do Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas para o Azure Sentinel.

* [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Como transmitir alertas para o Sentinela Azure](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de Automatização de Fluxos de Trabalho no Centro de Segurança Azure para acionar automaticamente as respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

* [Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para mais informações, consulte Controlo de [Segurança: Testes de penetração e exercícios vermelhos da equipa.](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as descobertas críticas de segurança no prazo de 60 dias

**Orientação**: * Por favor siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de [Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra a Microsoft gerida infraestruturas, serviços e aplicações em nuvem, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte o referencial de [segurança azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
