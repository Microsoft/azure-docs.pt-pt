---
title: 'Início rápido: detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias e o Java'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, saiba como usar a API do detector de anomalias para detectar anormalidades em sua série de dados como um lote ou sobre streaming de dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 3bc406b22b7e8a684713385dfd15daed99bcf977
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448929"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Início rápido: detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias e o Java

Use este guia de início rápido para começar a usar os dois modos de detecção da API do detector de anomalias para detectar anomalias nos dados de série temporal. Esse aplicativo Java envia duas solicitações de API que contêm dados de série temporal formatados por JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON que contém o status de anomalia (e outros dados) para cada ponto de dados nos dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalia do último ponto de dados | A resposta JSON que contém o status de anomalia (e outros dados) para o ponto de dados mais recente nos dados de série temporal.                                                                                                                                         |

 Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. Você pode encontrar o código-fonte deste guia de início rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Pré-requisitos

- O [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.
- Uma chave de detector de anomalias e um ponto de extremidade
- Importar essas bibliotecas do repositório Maven
    - [JSON no pacote Java](https://mvnrepository.com/artifact/org.json/json)
    - Pacote [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Um arquivo JSON que contém pontos de dados de série temporal. Os dados de exemplo deste guia de início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detector de anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Crie um novo projeto Java e importe as bibliotecas a seguir.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo estão os URIs que você pode usar para detecção de anomalias. Eles serão anexados ao ponto de extremidade de serviço posteriormente para criar as URLs de solicitação de API.

    |Método de deteção  |URI  |
    |---------|---------|
    |Detecção de lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no último ponto de dados     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma nova função chamada `sendRequest()` que usa as variáveis criadas acima. Em seguida, execute as etapas a seguir.

2. Crie um objeto `CloseableHttpClient` que possa enviar solicitações para a API. Envie a solicitação para um objeto de solicitação de `HttpPost` combinando seu ponto de extremidade e uma URL de detector de anomalias.

3. Use a função `setHeader()` da solicitação para definir o cabeçalho de `Content-Type` como `application/json`e adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

4. Use a função `setEntity()` da solicitação para os dados a serem enviados.

5. Use a função `execute()` do cliente para enviar a solicitação e salvá-la em um objeto `CloseableHttpResponse`.

6. Crie um objeto `HttpEntity` para armazenar o conteúdo da resposta. Obtenha o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorne-a.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Crie um método chamado `detectAnomaliesBatch()` para detectar anomalias em todos os dados como um lote. Chame o método de `sendRequest()` criado acima com seu ponto de extremidade, URL, chave de assinatura e dados JSON. Obtenha o resultado e imprima-o no console do.

2. Se a resposta contiver `code` campo, imprima o código de erro e a mensagem de erro.

3. Caso contrário, localize as posições de anomalias no conjunto de dados. O campo de `isAnomaly` da resposta contém um valor booliano relacionado a se um determinado ponto de dados é uma anomalia. Obtenha a matriz JSON e itere-a, imprimindo o índice de quaisquer `true` valores. Esses valores correspondem ao índice de pontos de dados anormais, se algum for encontrado.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalia do último ponto de dados

Crie um método chamado `detectAnomaliesLatest()` para detectar o status de anomalia do último ponto de dados no conjunto de dados. Chame o método de `sendRequest()` criado acima com seu ponto de extremidade, URL, chave de assinatura e dados JSON. Obtenha o resultado e imprima-o no console do.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar os dados de série temporal e enviar a solicitação

1. No método principal do seu aplicativo, leia no arquivo JSON que contém os dados que serão adicionados às solicitações.

2. Chame as duas funções de detecção de anomalias criadas acima.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção de lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo de resposta mais recente de detecção de ponto](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
