---
title: Perguntas frequentes (FAQ) sobre o Vigilante da Rede Azure [ Microsoft Docs
description: Este artigo responde frequentemente a perguntas sobre o serviço Azure Network Watcher.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471861"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Perguntas frequentes (FAQ) sobre o Vigilante da Rede Azure
O serviço [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornece um conjunto de ferramentas para monitorizar, diagnosticar, visualizar métricas e ativar ou desativar registos de recursos numa rede virtual Azure. Este artigo responde a perguntas comuns sobre o serviço.

## <a name="general"></a>Geral

### <a name="what-is-network-watcher"></a>O que é o Observador de Rede?
O Network Watcher foi concebido para monitorizar e reparar a saúde da rede dos componentes IaaS (Infraestrutura-as-a-Service), que inclui Máquinas Virtuais, Redes Virtuais, Gateways de Aplicação, Equilibradores de Carga e outros recursos numa rede virtual Azure. Não é uma solução para monitorizar a infraestrutura PaaS (Plataforma-as-a-Service) ou obter análises web/móveis.

### <a name="what-tools-does-network-watcher-provide"></a>Que ferramentas o Network Watcher fornece?
O Network Watcher fornece três grandes conjuntos de capacidades
* Monitorização
  * [A visão de topologia](https://docs.microsoft.com/azure/network-watcher/view-network-topology) mostra-lhe os recursos da sua rede virtual e as relações entre eles.
  * [O Monitor de Ligação](https://docs.microsoft.com/azure/network-watcher/connection-monitor) permite-lhe monitorizar a conectividade e a latência entre um VM e outro recurso de rede.
  * [O monitor](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) de desempenho da rede permite-lhe monitorizar a conectividade e as latenciências através de arquiteturas de rede híbridas, circuitos expressroute e pontos finais de serviço/aplicação.  
* Diagnóstico
  * [Ip Flow Check](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) permite detetar problemas de filtragem de tráfego a um nível VM.
  * [O Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) ajuda-o a verificar as rotas de tráfego e a detetar problemas de encaminhamento.
  * [A Conexão Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) permite uma verificação única de conectividade e latência entre um VM e outro recurso de rede.
  * [A Captura de Pacotes](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) permite-lhe capturar todo o tráfego de um VM na sua rede virtual.
  * [A VPN Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) executa múltiplas verificações de diagnóstico nos seus gateways VPN e ligações para ajudar a depurar problemas.
* Registo
  * [Os registos](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) de fluxo NSG permitem registar todo o tráfego nos seus Grupos de Segurança de [Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [O Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) processa os seus dados de Log de Fluxo NSG que lhe permitem visualizar, consultar, analisar e compreender o tráfego da sua rede.


Para obter informações mais detalhadas, consulte a [página de visão geral](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)do Observador da Rede .


### <a name="how-does-network-watcher-pricing-work"></a>Como funciona o preço dos preços do Network Watcher?
Visite a [página de Preços](https://azure.microsoft.com/pricing/details/network-watcher/) dos componentes do Observador da Rede e os seus preços.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Em que regiões é suportado/disponível o Network Watcher?
Pode ver a mais recente disponibilidade regional na página de disponibilidade do [Serviço Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Que permissões são necessárias para usar o Observador da Rede?
Consulte a lista de [permissões RBAC necessárias para utilizar](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions)o Observador de Rede . Para a implementação de recursos, necessita de permissões contributivas para o NetworkWatcherRG (ver abaixo).

### <a name="how-do-i-enable-network-watcher"></a>Como posso ativar o Observador de Rede?
O serviço De Observador de Rede está [ativado automaticamente](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) para cada subscrição.

### <a name="what-is-the-network-watcher-deployment-model"></a>Qual é o modelo de implantação do Observador de Rede?
O recurso-mãe do Observador de Rede é implantado com um caso único em todas as regiões. Formato de nomeação: NetworkWatcher_RegionName. Exemplo: NetworkWatcher_centralus é o recurso Network Watcher para a região "Centro dos EUA".

### <a name="what-is-the-networkwatcherrg"></a>O que é o NetworkWatcherRG?
Os recursos do Network Watcher estão localizados no grupo de recursos **NetworkWatcherRG** oculto que é criado automaticamente. Por exemplo, o recurso NSG Flow Logs é um recurso infantil do Observador de Rede e está ativado no NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Por que preciso instalar a extensão do Observador de Rede? 
A extensão do Observador de Rede é necessária para qualquer funcionalidade que precise de gerar ou intercetar tráfego a partir de um VM. 

### <a name="which-features-require-the-network-watcher-extension"></a>Quais as funcionalidades que requerem a extensão do Observador da Rede?
As funcionalidades de captura de pacotes, de resolução de problemas de ligação e de monitorização de ligação precisam da extensão do Observador da Rede para estar presente.

### <a name="what-are-resource-limits-on-network-watcher"></a>Quais são os limites de recursos no Observador da Rede?
Consulte a página [limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) de serviço para todos os limites.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Porque é que só é permitida uma instância de Observador de Rede por região? 
O Network Watcher só precisa de ser ativado uma vez para uma subscrição para as suas funcionalidades funcionarem, este não é um limite de serviço.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Como posso gerir o Recurso de Observador de Rede? 
O recurso Network Watcher representa o serviço de backend para O Observador de Rede e é totalmente gerido pelo Azure. Os clientes não precisam de o gerir. Operações como movimento não são suportadas no recurso. No entanto, [o recurso pode ser eliminado](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal). 

## <a name="nsg-flow-logs"></a>Registos de fluxo NSG

### <a name="what-does-nsg-flow-logs-do"></a>O que faz o NSG Flow Logs?
Os recursos da rede Azure podem ser combinados e geridos através de Grupos de Segurança da [Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview). Os registos de fluxo NSG permitem-lhe registar informações de fluxo de 5 tuple sobre todo o tráfego através dos seus NSGs. Os registos de fluxo bruto são escritos numa conta de Armazenamento Azure de onde podem ser processados, analisados, consultados ou exportados conforme necessário.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Como uso registos de fluxo NSG com uma conta de armazenamento atrás de uma firewall?

Para utilizar uma conta de Armazenamento atrás de uma firewall, tem de fornecer uma exceção para que os Serviços Microsoft Fifiados acedam à sua conta de armazenamento:

* Navegue na conta de armazenamento digitando o nome da conta de armazenamento na pesquisa global no portal ou na [página Contas](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) de Armazenamento
* Na secção **DEFINIÇÕES**, selecione **Firewalls e redes virtuais**
* Em "Permitir o acesso a partir", selecione **redes Selecionadas.** Em seguida, em **exceções**, marque a caixa ao lado **de "Permitir que os serviços confiáveis da Microsoft acedam a esta conta de armazenamento"** 
* Se já estiver selecionada, não é preciso alterar nada.  
* Localize o seu NSG alvo na página de visão geral dos Registos de [Fluxo nsg](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e ative os Registos de Fluxo NSG com a conta de armazenamento acima selecionada.

Pode verificar os registos de armazenamento após alguns minutos; deve ver um carimbo de data/hora atualizado ou um novo ficheiro JSON criado.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Como uso os Registos de Fluxo NSG com uma conta de Armazenamento atrás de um ponto final de serviço?

Os registos de fluxo NSG são compatíveis com os Pontos Finais do Serviço sem necessitar de qualquer configuração extra. Consulte o tutorial sobre a ativação de [pontos finais](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) de serviço na sua rede virtual.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Qual é a diferença entre as versões de registos de fluxo 1 & 2?
A versão 2 do Flow Logs introduz o conceito de *Flow State* & armazena informações sobre bytes e pacotes transmitidos. [Leia mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Passos Seguintes
 - Dirija-se à nossa página de visão geral de [documentação](https://docs.microsoft.com/azure/network-watcher/) para alguns tutoriais para começar com o Network Watcher.
