---
title: Perguntas frequentes (FAQ) sobre o observador de rede do Azure | Microsoft Docs
description: Este artigo responde às perguntas frequentes sobre o serviço observador de rede do Azure.
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
ms.openlocfilehash: 1cc3664ff8472a6b5a73fa89588611f59ac27e6a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720275"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Perguntas frequentes (FAQ) sobre o observador de rede do Azure
O serviço [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornece um conjunto de ferramentas para monitorizar, diagnosticar, visualizar métricas e ativar ou desativar registos de recursos numa rede virtual Azure. Este artigo responde a perguntas comuns sobre o serviço.

## <a name="general"></a>Geral

### <a name="what-is-network-watcher"></a>O que é O Observador da Rede?
O observador de rede foi projetado para monitorar e reparar a integridade da rede de componentes IaaS (infraestrutura como serviço), que inclui máquinas virtuais, redes virtuais, gateways de aplicativo, balanceadores de carga e outros recursos em uma rede virtual do Azure. Não é uma solução para monitorar a infraestrutura PaaS (plataforma como serviço) ou obter a análise Web/móvel.

### <a name="what-tools-does-network-watcher-provide"></a>Quais ferramentas o observador de rede fornece?
O observador de rede fornece três conjuntos principais de recursos
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


### <a name="how-does-network-watcher-pricing-work"></a>Como funciona o preço do observador de rede?
Visite a [página de Preços](https://azure.microsoft.com/pricing/details/network-watcher/) dos componentes do Observador da Rede e os seus preços.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Em quais regiões o observador de rede tem suporte/está disponível?
Pode ver a mais recente disponibilidade regional na página de disponibilidade do [Serviço Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="what-are-resource-limits-on-network-watcher"></a>O que são limites de recursos no observador de rede?
Consulte a página [limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) de serviço para todos os limites.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Por que apenas uma instância do observador de rede é permitida por região?
O observador de rede precisa ser habilitado apenas uma vez para uma assinatura para que seus recursos funcionem, esse não é um limite de serviço.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Por que preciso instalar a extensão do observador de rede? 
A extensão do observador de rede é necessária para qualquer recurso que precise gerar ou interceptar o tráfego de uma VM. 

### <a name="which-features-require-the-network-watcher-extension"></a>Quais recursos exigem a extensão do observador de rede?
Somente a captura de pacote, a solução de problemas de conexão e o monitor de conexão precisam que a extensão do observador de rede esteja presente.

## <a name="nsg-flow-logs"></a>Logs de fluxo do NSG

### <a name="what-does-nsg-flow-logs-do"></a>O que os logs de fluxo do NSG fazem?
Os recursos da rede Azure podem ser combinados e geridos através de Grupos de Segurança da [Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview). Os logs de fluxo do NSG permitem que você registre informações de fluxo de 5 tuplas sobre todo o tráfego por meio de seu NSGs. Os logs de fluxo brutos são gravados em uma conta de armazenamento do Azure de onde eles podem ser processados, analisados, consultados ou exportados conforme necessário.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Como uso registos de fluxo NSG com uma conta de armazenamento atrás de uma firewall?

Para utilizar uma conta de Armazenamento atrás de uma firewall, tem de fornecer uma exceção para que os Serviços Microsoft Fifiados acedam à sua conta de armazenamento:

* Navegue na conta de armazenamento digitando o nome da conta de armazenamento na pesquisa global no portal ou na [página Contas](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) de Armazenamento
* Na secção **DEFINIÇÕES**, selecione **Firewalls e redes virtuais**
* Em "Permitir o acesso a partir", selecione **redes Selecionadas.** Em seguida, em **exceções**, marque a caixa ao lado **de "Permitir que os serviços confiáveis da Microsoft acedam a esta conta de armazenamento"** 
* Se já estiver selecionada, não é preciso alterar nada.  
* Localize o seu NSG alvo na página de visão geral dos Registos de [Fluxo nsg](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e ative os Registos de Fluxo NSG com a conta de armazenamento acima selecionada.

Pode verificar os registos de armazenamento após alguns minutos; deve ver um carimbo de data/hora atualizado ou um novo ficheiro JSON criado.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Como uso os Registos de Fluxo NSG com uma conta de Armazenamento atrás de um ponto final de serviço?

Os registos de fluxo NSG são compantáveis com pontos finais de serviço sem necessitar de qualquer configuração extra. Consulte o tutorial sobre a ativação de [pontos finais](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) de serviço na sua rede virtual.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Qual é a diferença entre as versões 1 & 2 dos logs de fluxo?
A versão 2 do Flow Logs introduz o conceito de *Flow State* & stores information about bytes and packets transmitidos. [Ler mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Próximos Passos
 - Dirija-se à nossa página de visão geral de [documentação](https://docs.microsoft.com/azure/network-watcher/) para alguns tutoriais para começar com o Network Watcher.
