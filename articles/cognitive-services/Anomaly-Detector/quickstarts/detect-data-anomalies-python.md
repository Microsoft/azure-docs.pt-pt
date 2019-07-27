---
title: 'Início rápido: Detectar anomalias como um lote usando a API REST do detector de anomalias e o Python'
titleSuffix: Azure Cognitive Services
description: Use a API do detector de anomalias para detectar anormalidades em sua série de dados, seja como um lote ou em dados de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: f40f1b94b3e7c2732fd8bed0bc6e503277b533c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565825"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Início rápido: Detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias e o Python

Use este guia de início rápido para começar a usar os dois modos de detecção da API do detector de anomalias para detectar anomalias nos dados de série temporal. Esse aplicativo Python envia duas solicitações de API que contêm dados de série temporal formatados por JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON que contém o status de anomalia (e outros dados) para cada ponto de dados nos dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalia do último ponto de dados | A resposta JSON que contém o status de anomalia (e outros dados) para o ponto de dados mais recente nos dados de série temporal.                                                                                                                                         |

 Embora esse aplicativo seja escrito em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2. x ou 3. x](https://www.python.org/downloads/)

- A [biblioteca de solicitações](http://docs.python-requests.org) para Python

- Um arquivo JSON que contém pontos de dados de série temporal. Os dados de exemplo deste guia de início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Em seu editor de texto favorito ou IDE, crie um novo arquivo Python. Adicione as importações a seguir.

    ```python
    import requests
    import json
    ```

2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo estão os URIs que você pode usar para detecção de anomalias. Eles serão anexados ao ponto de extremidade de serviço posteriormente para criar as URLs de solicitação de API.

    |Método de detecção  |URI  |
    |---------|---------|
    |Detecção de lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no último ponto de dados     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Leia o arquivo de dados JSON abrindo-o e usando `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma nova função chamada `send_request()` que usa as variáveis criadas acima. Em seguida, execute as etapas a seguir.

2. Crie um dicionário para os cabeçalhos de solicitação. `Content-Type` Defina como `application/json`e Adicione`Ocp-Apim-Subscription-Key` sua chave de assinatura ao cabeçalho.

3. Envie a solicitação usando `requests.post()`. Combine seu ponto de extremidade e a URL de detecção de anomalias para a URL de solicitação completa e inclua seus cabeçalhos e dados de solicitação JSON. E, em seguida, retorna a resposta.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Crie um método chamado `detect_batch()` para detectar anomalias em todos os dados como um lote. Chame o `send_request()` método criado acima com seu ponto de extremidade, URL, chave de assinatura e dados JSON.

2. Chame `json.dumps()` o resultado para formatá-lo e imprima-o no console.

3. Se a resposta contiver `code` o campo, imprima o código de erro e a mensagem de erro.

4. Caso contrário, localize as posições de anomalias no conjunto de dados. O campo da `isAnomaly` resposta contém um valor booliano relacionado a se um determinado ponto de dados é uma anomalia. Itere na lista e imprima o índice de quaisquer `True` valores. Esses valores correspondem ao índice de pontos de dados anormais, se algum for encontrado.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(
            result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print(x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalia do último ponto de dados

1. Crie um método chamado `detect_latest()` para determinar se o último ponto de dados em sua série temporal é uma anomalia. Chame o `send_request()` método acima com seu ponto de extremidade, URL, chave de assinatura e dados JSON. 

2. Chame `json.dumps()` o resultado para formatá-lo e imprima-o no console.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar os dados de série temporal e enviar a solicitação

1. Carregue seus dados JSON de série temporal abrindo um manipulador de arquivo e `json.load()` usando nele. Em seguida, chame os métodos de detecção de anomalias criados acima.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção de lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo de resposta mais recente de detecção de ponto](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
