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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 09c306b85c910e8ec2021abf15013935c44b0df3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707715"
---
# <a name="monitor-media-services-metrics"></a>Monitorizar as métricas dos Serviços de Multimédia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[O Azure Monitor](../../azure-monitor/overview.md) permite-lhe monitorizar métricas e registos de diagnóstico que o ajudam a compreender como as suas aplicações estão a funcionar. Para uma descrição detalhada desta funcionalidade e para ver por que razão pretende utilizar as métricas e registos de diagnóstico do Azure Media Services, consulte [as métricas dos Serviços de Mídia do Monitor e os registos de diagnóstico](media-services-metrics-diagnostic-logs.md).

O Azure Monitor fornece várias formas de interagir com as métricas, incluindo cartografá-las no portal, acessá-las através da API REST, ou questioná-las usando o Azure CLI. Este artigo mostra como monitorizar métricas com as tabelas do portal Azure e Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta dos Media Services](./create-account-howto.md)
- Rever  [métricas de Serviços de Mídia monitor e registos de diagnóstico](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Ver métricas no portal Azure

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue na sua conta Azure Media Services e selecione **Métricas**.
1. Clique na caixa **RECURSO** e selecione o recurso para o qual pretende monitorizar as métricas.

    A **janela Select a resource** aparece à direita com a lista de recursos disponíveis para si. Neste caso, vê:

    * &lt;Nome da conta dos Serviços de Mídia&gt;
    * &lt;Nome de conta de serviços de &gt; / &lt; mídia streaming nome final&gt;
    * &lt;nome da conta de armazenamento&gt;

    Selecione o recurso e prima **Aplicar**. Para obter detalhes sobre recursos e métricas apoiados, consulte [as métricas do Monitor Media Services](media-services-metrics-diagnostic-logs.md).

    ![Screenshot que mostra o recurso selecionado e realça o botão Aplicar.](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Para alternar entre os recursos para os quais pretende monitorizar as métricas, clique novamente na caixa **RESOURCE** e repita este passo.
1. (Opcionalmente) dê um nome ao seu gráfico (edite o nome premindo o lápis na parte superior).
1. Adicione métricas que deseja ver.

    ![Métricas](media/media-services-metrics/metrics03.png)
1. Pode fixar a sua ficha no seu painel de instrumentos.

## <a name="view-metrics-with-azure-cli"></a>Ver métricas com Azure CLI

Para obter métricas de "Egress" com Azure CLI, executaria o seguinte `az monitor metrics` comando:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Para obter outras métricas, substitua "Egress" pelo nome métrico que lhe interessa.

## <a name="see-also"></a>Consulte também

* [Métricas do Monitor Azure](../../azure-monitor/platform/data-platform.md)
* [Criar, visualizar e gerir alertas métricos utilizando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Passos seguintes

[Registos de diagnósticos](media-services-diagnostic-logs-howto.md)
