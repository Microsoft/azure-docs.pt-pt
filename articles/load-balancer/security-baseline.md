---
title: Linha de base de segurança Azure para O Balançador de Carga Azure
description: A linha de base de segurança Azure Load Balancer fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bffc9eb3e75dda2b04ad4118d1f599f85a0013c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590165"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Linha de base de segurança Azure para O Balançador de Carga Azure

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para o Microsoft Azure Load Balancer. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Load Balancer. Foram excluídos **os controlos** não aplicáveis ao Azure Load Balancer.

 
Para ver como o Azure Load Balancer mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de base de segurança Azure Load Balancer](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Utilize os balançadores de carga Azure internos para permitir apenas que o tráfego rebedure recursos de determinadas redes virtuais ou redes virtuais espreguiçadas sem exposição à internet. Implementar um equilibrador de carga externo com rede de origem

Tradução de endereços (SNAT) para mascarar os endereços IP de recursos backend para proteção contra exposição direta à Internet.

A Azure oferece dois tipos de ofertas de Balancer de Carga, Standard e Basic. Utilize o Balanceador de Carga Padrão para todas as cargas de trabalho de produção. Implemente grupos de segurança de rede e apenas permita o acesso às portas fidedignas da sua aplicação e aos intervalos de endereços IP. Nos casos em que não exista um grupo de segurança de rede atribuído à sub-rede de backend ou ao NIC das máquinas virtuais de backend, o tráfego não será permitido a esses recursos a partir do balançador de carga. Com os Balanceadores de Carga Padrão, forneça regras de saída para definir o NAT de saída com um grupo de segurança de rede. Reveja estas regras de saída para afinar o comportamento das suas ligações de saída.

A utilização de um Balancer de Carga Padrão é recomendada para as suas cargas de produção e normalmente o Balanceador de Carga Básica só é utilizado para testes, uma vez que o tipo básico está aberto a ligações a partir da internet por padrão, e não requer grupos de segurança de rede para funcionamento.

- [Ligações de saída no Azure](load-balancer-outbound-connections.md)

- [Upgrade Azure Public Load Balancer](upgrade-basic-standard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: O Balanceador de Carga é um passe através do serviço, uma vez que se baseia nas regras dos grupos de segurança da rede aplicadas para reter recursos e as regras de saída configuradas para controlar o acesso à Internet.

Reveja as regras de saída configuradas para o seu Balancer de Carga Padrão através da lâmina de regras de saída do seu Balancer de Carga e da lâmina de regras de equilíbrio de carga, onde poderá ter as regras implícitas de saída ativadas.

Monitorize a contagem das suas ligações de saída para monitorizar a frequência com que os seus recursos estão a chegar à internet. 

Utilize o Security Center e siga as recomendações de proteção da rede para ajudar a proteger os recursos da rede Azure.

Siga as recomendações de segurança para os seus recursos de backend e permita registos de fluxo de grupos de segurança de rede e envie os registos para uma conta de Armazenamento Azure para auditoria.

Também envie os registos de fluxo para um espaço de trabalho do Log Analytics e, em seguida, use traffic Analytics para fornecer informações sobre os padrões de tráfego na sua nuvem Azure. As vantagens da Traffic Analytics incluem a capacidade de visualizar a atividade da rede, identificar pontos quentes e ameaças de segurança, compreender padrões de fluxo de tráfego e identificar as configurações erradas da rede.

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a segurança da rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Como posso verificar as minhas estatísticas de ligação de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Defina explicitamente a conectividade da Internet e os IPs de origem válidos através de regras de saída e grupos de segurança de rede com o seu Balancer de Carga para utilizar a inteligência de ameaça da Microsoft para proteger as suas aplicações web.

- [Integre a Firewall Azure](../firewall/integrate-lb.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Permitir a proteção padrão de Negação de Serviço Distribuída (DDoS) do Azure na sua Rede Virtual Azure para proteger contra ataques de DDoS. 

Implemente o Azure Firewall em cada um dos limites da rede da organização com filtragem baseada em inteligência de ameaça, ativada e configurada para "alertar e negar" para tráfego de rede malicioso.

 

Utilize a proteção contra ameaças do Security Center para detetar comunicações com endereços IP maliciosos conhecidos. 

O Balanceador de Carga Padrão foi concebido para ser seguro por padrão e parte de uma Rede Virtual privada e isolada. Está fechado a fluxos de entrada, a menos que seja aberto por grupos de segurança de rede para permitir explicitamente o tráfego e para não permitir endereços IP maliciosos conhecidos. A menos que exista um grupo de segurança de rede numa sub-rede ou NIC do seu recurso de máquina virtual por detrás do Balanceador de Carga, o tráfego não é permitido chegar a este recurso. 

Implemente o Azure Firewall em cada um dos limites da rede da organização com filtragem baseada em inteligência de ameaça, ativada e configurada para "alertar e negar" para tráfego de rede malicioso para evitar ataques de endereços IP maliciosos. 

Implemente orientação para integrar o Azure Firewall com o seu Balancer de Carga.

Utilize funcionalidades de proteção contra ameaças do Security Center para detetar comunicações com endereços IP maliciosos conhecidos. 

O Security Center (Standard Tier) fornece acesso a máquinas virtuais just-in-time, e configura os endereços IP de origem permitidos para permitir o acesso apenas a partir de endereços/intervalos IP aprovados.
 

Utilize a funcionalidade de Endurecimento de Rede Adaptativa do Centro de Segurança para recomendar configurações de grupos de segurança de rede que limitem as portas e os IPs de origem com base na inteligência real de tráfego e ameaças.
 

- [Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure](../ddos-protection/manage-ddos-protection.md)

- [Filtragem baseada em ameaças de Azure Firewall](../firewall/threat-intel.md)

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md)

- [Proteger as portas de gestão com acesso just-in-time](../security-center/security-center-just-in-time.md)

- [Endurecimento de rede adaptativa no Centro de Segurança Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Integre a Firewall Azure com o seu Balanceador de Carga](../firewall/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Permitir a captura de pacotes do Observador de Rede para investigar atividades anómalas.

- [Como criar uma instância de Observador de Rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Implementar uma oferta do Mercado Azure que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil para o ambiente do seu Balancer de Carga. 

Utilize a inteligência de ameaça Azure Firewall se a inspeção da carga útil não for um requisito. A filtragem baseada em ameaças de Azure Firewall é usada para alertar e/ou bloquear o tráfego de e/para e de endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft.

Implemente a solução de firewall à sua escolha em cada um dos limites de rede da sua organização para detetar e/ou bloquear tráfego malicioso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com a Azure Firewall](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Defina explicitamente a conectividade da Internet e os IPs de origem válidos através de regras de saída e grupos de segurança de rede com o seu Balancer de Carga para utilizar as funcionalidades de inteligência de ameaça da Microsoft para proteger as suas aplicações web.

- [Integre a Firewall Azure](../firewall/integrate-lb.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço no lugar de endereços IP específicos ao criar regras de segurança. Especifique o nome da etiqueta de serviço no campo de origem ou destino de uma regra para permitir ou negar o tráfego para o serviço correspondente. 

A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. 

Por predefinição, todos os grupos de segurança da rede incluem a etiqueta de serviço AzureLoadBalancer para permitir o tráfego de sondas de saúde. 

Consulte a documentação do Azure para todas as etiquetas de serviço disponíveis para utilização nas regras do grupo de segurança da rede.

- [Etiquetas de serviço disponíveis](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Definir e implementar configurações de segurança padrão para recursos de rede com a Política Azure.

Use plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlos e políticas do Azure RBAC, numa única definição de planta. 

Aplique o plano em novas subscrições e ajuste o controlo e a gestão através da versão.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para networking](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas de recursos para grupos de segurança de rede e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. 

Utilize o campo "Descrição" para documentar as regras que permitem o tráfego de/para uma rede para regras individuais do grupo de segurança da rede.

Implementar qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, como "Exigir etiqueta e seu valor", que garante que todos os recursos são criados com etiquetas e para notificar quaisquer recursos não marcados existentes.

Utilize a Azure PowerShell ou a Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual Azure](../virtual-network/quick-create-portal.md)

- [Como filtrar o tráfego da rede com regras do grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o registo de atividade azul para monitorizar as configurações dos recursos e detetar alterações nos seus recursos Azure. 

Crie alertas no Azure Monitor para o notificar quando os recursos críticos forem alterados.

- [Como visualizar e recuperar eventos de log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Reveja as alterações às regras de saída e aos grupos de segurança da rede para os seus Balanceadores de Carga, visualizando o Registo de Atividades nas suas subscrições. 

Consulte os registos internos do anfitrião para garantir que os seus recursos de backend estão seguros.

Exporte estes registos para Log Analytics ou outra plataforma de armazenamento. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Ativar e embarcar estes dados para a Azure Sentinel ou um SIEM de terceiros com base nos seus requisitos de negócio organizacional.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Registos de atividade da plataforma](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Rever as informações de registo e auditoria do Plano de Controlo e Gestão capturadas com registos de atividade para o Balanceador de Carga Básica. Estas definições de captura são ativadas por padrão. 

Utilize registos de atividades para monitorizar as ações em recursos para visualizar toda a atividade e o seu estado. 

Determinar que operações foram tomadas sobre os recursos da sua subscrição com registos de atividade: 

- que iniciou a operação
- quando a operação ocorreu
- o estado da operação

- os valores de outras propriedades que podem ajudá-lo a pesquisar a operação

Recupere informações do registo de atividade através do Azure PowerShell, da Interface da Linha de Comando Azure (CLI), da API Azure REST ou do portal Azure. 

Implementar diagnóstico multidimensional para as configurações do Balanceador de Carga Padrão com O Monitor Azure.  Estas incluem métricas disponíveis para segurança incluem informações sobre ligações de tradução de endereços de rede de origem (SNAT), portas. Estão também disponíveis métricas adicionais em pacotes SYN (sincronizar) e balcões de pacotes. 

Recupere as métricas multidimensionais programáticamente através de APIs e escreva-as numa conta de armazenamento através da opção 'Todas as Métricas'.

Utilize o pacote de conteúdos Azure Audit Logs com o Microsoft Power BI para analisar os seus dados com dashboards pré-configurados ou para personalizar as opiniões com base nos seus requisitos.

Faça o streaming de registos para um centro de eventos ou um espaço de trabalho log analytics. Também podem ser extraídos do armazenamento de blob Azure, e vistos em diferentes ferramentas, tais como Excel e Power BI. 

Ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros com base nos requisitos do seu negócio.

- [Reveja este artigo com instruções passo a passo para cada método detalhado nas operações de Auditoria com o Gestor de Recursos](../azure-resource-manager/management/view-activity-logs.md)

- [Registos do Azure Monitor para o Balanceador de Carga Básico público](load-balancer-monitor-log.md)

- [Ver registos de atividades para monitorizar ações em recursos](../azure-resource-manager/management/view-activity-logs.md)

- [Recuperar métricas multidimensionais programáticamente através de APIs](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: O registo de atividades é ativado por padrão e é preservado durante 90 dias na loja de Registos de Eventos da Azure. Desconfie do período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização no Azure Monitor. Utilize as contas de armazenamento Azure para armazenamento a longo prazo e arquivo.

- [Ver registos de atividades para monitorizar ações no artigo de recursos](../azure-resource-manager/management/view-activity-logs.md)

- [Alterar o período de retenção de dados em Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Monitorize, gere e resolva os recursos do Balanceador de Carga Padrão utilizando a página do Balanceador de Carga no portal Azure e na página de Saúde de Recursos no Monitor Azure. As métricas disponíveis para a segurança incluem informações sobre ligações de tradução de endereços de rede de origem (SNAT), portas. Além disso, também estão disponíveis métricas em pacotes SYN (sincronizar) e balcões de pacotes. 

Utilize o Azure Monitor para rever o estado da sonda de saúde de ponta com métricas multidimensionais para balanceadores de carga standard, externos e internos. Reúna estas métricas programáticamente através de APIs e escritas para uma conta de armazenamento através da opção 'Todas as Métricas'.

Os registos do Monitor Azure não estão disponíveis para os balançadores de carga básico internos. 

Utilize o Monitor Azure para ver o estado da sonda de saúde resumido por pool de backend para o Balanceador de Carga Externa Básico, como, por exemplo, o número de casos no seu pool de backend que não recebem pedidos do Balanceador de Carga devido a falhas na sonda de saúde. 

Implementar ADRagem com Insights Operacionais do Azure para fornecer estatísticas sobre o estado de saúde do equilibrador de carga. 

Utilize os Registos de Atividade para visualizar alertas e monitorizar as ações sobre os recursos e o seu estado nas suas subscrições Azure. Os registos de atividade são ativados por padrão e podem ser vistos no portal Azure. 

Utilize o Microsoft Power BI com o pacote de conteúdos de registos de auditoria Azure e analise os seus dados com dashboards pré-configurados. Personalize vistas de acordo com os seus requisitos com base nas necessidades do negócio. 

Faça o streaming de registos para um centro de eventos ou um espaço de trabalho log analytics. Também podem ser extraídos do armazenamento de blob Azure, e vistos em diferentes ferramentas, tais como Excel e Power BI. Pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Sondas de estado de funcionamento do Balanceador de Carga](load-balancer-custom-probe-overview.md)

- [API REST do Azure Monitor](/rest/api/monitor)

- [Como recuperar métricas através da REST API](/rest/api/monitor/metrics/list)

- [Diagnósticos padrão do Balancer de Carga com métricas, alertas e saúde de recursos](load-balancer-standard-diagnostics.md)

- [Registos do Azure Monitor para o Balanceador de Carga Básico público](load-balancer-monitor-log.md)

- [Veja as suas métricas de balançador de carga no portal Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Centro de Segurança com log analytics espaço de trabalho para monitorizar e alertar sobre a atividade anómala relacionada com o Balancer de Carga em registos e eventos de segurança.

Ativar e a bordo dados para Azure Sentinel ou uma ferramenta SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O controlo de acesso baseado em funções (Azure RBAC) permite-lhe gerir o acesso a recursos Azure, como o seu Balancer de Carga através de atribuições de funções. Atribua estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas.

Inventário Papéis pré-definidos e incorporados para determinados recursos com ferramentas como Azure CLI, Azure PowerShell ou o portal Azure.

- [Como obter um papel de diretório no Azure Ative Directory (Azure AD) com a PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Permitir a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações de Gestão de Identidade e Acesso do Centro de Segurança.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multifactor configurada para gerir e aceder aos recursos da rede Azure. 

- [Saiba mais sobre estações de trabalho de acesso privilegiado](/security/compass/privileged-access-devices)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gerir os recursos do Azure apenas a partir de locais aprovados

**Orientação**: Utilize locais nomeados para acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como um sistema central de autenticação e autorização para os seus serviços. O Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito e também sais, hashes e armazena de forma segura as credenciais dos utilizadores.  

- [Como criar e configurar instâncias do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Utilize o Azure Ative Directory (Azure AD) para fornecer registos para ajudar a descobrir contas velhas. 

As avaliações de acesso à identidade do Azure podem ser realizadas para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente para garantir que apenas os utilizadores ativos tenham acesso continuado.

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring/)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Integre o Azure Ative Directory (Azure AD) Fontes de registo de registo de eventos de auditoria e risco, com qualquer ferramenta SIEM ou Monitoring baseada no seu acesso.

Dinamize este processo criando Definições de Diagnóstico para contas de utilizadores Azure E envie os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Quaisquer alertas desejados podem ser configurados no Log Analytics Workspace.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize as funcionalidades de Proteção de Riscos e Identidade da Azure (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores. Ingerir dados no Azure Sentinel para mais investigações.

- [Como ver os inícios de sessão de risco do Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Certifique-se de que qualquer cliente que se conecte aos seus recursos Azure seja capaz de negociar TLS 1.2 ou superior.

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, sempre que aplicável.

- [Compreender a encriptação em trânsito com Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Utilize o Azure RBAC para controlar o acesso aos seus recursos do Balancer de Carga.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Balanceador de Carga é um serviço que não armazena os dados do cliente. É uma parte da plataforma subjacente que é gerida pela Microsoft. 

A Microsoft trata todos os conteúdos do cliente como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados do cliente. 

Para garantir que os dados dos clientes no Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados. 

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade Azure para criar alertas quando ocorrerem alterações para recursos críticos do Azure, tais como balanceadores de carga utilizados para importantes cargas de trabalho de produção.

- [Como criar alertas para eventos de registo de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (tais como computação, armazenamento, rede, portas, protocolos, e assim por diante) nas suas subscrições. Recomenda-se a criação e utilização de recursos atuais para o Azure Resource Manager.

Certifique-se de permissões (ler) adequadas no seu inquilino e enumera todas as subscrições e recursos da Azure nas suas subscrições.

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas nos recursos Azure com metadados para organizar logicamente de acordo com uma taxonomia.

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. 

Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados das suas subscrições em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Crie uma lista de recursos Azure aprovados de acordo com as suas necessidades organizacionais que pode alavancar como mecanismo de admissão. Isto permitirá que a sua organização entre a bordo de quaisquer serviços Azure recém-disponíveis depois de serem formalmente revistos e aprovados pelos processos típicos de avaliação de segurança da sua organização.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições.

Consultar e descobrir recursos com o Azure Resource Graph dentro de subscrições próprias. 

Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Amostra de política Azure incorporada para rede virtual](/azure/virtual-network/policy-samples)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Azure Ative Directy (Azure AD) Acesso Condicional para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: O software necessário para operações comerciais, mas que pode incorrer em maior risco para a organização, deve ser isolado dentro da sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um Azure Firewall ou um grupo de segurança de rede.

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

- [Como criar um grupo de segurança de rede com um config de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração dos seus recursos Azure. Utilize definições de Política Azure incorporadas.

O Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem os requisitos de segurança para a sua organização.

Exporte os modelos do Gestor de Recursos Azure em formatos JavaScript Object Notation (JSON) e reveja-os periodicamente para garantir que as configurações cumprem os seus requisitos de segurança organizacional.

Implemente recomendações do Security Center como uma base de configuração segura para os seus recursos Azure.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.  Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como definições personalizadas de Política Azure, modelos de Gestor de Recursos Azure e scripts de configuração de estado desejados.

Conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se estiver integrado com Azure DevOps, ou em Azure AD se estiver integrado com TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Definir e implementar configurações de segurança padrão para recursos Azure utilizando a Política Azure.  Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos Azure. Implemente definições de política incorporadas relacionadas com os seus recursos específicos do Azure Load Balancer.  Além disso, utilize a Azure Automation para implementar alterações de configuração. aos seus recursos Azure Load Balancer.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar pseudónimos](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Centro de Segurança para realizar análises de base para a sua Política de Recursos Azure e Azure para alertar e auditar configurações de recursos.

- [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

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

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. 

A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Marque subscrições usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis.  

É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e, em seguida, reveja o seu plano de resposta conforme necessário. 

- [Publicação do NIST--Guia para Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Seu Centro de Segurança utilizando a funcionalidade de exportação contínua para ajudar a identificar riscos para os recursos da Azure. 

Utilize uma funcionalidade de exportação contínua no Centro de Segurança que lhe permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. 

Utilize o conector de dados do Centro de Segurança para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança para acionar automaticamente respostas a alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho em Segurança](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
