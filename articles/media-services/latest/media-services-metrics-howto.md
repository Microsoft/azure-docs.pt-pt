---
title: Ver métricas com monitor Azure
description: Este artigo mostra como monitorizar as métricas com as tabelas de portais Azure e Azure CLI.
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
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382941"
---
# <a name="monitor-media-services-metrics"></a>Monitorizar as métricas dos Serviços de Multimédia

[O Monitor Azure](../../azure-monitor/overview.md) permite-lhe monitorizar métricas e registos de diagnóstico que o ajudam a compreender como as suas aplicações estão a funcionar. Para uma descrição detalhada desta funcionalidade e para ver por que razão quereria utilizar métricas e registos de diagnóstico do Azure Media Services, consulte as métricas do [Monitor Media Services e os registos de diagnóstico.](media-services-metrics-diagnostic-logs.md)

O Azure Monitor fornece várias formas de interagir com métricas, incluindo mapeá-las no portal, acessá-las através da API REST, ou questioná-las usando o Azure CLI. Este artigo mostra como monitorizar as métricas com as tabelas de portais Azure e Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta dos Media Services](create-account-cli-how-to.md)
- Analisar [as métricas dos Serviços de Media e os registos de diagnóstico](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Ver métricas no portal Azure

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue na sua conta Azure Media Services e selecione **Métricas**.
1. Clique na caixa **DE RECURSOS** e selecione o recurso para o qual pretende monitorizar as métricas.

    A janela **Select a resource** aparece à direita com a lista de recursos disponíveis para si. Neste caso, vê-se:

    * &lt;Nome da conta dos Serviços de Media&gt;
    * &lt;Nome final&gt;/&lt;da conta de Media Services&gt;
    * &lt;Nome da conta de armazenamento&gt;

    Selecione o recurso e prima **Aplicar**. Para mais detalhes sobre recursos e métricas suportados, consulte [as métricas do Monitor Media Services.](media-services-metrics-diagnostic-logs.md)

    ![Métricas](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Para alternar entre os recursos para os quais pretende monitorizar as métricas, clique novamente na caixa **DERECURSOS** e repita este passo.
1. (Opcionalmente) dê um nome ao seu gráfico (edite o nome premindo o lápis na parte superior).
1. Adicione métricas que pretende ver.

    ![Métricas](media/media-services-metrics/metrics03.png)
1. Pode fixar a sua ficha no seu painel de instrumentos.

## <a name="view-metrics-with-azure-cli"></a>Ver métricas com Azure CLI

Para obter métricas de "Egress" com o Azure CLI, executaria o seguinte `az monitor metrics` comando:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Para obter outras métricas, substitua "Egress" pelo nome métrico que lhe interessa.

## <a name="see-also"></a>Consulte também

* [Métricas do Monitor Azure](../../azure-monitor/platform/data-platform.md)
* [Criar, visualizar e gerir alertas métricos utilizando o Monitor Azure.](../../azure-monitor/platform/alerts-metric.md)

## <a name="next-steps"></a>Passos seguintes

[Registos de diagnóstico](media-services-diagnostic-logs-howto.md)
