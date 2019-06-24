---
title: 'Início rápido: Detetar anomalias como um lote usando a API de REST de detetor de anomalias e o Python | Documentos da Microsoft'
description: Utilize a API de detetor de anomalias para detetar anomalias na sua série de dados como um lote ou nos dados de transmissão em fluxo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 0ab39a7bb86671efb6258e3171e3bf7847aa241f
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341733"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Início rápido: Detetar anomalias nos seus dados de séries de tempo com a API de REST de detetor de anomalias e Python

Utilize este guia de introdução para começar a utilizar dois modos de deteção a API de detetor de anomalias para detetar anomalias nos seus dados de séries de tempo. Esta aplicação de Python envia dois pedidos de API que contém dados de séries de tempo de formato JSON e obtém as respostas.

| Pedido de API                                        | Saída da aplicação                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detetar anomalias como um lote                        | A resposta JSON que contém o estado de anomalias (e outros dados) para cada ponto de dados em dados de séries de tempo e as posições de quaisquer anomalias detetadas. |
| Detectar o status de anomalias do ponto de dados mais recentes | A resposta JSON que contém o estado de anomalias (e outros dados) para o ponto de dados mais recentes nos dados de séries de tempo.                                                                                                                                         |

 Embora esse aplicativo é escrito em Python, a API é um serviço RESTful web compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.x ou 3.x](https://www.python.org/downloads/)

- O [biblioteca de pedidos](http://docs.python-requests.org) para python

- Pontos de dados de séries de tempo que contém um JSON ficheiro. Os dados de exemplo para este início rápido, podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No IDE ou editor de texto favorito, crie um novo ficheiro de python. Adicione as seguintes importações.

    ```python
    import requests
    import json
    ```

2. Crie variáveis para a chave de subscrição e o ponto final. Abaixo estão os URIs que pode utilizar para a deteção de anomalias. Estes serão anexados ao seu ponto final de serviço mais tarde para criar a API de URLs de pedido.

    |Método de deteção  |URI  |
    |---------|---------|
    |Deteção de batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Deteção no ponto de dados mais recentes     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Ler o arquivo de dados JSON ao abri-lo e utilizar `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Criar uma nova função chamada `send_request()` que usa as variáveis criadas acima. Em seguida, execute os seguintes passos.

2. Crie um dicionário para os cabeçalhos de pedido. Definir o `Content-Type` para `application/json`e adicione a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho.

3. Enviar o pedido através de `requests.post()`. Combinar o ponto final e o URL de deteção de anomalias para todo o URL do pedido e incluir os cabeçalhos e dados de pedido do json. E, em seguida, retornar a resposta.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Detetar anomalias como um lote

1. Criar um método chamado `detect_batch()` para detetar anomalias em todo os dados como um lote. Chamar o `send_request()` método criado acima com o seu ponto final, o url, a chave de subscrição e a dados json.

2. Chamar `json.dumps()` no resultado para formatá-los e imprimi-lo na consola.

3. Se a resposta contém `code` campo, o código de erro e a mensagem de erro de impressão.

4. Caso contrário, encontre as posições de anomalias no conjunto de dados. A resposta `isAnomaly` campo contém um valor booleano relacionados ao se um ponto de dados fornecida é uma anomalia. Reiterar pela lista e imprimir o índice de qualquer `True` valores. Esses valores correspondem ao índice de pontos de dados anómalas, se for detetada qualquer.

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

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do ponto de dados mais recentes

1. Criar um método chamado `detect_latest()` para determinar se o ponto de dados mais recentes na sua série de tempo é uma anomalia. Chamar o `send_request()` método acima com o seu ponto final, o url, a chave de subscrição e a dados json. 

2. Chamar `json.dumps()` no resultado para formatá-los e imprimi-lo na consola.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregue os dados de séries de tempo e enviar o pedido

1. Carregue os dados de séries de tempo JSON abrir um manipulador de arquivo e utilizar `json.load()` nele. Em seguida, chame a anomalias métodos de deteção criados acima.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Resposta de exemplo

Uma resposta com êxito, é devolvida no formato JSON. Clique nos links abaixo para ver a resposta JSON no GitHub:
* [Resposta de deteção de batch de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Resposta de deteção de ponto mais recente do exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
