---
title: 'Quickstart: Detete anomalias nos dados da série de tempo utilizando a API e java do Detetor de Anomalias'
titleSuffix: Azure Cognitive Services
description: Aprenda a utilizar a API do Detetor de Anomalias para detetar anomalias na sua série de dados, quer como um lote, quer em dados de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239059"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Quickstart: Detete anomalias nos dados da série de tempo utilizando a API e java do Detetor de Anomalias

Utilize este arranque rápido para começar a utilizar os dois modos de deteção da API do Detetor de Anomalias para detetar anomalias nos dados da série de tempo. Esta aplicação Java envia dois pedidos de API contendo dados da série de tempo formada jSON, e obtém as respostas.

| Pedido da API                                        | Saída de candidatura                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detetar anomalias como um lote                        | A resposta JSON contendo o estado de anomalia (e outros dados) para cada ponto de dados nos dados da série de tempo, e as posições de quaisquer anomalias detetadas. |
| Detete o estado da anomalia do último ponto de dados | A resposta JSON contendo o estado da anomalia (e outros dados) para os dados mais recentes nos dados da série de tempo.                                                                                                                                         |

 Embora esta aplicação esteja escrita em Java, a API é um serviço web RESTful compatível com a maioria dos idiomas de programação. Pode encontrar o código fonte para este arranque rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Pré-requisitos

- O Kit de [Desenvolvimento java&trade; (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou mais tarde.
- Uma chave e ponto final do detetor de anomalias
- Importar estas bibliotecas do Repositório Maven
    - [JSON no](https://mvnrepository.com/artifact/org.json/json) pacote Java
    - [Pacote Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Um ficheiro JSON contendo pontos de dados da série de tempo. Os dados de exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detetor de Anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Crie um novo projeto Java e importe as seguintes bibliotecas.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Crie variáveis para a sua chave de subscrição e para o seu ponto final. Abaixo estão as URIs que pode utilizar para deteção de anomalias. Estes serão anexados ao seu ponto final de serviço mais tarde para criar os URLs de pedido API.

    |Método de deteção  |URI  |
    |---------|---------|
    |Deteção de lotes    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Deteção no último ponto de dados     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Crie uma `sendRequest()` nova função chamada que leve as variáveis criadas acima. Em seguida, execute os seguintes passos.

2. Crie `CloseableHttpClient` um objeto que possa enviar pedidos para a API. Envie o pedido `HttpPost` a um objeto de pedido combinando o seu ponto final e um URL do Detetor de Anomalias.

3. Utilize a função `setHeader()` do `Content-Type` pedido `application/json`para definir o cabeçalho `Ocp-Apim-Subscription-Key` e adicione a sua chave de subscrição ao cabeçalho.

4. Utilize a função do `setEntity()` pedido para os dados a enviar.

5. Use a função do `execute()` cliente para enviar o `CloseableHttpResponse` pedido e guarde-o para um objeto.

6. Crie `HttpEntity` um objeto para armazenar o conteúdo de resposta. Obtenha o `getEntity()`conteúdo com . Se a resposta não estiver vazia, devolva-a.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Detetar anomalias como um lote

1. Crie um `detectAnomaliesBatch()` método chamado para detetar anomalias em todos os dados como um lote. Ligue `sendRequest()` para o método acima criado com o seu ponto final, url, chave de subscrição e dados json. Obtenha o resultado e imprima-o para a consola.

2. Se a `code` resposta contiver campo, imprima o código de erro e a mensagem de erro.

3. Caso contrário, encontre as posições de anomalias no conjunto de dados. O campo `isAnomaly` da resposta contém um valor booleano relacionado com se um determinado ponto de dados é uma anomalia. Obtenha a matriz JSON, e iterar através `true` dela, imprimindo o índice de quaisquer valores. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontre algum.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detete o estado da anomalia do último ponto de dados

Criar um `detectAnomaliesLatest()` método chamado para detetar o estado de anomalia do último ponto de dados no conjunto de dados. Ligue `sendRequest()` para o método acima criado com o seu ponto final, url, chave de subscrição e dados json. Obtenha o resultado e imprima-o para a consola.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregue os dados da série de tempo e envie o pedido

1. No método principal da sua aplicação, leia no ficheiro JSON contendo os dados que serão adicionados aos pedidos.

2. Ligue para as duas funções de deteção de anomalias criadas acima.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem sucedida é devolvida em formato JSON. Clique nos links abaixo para ver a resposta JSON no GitHub:
* [Resposta de deteção de lotes de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Resposta de deteção de pontos mais recentes](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
