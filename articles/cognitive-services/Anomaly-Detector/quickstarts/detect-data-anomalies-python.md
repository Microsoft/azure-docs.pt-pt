---
title: 'Quickstart: Detete anomalias como um lote utilizando a API e Python do Detetor de Anomalias'
titleSuffix: Azure Cognitive Services
description: Utilize a API do Detetor de Anomalias para detetar anomalias na sua série de dados, quer como um lote, quer em dados de streaming neste arranque rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 684aba561dc50b64dd7cc564cff8e55229ce1429
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239033"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Quickstart: Detete anomalias nos dados da série de tempo utilizando a API e python do Detetor de Anomalias

Utilize este arranque rápido para começar a utilizar os dois modos de deteção da API do Detetor de Anomalias para detetar anomalias nos dados da série de tempo. Esta aplicação Python envia dois pedidos de API contendo dados da série de tempo formada jSON, e obtém as respostas.

| Pedido da API                                        | Saída de candidatura                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detetar anomalias como um lote                        | A resposta JSON contendo o estado de anomalia (e outros dados) para cada ponto de dados nos dados da série de tempo, e as posições de quaisquer anomalias detetadas. |
| Detete o estado da anomalia do último ponto de dados | A resposta JSON contendo o estado da anomalia (e outros dados) para os dados mais recentes nos dados da série de tempo.                                                                                                                                         |

 Embora esta aplicação esteja escrita em Python, a API é um serviço web RESTful compatível com a maioria dos idiomas de programação. Pode encontrar o código fonte para este arranque rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.x ou 3.x](https://www.python.org/downloads/)
- Uma chave e ponto final do detetor de anomalias
- A [biblioteca De Pedidos](https://pypi.org/project/requests/) de Python

- Um ficheiro JSON contendo pontos de dados da série de tempo. Os dados de exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detetor de Anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Crie um novo ficheiro python e adicione as seguintes importações.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Crie variáveis para a sua chave de subscrição e para o seu ponto final. Abaixo estão as URIs que pode utilizar para deteção de anomalias. Estes serão anexados ao seu ponto final de serviço mais tarde para criar os URLs de pedido API.

    |Método de deteção  |URI  |
    |---------|---------|
    |Deteção de lotes    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Deteção no último ponto de dados     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Leia no ficheiro de dados da JSON abrindo-o e utilizando `json.load()`.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Crie uma `send_request()` nova função chamada que leve as variáveis criadas acima. Em seguida, execute os seguintes passos.

2. Crie um dicionário para os cabeçalhos de pedido. Desloque o `Content-Type` para `application/json`e `Ocp-Apim-Subscription-Key` adicione a sua chave de subscrição ao cabeçalho.

3. Envie o `requests.post()`pedido usando . Combine o seu URL de deteção de ponto final e anomalia para o URL de pedido completo e inclua os seus cabeçalhos e dados de pedido json. E depois devolver a resposta.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Detetar anomalias como um lote

1. Crie um `detect_batch()` método chamado para detetar anomalias em todos os dados como um lote. Ligue `send_request()` para o método acima criado com o seu ponto final, url, chave de subscrição e dados json.

2. Ligue `json.dumps()` para o resultado para forme-lo e imprima-o para a consola.

3. Se a `code` resposta contiver campo, imprima o código de erro e a mensagem de erro.

4. Caso contrário, encontre as posições de anomalias no conjunto de dados. O campo `isAnomaly` da resposta contém um valor booleano relacionado com se um determinado ponto de dados é uma anomalia. Iterar através da lista, e `True` imprimir o índice de quaisquer valores. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontre algum.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detete o estado da anomalia do último ponto de dados

1. Crie um `detect_latest()` método chamado para determinar se o ponto de dados mais recente da sua série de tempo é uma anomalia. Ligue `send_request()` para o método acima com o seu ponto final, url, chave de subscrição e dados json. 

2. Ligue `json.dumps()` para o resultado para forme-lo e imprima-o para a consola.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>Enviar o pedido

Ligue para os métodos de deteção de anomalias acima criados.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem sucedida é devolvida em formato JSON. Clique nos links abaixo para ver a resposta JSON no GitHub:
* [Resposta de deteção de lotes de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Resposta de deteção de pontos mais recentes](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
