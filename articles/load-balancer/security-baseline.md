---
title: Linha de base de segurança Azure para O Balançador de Carga Azure
description: A linha de base de segurança Azure Load Balancer fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 07dd369ceeefee85f4d9180a0b9b33a0d4c8bbdb
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698211"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Linha de base de segurança Azure para O Balançador de Carga Azure

A Linha de Base de Segurança Azure para o Microsoft Azure Load Balancer contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação. A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas. Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Utilize os balançadores de carga Azure internos para permitir apenas que o tráfego rebedure recursos de determinadas redes virtuais ou redes virtuais espreguiçadas sem exposição à internet. Implementar um Balanceador de Carga externo com tradução de endereços de rede de origem (SNAT) para mascarar os endereços IP dos recursos backend para proteção contra a exposição direta à Internet.

A Azure oferece dois tipos de ofertas de Balancer de Carga, Standard e Basic. Utilize o Balanceador de Carga Padrão para todas as cargas de trabalho de produção. Implemente grupos de segurança de rede e apenas permita o acesso às portas fidedignas da sua aplicação e aos intervalos de endereços IP. Nos casos em que não exista um grupo de segurança de rede atribuído à sub-rede de backend ou ao NIC das máquinas virtuais de backend, o tráfego não será permitido a esses recursos a partir do balançador de carga. Com os Balanceadores de Carga Padrão, forneça regras de saída para definir o NAT de saída com um grupo de segurança de rede. Reveja estas regras de saída para afinar o comportamento das suas ligações de saída. 

A utilização de um Balancer de Carga Padrão é recomendada para as suas cargas de produção e normalmente o Balanceador de Carga Básica só é utilizado para testes, uma vez que o tipo básico está aberto a ligações a partir da internet por padrão, e não requer grupos de segurança de rede para funcionamento. 

- [Ligações de saída no Azure](load-balancer-outbound-connections.md)

- [Upgrade Azure Public Load Balancer](./upgrade-basic-standard.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: O Balanceador de Carga é um passe através do serviço, uma vez que se baseia nas regras dos grupos de segurança da rede aplicadas para reter recursos e as regras de saída configuradas para controlar o acesso à Internet.

Reveja as regras de saída configuradas para o seu Balancer de Carga Padrão através da lâmina de regras de saída do seu balanceador de carga e da lâmina de regras de equilíbrio de carga, onde poderá ter as regras implícitas de saída ativadas.

Monitorize a contagem das suas ligações de saída para monitorizar a frequência com que os seus recursos estão a chegar à internet. 

Utilize o Security Center e siga as recomendações de proteção da rede para ajudar a proteger os recursos da rede Azure.

Siga as recomendações de segurança para os seus recursos de backend e permita registos de fluxo de grupos de segurança de rede e envie os registos para uma conta de Armazenamento Azure para auditoria.

Também envie os registos de fluxo para um espaço de trabalho do Log Analytics e, em seguida, use traffic Analytics para fornecer informações sobre os padrões de tráfego na sua nuvem Azure. As vantagens da Traffic Analytics incluem a capacidade de visualizar a atividade da rede, identificar pontos quentes e ameaças de segurança, compreender padrões de fluxo de tráfego e identificar as configurações erradas da rede.

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a segurança da rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Como posso verificar as minhas estatísticas de ligação de saída](./load-balancer-standard-diagnostics.md#how-do-i-check-my-outbound-connection-statistics)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Defina explicitamente a conectividade da Internet e os IPs de origem válidos através de regras de saída e grupos de segurança de rede com o seu Balancer de Carga para utilizar a inteligência de ameaça da Microsoft para proteger as suas aplicações web.

- [Integre a Firewall Azure](../firewall/integrate-lb.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

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

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Permitir a captura de pacotes do Observador de Rede para investigar atividades anómalas.

- [Como criar uma instância de Observador de Rede](../network-watcher/network-watcher-create.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Implementar uma oferta do Mercado Azure que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil para o ambiente do seu Balancer de Carga. 

Utilize a inteligência de ameaça Azure Firewall Se a inspeção da carga útil não for um requisito. A filtragem baseada em ameaças de Azure Firewall é usada para alertar e/ou bloquear o tráfego de e/para e de endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft.

Implemente a solução de firewall à sua escolha em cada um dos limites de rede da sua organização para detetar e/ou bloquear tráfego malicioso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com a Azure Firewall](../firewall/threat-intel.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Defina explicitamente a conectividade da Internet e os IPs de origem válidos através de regras de saída e grupos de segurança de rede com o seu Balancer de Carga para utilizar as funcionalidades de inteligência de ameaça da Microsoft para proteger as suas aplicações web.

- [Integre a Firewall Azure](../firewall/integrate-lb.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço no lugar de endereços IP específicos ao criar regras de segurança. Especifique o nome da etiqueta de serviço no campo de origem ou destino de uma regra para permitir ou negar o tráfego para o serviço correspondente. 

A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. 

Por predefinição, todos os grupos de segurança da rede incluem a etiqueta de serviço AzureLoadBalancer para permitir o tráfego de sondas de saúde. 

Consulte a documentação do Azure para todas as etiquetas de serviço disponíveis para utilização nas regras do grupo de segurança da rede.

- [Etiquetas de serviço disponíveis](../virtual-network/service-tags-overview.md#available-service-tags)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Definir e implementar configurações de segurança padrão para recursos de rede com a Política Azure.

Use plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlos e políticas do Azure RBAC, numa única definição de planta. 

Aplique o plano em novas subscrições e ajuste o controlo e a gestão através da versão.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas de recursos para grupos de segurança de rede e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. 

Utilize o campo "Descrição" para documentar as regras que permitem o tráfego de/para uma rede para regras individuais do grupo de segurança da rede.

Implementar qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, como "Exigir etiqueta e seu valor", que garante que todos os recursos são criados com etiquetas e para notificar quaisquer recursos não marcados existentes.

Utilize a Azure PowerShell ou a Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual Azure](../virtual-network/quick-create-portal.md)

- [Como filtrar o tráfego da rede com regras do grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o registo de atividade azul para monitorizar as configurações dos recursos e detetar alterações nos seus recursos Azure. 

Crie alertas no Azure Monitor para o notificar quando os recursos críticos forem alterados.

- [Como visualizar e recuperar eventos de log de atividades do Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Reveja as alterações às regras de saída e aos grupos de segurança da rede para os seus Balanceadores de Carga, visualizando o Registo de Atividades nas suas subscrições. 

Consulte os registos internos do anfitrião para garantir que os seus recursos de backend estão seguros.

Exporte estes registos para Log Analytics ou outra plataforma de armazenamento. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Ativar e embarcar estes dados para a Azure Sentinel ou um SIEM de terceiros com base nos seus requisitos de negócio organizacional.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Registos de atividade da plataforma](../azure-monitor/platform/activity-log.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

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

- [Registos do Azure Monitor para o Balanceador de Carga Básico público](./load-balancer-monitor-log.md)

- [Ver registos de atividades para monitorizar ações em recursos](../azure-resource-manager/management/view-activity-logs.md)

- [Recuperar métricas multidimensionais programáticamente através de APIs](./load-balancer-standard-diagnostics.md#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: O registo de atividades é ativado por padrão e é preservado durante 90 dias na loja de Registos de Eventos da Azure. Desconfie do período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização no Azure Monitor. Utilize as contas de armazenamento Azure para armazenamento a longo prazo e arquivo.

- [Ver registos de atividades para monitorizar ações no artigo de recursos](../azure-resource-manager/management/view-activity-logs.md)

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Monitorize, gere e resolva os recursos do Balanceador de Carga Padrão utilizando a página do Balanceador de Carga no portal Azure e na página de Saúde de Recursos no Monitor Azure. As métricas disponíveis para a segurança incluem informações sobre ligações de tradução de endereços de rede de origem (SNAT), portas. Além disso, também estão disponíveis métricas em pacotes SYN (sincronizar) e balcões de pacotes. 

Utilize o Azure Monitor para rever o estado da sonda de saúde de ponta com métricas multidimensionais para balanceadores de carga standard, externos e internos. Reúna estas métricas programáticamente através de APIs e escritas para uma conta de armazenamento através da opção 'Todas as Métricas'.

Os registos do Monitor Azure não estão disponíveis para os balançadores de carga básico internos. 

Utilize o Monitor Azure para ver o estado da sonda de saúde resumido por pool de backend para o Balanceador de Carga Externa Básico, como, por exemplo, o número de casos no seu pool de backend que não recebem pedidos do Balanceador de Carga devido a falhas na sonda de saúde. 

Implementar ADRagem com Insights Operacionais do Azure para fornecer estatísticas sobre o estado de saúde do equilibrador de carga. 

Utilize os Registos de Atividade para visualizar alertas e monitorizar as ações sobre os recursos e o seu estado nas suas subscrições Azure. Os registos de atividade são ativados por padrão e podem ser vistos no portal Azure. 

Utilize o Microsoft Power BI com o pacote de conteúdos de registos de auditoria Azure e analise os seus dados com dashboards pré-configurados. Personalize vistas de acordo com os seus requisitos com base nas necessidades do negócio. 

Faça o streaming de registos para um centro de eventos ou um espaço de trabalho log analytics. Também podem ser extraídos do armazenamento de blob Azure, e vistos em diferentes ferramentas, tais como Excel e Power BI. Pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Sondas de estado de funcionamento do Balanceador de Carga](./load-balancer-custom-probe-overview.md)

- [API REST do Azure Monitor](/rest/api/monitor)

- [Como recuperar métricas através da REST API](/rest/api/monitor/metrics/list)

- [Diagnósticos padrão do Balancer de Carga com métricas, alertas e saúde de recursos](./load-balancer-standard-diagnostics.md)

- [Registos do Azure Monitor para o Balanceador de Carga Básico público](./load-balancer-monitor-log.md)

- [Veja as suas métricas de balançador de carga no portal Azure](./load-balancer-standard-diagnostics.md#view-your-load-balancer-metrics-in-the-azure-portal)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Centro de Segurança com log analytics espaço de trabalho para monitorizar e alertar sobre a atividade anómala relacionada com o Balancer de Carga em registos e eventos de segurança.

Ativar e a bordo dados para Azure Sentinel ou uma ferramenta SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/learn/tutorial-response.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável ao Balançador de Carga Azure. Esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável como Azure Load Balancer é um serviço de rede de base que não faz consultas de DNS.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável ao Azure Load Balancer, uma vez que esta recomendação se aplica aos recursos de cálculo.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O controlo de acesso baseado em funções (Azure RBAC) permite-lhe gerir o acesso a recursos Azure, como o seu Balancer de Carga através de atribuições de funções. Atribua estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. 

Inventário Papéis pré-definidos e incorporados para determinados recursos com ferramentas como Azure CLI, Azure PowerShell ou o portal Azure.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Utilize o Azure RBAC para controlar o acesso aos seus recursos do Balancer de Carga.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Balanceador de Carga é um serviço que não armazena os dados do cliente. É uma parte da plataforma subjacente que é gerida pela Microsoft. 

A Microsoft trata todos os conteúdos do cliente como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados do cliente. 

Para garantir que os dados dos clientes no Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados. 

- [Compreender a proteção de dados do cliente em Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade Azure para criar alertas quando ocorrerem alterações para recursos críticos do Azure, tais como balanceadores de carga utilizados para importantes cargas de trabalho de produção.

- [Como criar alertas para eventos de registo de atividades do Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (tais como computação, armazenamento, rede, portas, protocolos, e assim por diante) nas suas subscrições. Recomenda-se a criação e utilização de recursos atuais para o Azure Resource Manager. 

Certifique-se de permissões (ler) adequadas no seu inquilino e enumera todas as subscrições e recursos da Azure nas suas subscrições.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas nos recursos Azure com metadados para organizar logicamente de acordo com uma taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. 

Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados das suas subscrições em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Crie uma lista de recursos Azure aprovados de acordo com as suas necessidades organizacionais que pode alavancar como mecanismo de lista de autorização. Isto permitirá que a sua organização entre a bordo de quaisquer serviços Azure recém-disponíveis depois de serem formalmente revistos e aprovados pelos processos típicos de avaliação de segurança da sua organização.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições.

Consultar e descobrir recursos com o Azure Resource Graph dentro de subscrições próprias. 

Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional AD Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: O software necessário para operações comerciais, mas que pode incorrer em maior risco para a organização, deve ser isolado dentro da sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um Azure Firewall ou um grupo de segurança de rede.

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

- [Como criar um grupo de segurança de rede com um config de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração dos seus recursos Azure. Utilize definições de Política Azure incorporadas.

O Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem os requisitos de segurança para a sua organização.

Exporte os modelos do Gestor de Recursos Azure em formatos JavaScript Object Notation (JSON) e reveja-os periodicamente para garantir que as configurações cumprem os seus requisitos de segurança organizacional. 

Implemente recomendações do Security Center como uma base de configuração segura para os seus recursos Azure. 

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.  Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como definições personalizadas de Política Azure, modelos de Gestor de Recursos Azure e scripts de configuração de estado desejados. 

Conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se estiver integrado com Azure DevOps, ou no Ative Directory se estiver integrado com o TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Definir e implementar configurações de segurança padrão para recursos Azure utilizando a Política Azure.  Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos Azure. Implemente definições de política incorporadas relacionadas com os seus recursos específicos do Azure Load Balancer.  Além disso, utilize a Azure Automation para implementar alterações de configuração. aos seus recursos Azure Load Balancer.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como usar pseudónimos](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Centro de Segurança para realizar análises de base para a sua Política de Recursos Azure e Azure para alertar e auditar configurações de recursos.

- [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. 

A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Marque subscrições usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis.  

É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Seu Centro de Segurança utilizando a funcionalidade de exportação contínua para ajudar a identificar riscos para os recursos da Azure. 

Utilize uma funcionalidade de exportação contínua no Centro de Segurança que lhe permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. 

Utilize o conector de dados do Centro de Segurança para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança para acionar automaticamente respostas a alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho em Segurança](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft. 

- [Regras de teste de penetração de compromisso](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre [as linhas de base de segurança da Azure](../security/benchmarks/security-baselines-overview.md)