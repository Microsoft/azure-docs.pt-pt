---
title: Visualização de múltiplos recursos no Metrics Explorer
description: Saiba como visualizar vários recursos no Azure Monitor Metrics Explorer
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 724809dbce3ca1b5a36f4da0ba5c03d0f78897f5
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577756"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>Visualização de múltiplos recursos no Metrics Explorer

O selecionador de recursos permite-lhe visualizar métricas em vários recursos que estão dentro da mesma subscrição e região. Abaixo estão as instruções sobre como ver vários recursos no Azure Monitor Metrics Explorer. 

## <a name="selecting-a-resource"></a>Selecionando um recurso 

Selecione **métricas** do menu **Azure Monitor** ou da secção de **monitorização** do menu de um recurso. Clique no botão "Selecione um âmbito" para abrir o seletor de âmbito de recursos, o que lhe permitirá selecionar os recursos para os quais pretende ver métricas. Isto já deve ser preenchido se você abriu métricas explorador a partir do menu de um recurso. 

![Screenshot do picker de âmbito de recursos destacado a vermelho](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>Seleção de múltiplos recursos 

Alguns tipos de recursos permitiram a possibilidade de consultar métricas sobre vários recursos, desde que estejam dentro da mesma subscrição e localização. Estes tipos de recursos podem ser encontrados no topo da redução dos "Tipos de Recursos". 

![Screenshot que mostra uma queda de recursos que são compatíveis com vários recursos ](./media/metrics-charts/020.png)

> [!WARNING] 
> Tem de ter permissão do Monitoring Reader ao nível da subscrição para visualizar métricas em vários recursos, grupos de recursos ou uma subscrição. Para tal, siga as instruções [deste documento.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Para visualizar métricas sobre vários recursos, comece por selecionar múltiplos recursos dentro do selecionador de recursos. 

![Screenshot que mostra como selecionar vários recursos](./media/metrics-charts/021.png)

> [!NOTE]
> Só é possível selecionar vários recursos dentro do mesmo tipo de recurso, localização e subscrição. Os recursos fora destes critérios não serão selecionáveis. 

Quando terminar de selecionar, clique no botão "Aplicar" para guardar a sua seleção. 

## <a name="selecting-a-resource-group-or-subscription"></a>Selecionando um grupo de recursos ou subscrição 

> [!WARNING]
> Tem de ter permissão do Monitoring Reader ao nível da subscrição para visualizar métricas em vários recursos, grupos de recursos ou uma subscrição. 

Para tipos compatíveis com vários recursos, também pode consultar métricas através de uma subscrição ou de vários grupos de recursos. Comece por selecionar uma subscrição ou um ou mais grupos de recursos: 

![Screenshot que mostra como consultar vários grupos de recursos ](./media/metrics-charts/022.png)

Em seguida, terá de selecionar um tipo de recurso e localização antes de poder continuar a aplicar o seu novo âmbito de aplicação. 

![Screenshot que mostra os grupos de recursos selecionados ](./media/metrics-charts/023.png)

Também é possível expandir os âmbitos selecionados para verificar a que recursos se aplicará.

![Screenshot que mostra os recursos selecionados dentro dos grupos ](./media/metrics-charts/024.png)

Assim que terminar de selecionar os seus âmbitos, clique em "Aplicar" para guardar as suas seleções. 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>Divisão e filtragem por grupo de recursos ou recursos

Depois de traçar os seus recursos, pode utilizar a ferramenta de divisão e filtragem para obter mais informações sobre os seus dados. 

A divisão permite visualizar como diferentes segmentos da métrica se comparam entre si. Por exemplo, quando estiver a traçar uma métrica para múltiplos recursos, pode utilizar a ferramenta "Aplicar divisão" para dividir por id de recursos ou grupo de recursos. Isto permitir-lhe-á comparar facilmente uma única métrica entre vários recursos ou grupos de recursos.  

Por exemplo, abaixo está um gráfico da percentagem de CPU em 9VMs. Ao dividir por id de recursos, você pode facilmente ver como a percentagem de CPU difere por VM. 

![Screenshot que mostra como você pode usar a divisão para ver a percentagem de CPU por VM](./media/metrics-charts/026.png)

Além da divisão, pode utilizar a função de filtragem apenas para exibir os grupos de recursos que pretende ver.  Por exemplo, se pretender visualizar a percentagem de CPU para VMs para um determinado grupo de recursos, pode utilizar a ferramenta "Adicionar filtro" para filtrar por grupo de recursos. Neste exemplo filtramos por TailspinToys, que remove métricas associadas a recursos em TailspinToysDemo. 

![Screenshot que mostra como pode filtrar por grupo de recursos](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>Fixando os seus gráficos de vários recursos 

> [!WARNING] 
> Tem de ter permissão do Monitoring Reader ao nível da subscrição para visualizar métricas em vários recursos, grupos de recursos ou uma subscrição. Para tal, siga as instruções [deste documento.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 

Para fixar o seu gráfico multi-recursos, siga as instruções [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#create-alert-rules). 

## <a name="next-steps"></a>Passos seguintes

* [Resolução de Problemas do Explorador de Métricas](metrics-troubleshoot.md)
* [See a list of available metrics for Azure services](metrics-supported.md) (Ver uma lista de métricas disponíveis para serviços do Azure)
* [See examples of configured charts](metric-chart-samples.md) (Ver exemplos de gráficos configurados)

