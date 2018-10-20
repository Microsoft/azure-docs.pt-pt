---
title: Como utilizar a API de localizador de anomalias com cURL - serviços cognitivos da Microsoft | Documentos da Microsoft
description: Obtenha informações para ajudá-lo a rapidamente começar a utilizar cURL e a API de localizador de anomalias nos serviços cognitivos.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 9f47a49b41156967e0d4ef1c729a6bbb1cca1770
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456308"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Utilizar a API de localizador de anomalias com cURL

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Este artigo fornece informações e exemplos de código para ajudá-lo a rapidamente começar a utilizar a API de localizador de anomalias com o cURL para realizar a tarefa de obter o resultado de anomalias de dados de séries de tempo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Obtendo pontos de anomalias com a API de localizador de anomalias com cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemplo de dados de séries de tempo

Segue-se o exemplo do tempo de pontos de dados de série.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Analisar dados e obter pontos de anomalias exemplo cURL

Seguem-se os passos para utilizar o exemplo.

1. Substitua o valor `[YOUR_SUBSCRIPTION_KEY]` pela sua chave de subscrição válida.
2. Substitua o `[YOUR_REGION]` para utilizar a localização onde obteve as chaves de subscrição.
3. Substitua o `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` com o exemplo ou seus próprios pontos de dados.
4. Execute e verificar a resposta.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Resposta de exemplo
O JSON devolve uma resposta de êxito. Resposta de exemplo é o seguinte: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
