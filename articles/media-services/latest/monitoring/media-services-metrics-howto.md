---
title: Ver métricas com monitor Azure
description: Este artigo mostra como monitorizar métricas com as tabelas do portal Azure e Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7f8aa7e8cd38de37f5d3757804f7836612ad64cb
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227760"
---
# <a name="monitor-media-services-metrics"></a>Monitorizar as métricas dos Serviços de Multimédia

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[O Azure Monitor](/azure/azure-monitor/overview) permite-lhe monitorizar métricas e registos de diagnóstico que o ajudam a compreender como as suas aplicações estão a funcionar. Para uma descrição detalhada desta funcionalidade e para compreender por que deve utilizar as métricas e registos de diagnóstico do Azure Media Services, consulte [as métricas dos Serviços de Mídia do Monitor e os registos de diagnóstico](monitor-media-services-data-reference.md).

O Azure Monitor fornece várias formas de interagir com as métricas, incluindo cartografá-las no portal, acessá-las através da API REST, ou questioná-las usando o Azure CLI. Este artigo mostra como monitorizar métricas com as tabelas do portal Azure e Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta dos Media Services](../account-create-how-to.md)
- Rever  [métricas de Serviços de Mídia monitor e registos de diagnóstico](monitor-media-services-data-reference.md)

## <a name="view-metrics-in-azure-portal"></a>Ver métricas no portal Azure

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue na sua conta Azure Media Services e selecione **Métricas**.
1. Clique na caixa **'Âmbito'** e selecione o recurso que pretende monitorizar.

    A janela **Select a scope** aparece à direita com a lista de recursos disponíveis para si. Neste caso, vê:

    * &lt;Nome da conta dos Serviços de Mídia&gt;
    * &lt;Nome de conta de serviços de &gt; / &lt; mídia streaming nome final&gt;
    * &lt;nome da conta de armazenamento&gt;

    Em seguida, selecione o recurso e prima **Aplicar**. Para obter detalhes sobre recursos e métricas apoiados, consulte [as métricas do Monitor Media Services](monitor-media-services-data-reference.md).

    > [!NOTE]
    > Para alternar entre os recursos que pretende monitorizar, clique novamente na caixa **'Fonte'** e repita este passo.

1. Opcional: dê um nome ao seu gráfico (edite o nome premindo o lápis na parte superior).
1. Adicione as métricas que deseja ver.
1. Pode fixar a sua ficha no seu painel de instrumentos.

## <a name="view-metrics-with-azure-cli"></a>Ver métricas com Azure CLI

Para obter métricas de "Egress" com Azure CLI, executaria o seguinte `az monitor metrics` comando:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Para obter outras métricas, substitua "Egress" pelo nome métrico que lhe interessa.

## <a name="see-also"></a>Ver também

- [Métricas do Azure Monitor](/azure/azure-monitor/data-platform)
- [Criar, visualizar e gerir alertas métricos utilizando o Azure Monitor](/azure/azure-monitor/alerts/alerts-metric).

## <a name="next-steps"></a>Passos seguintes

[Registos de diagnóstico](../media-services-diagnostic-logs-howto.md)
