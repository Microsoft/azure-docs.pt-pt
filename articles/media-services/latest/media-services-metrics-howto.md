---
title: Ver métricas com o Azure Monitor
description: Este artigo mostra como monitorizar as métricas com os gráficos de portais do Azure e a CLI do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795828"
---
# <a name="monitor-media-services-metrics"></a>Monitorizar as métricas de serviços de multimédia 

[O Azure Monitor](../../azure-monitor/overview.md) permite-lhe monitorizar as métricas e registos de diagnóstico que o ajudam a compreende o desempenho das suas aplicações. Para obter uma descrição detalhada desta funcionalidade e para ver por que iria querer utilizar registos de métricas e diagnósticos de serviços de multimédia do Azure, consulte [métricas de serviços de multimédia do Monitor e registos de diagnóstico](media-services-metrics-diagnostic-logs.md).

O Azure Monitor proporciona várias formas de interagir com métricas, incluindo gráficos-las no portal, o acesso aos mesmos através da API REST ou consultando-os com a CLI. Este artigo mostra como monitorizar as métricas com os gráficos de portais do Azure e a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de serviços de multimédia](create-account-cli-how-to.md)
- Revisão [métricas de serviços de multimédia do Monitor e registos de diagnóstico](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Ver métricas no portal do Azure

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue até à sua conta de Media Services do Azure e selecione **métricas**.
1. Clique nas **recursos** caixa e selecione o recurso para o qual pretende monitorizar as métricas. 

    O **selecionar um recurso** janela aparece à direita com a lista de recursos disponíveis para si. Neste caso, ver 

    * &lt;Nome da conta dos serviços de multimédia&gt;
    * &lt;Nome da conta dos serviços de multimédia&gt;/&lt;nome do ponto final de transmissão em fluxo&gt;
    * &lt;Nome da conta de armazenamento&gt;

    Selecione o recurso e prima **aplicar**. Para obter detalhes sobre as métricas e de recursos suportados, consulte [métricas de serviços de multimédia do Monitor](media-services-metrics-diagnostic-logs.md).
 
    ![Métricas](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Para alternar entre recursos para o qual pretende monitorizar métricas, clique nas **recursos** caixa novamente e repita este passo.
1. Atribua o gráfico (opcionalmente), um nome (editar o nome ao premir lápis na parte superior).
1. Adicione métricas que pretende ver.

    ![Métricas](media/media-services-metrics/metrics03.png)
1. Pode afixar o gráfico ao dashboard.

## <a name="view-metrics-with-azure-cli"></a>Ver métricas com a CLI do Azure

Para obter métricas de "Saída" com a CLI, deve executar o seguinte `az monitor metrics` comando da CLI:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Para obter outras métricas, substitua "Saída" para o nome da métrica que está interessado.

## <a name="see-also"></a>Consulte também

* [Métricas de Monitor do Azure](../../azure-monitor/platform/data-platform.md)
* [Criar, ver e gerir alertas de métricas através do Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Passos Seguintes

[Registos de diagnóstico](media-services-diagnostic-logs-howto.md)
