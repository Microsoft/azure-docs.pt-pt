---
title: Perguntas frequentes (FAQ) sobre O Observador da Rede Azure Microsoft Docs
description: Este artigo responde a perguntas frequentes sobre o serviço Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77471861"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Perguntas frequentes (FAQ) sobre O Observador de Redes Azure
O serviço [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornece um conjunto de ferramentas para monitorizar, diagnosticar, visualizar métricas e ativar ou desativar registos de recursos numa rede virtual Azure. Este artigo responde a perguntas comuns sobre o serviço.

## <a name="general"></a>Geral

### <a name="what-is-network-watcher"></a>O que é o Observador de Rede?
O Network Watcher foi concebido para monitorizar e reparar a saúde da rede de componentes IaaS (Infrastructure-as-a-Service), que inclui máquinas virtuais, redes virtuais, gateways de aplicações, equilibres de carga e outros recursos numa rede virtual Azure. Não é uma solução para monitorizar a infraestrutura PaaS (Platform-as-a-Service) ou obter análises web/mobile.

### <a name="what-tools-does-network-watcher-provide"></a>Que ferramentas o Network Watcher fornece?
O Network Watcher fornece três grandes conjuntos de capacidades
* Monitorização
  * [A vista de topologia](https://docs.microsoft.com/azure/network-watcher/view-network-topology) mostra-lhe os recursos na sua rede virtual e as relações entre eles.
  * [O Monitor de Ligação](https://docs.microsoft.com/azure/network-watcher/connection-monitor) permite-lhe monitorizar a conectividade e a latência entre um VM e outro recurso de rede.
  * [O monitor de desempenho da rede](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) permite-lhe monitorizar a conectividade e as latências através de arquiteturas de rede híbridas, circuitos Expressroute e pontos finais de serviço/aplicação.  
* Diagnóstico
  * [A VERIFICAção do Fluxo IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) permite-lhe detetar problemas de filtragem de tráfego a nível de VM.
  * [O Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) ajuda-o a verificar as rotas de tráfego e a detetar problemas de encaminhamento.
  * [A resolução](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) de problemas de ligação permite uma verificação única de conectividade e latência entre um VM e outro recurso de rede.
  * [O Packet Capture](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) permite-lhe capturar todo o tráfego num VM na sua rede virtual.
  * [A VPN Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) executa múltiplas verificações de diagnóstico nos seus gateways e ligações VPN para ajudar a depurar problemas.
* Registo
  * [Os Registos de Fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) permitem registar todo o tráfego nos seus [Grupos de Segurança de Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) processa os seus dados de Registo de Fluxo NSG que lhe permitem visualizar, consultar, analisar e compreender o tráfego da sua rede.


Para obter informações mais detalhadas, consulte a [página geral do Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Como funciona o preço do Network Watcher?
Visite [a página de preços](https://azure.microsoft.com/pricing/details/network-watcher/) dos componentes do Observador de Rede e os seus preços.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Em que regiões está suportado/disponível o Network Watcher?
Pode ver a mais recente disponibilidade regional na [página de disponibilidade do Serviço Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Que permissões são necessárias para utilizar o Network Watcher?
Consulte a lista de [permissões do RBAC necessárias para utilizar o Network Watcher](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions). Para a implementação de recursos, necessita de permissões de colaboradores para o NetworkWatcherRG (ver abaixo).

### <a name="how-do-i-enable-network-watcher"></a>Como posso ativar o Observador de Rede?
O serviço Network Watcher é [ativado automaticamente](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) para cada subscrição.

### <a name="what-is-the-network-watcher-deployment-model"></a>O que é o modelo de implementação do Network Watcher?
O recurso parental do Network Watcher é implementado com um caso único em todas as regiões. Formato de nomeação: NetworkWatcher_RegionName. Exemplo: NetworkWatcher_centralus é o recurso Network Watcher para a região "Central US".

### <a name="what-is-the-networkwatcherrg"></a>O que é o NetworkWatcherRG?
Os recursos do Network Watcher estão localizados no grupo de recursos **oculta NetworkWatcherRG** que é criado automaticamente. Por exemplo, o recurso NSG Flow Logs é um recurso infantil do Network Watcher e está ativado no NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Por que preciso instalar a extensão do Observador de Rede? 
A extensão do Observador de Rede é necessária para qualquer funcionalidade que precise de gerar ou intercetar tráfego a partir de um VM. 

### <a name="which-features-require-the-network-watcher-extension"></a>Quais as funcionalidades que requerem a extensão do Observador de Rede?
As funcionalidades de captura de pacotes, resolução de problemas de ligação e monitor de conexão precisam da extensão do Observador de Rede para estar presente.

### <a name="what-are-resource-limits-on-network-watcher"></a>Quais são os limites de recursos no Network Watcher?
Consulte a página [limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) de Serviço para todos os limites.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Porque é que só é permitido um caso de Observador de Rede por região? 
O Network Watcher apenas precisa de ser ativado uma vez para uma subscrição para as suas funcionalidades funcionarem, isto não é um limite de serviço.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Como posso gerir o Recurso do Observador de Rede? 
O recurso Network Watcher representa o serviço de backend para Network Watcher e é totalmente gerido pelo Azure. Os clientes não precisam de o gerir. Operações como o movimento não são suportadas no recurso. No entanto, [o recurso pode ser eliminado.](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal) 

## <a name="nsg-flow-logs"></a>Registos de fluxo NSG

### <a name="what-does-nsg-flow-logs-do"></a>O que faz os Registos de Fluxo NSG?
Os recursos da rede Azure podem ser combinados e geridos através de Grupos de Segurança de [Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview). Os Registos de Fluxo NSG permitem registar informações de fluxo de 5 tuple sobre todo o tráfego através dos seus NSGs. Os registos de fluxo bruto são escritos numa conta de Armazenamento Azure de onde podem ser processados, analisados, consultados ou exportados conforme necessário.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Como uso registos de fluxo NSG com uma conta de armazenamento atrás de uma firewall?

Para utilizar uma conta de Armazenamento por trás de uma firewall, tem de providenciar uma exceção para que os Serviços De Confiança da Microsoft acedam à sua conta de armazenamento:

* Navegue para a conta de armazenamento digitando o nome da conta de armazenamento na pesquisa global no portal ou na [página contas de armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* Na secção **DEFINIÇÕES**, selecione **Firewalls e redes virtuais**
* Em "Permitir o acesso a partir", **selecione redes selecionadas.** Em seguida, no âmbito **das Exceções,** marque a caixa ao lado **de "Permitir que serviços confiáveis da Microsoft acedam a esta conta de armazenamento"** 
* Se já estiver selecionada, não é preciso alterar nada.  
* Localize o seu NSG alvo na página de visão geral dos [Registos de Fluxo NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e ative os Registos de Fluxo NSG com a conta de armazenamento acima selecionada.

Pode verificar os registos de armazenamento após alguns minutos; deve ver um carimbo de data/hora atualizado ou um novo ficheiro JSON criado.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Como uso registos de fluxo NSG com uma conta de armazenamento atrás de um ponto de serviço?

Os Registos de Fluxo NSG são compatíveis com os pontos finais de serviço sem necessitar de qualquer configuração extra. Consulte o [tutorial sobre como ativar os pontos finais de serviço](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) na sua rede virtual.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Qual é a diferença entre as versões de registos de fluxo 1 & 2?
A versão 2 do Flow Logs introduz o conceito de *Flow State* & armazena informações sobre bytes e pacotes transmitidos. [Leia mais.](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)

## <a name="next-steps"></a>Passos Seguintes
 - Dirija-se à nossa [página geral de documentação](https://docs.microsoft.com/azure/network-watcher/) para alguns tutoriais para começar com o Network Watcher.
