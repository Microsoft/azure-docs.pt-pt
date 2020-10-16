---
title: 'Quickstart: Detete anomalias nos dados da sua série de tempo usando o Detetor de Anomalia REST API e Java'
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a API do Detetor de Anomalias para detetar anomalias na sua série de dados, seja como um lote ou em dados de streaming.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.custom: devx-track-java
ms.author: mbullwin
ms.openlocfilehash: e23e9016b53a1d9f99809d792d710f865918a098
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019686"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Quickstart: Detete anomalias nos dados da sua série de tempo usando o Detetor de Anomalia REST API e Java

Utilize este quickstart para começar a utilizar os dois modos de deteção do Detetor de Anomalias da API para detetar anomalias nos dados da série de tempo. Esta aplicação Java envia pedidos de API contendo dados de séries de tempo formatadas com JSON e obtém as respostas.

| Pedido da API                                        | Saída da aplicação                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detetar anomalias como um lote                        | A resposta JSON contendo o estado de anomalia (e outros dados) para cada ponto de dados nos dados da série de tempo, e as posições de quaisquer anomalias detetadas. |
| Detetar o estado de anomalia do último ponto de dados | A resposta JSON contendo o estado de anomalia (e outros dados) para o último ponto de dados nos dados da série de tempo.   |
| Detetar pontos de mudança que marcam novas tendências de dados | A resposta JSON que contém os pontos de alteração detetados nos dados da série de tempo. |

Embora esta aplicação esteja escrita em Java, a API é um serviço web RESTful compatível com a maioria das linguagens de programação. Pode encontrar o código-fonte para este arranque rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
- Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Anomalias no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    - Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
- O [Kit de Desenvolvimento de Java &trade; (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou mais tarde.
- Importe estas bibliotecas do Repositório de Maven
    - [JSON no](https://mvnrepository.com/artifact/org.json/json) pacote Java
    - [Pacote Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Um ficheiro JSON contendo pontos de dados da série de tempo. Os dados de exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Crie um novo projeto Java e importe as seguintes bibliotecas.

    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Crie variáveis para a sua chave de subscrição e para o seu ponto final. Abaixo estão os URIs que pode usar para deteção de anomalias. Estes serão anexados ao seu ponto final de serviço mais tarde para criar os URLs de pedido de API.

    |Método de deteção  |URI  |
    |---------|---------|
    |Deteção de lotes    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Deteção no último ponto de dados     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    | Deteção de pontos de alteração | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Criar uma nova função chamada `sendRequest()` que leva as variáveis criadas acima. Em seguida, execute os seguintes passos.

2. Crie um `CloseableHttpClient` objeto que possa enviar pedidos para a API. Envie o pedido a um `HttpPost` objeto de pedido combinando o seu ponto final e um URL de detetor de anomalias.

3. Utilize a função do pedido `setHeader()` para definir o `Content-Type` cabeçalho para , e adicione a `application/json` sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

4. Utilize a função do pedido `setEntity()` para os dados a enviar.

5. Use a função do cliente `execute()` para enviar o pedido e guarde-o para um `CloseableHttpResponse` objeto.

6. Crie um `HttpEntity` objeto para armazenar o conteúdo da resposta. Obtenha o conteúdo com `getEntity()` . Se a resposta não estiver vazia, devolva-a.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Detetar anomalias como um lote

1. Crie um método chamado `detectAnomaliesBatch()` para detetar anomalias ao longo dos dados como um lote. Ligue para o `sendRequest()` método acima criado com o seu ponto final, URL, chave de subscrição e dados json. Obtenha o resultado e imprima-o na consola.

2. Se a resposta `code` contiver campo, imprima o código de erro e a mensagem de erro.

3. Caso contrário, encontre as posições de anomalias no conjunto de dados. O campo da resposta `isAnomaly` contém um valor booleano relacionado com se um determinado ponto de dados é uma anomalia. Obtenha a matriz JSON, e itere através dela, imprimindo o índice de quaisquer `true` valores. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontrem.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detetar o estado de anomalia do último ponto de dados

Crie um método chamado `detectAnomaliesLatest()` para detetar o estado de anomalia do último ponto de dados no conjunto de dados. Ligue para o `sendRequest()` método acima criado com o seu ponto final, URL, chave de subscrição e dados json. Obtenha o resultado e imprima-o na consola.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]


## <a name="detect-change-points-in-the-data"></a>Detetar pontos de alteração nos dados

1. Crie um método chamado `detectChangePoints()` para detetar anomalias ao longo dos dados como um lote. Ligue para o `sendRequest()` método acima criado com o seu ponto final, URL, chave de subscrição e dados json. Obtenha o resultado e imprima-o na consola.

2. Se a resposta contiver um `code` campo, imprima o código de erro e a mensagem de erro.

3. Caso contrário, encontre as posições dos pontos de alteração no conjunto de dados. O campo da resposta `isChangePoint` contém um valor boolean que indica se um determinado ponto de dados é um ponto de mudança de tendência. Obtenha a matriz JSON e itere através dela, imprimindo o índice de quaisquer `true` valores. Estes valores correspondem aos índices de variação de tendência, se algum for encontrado.

    [!code-java[detect change points](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectChangePoint)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregue os dados da série de tempo e envie o pedido

1. No método principal da sua aplicação, leia no ficheiro JSON contendo os dados que serão adicionados aos pedidos.

2. Ligue para as duas funções de deteção de anomalias criadas acima.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem sucedida é devolvida no formato JSON. Clique nos links abaixo para ver a resposta JSON no GitHub:
* [Resposta de deteção de lote de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo da resposta de deteção de pontos mais recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Exemplo de resposta de deteção de ponto de alteração](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
