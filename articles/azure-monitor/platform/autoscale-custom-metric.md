---
title: Dimensionamento automático no Azure usando uma métrica personalizada
description: Saiba como dimensionar seu recurso por métrica personalizada no Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: f83c68dd2f2ddbb73974a2ea4e91e746308945c6
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958102"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Introdução ao dimensionamento automático por métrica personalizada no Azure
Este artigo descreve como dimensionar seu recurso por uma métrica personalizada no portal do Azure.

Azure Monitor dimensionamento automático se aplica somente aos [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços de nuvem](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicativo-aplicativos Web](https://azure.microsoft.com/services/app-service/web/)e [serviços de gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Vamos começar
Este artigo pressupõe que você tenha um aplicativo Web com o Application insights configurado. Se você ainda não tiver um, poderá [configurar Application insights para o site do ASP.net][1]

- Abrir [portal do Azure][2]
- Clique no ícone de Azure Monitor no painel de navegação à esquerda.
  ![iniciar Azure Monitor][3]
- Clique na configuração de dimensionamento automático para exibir todos os recursos para os quais o dimensionamento automático é aplicável, juntamente com seu status atual de dimensionamento automático ![descobrir a escala automática no Azure monitor][4]
- Abra a folha "dimensionamento automático" em Azure Monitor e selecione um recurso que você deseja dimensionar
  > Observação: as etapas a seguir usam um plano do serviço de aplicativo associado a um aplicativo Web que tem o app insights configurado.
- Na folha de configuração de escala do recurso, observe que a contagem de instâncias atual é 1. Clique em ' Habilitar dimensionamento automático '.
  ![configuração de escala para o novo aplicativo Web][5]
- Forneça um nome para a configuração de escala e clique em "adicionar uma regra". Observe as opções de regra de escala que são abertos como um painel de contexto no lado direito. Por padrão, ele define a opção para dimensionar sua contagem de instâncias em 1 se o percentual de CPU do recurso exceder 70%. Altere a origem da métrica na parte superior para "Application Insights", selecione o recurso do App insights na lista suspensa "recurso" e, em seguida, selecione a métrica personalizada com base na qual você deseja dimensionar.
  Escala de ![por métrica personalizada][6]
- Semelhante à etapa acima, adicione uma regra de escala que será dimensionada e diminua a contagem de escala em 1 se a métrica personalizada estiver abaixo de um limite.
  Escala de ![com base na][7] de CPU
- Defina os limites da instância. Por exemplo, se você quiser dimensionar entre 2-5 instâncias dependendo das flutuações de métricas personalizadas, defina ' mínimo ' como ' 2 ', ' máximo ' como ' 5 ' e ' padrão ' como ' 2 '
  > Observação: caso haja um problema ao ler as métricas de recurso e a capacidade atual esteja abaixo da capacidade padrão, para garantir a disponibilidade do recurso, o dimensionamento automático será dimensionado para o valor padrão. Se a capacidade atual já for maior que a capacidade padrão, o dimensionamento automático não será dimensionado horizontalmente.
- Clique em ' salvar '

Parabéns! Agora você criou com êxito sua configuração de dimensionamento para dimensionar automaticamente seu aplicativo Web com base em uma métrica personalizada.

> Observação: as mesmas etapas são aplicáveis para começar com uma função de serviço de nuvem ou VMSS.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

