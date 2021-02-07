---
title: Linha de segurança Azure para Azure Application Gateway
description: Linha de segurança Azure para Azure Application Gateway
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4f28665998dcac9f641d4142a0dea60707fb02e9
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805365"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>Linha de segurança Azure para Azure Application Gateway

A Linha de Base de Segurança Azure para O Gateway de Aplicação Azure contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte a visão geral das [linhas de base de segurança Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](../security/benchmarks/security-control-network-security.md)*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Certifique-se de que todas as implementações da sub-rede virtual Azure Application Gateway têm um grupo de segurança de rede (NSG) aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Embora os grupos de segurança da rede sejam suportados no Gateway de aplicações Azure, existem algumas restrições e requisitos que devem ser cumpridos para que o seu Gateway de Aplicação NSG e Azure funcione como esperado.

* [Compreender restrições e requisitos em torno da utilização de NSGs com Gateway de aplicação Azure](./configuration-overview.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: Para os grupos de segurança de rede (NSGs) associados às suas sub-redes Azure Application Gateway, ativar registos de fluxo NSG e enviar registos numa Conta de Armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Nota: Existem alguns casos em que os registos de fluxo NSG associados às suas sub-redes Azure Application Gateway não mostram tráfego que tenha sido permitido. Se a sua configuração corresponder ao seguinte cenário, não verá tráfego permitido nos seus registos de fluxo NSG:
- Você implantou o Application Gateway v2
- Você tem um NSG na sub-rede de gateway de aplicação
- Você ativou registos de fluxo NSG em que NSG

* [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

* [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

* [FAQ para diagnóstico e registo para gateway de aplicação Azure](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Implementar firewall de aplicação web Azure (WAF) em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS. A WAF baseia-se nas regras do OWASP (Open Web Application Security Project) que define 3.1 (apenas WAF_v2), 3.0 e 2.2.9.

* [Compreenda as funcionalidades do Gateway de Aplicações Azure](./features.md)

* [Compreenda Azure WAF](../web-application-firewall/ag/ag-overview.md)

* [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative a proteção DDoS Standard nas suas Redes Virtuais Azure associadas às suas instâncias de produção do Azure Application Gateway para proteger contra ataques DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP maliciosos conhecidos.

* [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

* [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Para os grupos de segurança de rede (NSGs) associados às suas sub-redes Azure Application Gateway, ativar registos de fluxo NSG e enviar registos numa Conta de Armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Nota: Existem alguns casos em que os registos de fluxo NSG associados às suas sub-redes Azure Application Gateway não mostram tráfego que tenha sido permitido. Se a sua configuração corresponder ao seguinte cenário, não verá tráfego permitido nos seus registos de fluxo NSG:
- Você implantou o Application Gateway v2
- Você tem um NSG na sub-rede de gateway de aplicação
- Você ativou registos de fluxo NSG em que NSG

* [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

* [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

* [FAQ para diagnóstico e registo para gateway de aplicação Azure](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Implementar firewall de aplicação web Azure (WAF) em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS. A WAF baseia-se nas regras do OWASP (Open Web Application Security Project) que define 3.1 (apenas WAF_v2), 3.0 e 2.2.9.

Em alternativa, existem várias opções de mercado como o Barracuda WAF para Azure que estão disponíveis no Azure Marketplace que incluem funcionalidades IDS/IPS.

* [Compreenda as funcionalidades do Gateway de Aplicações Azure](./features.md)

* [Compreenda Azure WAF](../web-application-firewall/ag/ag-overview.md)

* [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Compreender o Serviço de Nuvem WAF Barracuda](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Implementar gateway de aplicações Azure para aplicações web com HTTPS/SSL habilitado para certificados fidedignos.

* [Como implementar o Gateway de Aplicações](./quick-create-portal.md)

* [Como configurar o Gateway de aplicações para utilizar HTTPS](./create-ssl-portal.md)

* [Compreenda o equilíbrio de carga da camada 7 com os gateways de aplicações web Azure](./overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em Grupos de Segurança de Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, GatewayManager) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Para os grupos de segurança de rede (NSGs) associados às suas sub-redes Azure Application Gateway, deve permitir a entrada de tráfego de Internet nas portas TCP 65503-65534 para o Gateway de Aplicação v1 SKU e portas TCP 65200-65535 para o V2 SKU com a sub-rede de destino como Qualquer e fonte como tag de serviço GatewayManager. Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) pelos certificados Azure. Entidades externas, incluindo os clientes desses gateways, não podem comunicar nestes pontos finais.

* [Compreender e utilizar tags de serviço](../virtual-network/service-tags-overview.md)

* [Visão geral da configuração do Gateway de aplicação Azure](./configuration-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para definições de rede relacionadas com as suas implementações do Gateway de aplicações Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus Gateways de Aplicações Azure, Redes Virtuais Azure e grupos de segurança de rede. Também pode utilizar a definição de política incorporada.

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em funções Azure (Azure RBAC), e políticas numa única definição de planta. Pode aplicar facilmente o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize Tags para grupos de segurança de rede (NSGs) associados à sua sub-rede Azure Application Gateway, bem como quaisquer outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de política incorporadas do Azure relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas Tags.

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para as definições de rede e recursos relacionados com as suas implementações do Gateway de Aplicações Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nas definições ou recursos críticos da rede.

* [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como o Azure App Service.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Para controlar a verificação de dados, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure. Utilizando dados do Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para o seu Gateway de Aplicação Azure e recursos relacionados, como grupos de segurança de rede (NSGs), sendo usados para proteger a sub-rede Azure Application Gateway.

Além dos Registos de Atividade, pode configurar definições de diagnóstico para as suas implementações do Gateway de Aplicação Azure. as definições de diagnóstico são usadas para configurar a exportação de streaming de registos e métricas de plataforma para um recurso para o destino à sua escolha (Contas de Armazenamento, Centros de Eventos e Log Analytics).

O Azure Application Gateway também oferece integração incorporada com a Azure Application Insights. O Application Insights recolhe dados de registo, desempenho e erro. O Application Insights deteta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para o ajudar a diagnosticar problemas e a compreender como as suas aplicações web estão a ser utilizadas. Pode permitir a exportação contínua para a telemetria de exportação da Application Insights para um local centralizado para manter os dados por mais tempo do que o período de retenção padrão.

* [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/platform/activity-log.md)

* [Como ativar as definições de diagnóstico para O Gateway de Aplicação Azure](./application-gateway-diagnostics.md)

* [Como ativar insights de aplicações](../azure-monitor/app/app-insights-overview.md)

* [Como configurar a exportação contínua](../azure-monitor/app/export-telemetry.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Para controlar a verificação de dados, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure. Utilizando dados do Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para o seu Gateway de Aplicação Azure e recursos relacionados, como grupos de segurança de rede (NSGs), sendo usados para proteger a sub-rede Azure Application Gateway.

Além dos Registos de Atividade, pode configurar definições de diagnóstico para as suas implementações do Gateway de Aplicação Azure. as definições de diagnóstico são usadas para configurar a exportação de streaming de registos e métricas de plataforma para um recurso para o destino à sua escolha (Contas de Armazenamento, Centros de Eventos e Log Analytics).

O Azure Application Gateway também oferece integração incorporada com a Azure Application Insights. O Application Insights recolhe dados de registo, desempenho e erro. O Application Insights deteta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para o ajudar a diagnosticar problemas e a compreender como as suas aplicações web estão a ser utilizadas. Pode permitir a exportação contínua para a telemetria de exportação da Application Insights para um local centralizado para manter os dados por mais tempo do que o período de retenção padrão.

* [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/platform/activity-log.md)

* [Como ativar as definições de diagnóstico para O Gateway de Aplicação Azure](./application-gateway-diagnostics.md)

* [Como ativar insights de aplicações](../azure-monitor/app/app-insights-overview.md)

* [Como configurar a exportação contínua](../azure-monitor/app/export-telemetry.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desa um ponto final no período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

* [Como definir parâmetros de retenção de registos para log analytics workspaces](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividades Azure, bem como as definições de diagnóstico para o seu Gateway de Aplicação Azure e enviar os registos para um espaço de trabalho log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos.

Utilize o Monitor Azure para redes para uma visão abrangente da saúde e métricas para todos os recursos de rede implantados, incluindo os seus Gateways de Aplicação Azure.

Opcionalmente, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

* [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/platform/activity-log.md)

* [Como ativar as definições de diagnóstico para O Gateway de Aplicação Azure](./application-gateway-diagnostics.md)

* [Como utilizar o Monitor Azure para redes](../azure-monitor/insights/network-insights-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Implementar Azure Web Application Firewall (WAF) v2 SKU em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS. A WAF baseia-se nas regras do OWASP (Open Web Application Security Project) que define 3.1 (apenas WAF_v2), 3.0 e 2.2.9.

Ativar as definições de diagnóstico do Registo de Atividades Azure, bem como as definições de diagnóstico para o seu Azure WAF e enviar os registos para um espaço de trabalho do Log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos. Pode criar alertas com base nas suas consultas de espaço de trabalho Log Analytics.

Utilize o Monitor Azure para redes para uma visão abrangente da saúde e métricas para todos os recursos de rede implantados, incluindo os seus Gateways de Aplicação Azure. Dentro da consola Azure Monitor for Networks, pode ver e criar alertas para o Azure Application Gateway.

* [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/platform/activity-log.md)

* [Como ativar as definições de diagnóstico para O Gateway de Aplicação Azure](./application-gateway-diagnostics.md)

* [Como utilizar o Monitor Azure para redes](../azure-monitor/insights/network-insights-overview.md)

* [Como criar alertas dentro do Azure](../azure-monitor/learn/tutorial-response.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Implementar a Azure Web Application Firewall (WAF) v2 em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS.

Configure as definições de diagnóstico para as suas implementações do Gateway de aplicação Azure. as definições de diagnóstico são usadas para configurar a exportação de streaming de registos e métricas de plataforma para um recurso para o destino à sua escolha (Contas de Armazenamento, Centros de Eventos e Log Analytics).

* [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Como configurar definições de diagnóstico para Azure WAF](./application-gateway-diagnostics.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; O Azure Application Gateway não processa nem produz registos DNS acessíveis ao utilizador.

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

* [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O acesso do avião ao Azure Application Gateway é controlado através do Azure Ative Directory (AD). A Azure AD não tem o conceito de senhas padrão.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:
- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

* [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../security-center/security-center-identity-access.md)

* [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Utilize um registo de aplicações Azure (principal serviço) para recuperar um token que pode ser usado para interagir com os seus Gateways de Aplicação Azure através de chamadas API.

* [Como chamar Azure REST APIs](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Como registar a sua aplicação ao cliente (principal serviço) com a Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informação da Azure Recovery Services API](/rest/api/recoveryservices/)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar O Azure AD MFA e seguir as recomendações do Azure Security Center Identity and Access Management.

* [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com MFA configurados para iniciar sessão e configurar recursos Azure.

* [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

* [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

* [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AAD) como sistema central de autenticação e autorização. AAD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. AAD também sai, hashes e armazena seguramente credenciais de utilizador.

* [Como criar e configurar um caso AAD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: A Azure AD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

* [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

* [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a fontes de registo de registo de registos de registos, auditorias e eventos de risco Azure, que lhe permitem integrar-se com qualquer ferramenta SIEM/Monitoring.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores do Azure Ative Directory e enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace.

* [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de proteção de identidade Azure AD e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

* [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não aplicável; O Lockbox do cliente não é aplicável ao Gateway de Aplicação Azure.

* [Lista de serviços suportados pelo Cliente Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize tags para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Certifique-se de que todas as implementações da sub-rede Virtual Network Azure Application Gateway têm um grupo de segurança de rede (NSG) aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Embora os grupos de segurança da rede sejam suportados no Gateway de aplicações Azure, existem algumas restrições e requisitos que devem ser cumpridos para que o seu Gateway de Aplicação NSG e Azure funcione como esperado.

* [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

* [Compreender restrições e requisitos em torno da utilização de NSGs com Gateway de aplicação Azure](./configuration-overview.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Certifique-se de que todas as implementações da sub-rede virtual Azure Application Gateway têm um grupo de segurança de rede (NSG) aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Restringir o tráfego de saída a apenas locais fidedignos para ajudar a mitigar a ameaça de exfiltração de dados. Embora os grupos de segurança da rede sejam suportados no Gateway de aplicações Azure, existem algumas restrições e requisitos que devem ser cumpridos para que o seu Gateway de Aplicação NSG e Azure funcione como esperado.

* [Compreender restrições e requisitos em torno da utilização de NSGs com Gateway de aplicação Azure](./configuration-overview.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Configurar encriptação de ponta a ponta com TLS para os seus Gateways de Aplicação Azure.

* [Como configurar tLS de ponta a ponta usando gateway de aplicação Azure](./end-to-end-ssl-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Não aplicável, a Azure Application Gateway não armazena os dados do cliente em repouso.

A Microsoft gere a infraestrutura subjacente ao Azure Application Gateway e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

* [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso ao plano de controlo Azure Application Gateway (o portal Azure).

* [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Não aplicável; A Azure Application Gateway não armazena os dados do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações na produção de instâncias do Gateway de aplicação Azure, bem como outros recursos críticos ou relacionados.

* [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Atualmente não disponível; a avaliação de vulnerabilidade no Azure Security Center ainda não está disponível para o Azure Application Gateway.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Reveja os controlos de segurança disponíveis para o Azure Application Gateway para reduzir as vulnerabilidades relacionadas com a configuração do serviço.

* [Cobertura de recursos (incluindo avaliação de vulnerabilidade) para serviços Azure PaaS](../security-center/features-paas.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Ainda não disponível; a avaliação de vulnerabilidade no Azure Security Center ainda não está disponível para o Azure Application Gateway.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Reveja os controlos de segurança disponíveis para o Azure Application Gateway para reduzir as vulnerabilidades relacionadas com a configuração do serviço.

* [Cobertura de recursos (incluindo avaliação de vulnerabilidade) para serviços Azure PaaS](../security-center/features-paas.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Ainda não disponível; a avaliação de vulnerabilidade no Azure Security Center ainda não está disponível para o Azure Application Gateway.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Reveja os controlos de segurança disponíveis para o Azure Application Gateway para reduzir as vulnerabilidades relacionadas com a configuração do serviço.

* [Cobertura de recursos (incluindo avaliação de vulnerabilidade) para serviços Azure PaaS](../security-center/features-paas.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize a solução automatizada de Deteção de Ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s). Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

* [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

* [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a política da Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e Manter um inventário dos recursos aprovados do Azure

**Orientação**: Definir recursos Azure aprovados.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição.

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

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

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Certifique-se de que todas as implementações da sub-rede Virtual Network Azure Application Gateway têm um grupo de segurança de rede (NSG) aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Embora os grupos de segurança da rede sejam suportados no Gateway de aplicações Azure, existem algumas restrições e requisitos que devem ser cumpridos para que o seu Gateway de Aplicação NSG e Azure funcione como esperado.

* [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

* [Compreender restrições e requisitos em torno da utilização de NSGs com Gateway de aplicação Azure](./configuration-overview.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para definições de rede relacionadas com as suas implementações do Gateway de aplicações Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus Gateways de Aplicações Azure, Redes Virtuais Azure e grupos de segurança de rede. Também pode utilizar a definição de política incorporada.

* [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições de política Azure personalizadas, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

* [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize identidades geridas para fornecer ao seu Gateway de aplicação Azure uma identidade gerida automaticamente no Diretório Ativo Azure (AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

Utilize o Cofre da Chave Azure para armazenar certificados de forma segura. O Azure Key Vault é uma loja secreta gerida pela plataforma que pode usar para salvaguardar segredos, chaves e certificados SSL. O Azure Application Gateway suporta a integração com o Key Vault para certificados de servidor que estão ligados a ouvintes com via HTTPS. Este suporte está limitado ao Gateway de Aplicação v2 SKU.

* [Como configurar a rescisão SSL com certificados Key Vault utilizando a Azure PowerShell](./configure-keyvault-ps.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer ao seu Gateway de aplicação Azure uma identidade gerida automaticamente no Diretório Ativo Azure (AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

Utilize o Cofre da Chave Azure para armazenar certificados de forma segura. O Azure Key Vault é uma loja secreta gerida pela plataforma que pode usar para salvaguardar segredos, chaves e certificados SSL. O Azure Application Gateway suporta a integração com o Key Vault para certificados de servidor que estão ligados a ouvintes com via HTTPS. Este suporte está limitado ao Gateway de Aplicação v2 SKU.

* [Como configurar a rescisão SSL com certificados Key Vault utilizando a Azure PowerShell](./configure-keyvault-ps.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Implementar a Azure Web Application Firewall (WAF) v2 em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS.

Configure as definições de diagnóstico para as suas implementações do Gateway de aplicação Azure. as definições de diagnóstico são usadas para configurar a exportação de streaming de registos e métricas de plataforma para um recurso para o destino à sua escolha (Contas de Armazenamento, Centros de Eventos e Log Analytics).

* [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Como configurar definições de diagnóstico para Azure WAF](./application-gateway-diagnostics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Não aplicável; A Azure Application Gateway não armazena os dados do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Ao utilizar a Firewall de Aplicação Web Azure (WAF), pode configurar as políticas WAF. Uma política da WAF consiste em dois tipos de regras de segurança: regras personalizadas que são da autoria do cliente, e conjuntos de regras geridos que são uma coleção de regras pré-configuradas geridas pela Azure. Os conjuntos de regras geridos pelo Azure fornecem uma forma fácil de implementar proteção contra um conjunto comum de ameaças à segurança. Uma vez que tais regras são geridas pelo Azure, as regras são atualizadas conforme necessário para proteger contra novas assinaturas de ataque.

* [Compreenda os conjuntos de regras waf geridos pelo Azure](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: A Azure Application Gateway não armazena os dados do cliente. No entanto, se utilizar definições de política Azure personalizadas, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

Os Serviços de DevOps do Azure tiram partido das muitas funcionalidades de armazenamento do Azure para garantir a disponibilidade de dados no caso de falhas de hardware, interrupções de serviço ou desastre na região. Adicionalmente, a equipa do Azure DevOps segue procedimentos para proteger dados contra eliminação acidental ou maliciosa.

* [Compreender a disponibilidade de dados em Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: O cliente de backup geriu certificados dentro do Cofre da Chave Azure.

* [Como backup certificados de cofre chave em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Teste de restauração de certificados geridos por clientes com suporte.

* [Como restaurar os certificados de cofre chave](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Certifique-se de que a eliminação suave está ativada para o Cofre da Chave Azure. A eliminação suave permite a recuperação de cofres e objetos de abóbada apagados, tais como chaves, segredos e certificados.

* [Como usar o Soft Delete do Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

* [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

* [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

* [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

* [Como configurar a exportação contínua](../security-center/continuous-export.md)

* [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

* [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação:** 

* [Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)