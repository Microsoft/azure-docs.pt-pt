---
title: 'Quickstart: Detetar anomalias como um lote usando o Detetor de AnomaliaS REST API e Python'
titleSuffix: Azure Cognitive Services
description: Utilize a API do Detetor de Anomalias para detetar anomalias na sua série de dados, quer como lote, quer em dados de streaming neste arranque rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: daee5a921e25dc74d6e869ca3feb6c66d8c4ce7d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851036"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Quickstart: Detete anomalias nos dados da sua série de tempo usando o Detetor de Anomalia REST API e Python

Utilize este quickstart para começar a utilizar os dois modos de deteção do Detetor de Anomalias da API para detetar anomalias nos dados da série de tempo. Esta aplicação Python envia dois pedidos de API contendo dados de séries de tempo formatadas com JSON, e obtém as respostas.

| Pedido da API                                        | Saída da aplicação                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detetar anomalias como um lote                        | A resposta JSON contendo o estado de anomalia (e outros dados) para cada ponto de dados nos dados da série de tempo, e as posições de quaisquer anomalias detetadas. |
| Detetar o estado de anomalia do último ponto de dados | A resposta JSON contendo o estado de anomalia (e outros dados) para o último ponto de dados nos dados da série de tempo.                                                                                                                                         |

 Embora esta aplicação esteja escrita em Python, a API é um serviço web RESTful compatível com a maioria das linguagens de programação. Pode encontrar o código-fonte para este arranque rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/)
- Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Anomalias no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    - Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
- [Python 2.x ou 3.x](https://www.python.org/downloads/)
- A [biblioteca de pedidos](https://pypi.org/project/requests/) para python

- Um ficheiro JSON contendo pontos de dados da série de tempo. Os dados de exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Crie um novo ficheiro python e adicione as seguintes importações.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Crie variáveis para a sua chave de subscrição e para o seu ponto final. Abaixo estão os URIs que pode usar para deteção de anomalias. Estes serão anexados ao seu ponto final de serviço mais tarde para criar os URLs de pedido de API.

    |Método de deteção  |URI  |
    |---------|---------|
    |Deteção de lotes    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Deteção no último ponto de dados     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Leia no ficheiro de dados JSON abrindo-o e utilizando `json.load()` .

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Criar uma nova função chamada `send_request()` que leva as variáveis criadas acima. Em seguida, execute os seguintes passos.

2. Crie um dicionário para os cabeçalhos de pedido. Desaça a `Content-Type` seguinte , e adicione a chave de `application/json` subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

3. Envie o pedido utilizando `requests.post()` . Combine o seu URL de deteção de ponto final e anomalia para o URL de pedido completo, e inclua os seus cabeçalhos e dados de pedido de json. E, em seguida, devolver a resposta.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Detetar anomalias como um lote

1. Crie um método chamado `detect_batch()` para detetar anomalias ao longo dos dados como um lote. Ligue para o `send_request()` método acima criado com o seu ponto final, url, chave de subscrição e dados json.

2. Ligue `json.dumps()` para o resultado para o formatar e imprima-o na consola.

3. Se a resposta `code` contiver campo, imprima o código de erro e a mensagem de erro.

4. Caso contrário, encontre as posições de anomalias no conjunto de dados. O campo da resposta `isAnomaly` contém um valor booleano relacionado com se um determinado ponto de dados é uma anomalia. Iterar através da lista, e imprimir o índice de quaisquer `True` valores. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontrem.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detetar o estado de anomalia do último ponto de dados

1. Crie um método chamado `detect_latest()` para determinar se o último ponto de dados da sua série de tempo é uma anomalia. Ligue para o `send_request()` método acima com o seu ponto final, url, chave de subscrição e dados json. 

2. Ligue `json.dumps()` para o resultado para o formatar e imprima-o na consola.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>Enviar o pedido

Chame os métodos de deteção de anomalias criados acima.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem sucedida é devolvida no formato JSON. Clique nos links abaixo para ver a resposta JSON no GitHub:
* [Resposta de deteção de lote de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo da resposta de deteção de pontos mais recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
