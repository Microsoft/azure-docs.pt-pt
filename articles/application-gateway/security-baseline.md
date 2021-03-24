---
title: Linha de base de segurança Azure para Gateway de Aplicação
description: A linha de base de segurança Application Gateway fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: application-gateway
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4fc26d8e154dc0a58bb3436a8161d2e21efa2b86
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952019"
---
# <a name="azure-security-baseline-for-application-gateway"></a>Linha de base de segurança Azure para Gateway de Aplicação

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../security/benchmarks/overview-v1.md) para o Gateway de Aplicação. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Gateway de Aplicação. Foram excluídos **os controlos** não aplicáveis ao Gateway de Aplicação.

 
Para ver como o Application Gateway mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança de segurança da Aplicação Gateway](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Certifique-se de que todas as implementações da sub-rede virtual Azure Application Gateway têm um grupo de segurança de rede (NSG) aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Embora os grupos de segurança da rede sejam suportados no Gateway de aplicações Azure, existem algumas restrições e requisitos que devem ser cumpridos para que o seu Gateway de Aplicação NSG e Azure funcione como esperado.

- [Compreender restrições e requisitos em torno da utilização de NSGs com Gateway de aplicação Azure](configuration-overview.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Para os grupos de segurança de rede (NSGs) associados às suas sub-redes Azure Application Gateway, ativar registos de fluxo NSG e enviar registos numa Conta de Armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Nota: Existem alguns casos em que os registos de fluxo NSG associados às suas sub-redes Azure Application Gateway não mostram tráfego que tenha sido permitido. Se a sua configuração corresponder ao seguinte cenário, não verá tráfego permitido nos seus registos de fluxo NSG:

- Você implantou o Application Gateway v2
- Você tem um NSG na sub-rede de gateway de aplicação
- Você ativou registos de fluxo NSG em que NSG

Para mais informações, consulte as referências abaixo.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

- [FAQ para diagnóstico e registo para gateway de aplicação Azure](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Implementar firewall de aplicação web Azure (WAF) em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS. A WAF baseia-se nas regras do OWASP (Open Web Application Security Project) que define 3.1 (apenas WAF_v2), 3.0 e 2.2.9. 

- [Compreenda as funcionalidades do Gateway de Aplicações Azure](features.md)

- [Compreenda Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative a proteção DDoS Standard nas suas Redes Virtuais Azure associadas às suas instâncias de produção do Azure Application Gateway para proteger contra ataques DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP maliciosos conhecidos.

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Para os grupos de segurança de rede (NSGs) associados às suas sub-redes Azure Application Gateway, ativar registos de fluxo NSG e enviar registos numa Conta de Armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Nota: Existem alguns casos em que os registos de fluxo NSG associados às suas sub-redes Azure Application Gateway não mostram tráfego que tenha sido permitido. Se a sua configuração corresponder ao seguinte cenário, não verá tráfego permitido nos seus registos de fluxo NSG:

- Você implantou o Application Gateway v2
- Você tem um NSG na sub-rede de gateway de aplicação
- Você ativou registos de fluxo NSG em que NSG

Para mais informações, consulte as referências abaixo.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

- [FAQ para diagnóstico e registo para gateway de aplicação Azure](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Implementar firewall de aplicação web Azure (WAF) em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS. A WAF baseia-se nas regras do OWASP (Open Web Application Security Project) que define 3.1 (apenas WAF_v2), 3.0 e 2.2.9. 

Em alternativa, existem várias opções de mercado como o Barracuda WAF para Azure que estão disponíveis no Azure Marketplace que inclui funcionalidades IDS/IPS.

- [Compreenda as funcionalidades do Gateway de Aplicações Azure](features.md)

- [Compreenda Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Compreender o Serviço de Nuvem WAF Barracuda](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Implementar gateway de aplicações Azure para aplicações web com HTTPS/SSL habilitado para certificados fidedignos.

- [Como implementar o Gateway de Aplicações](quick-create-portal.md)

- [Como configurar o Gateway de aplicações para utilizar HTTPS](create-ssl-portal.md)

- [Compreenda o equilíbrio de carga da camada 7 com os gateways de aplicações web Azure](overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em Grupos de Segurança de Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, GatewayManager) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Para os grupos de segurança de rede (NSGs) associados às suas sub-redes Azure Application Gateway, deve permitir a entrada de tráfego de Internet nas portas TCP 65503-65534 para o Gateway de Aplicação v1 SKU e portas TCP 65200-65535 para o V2 SKU com a sub-rede de destino como Qualquer e fonte como tag de serviço GatewayManager. Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) pelos certificados Azure. Entidades externas, incluindo os clientes desses gateways, não podem comunicar nestes pontos finais.

- [Compreender e utilizar tags de serviço](../virtual-network/service-tags-overview.md)

- [Visão geral da configuração do Gateway de aplicação Azure](configuration-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para definições de rede relacionadas com as suas implementações do Gateway de aplicações Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus Gateways de Aplicações Azure, Redes Virtuais Azure e grupos de segurança de rede. Também pode utilizar a definição de política incorporada.

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em funções Azure (Azure RBAC), e políticas numa única definição de planta. Pode aplicar facilmente o projeto a novas subscrições, ambientes e controlo e gestão de afinação através da versão.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize Tags para grupos de segurança de rede (NSGs) associados à sua sub-rede Azure Application Gateway, bem como quaisquer outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de política incorporadas do Azure relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas Tags.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para as definições de rede e recursos relacionados com as suas implementações do Gateway de Aplicações Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nas definições ou recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Para controlar a verificação de dados, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure. Utilizando dados do Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para o seu Gateway de Aplicação Azure e recursos relacionados, como grupos de segurança de rede (NSGs), sendo usados para proteger a sub-rede Azure Application Gateway.

Além dos Registos de Atividade, pode configurar definições de diagnóstico para as suas implementações do Gateway de Aplicação Azure. as definições de diagnóstico são usadas para configurar a exportação de streaming de registos e métricas de plataforma para um recurso para o destino à sua escolha (Contas de Armazenamento, Centros de Eventos e Log Analytics).

O Azure Application Gateway também oferece integração incorporada com a Azure Application Insights. O Application Insights recolhe dados de registo, desempenho e erro. O Application Insights deteta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para o ajudar a diagnosticar problemas e a compreender como as suas aplicações web estão a ser utilizadas. Pode permitir a exportação contínua para a telemetria de exportação da Application Insights para um local centralizado para manter os dados por mais tempo do que o período de retenção padrão.

- [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/essentials/activity-log.md)

- [Como ativar as definições de diagnóstico para O Gateway de Aplicação Azure](application-gateway-diagnostics.md)

- [Como ativar insights de aplicações](../azure-monitor/app/app-insights-overview.md)

- [Como configurar a exportação contínua](../azure-monitor/app/export-telemetry.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Para controlar a verificação de dados, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure. Utilizando dados do Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para o seu Gateway de Aplicação Azure e recursos relacionados, como grupos de segurança de rede (NSGs), sendo usados para proteger a sub-rede Azure Application Gateway.

Além dos Registos de Atividade, pode configurar definições de diagnóstico para as suas implementações do Gateway de Aplicação Azure. as definições de diagnóstico são usadas para configurar a exportação de streaming de registos e métricas de plataforma para um recurso para o destino à sua escolha (Contas de Armazenamento, Centros de Eventos e Log Analytics).

O Azure Application Gateway também oferece integração incorporada com a Azure Application Insights. O Application Insights recolhe dados de registo, desempenho e erro. O Application Insights deteta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para o ajudar a diagnosticar problemas e a compreender como as suas aplicações web estão a ser utilizadas. Pode permitir a exportação contínua para a telemetria de exportação da Application Insights para um local centralizado para manter os dados por mais tempo do que o período de retenção padrão.

- [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/essentials/activity-log.md)

- [Como ativar as definições de diagnóstico para O Gateway de Aplicação Azure](application-gateway-diagnostics.md)

- [Como ativar insights de aplicações](../azure-monitor/app/app-insights-overview.md)

- [Como configurar a exportação contínua](../azure-monitor/app/export-telemetry.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desa um ponto final no período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Como definir parâmetros de retenção de registos para log analytics workspaces](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividades Azure, bem como as definições de diagnóstico para o seu Gateway de Aplicação Azure e enviar os registos para um espaço de trabalho log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos.

Utilize o Monitor Azure para redes para uma visão abrangente da saúde e métricas para todos os recursos de rede implantados, incluindo os seus Gateways de Aplicação Azure. 

Opcionalmente, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/essentials/activity-log.md)

- [Como ativar as definições de diagnóstico para O Gateway de Aplicação Azure](application-gateway-diagnostics.md)

- [Como utilizar o Monitor Azure para redes](../azure-monitor/insights/network-insights-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Implementar Azure Web Application Firewall (WAF) v2 SKU em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS. A WAF baseia-se nas regras do OWASP (Open Web Application Security Project) que define 3.1 (apenas WAF_v2), 3.0 e 2.2.9. 

Ativar as definições de diagnóstico do Registo de Atividades Azure, bem como as definições de diagnóstico para o seu Azure WAF e enviar os registos para um espaço de trabalho do Log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos.  Pode criar alertas com base nas suas consultas de espaço de trabalho Log Analytics.

Utilize o Monitor Azure para redes para uma visão abrangente da saúde e métricas para todos os recursos de rede implantados, incluindo os seus Gateways de Aplicação Azure. Dentro da consola Azure Monitor for Networks, pode ver e criar alertas para o Azure Application Gateway.

- [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/essentials/activity-log.md)

- [Como ativar as definições de diagnóstico para O Gateway de Aplicação Azure](application-gateway-diagnostics.md)

- [Como utilizar o Monitor Azure para redes](../azure-monitor/insights/network-insights-overview.md)

- [Como criar alertas dentro do Azure](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Implementar a Azure Web Application Firewall (WAF) v2 em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS.

Configure as definições de diagnóstico para as suas implementações do Gateway de aplicação Azure. as definições de diagnóstico são usadas para configurar a exportação de streaming de registos e métricas de plataforma para um recurso para o destino à sua escolha (Contas de Armazenamento, Centros de Eventos e Log Analytics).

- [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Como configurar definições de diagnóstico para Azure WAF](application-gateway-diagnostics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O acesso do avião ao Azure Application Gateway é controlado através do Azure Ative Directory (Azure AD). A Azure AD não tem o conceito de senhas padrão.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Para mais informações, consulte as referências abaixo.

- [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Utilize um registo de aplicações Azure (principal serviço) para recuperar um token que pode ser usado para interagir com os seus Gateways de Aplicação Azure através de chamadas API.

- [Como chamar Azure REST APIs](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registar a sua aplicação ao cliente (principal serviço) com o Azure Ative Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informação da Azure Recovery Services API](/rest/api/recoveryservices/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com autenticação multifactor configurada para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory (Azure AD) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

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

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a Fontes de registo de registo de eventos de inscrição, de auditoria e de eventos de risco da Azure Ative, que lhe permitem integrar-se com qualquer ferramenta SIEM/Monitoring.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Proteção de Identidade e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize tags para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis. 

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Certifique-se de que todas as implementações da sub-rede Virtual Network Azure Application Gateway têm um grupo de segurança de rede (NSG) aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Embora os grupos de segurança da rede sejam suportados no Gateway de aplicações Azure, existem algumas restrições e requisitos que devem ser cumpridos para que o seu Gateway de Aplicação NSG e Azure funcione como esperado.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Compreender restrições e requisitos em torno da utilização de NSGs com Gateway de aplicação Azure](configuration-overview.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Certifique-se de que todas as implementações da sub-rede virtual Azure Application Gateway têm um grupo de segurança de rede (NSG) aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Restringir o tráfego de saída a apenas locais fidedignos para ajudar a mitigar a ameaça de exfiltração de dados. Embora os grupos de segurança da rede sejam suportados no Gateway de aplicações Azure, existem algumas restrições e requisitos que devem ser cumpridos para que o seu Gateway de Aplicação NSG e Azure funcione como esperado.

- [Compreender restrições e requisitos em torno da utilização de NSGs com Gateway de aplicação Azure](configuration-overview.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Configurar encriptação de ponta a ponta com TLS para os seus Gateways de Aplicação Azure.

- [Como configurar tLS de ponta a ponta usando gateway de aplicação Azure](end-to-end-ssl-portal.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso ao plano de controlo Azure Application Gateway (o portal Azure).

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações na produção de instâncias do Gateway de aplicação Azure, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Atualmente não disponível; a avaliação de vulnerabilidade no Azure Security Center ainda não está disponível para o Azure Application Gateway.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Reveja os controlos de segurança disponíveis para o Azure Application Gateway para reduzir as vulnerabilidades relacionadas com a configuração.

- [Cobertura de recursos (incluindo avaliação de vulnerabilidade) para serviços Azure PaaS](../security-center/features-paas.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Ainda não disponível; a avaliação de vulnerabilidade no Azure Security Center ainda não está disponível para o Azure Application Gateway.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Reveja os controlos de segurança disponíveis para o Azure Application Gateway para reduzir as vulnerabilidades relacionadas com a configuração.

- [Cobertura de recursos (incluindo avaliação de vulnerabilidade) para serviços Azure PaaS](../security-center/features-paas.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Ainda não disponível; a avaliação de vulnerabilidade no Azure Security Center ainda não está disponível para o Azure Application Gateway.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Reveja os controlos de segurança disponíveis para o Azure Application Gateway para reduzir as vulnerabilidades relacionadas com a configuração.

- [Cobertura de recursos (incluindo avaliação de vulnerabilidade) para serviços Azure PaaS](../security-center/features-paas.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s). Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

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

Para mais informações, consulte as referências abaixo.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

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

Para mais informações, consulte as referências abaixo.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Certifique-se de que todas as implementações da sub-rede Virtual Network Azure Application Gateway têm um grupo de segurança de rede (NSG) aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Embora os grupos de segurança da rede sejam suportados no Gateway de aplicações Azure, existem algumas restrições e requisitos que devem ser cumpridos para que o seu Gateway de Aplicação NSG e Azure funcione como esperado.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Compreender restrições e requisitos em torno da utilização de NSGs com Gateway de aplicação Azure](configuration-overview.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para definições de rede relacionadas com as suas implementações do Gateway de aplicações Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus Gateways de Aplicações Azure, Redes Virtuais Azure e grupos de segurança de rede. Também pode utilizar a definição de política incorporada.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

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

**Orientação**: Se utilizar definições de política Azure personalizadas, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize identidades geridas para fornecer ao seu Gateway de aplicação Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

Utilize o Cofre da Chave Azure para armazenar certificados de forma segura. O Azure Key Vault é uma loja secreta gerida pela plataforma que pode usar para salvaguardar segredos, chaves e certificados SSL. O Azure Application Gateway suporta a integração com o Key Vault para certificados de servidor que estão ligados a ouvintes com via HTTPS. Este suporte está limitado ao Gateway de Aplicação v2 SKU.

- [Como configurar a rescisão SSL com certificados Key Vault utilizando a Azure PowerShell](configure-keyvault-ps.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer ao seu Gateway de aplicação Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

Utilize o Cofre da Chave Azure para armazenar certificados de forma segura. O Azure Key Vault é uma loja secreta gerida pela plataforma que pode usar para salvaguardar segredos, chaves e certificados SSL. O Azure Application Gateway suporta a integração com o Key Vault para certificados de servidor que estão ligados a ouvintes com via HTTPS. Este suporte está limitado ao Gateway de Aplicação v2 SKU.

- [Como configurar a rescisão SSL com certificados Key Vault utilizando a Azure PowerShell](configure-keyvault-ps.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação**: Implementar a Azure Web Application Firewall (WAF) v2 em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Web Application Firewall (WAF) é um serviço (característica do Azure Application Gateway) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger as suas aplicações web do Azure App Service inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting cross-site, uploads de malware e ataques DDoS.

Configure as definições de diagnóstico para as suas implementações do Gateway de aplicação Azure. as definições de diagnóstico são usadas para configurar a exportação de streaming de registos e métricas de plataforma para um recurso para o destino à sua escolha (Contas de Armazenamento, Centros de Eventos e Log Analytics).

- [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Como configurar definições de diagnóstico para Azure WAF](application-gateway-diagnostics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Ao utilizar a Firewall de Aplicação Web Azure (WAF), pode configurar as políticas WAF. Uma política da WAF consiste em dois tipos de regras de segurança: regras personalizadas que são da autoria do cliente, e conjuntos de regras geridos que são uma coleção de regras pré-configuradas geridas pela Azure. Os conjuntos de regras geridos pelo Azure fornecem uma forma fácil de implementar proteção contra um conjunto comum de ameaças à segurança. Uma vez que tais regras são geridas pelo Azure, as regras são atualizadas conforme necessário para proteger contra novas assinaturas de ataque.

- [Compreenda os conjuntos de regras waf geridos pelo Azure](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: A Azure Application Gateway não armazena os dados do cliente. No entanto, se utilizar definições de política Azure personalizadas, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

Os Serviços de DevOps do Azure tiram partido das muitas funcionalidades de armazenamento do Azure para garantir a disponibilidade de dados no caso de falhas de hardware, interrupções de serviço ou desastre na região. Adicionalmente, a equipa do Azure DevOps segue procedimentos para proteger dados contra eliminação acidental ou maliciosa.

- [Compreender a disponibilidade de dados em Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Faça o reforço dos certificados geridos pelo cliente dentro do Cofre da Chave Azure.

- [Como backup certificados de cofre chave em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Teste de restauração de certificados geridos pelo cliente.

- [Como restaurar os certificados de cofre chave](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Certifique-se de que a eliminação suave está ativada para o Cofre da Chave Azure. A eliminação suave permite a recuperação de cofres e objetos de abóbada apagados, tais como chaves, segredos e certificados.

- [Como usar o Soft Delete do Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

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

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
