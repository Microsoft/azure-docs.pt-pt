---
title: Ver vários recursos no explorador de métricas Azure
description: Aprenda a visualizar múltiplos recursos utilizando o explorador de métricas Azure.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 23507a01f3ed0015690d9655fde92a875f89c2da
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095474"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Ver vários recursos no explorador de métricas Azure

O selecionador de recursos permite-lhe visualizar métricas em vários recursos que estão dentro da mesma subscrição e região. Este artigo explica como visualizar múltiplos recursos utilizando a funcionalidade de explorador de métricas Azure do Azure Monitor. 

## <a name="select-a-resource"></a>Selecionar um recurso 

Selecione **métricas** do menu **Azure Monitor** ou da secção de **monitorização** do menu de um recurso. Em seguida, escolha **Selecionar um âmbito** para abrir o selecionador de âmbito. 

Utilize o selecionador de âmbito para selecionar os recursos cujas métricas pretende ver. O âmbito deve ser preenchido se abrir o explorador de métricas a partir do menu de um recurso. 

![Screenshot mostrando como abrir o selecionador de âmbito de recurso.](./media/metrics-dynamic-scope/019.png)

## <a name="select-multiple-resources"></a>Selecione vários recursos 

Alguns tipos de recursos podem consultar métricas sobre vários recursos. As métricas devem estar dentro da mesma subscrição e local. Encontre estes tipos de recursos no topo do menu **de tipos de recursos.**

![Screenshot que mostra um menu de recursos que são compatíveis com múltiplos recursos.](./media/metrics-dynamic-scope/020.png)

> [!WARNING] 
> Tem de ter permissão do Monitoring Reader ao nível da subscrição para visualizar métricas em vários recursos, grupos de recursos ou uma subscrição. Para obter mais informações, consulte [as funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).

Para visualizar métricas sobre vários recursos, comece por selecionar vários recursos dentro do selecionador de recursos. 

![Screenshot que mostra como selecionar vários recursos.](./media/metrics-dynamic-scope/021.png)

> [!NOTE]
> Os recursos que seleciona devem estar dentro do mesmo tipo de recurso, localização e subscrição. Recursos que não se adequam a estes critérios não são selecionáveis. 

Quando terminar, escolha **Aplicar** para guardar as suas seleções. 

## <a name="select-a-resource-group-or-subscription"></a>Selecione um grupo de recursos ou subscrição 

> [!WARNING]
> Tem de ter permissão do Monitoring Reader ao nível da subscrição para visualizar métricas em vários recursos, grupos de recursos ou uma subscrição. 

Para tipos compatíveis com múltiplos recursos, pode consultar métricas através de uma subscrição ou de vários grupos de recursos. Comece por selecionar uma subscrição ou um ou mais grupos de recursos: 

![Screenshot que mostra como consultar vários grupos de recursos.](./media/metrics-dynamic-scope/022.png)

Selecione um tipo de recurso e localização. 

![Screenshot que mostra os grupos de recursos selecionados.](./media/metrics-dynamic-scope/023.png)

Pode expandir os âmbitos selecionados para verificar os recursos a que as suas seleções se aplicam.

![Screenshot que mostra os recursos selecionados dentro dos grupos.](./media/metrics-dynamic-scope/024.png)

Quando terminar de selecionar os âmbitos, **selecione Aplicar**. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>Dividido e filtrado por grupo de recursos ou recursos

Depois de traçar os seus recursos, pode utilizar a divisão e filtragem para obter mais informações sobre os seus dados. 

A divisão permite visualizar como diferentes segmentos da métrica se comparam entre si. Por exemplo, quando se traça uma métrica para múltiplos recursos, pode escolher **aplicar a divisão** para dividir por ID de recurso ou grupo de recursos. A divisão permite comparar uma única métrica entre vários recursos ou grupos de recursos.  

Por exemplo, o gráfico seguinte mostra a percentagem de CPU em nove VMs. Quando se divide por ID de recurso, vê-se como a percentagem de CPU difere por VM. 

![Screenshot que mostra como usar a divisão para ver a percentagem de CPU em VMs.](./media/metrics-dynamic-scope/026.png)

Juntamente com a divisão, pode utilizar a filtragem para exibir apenas os grupos de recursos que pretende ver.  Por exemplo, para visualizar a percentagem de CPU para VMs para um determinado grupo de recursos, pode selecionar **adicionar filtro** para filtrar por grupo de recursos. 

Neste exemplo, filtramos por TailspinToysDemo. Aqui, o filtro remove métricas associadas aos recursos em TailspinToys. 

![Screenshot que mostra como filtrar por grupo de recursos.](./media/metrics-dynamic-scope/027.png)

## <a name="pin-multiple-resource-charts"></a>Pin gráficos de vários recursos 

Gráficos de vários recursos que visualizam métricas entre grupos de recursos e subscrições requerem que o utilizador tenha permissão *do Leitor de Monitorização* ao nível da subscrição. Certifique-se de que todos os utilizadores dos dashboards aos quais pine gráficos de vários recursos têm permissões suficientes. Para obter mais informações, consulte [as funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).

Para fixar o seu gráfico de múltiplos recursos num dashboard, consulte [Pinning para os painéis](../essentials/metrics-charts.md#pinning-to-dashboards)de instrumentos . 

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas do explorador de métricas](../essentials/metrics-troubleshoot.md)
* [See a list of available metrics for Azure services](../platform/metrics-supported.md) (Ver uma lista de métricas disponíveis para serviços do Azure)
* [See examples of configured charts](../essentials/metric-chart-samples.md) (Ver exemplos de gráficos configurados)