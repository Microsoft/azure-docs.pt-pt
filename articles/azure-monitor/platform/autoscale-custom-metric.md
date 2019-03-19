---
title: Dimensionamento automático no Azure através de uma métrica personalizada
description: Saiba como dimensionar o seu recurso por métrica personalizada no Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: e6423f2ce3659fd3dd738dcc8a990261bc7bf60c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088388"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Introdução ao dimensionamento automático por métrica personalizada no Azure
Este artigo descreve como dimensionar o seu recurso por uma métrica personalizada no portal do Azure.

Dimensionamento automático de Monitor do Azure aplicam-se apenas ao [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços Cloud](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicações - aplicações Web](https://azure.microsoft.com/services/app-service/web/), e [deserviçosdegestãodeAPI](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Permite começar a utilizar
Este artigo pressupõe que tem uma aplicação web com o application insights configurado. Se ainda não tiver uma, pode [configurar Application Insights para o seu Web site ASP.NET][1]

- Abra [portal do Azure][2]
- Clique no ícone do Azure Monitor no painel de navegação esquerdo.
  ![Iniciar o Monitor do Azure][3]
- Clique na definição de dimensionamento automático para ver todos os recursos para que automaticamente a escala é aplicável, juntamente com o respetivo estado atual do dimensionamento automático ![detetar o dimensionamento automático no Azure monitor][4]
- Abra o painel do "Dimensionamento automático" no Azure Monitor e selecione um recurso que pretende dimensionar
  > Nota: Os passos abaixo utilizam um plano do serviço de aplicação associado a uma aplicação web que tenha configurado o app insights.
- No painel de definição do dimensionamento do recurso, tenha em atenção que a contagem de instâncias atual é 1. Clique em "Ativar o dimensionamento automático".
  ![Definição de dimensionamento para a nova aplicação web][5]
- Forneça um nome para a definição de dimensionamento e um simples clique em "Adicionar uma regra". Observe as opções de regra de dimensionamento que abre-se como um painel de contexto no lado direito. Por padrão, ele define a opção de dimensionar sua contagem de instâncias por 1 se a percentagem de CPU do recurso superior a 70%. Alterar a origem de métrica na parte superior para "Application Insights", selecione o recurso de informações de aplicação na lista pendente "Recurso" e, em seguida, selecione a métrica personalizada com base no qual pretende dimensionar.
  ![Dimensionar por métrica personalizada][6]
- À semelhança do passo acima, adicione uma regra de dimensionamento que irá reduzir horizontalmente e reduzir a contagem de dimensionamento em 1. Se a métrica personalizada estiver abaixo do limite.
  ![Dimensionar com base na cpu][7]
- Definir o que limites de instância. Por exemplo, se quiser Dimensionar entre instâncias de 2 a 5 consoante as flutuações de métricas personalizadas, defina "mínimo' para '2', 'máximo' para '5' e 'default' para ' 2'
  > Nota: No caso de existir um problema ao ler as métricas de recurso e a capacidade atual estiver abaixo da capacidade predefinida, em seguida, para garantir a disponibilidade do recurso, dimensionamento automático irá aumentar horizontalmente para o valor predefinido. Se a capacidade atual já for superior a capacidade predefinida, o dimensionamento automático não irá aumentar no.
- Clique em "Guardar"

Parabéns! Agora criado com êxito o dimensionamento na definição de dimensionamento automático a aplicação web com base numa métrica personalizada.

> Nota: Os mesmos passos são aplicáveis para começar com uma função de serviço VMSS ou na cloud.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

