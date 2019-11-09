---
title: Azure Monitor para redes (visualização)
description: Uma visão geral rápida de Azure Monitor para rede que fornece uma visão abrangente da integridade e das métricas para todos os recursos de rede implantados sem nenhuma configuração.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 3bb92b85779366081349dab777be67e063469678
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73840057"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor para redes (visualização)
O Azure Monitor para rede fornece uma visão abrangente da integridade e das métricas para todos os recursos de rede implantados sem nenhuma configuração. A capacidade de pesquisa avançada ajuda a identificar dependências de recursos, permitindo cenários como identificar recursos que hospedam seu site simplesmente pesquisando o nome do site hospedado.

A página **visão geral** do Azure monitor for Networks fornece uma maneira fácil de visualizar o inventário de seus recursos de rede junto com os alertas e a integridade de recursos. Ele é dividido em quatro áreas funcionais principais:

- Pesquisa e filtragem
- Resource Health e métricas
- Alertas 
- Exibição de dependência

![Página de visão geral](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Pesquisa e filtragem
A exibição de integridade e alertas de recursos pode ser personalizada usando filtros como **assinatura**, **grupo de recursos** e **tipo de recurso**. A caixa de pesquisa fornece a capacidade de Pesquisar por propriedades de recurso.

A caixa de pesquisa pode ser usada para pesquisar recursos e recursos associados. Por exemplo, um IP público é associado a um gateway de aplicativo. Pesquisar o nome DNS do IPs público identificará o IP público e o gateway de aplicativo associado.

![Pesquisa](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Resource Health e métrica
Cada bloco representa um tipo de recurso, com o número de instâncias implantadas em todas as assinaturas selecionadas junto com o status de integridade do recurso. No exemplo a seguir, há 105 conexões de ER e VPN implantadas, 103 estão íntegras e 2 não disponíveis.

![Estado de funcionamento de recursos](media/network-insights-overview/resource-health.png)

Ao clicar nas duas conexões de ER e VPN indisponíveis, o inicia uma exibição de métrica. 

![Exibição de métrica](media/network-insights-overview/metric-view.png)

Você pode clicar em cada elemento no modo de exibição de grade. Clique no ícone de integridade para redirecionar para o Resource Health para essa conexão. Clique em alertas para redirecionar para a página de alertas e métricas respectivamente para essa conexão. 

## <a name="alerts"></a>Alertas
A grade de **alertas** à direita fornece uma exibição de todos os alertas gerados para os recursos selecionados em todas as assinaturas. Clique no alerta contagens para navegar até a página alertas detalhados.

## <a name="dependency-view"></a>Exibição de dependência
A exibição de **dependência** ajuda a visualizar como o recurso é configurado. Atualmente, há suporte para a exibição de dependência somente no gateway de aplicativo. A exibição de dependência pode ser acessada clicando no nome do recurso do gateway de aplicativo na exibição de grade de métricas.

![Exibição do gateway de aplicativo](media/network-insights-overview/application-gateway.png)

A exibição de **dependência** do gateway de aplicativo fornece uma visão simplificada de como os IPs de front-end são conectados aos ouvintes, às regras e ao pool de back-end. As bordas de conexão são codificadas por cores e fornecem detalhes adicionais com base na integridade do pool de back-end. A exibição também fornece uma exibição detalhada das métricas e métricas do gateway de aplicativo para todos os pools de back-end relacionados, como VMSS e instâncias de VM.

![Exibição de dependência](media/network-insights-overview/dependency-view.png)

O grafo de dependência permite a navegação fácil para as definições de configuração. Clique com o botão direito do mouse em um pool de back-end para acessar outras funcionalidades. Por exemplo, se o pool de back-end for uma VM, você poderá acessar diretamente as informações da VM e a conexão do observador de rede para identificar problemas de conectividade.

![Menu de exibição de dependência](media/network-insights-overview/dependency-view-menu.png)

A barra de pesquisa e filtro na exibição de dependência fornece uma maneira fácil de Pesquisar no grafo. Por exemplo, a pesquisa de *AppGWTestRule* no exemplo abaixo restringirá a exibição gráfica a todos os nós conectados por meio de *AppGWTestRule*. 

![Exemplo de pesquisa](media/network-insights-overview/search-example.png)

Filtros diferentes fornecem ajuda para restringir a um caminho e estado específicos. Por exemplo, selecione somente não *íntegro* no menu suspenso **status de integridade** para mostrar todas as bordas em que o estado não está *íntegro*.

Clique em **exibição de métrica detalhada** para iniciar uma pasta de trabalho pré-configurada com métricas detalhadas para o gateway de aplicativo, todos os recursos de pool de back-end e IPS de front-end. 

## <a name="next-steps"></a>Passos seguintes 

- Saiba mais sobre o monitoramento de rede em o que é o [observador de rede do Azure?](/azure/network-watcher/network-watcher-monitoring-overview).
