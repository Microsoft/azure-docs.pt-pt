---
title: Como utilizar a API de localizador de anomalias com Python - serviços cognitivos da Microsoft | Documentos da Microsoft
description: Exemplos de código e informações de GET para ajudá-lo a rapidamente começar a utilizar o Explorador de anomalias com Python nos serviços cognitivos.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: df894fee3328b2199b8f7436adabfd3db62f4004
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737132"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Utilizar o Explorador de anomalias API com Python

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Este artigo fornece informações e exemplos de código para ajudá-lo a rapidamente começar a utilizar a API de localizador de anomalias com o Python para realizar a tarefa de obter o resultado de anomalias para dados de séries de tempo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Obtendo pontos de anomalias com a API de localizador de anomalias com Python 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemplo de dados de séries de tempo

Segue-se o exemplo do tempo de pontos de dados de série.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Analisar dados e obter o exemplo de Python de pontos de anomalias

Certifique-se de ter instalado o python3, em seguida, crie um ficheiro executável de python com o nome detect.py. No detect.py, deve incluir o código abaixo. Antes de executar o código, não se esqueça de substituir o `[YOUR_SUBSCRIPTION_KEY]` valor com a sua chave de subscrição válido.
Substitua o `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` com seus pontos de dados.

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>Resposta de exemplo

O JSON devolve uma resposta de êxito. Segue-se a resposta de exemplo.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicação Python](../tutorials/python-tutorial.md)
