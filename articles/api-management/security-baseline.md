---
title: Linha de base de segurança Azure para a Gestão da API
description: Linha de base de segurança Azure para a Gestão da API
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d6c954d6336a0b88f57ba1f70571263af5d1c583
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229808"
---
# <a name="azure-security-baseline-for-api-management"></a>Linha de base de segurança Azure para a Gestão da API

A Linha de Base de Segurança Azure para a Gestão da API contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte a visão geral das [linhas de base de segurança Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](../security/benchmarks/security-control-network-security.md)*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: A Azure API Management pode ser implantada dentro de uma Rede Virtual Azure (Vnet), para que possa aceder a serviços de backend dentro da rede. O portal de desenvolvedores e o gateway de gestão da API, podem ser configurados para serem acessíveis a partir da Internet (Externa) ou apenas dentro do Vnet (Internal).
- Externo: o portal de gateway e developer da API Management são acessíveis a partir da internet pública através de um equilibrador de carga externo. O gateway pode aceder a recursos dentro da rede virtual.
- Interno: o portal de gateway e developer da API Management são acessíveis apenas a partir da rede virtual através de um equilibrador de carga interno. O gateway pode aceder a recursos dentro da rede virtual.

O tráfego de entrada e saída na sub-rede em que a API Management é implantada pode ser controlado através do Grupo de Segurança da Rede.

* [Como utilizar a Gestão de API do Azure com redes virtuais](./api-management-using-with-vnet.md)

* [Utilizar o serviço Gestão de API do Azure com uma rede virtual interna](./api-management-using-with-internal-vnet.md)

* [Integrar a Gestão da API num VNET interno com Gateway de Aplicação](./api-management-howto-integrate-internal-vnet-appgateway.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICs

**Orientação**: O tráfego de entrada e saída na sub-rede em que a API Management é implantada pode ser controlado utilizando grupos de segurança de rede (NSGs). Implemente um NSG na sua sub-rede de Gestão API e ative registos de fluxo NSG e envie registos para uma conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Atenção: Ao configurar um NSG na sub-rede API Management, há um conjunto de portas que devem estar abertas. Se alguma destas portas não estiver disponível, a API Management pode não funcionar corretamente e pode tornar-se inacessível.

* [Compreender as configurações do NSG para a Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Para proteger a web/HTTP APIs configurar a API Management dentro de uma Rede Virtual (Vnet) em modo interno e configurar um Gateway de aplicações Azure. Application Gateway é um serviço PaaS. Atua como um proxy invertido e fornece o equilíbrio de carga L7, encaminhamento, firewall de aplicação web (WAF) e outros serviços.

Combinar a Gestão da API a provisionada num Vnet interno com o frontend Do Gateway de Aplicação permite os seguintes cenários:
- Utilize um único recurso de Gestão de API para expor todas as APIs tanto aos consumidores internos como aos consumidores externos.
- Utilize um único recurso de Gestão de API para expor um subconjunto de APIs a consumidores externos.
- Fornecer uma forma de mudar o acesso à API Management a partir da Internet pública dentro e fora.

Nota: Esta funcionalidade está disponível nos níveis Premium e Developer da API Management.

* [Como integrar a Gestão da API num VNET interno com Gateway de Aplicação](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Compreender gateway de aplicação Azure](../application-gateway/index.yml)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Configurar a Gestão da API dentro de uma Rede Virtual (Vnet) em modo interno e configurar um Gateway de aplicações Azure. Application Gateway é um serviço PaaS. Atua como um proxy invertido e fornece o equilíbrio de carga L7, encaminhamento, firewall de aplicação web (WAF) e outros serviços.

Combinar a Gestão da API a provisionada num Vnet interno com o frontend Do Gateway de Aplicação permite os seguintes cenários:
- Utilize um único recurso de Gestão de API para expor todas as APIs tanto aos consumidores internos como aos consumidores externos.
- Utilize um único recurso de Gestão de API para expor um subconjunto de APIs a consumidores externos.
- Fornecer uma forma de mudar o acesso à API Management a partir da Internet pública dentro e fora.

Nota: Esta funcionalidade está disponível nos níveis Premium e Developer da API Management.

Ativar a Norma de Proteção DDoS do Azure na Vnet associada à sua implementação de Gestão API para proteger contra ataques de negação de serviço distribuídos (DDoS).

Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

* [Como integrar a Gestão da API num VNET interno com Gateway de Aplicação](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Compreender gateway de aplicação Azure](../application-gateway/index.yml)

* [Como configurar a Norma de Proteção DDoS do Azure](../virtual-network/manage-ddos-protection.md)

* [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/threat-protection.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: O tráfego de entrada e saída na sub-rede em que a API Management é implantada pode ser controlado através de Grupos de Segurança de Rede (NSG). Implemente um NSG na sua sub-rede de Gestão API e ative registos de fluxo NSG e envie registos para uma conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Atenção: Ao configurar um NSG na sub-rede API Management, há um conjunto de portas que devem estar abertas. Se alguma destas portas não estiver disponível, a API Management pode não funcionar corretamente e pode tornar-se inacessível.

* [Compreender as configurações do NSG para a Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Configurar a Gestão da API dentro de uma Rede Virtual (Vnet) em modo interno e configurar um Gateway de aplicações Azure. Application Gateway é um serviço PaaS. Atua como um proxy invertido e fornece o equilíbrio de carga L7, encaminhamento, firewall de aplicação web (WAF) e outros serviços. O Application Gateway WAF fornece proteção contra explorações e vulnerabilidades de segurança comuns e pode ser executado nos dois modos seguintes:
- Modo de deteção: Monitores e regista todos os alertas de ameaça. Pode ligar os diagnósticos de registo de registo sonoro para o Gateway de Aplicações na secção De Diagnóstico. Deve certificar-se de que o registo WAF é selecionado e ligado. A firewall da aplicação web não bloqueia os pedidos de entrada quando está a funcionar no modo deteção.
- Modo de prevenção: Bloqueia intrusões e ataques que as regras detetam. O intruso recebe uma exceção de "acesso não autorizado 403" e a ligação está fechada. O modo de prevenção regista tais ataques nos registos da WAF.

Combinar a Gestão da API a provisionada num Vnet interno com o frontend Do Gateway de Aplicação permite os seguintes cenários:
- Utilize um único recurso de Gestão de API para expor todas as APIs tanto aos consumidores internos como aos consumidores externos.
- Utilize um único recurso de Gestão de API para expor um subconjunto de APIs a consumidores externos.
- Fornecer uma forma de mudar o acesso à API Management a partir da Internet pública dentro e fora.

Nota: Esta funcionalidade está disponível nos níveis Premium e Developer da API Management.

* [Como integrar a Gestão da API num VNET interno com Gateway de Aplicação](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Compreender Azure Application Gateway WAF](../web-application-firewall/ag/ag-overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Para gerir o fluxo de tráfego para ASP Web/HTTP implemente a API Management para uma Rede Virtual (Vnet) associada ao Ambiente de Serviço de Aplicações em modo externo ou interno.

Em modo interno, configurar um Gateway de aplicação Azure em frente à API Management. Application Gateway é um serviço PaaS. Atua como um proxy invertido e fornece o equilíbrio de carga L7, encaminhamento, firewall de aplicação web (WAF) e outros serviços. O Gateway WAF de aplicação fornece proteção contra explorações e vulnerabilidades de segurança comuns.

Combinar a Gestão da API a provisionada num Vnet interno com o frontend Do Gateway de Aplicação permite os seguintes cenários:
- Utilize um único recurso de Gestão de API para expor todas as APIs tanto aos consumidores internos como aos consumidores externos.
- Utilize um único recurso de Gestão de API para expor um subconjunto de APIs a consumidores externos.
- Fornecer uma forma de mudar o acesso à API Management a partir da Internet pública dentro e fora.

Nota: Esta funcionalidade está disponível nos níveis Premium e Developer da API Management.

* [Como expor as APIs privadas a consumidores externos](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [Como utilizar a Gestão da API dentro de um Vnet](./api-management-using-with-vnet.md)

* [Firewall de aplicação web Azure no Gateway de aplicações Azure](../web-application-firewall/ag/ag-overview.md)

* [Compreender gateway de aplicação Azure](../application-gateway/overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtual (Vnet) para definir controlos de acesso à rede em Grupos de Segurança de Rede (NSGs) utilizados nas suas subnetas de Gestão API. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Atenção: Ao configurar um NSG na sub-rede API Management, há um conjunto de portas que devem estar abertas. Se alguma destas portas não estiver disponível, a API Management pode não funcionar corretamente e pode tornar-se inacessível.

* [Compreensão e utilização de etiquetas de serviço](../virtual-network/service-tags-overview.md)

* [Portos necessários para a Gestão da API](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para definições de rede relacionadas com as suas implementações de Gestão API Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.ApiManagement" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede das suas implementações de Gestão API Azure e recursos relacionados. Também pode utilizar definições políticas incorporadas para redes virtuais Azure, tais como:
- A Norma de Proteção DDoS deve ser ativada

Também pode utilizar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em funções (RBAC) e políticas numa única definição de planta. Pode aplicar facilmente o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

* [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

* [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para grupos de segurança de rede (NSGs) e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras NSG individuais, pode utilizar o campo "Descrição" para especificar a necessidade do negócio e/ou a duração (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede associados às suas implementações de Gestão API Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

* [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para a Azure API Management.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Dentro do Monitor Azure, utilize o log analytics workspace(s) para consultar e realizar análises, enviar registos para o Azure Storage para armazenamento de longo prazo/arquivo ou análise offline, ou guardar registos para outra solução de análise no Azure e em outros lugares usando Azure Event Hubs. A Azure API Management produz registos e métricas para O Azure Monitor por padrão. A verbosidade do registo pode ser configurada numa base de serviço e por API.

Além do Azure Monitor, a Azure API Management pode ser integrada com um ou vários serviços de Azure Application Insights. As definições de registo de Insights de Aplicação podem ser configuradas por serviço ou por API.

Opcionalmente, ativar e a bordo dados para Azure Sentinel ou um terceiro Security Incident and Event Management (SIEM).

* [Como configurar definições de diagnóstico](../azure-monitor/platform/diagnostic-settings.md#create-in-azure-portal)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Como criar o pipeline de registo e análise personalizado](./api-management-howto-log-event-hubs.md)

* [Como integrar-se com a Azure Application Insights](./api-management-howto-app-insights.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Para controlar a verificação de dados, permita a ação de diagnóstico do Registo de Atividades do Azure e envie registos de atividade para um espaço de trabalho log Analytics para reportagem e análise, para o Azure Storage para uma manutenção de longa duração, para Azure Event Hubs para exportação em outras soluções de análise em Azure e em outros lugares. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para o seu serviço de Gestão API Azure.

Para a verificação de dados, os registos de diagnóstico fornecem informações ricas sobre operações e erros que são importantes para a auditoria, bem como para efeitos de resolução de problemas. Os registos de diagnóstico diferem dos registos de atividades. Os registos de atividades fornecem informações aprofundadas sobre as operações executadas nos recursos do Azure. Os registos de diagnóstico fornecem informações aprofundadas sobre as operações executadas pelo recurso.

* [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/platform/activity-log.md)

* [Como ativar definições de diagnóstico para gestão de API Azure](./api-management-howto-use-azure-monitor.md#activity-logs)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desa um ponto final no período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize as contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

* [Como definir parâmetros de retenção de registos para log analytics workspaces](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Como arquivar registos numa conta de Armazenamento Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: A Azure API Management emite continuamente registos e métricas ao Azure Monitor, dando-lhe uma visibilidade quase em tempo real para o estado e saúde das suas APIs. Com o Azure Monitor e Log Analytics, pode rever, consultar, visualizar, encaminhar, arquivar, configurar alertas e tomar ações sobre métricas e registos provenientes da API Management e recursos conexos. Analise e monitorize registos para comportamentos anómalos e reveja regularmente os resultados.

Opcionalmente, integrar a Gestão da API com a Azure Application Insights e usá-la como ferramenta de monitorização, rastreio, reporte e alerta primário ou secundário.

* [Como monitorizar e rever registos para a Azure API Management](./api-management-howto-use-azure-monitor.md)

* [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

* [Compreender log analytics workspace](../azure-monitor/log-query/get-started-portal.md)

* [Como integrar-se com a Azure Application Insights](./api-management-howto-app-insights.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividades Azure, bem como as definições de diagnóstico para as suas instâncias de Gestão API Azure e enviar os registos para um espaço de trabalho log analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos. Pode criar alertas com base nas suas consultas de espaço de trabalho Log Analytics.

Crie alertas métricos para que saiba quando algo inesperado está a acontecer. Por exemplo, obtenha notificações quando a sua instância de Gestão API Azure tiver excedido a sua capacidade máxima esperada durante um determinado período de tempo ou se tiver havido um certo número de pedidos ou erros não autorizados de gateway durante um período de tempo pré-determinado.

Opcionalmente, integrar a Gestão da API com a Azure Application Insights e usá-la como ferramenta de monitorização, rastreio, reporte e alerta primário ou secundário.

Opcionalmente, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

* [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/platform/activity-log.md)

* [Como permitir configurações de diagnóstico para a Azure API Management](./api-management-howto-use-azure-monitor.md#activity-logs)

* [Como configurar uma regra de alerta para pedido não autorizado](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule-for-unauthorized-request)

* [Como visualizar métricas de capacidade de uma instância de gestão da API Azure](./api-management-capacity.md)

* [Como integrar-se com a Azure Application Insights](./api-management-howto-app-insights.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável; A Azure API Management não processa nem produz registos relacionados com anti-malware.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; A Azure API Management não processa nem produz registos DNS acessíveis ao utilizador.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Manter um inventário de contas que tenham acesso administrativo ao plano de controlo de gestão API Azure (portal Azure).

O Azure Ative Directory (AD) tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. A API Management conta com estas funções e controlo de acesso baseado em funções para permitir uma gestão de acessos finos para serviços e entidades de Gestão de API.

Adicionalmente, a API Management contém um grupo de Administradores incorporados no sistema de utilizador da API Management. Os grupos na API Management controlam a visibilidade das APIs no portal do desenvolvedor e os membros do grupo de Administradores podem ver todas as APIs.

Siga as recomendações do Centro de Segurança Azure para a gestão e manutenção de contas administrativas.

* [Como utilizar o Controlo de Acesso Baseado em Funções na Gestão de API do Azure](./api-management-role-based-access-control.md)

* [Como obter uma lista de utilizadores ao abrigo de uma Instância de Gestão da API da Azure](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Como obter uma lista de utilizadores atribuídos a um papel de diretório em Azure AD com PowerShell](/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [Como obter uma definição de papel de diretório em Azure AD com PowerShell](/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [Compreender recomendações de identidade e acesso do Azure Security Center](../security-center/recommendations-reference.md#recs-identity)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: A Azure API Management não tem o conceito de senhas/chave padrão.

As subscrições da API Management, que são um meio de garantir o acesso às APIs, vêm no entanto com um par de chaves de subscrição geradas. Os clientes podem regenerar estas chaves de subscrição a qualquer momento.

* [Compreensão das assinaturas de gestão da API da AZure](./api-management-subscriptions.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:
- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

* [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../security-center/security-center-identity-access.md)

* [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: A Azure API Management pode ser configurada para alavancar o Azure Ative Directory como fornecedor de identidade para autenticar utilizadores no Portal do Desenvolvedor, de modo a beneficiar das capacidades SSO oferecidas pela Azure AD. Uma vez configurado, os novos utilizadores do Portal do Desenvolvimento podem optar por seguir o processo de inscrição fora da caixa, autenticando primeiro através do Azure AD e, em seguida, completando o processo de inscrição no portal uma vez autenticado.

* [Autorizar as contas de programador ao utilizar o Azure Active Directory na Gestão de API do Azure](./api-management-howto-aad.md)

Em alternativa, o processo de inscrição/inscrição pode ser ainda mais personalizado através da delegação. A delegação permite-lhe utilizar o seu website existente para lidar com o sinal de desenvolvimento/inscrição e subscrição de produtos, em oposição à utilização da funcionalidade incorporada no portal do desenvolvedor. Permite que o seu website possua os dados do utilizador e realize a validação destes passos de forma personalizada.

* [Como delegar o registo do utilizador e a subscrição do produto](./api-management-howto-setup-delegation.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar o Azure Ative Directory (AD) Autenticação multi-factor (MFA) e seguir as recomendações do Azure Security Center Identity and Access Management.

* [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos Azure.

* [Saiba mais sobre estações de trabalho de acesso privilegiado](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Além disso, utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

* [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Compreender deteções de risco Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso ao portal Azure a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Sempre que possível, utilize o Azure AD como sistema central de autenticação e autorização. AAD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Configure o seu Portal de Desenvolvimento de Gestão API da Azure para autenticar contas de programadores utilizando o Azure Ative Directory.

Configure a sua instância de Gestão API Azure para proteger as suas APIs utilizando o protocolo OAuth 2.0 com o Azure Ative Directory (AD).

* [Como autorizar contas de programadores utilizando o Azure Ative Directory in Azure API Management](./api-management-howto-aad.md)

* [Como proteger uma API utilizando o OAuth 2.0 com o Azure Ative Directory e a API Management](./api-management-howto-protect-backend-with-aad.md)

* [Como criar e configurar um caso AAD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory fornece registos para ajudar a descobrir contas velhas. Os clientes podem utilizar a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos continuem a ter acesso adequado.

Os clientes podem manter o inventário das contas de utilizador da API Management e conciliar o acesso conforme necessário. Na Gestão da API, os desenvolvedores são os consumidores das APIs que expõem a API Management. Por padrão, as contas de programadores recém-criadas são Ativas e associadas ao grupo Developers. As contas de programadores que se encontrem num estado ativo podem ser usadas para aceder a todas as APIs para as quais têm subscrições.

Os administradores podem criar grupos personalizados ou alavancar grupos externos em inquilinos associados do Azure Ative Directory. É possível utilizar grupos personalizados e externos em conjunto com grupos de sistema para dar aos programadores acesso e visibilidade a produtos de API.

* [Como gerir contas de utilizador na Gestão de API do Azure](./api-management-howto-create-or-invite-developers.md)

* [Como obter a lista de utilizadores da API Management](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Como criar e utilizar grupos para gerir contas de programador na Gestão de API do Azure](./api-management-howto-create-groups.md)

* [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação**: Configure a sua instância de Gestão API Azure para autenticar contas de programadores utilizando o Azure Ative Directory como fornecedor de identidade na Azure API Management.

Configure a sua instância de Gestão API Azure para proteger as suas APIs utilizando o protocolo OAuth 2.0 com o Azure Ative Directory (AD).

Configure a política de validação da JWT aos pedidos de API que chegam para ajudar a impor a existência e validade de um token válido.

Crie definições de diagnóstico para contas de utilizadores AZure E envie os registos de auditoria e registos de inscrição para um espaço de trabalho do Log Analytics. Configure os alertas desejados dentro do Log Analytics. Além disso, pode embarcar no espaço de trabalho Log Analytics para Azure Sentinel ou um SIEM de terceiros.

Configure uma monitorização avançada com a API Management utilizando a `log-to-eventhub` política, capture todas as informações de contexto adicionais necessárias para análise de segurança e envie para o Azure Sentinel ou para o SIEM de terceiros.

* [Como autorizar contas de programadores utilizando o Azure Ative Directory in Azure API Management](./api-management-howto-aad.md)

* [Como proteger uma API utilizando o OAuth 2.0 com o Azure Ative Directory e a API Management](./api-management-howto-protect-backend-with-aad.md)

* [Políticas de restrição de acesso à Gestão de API](./api-management-access-restriction-policies.md)

* [Como integrar os registos AD do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Monitorização avançada das APIs](./api-management-log-to-eventhub-sample.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Para desvio de comportamento de login de conta no plano de controlo (portal Azure), utilize o Azure Ative Directory (AD) Proteção de Identidade e funcionalidades de deteção de riscos para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

* [Como ver a Azure AD a entrar em risco](../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não disponível atualmente; O Lockbox do cliente não é atualmente suportado para a Azure API Management.

* [Lista de serviços suportados pelo Cliente Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. As instâncias de gestão da API Azure devem ser separadas por rede virtual (VNet)/sub-rede e marcadas adequadamente.

* [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../governance/management-groups/create.md)

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

* [Como utilizar a Gestão de API do Azure com redes virtuais](./api-management-using-with-vnet.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Não disponível atualmente; as funcionalidades de identificação, classificação e prevenção de perdas não estão atualmente disponíveis para a Azure API Management.

A Microsoft gere a infraestrutura subjacente à Azure API Management e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

* [Compreender a proteção de dados do cliente em Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: As chamadas de avião de gestão são feitas através do Azure Resource Manager sobre o TLS. É necessário um token web JSON válido (JWT). As chamadas de plano de dados podem ser asseguradas com TLS e um dos mecanismos de autenticação suportados (por exemplo, certificado de cliente ou JWT).

* [Compreender a proteção de dados na Azure API Management](./api-management-security-controls.md#data-protection)

* [Gerir as definições de TLS na Gestão API da Azure](./api-management-howto-manage-protocols-ciphers.md)

* [Proteja as APIs na Gestão API da Azure com diretório ativo Azure](./api-management-howto-protect-backend-with-aad.md)

* [Proteger APIs na Gestão API da Azure com Diretório Ativo Azure B2C](./howto-protect-backend-frontend-azure-ad-b2c.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Não disponível atualmente; as funcionalidades de identificação, classificação e prevenção de perdas não estão atualmente disponíveis para a Azure API Management. Marque serviços de Gestão API Azure que possam estar a processar informações sensíveis como tal e implementar solução de terceiros se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

* [Compreender a proteção de dados do cliente em Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções para controlar o acesso à Azure API Management. A Azure API Management conta com o controlo de acesso baseado em funções Azure (Azure RBAC) para permitir uma gestão de acessos finos para serviços e entidades de Gestão de API (por exemplo, APIs e políticas).

* [Como utilizar o Controlo de Acesso Baseado em Funções na Gestão de API do Azure](./api-management-role-based-access-control.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

A Microsoft gere a infraestrutura subjacente à Azure API Management e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

* [Compreender a proteção de dados do cliente em Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Os dados sensíveis, tais como certificados, chaves e valores secretos nomeados são encriptados com chaves geridas pelo serviço, por instância de serviço. Todas as chaves de encriptação são por instância de serviço e são geridas pelo serviço.

* [Compreender a proteção de dados/encriptação em repouso com a Azure API Management](./api-management-security-controls.md#data-protection)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações na produção de aplicações Azure Functions, bem como outros recursos críticos ou relacionados.

* [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/platform/alerts-activity-log.md)

* [Como utilizar o Monitor Azure e o Registo de Atividades Azure na Gestão API da Azure](./api-management-howto-use-azure-monitor.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Não disponível atualmente; a avaliação de vulnerabilidade no Azure Security Center não está atualmente disponível para a Azure API Management.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Reveja os controlos de segurança disponíveis para reduzir as vulnerabilidades relacionadas com a configuração do serviço.

* [Compreender os controlos de segurança disponíveis para a Azure API Management](./api-management-security-controls.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Não disponível atualmente; a avaliação de vulnerabilidade no Azure Security Center não está atualmente disponível para a Azure API Management.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Cliente para rever os controlos de segurança disponíveis para reduzir as vulnerabilidades relacionadas com a configuração do serviço.

* [Compreender os controlos de segurança disponíveis para a Azure API Management](./api-management-security-controls.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s). Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

* [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

* [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

* [Como criar e utilizar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a política da Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../governance/management-groups/create.md)

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos aprovados da Azure e dos títulos de software

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição(s) utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

* [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo específico de recurso com a Política Azure](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure através de scripts

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

* [Controlo de acesso baseado em funções na Gestão API da Azure](./api-management-role-based-access-control.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para o seu serviço de Gestão API Azure com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração dos seus serviços de Gestão API Azure.

* [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os seus serviços de Gestão API Azure com Azure Policy. Use pseudónimos da Azure Policy no espaço de nomes "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de instâncias de Gestão API da Azure. Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

* [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições de política Azure personalizadas, utilize Azure DevOps ou Azure Repos para armazenar e gerir de forma segura a configuração do serviço de Gestão API Azure.

* [Como armazenar ficheiros em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops)

* [Compreenda o Kit de Recursos de Gestão de DevOps da Azure](./api-management-security-controls.md#configuration-management)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Defina e implemente configurações de segurança padrão para os seus serviços de Gestão API Azure com Azure Policy. Use pseudónimos da Azure Policy no espaço de nomes "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de instâncias de Gestão API da Azure. Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

* [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação**: Utilize o Kit de Recursos de Gestão API da Azure para executar a gestão de configuração para a Gestão API da Azure.

Além disso, defina e implemente configurações de segurança padrão para os seus serviços de Gestão API Azure com Azure Policy. Use pseudónimos da Azure Policy no espaço de nomes "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de instâncias de Gestão API da Azure. Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Compreenda o Kit de Recursos de Gestão de DevOps da Azure](./api-management-security-controls.md#configuration-management)

* [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

* [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize o Cofre-Chave para gerir certificados e coloque-os para a autorotate. Se utilizar o Cofre de Chaves Azure para gerir o certificado SSL de domínio personalizado, certifique-se de que o certificado é inserido no Cofre de Chaves como certificado, não como segredo.

* [Como definir nomes de domínio personalizados com orientação para a rotação da chave do cofre](./configure-custom-domain.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidade de serviço gerido gerada pelo Azure Ative Directory (AD) para permitir que a sua instância de Gestão API aceda de forma fácil e segura a outros recursos protegidos por Azure, como o Azure Key Vault.

* [Como criar uma identidade gerida para um caso de Gestão de API](./api-management-howto-use-managed-service-identity.md)

* [Política de autenticação com identidade gerida](./api-management-authentication-policies.md#ManagedIdentity)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure API Management), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos não computacional destinados a armazenar dados.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure API Management), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos não computacional destinados a armazenar dados.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure API Management), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Ao publicar e gerir as suas APIs através da Azure API Management, está a aproveitar a tolerância a falhas e as capacidades de infraestrutura que de outra forma conceberia, implementaria e geriria manualmente. A API Management apoia a implantação de várias regiões, o que torna o plano de dados impermeável a falhas regionais sem adicionar qualquer sobrecarga operacional.

As funcionalidades de backup e restauro do serviço da API Management fornecem os blocos de construção necessários para a implementação de uma estratégia de recuperação de desastres. As operações de backup e restauro podem ser realizadas manualmente ou automatizadas.

* [Como implantar o plano de dados da API Management para várias regiões](./api-management-howto-deploy-multi-region.md)

* [Como implementar a recuperação após desastre através do serviço de cópia de segurança e restauro na Gestão de API do Azure](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Como chamar a operação de backup da API Management](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [Como chamar a operação de restauro da API Management](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: As operações de backup e restauro fornecidas pela Azure API Management executam cópias de segurança completas do sistema e restauro.

Identidades geridas podem ser usadas para obter certificados do Azure Key Vault para nomes de domínio personalizados da API Management. Cópia de segurança de quaisquer certificados armazenados dentro do Cofre da Chave Azure.

* [Como implementar a recuperação após desastre através do serviço de cópia de segurança e restauro na Gestão de API do Azure](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Como apoiar os certificados Azure Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Validar cópias de segurança através da efetuação de um teste de restauro do serviço e certificados de cópias de segurança.

* [Como chamar a operação de restauro da API Management](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [Como restaurar os certificados Azure Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: A Azure API Management escreve backups para contas de Armazenamento Azure detidas pelo cliente. Siga as recomendações de segurança do Azure Storage para proteger a sua cópia de segurança.

* [Como implementar a recuperação após desastre através do serviço de cópia de segurança e restauro na Gestão de API do Azure](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Recomendação de segurança para armazenamento de bolhas](../storage/blobs/security-recommendations.md)

Ativar a eliminação suave no cofre de chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

* [Como permitir o Soft-Delete no Cofre da Chave](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Crie um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente.

* [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Alavancar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis. É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

* [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

* [Utilizar etiquetas para organizar os seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

* [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

* [Como configurar a exportação contínua](../security-center/continuous-export.md)

* [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Logic Apps" em alertas e recomendações de segurança.

* [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir que remedia todas as conclusões críticas de segurança no prazo de 60 dias

**Orientação**: * [Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridos pela Microsoft em nuvem, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre [as linhas de base de segurança da Azure](../security/benchmarks/security-baselines-overview.md)
