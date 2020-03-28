---
title: 'Quickstart: Detete anomalias nos dados da série de tempo utilizando o Detetor de Anomalias REST API e C #'
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
ms.openlocfilehash: b53fecad3655048a7b9d799134926b2730b16dae
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239099"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Quickstart: Detete anomalias nos dados da série de tempo utilizando o Detetor de Anomalias REST API e C # 

Utilize este arranque rápido para começar a utilizar os dois modos de deteção da API do Detetor de Anomalias para detetar anomalias nos dados da série de tempo. Esta aplicação C# envia dois pedidos de API contendo dados da série de tempo formada jSON, e obtém as respostas.

| Pedido da API                                        | Saída de candidatura                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Detetar anomalias como um lote                        | A resposta JSON contendo o estado de anomalia (e outros dados) para cada ponto de dados nos dados da série de tempo, e as posições de quaisquer anomalias detetadas. |
| Detete o estado da anomalia do último ponto de dados | A resposta JSON contendo o estado da anomalia (e outros dados) para os dados mais recentes nos dados da série de tempo.                                        |

 Embora esta aplicação esteja escrita em C#, a API é um serviço web RESTful compatível com a maioria dos idiomas de programação. Pode encontrar o código fonte para este arranque rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017 ou mais tarde,](https://visualstudio.microsoft.com/downloads/)
- Uma chave e ponto final do detetor de anomalias
- O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet. Para instalar newtonsoft.Json como um pacote NuGet no Estúdio Visual:
    
    1. Clique no seu projeto no **Solution Explorer**.
    2. Selecione **Gerir pacotes NuGet**.
    3. Procure *newtonsoft.Json* e instale o pacote.

- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada utilizando [o Mono](https://www.mono-project.com/).

- Um ficheiro JSON contendo pontos de dados da série de tempo. Os dados de exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detetor de Anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No Visual Studio, crie uma nova solução de consola e adicione os seguintes pacotes. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Crie variáveis para a sua chave de subscrição e para o seu ponto final. Abaixo estão as URIs que pode utilizar para deteção de anomalias. Estes serão anexados ao seu ponto final de serviço mais tarde para criar os URLs de pedido API.

    | Método de deteção                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Deteção de lotes                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Deteção no último ponto de dados | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Crie uma nova `Request` função de assinos chamada que leve as variáveis criadas acima.

2. Detete o protocolo de segurança `HttpClient` do cliente e a informação do cabeçalho usando um objeto. Certifique-se de adicionar a `Ocp-Apim-Subscription-Key` sua chave de subscrição ao cabeçalho. Em seguida, crie um `StringContent` objeto para o pedido.

3. Envie o `PostAsync()`pedido com, e depois devolva a resposta.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Detetar anomalias como um lote

1. Criar uma nova `detectAnomaliesBatch()`função chamada . Construa o pedido e `Request()` envie-o chamando a função com o seu ponto final, chave de subscrição, o URL para deteção de anomalias do lote e os dados da série de tempo.

2. Desserialize o objeto JSON e escreva-o para a consola.

3. Se a `code` resposta contiver campo, imprima o código de erro e a mensagem de erro. 

4. Caso contrário, encontre as posições de anomalias no conjunto de dados. O campo `isAnomaly` da resposta contém uma série de valores booleanos, cada um dos quais indica se um ponto de dados é uma anomalia. Converta isto numa matriz de `ToObject<bool[]>()` cordas com a função do objeto de resposta. Iterar através da matriz, e `true` imprimir o índice de quaisquer valores. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontre algum.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detete o estado da anomalia do último ponto de dados

1. Criar uma nova `detectAnomaliesLatest()`função chamada . Construa o pedido e `Request()` envie-o chamando a função com o seu ponto final, chave de subscrição, o URL para deteção de anomalias de pontomais recentes e os dados da série de tempo.

2. Desserialize o objeto JSON e escreva-o para a consola.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Carregue os dados da série de tempo e envie o pedido

1. No método principal da sua aplicação, carregue os `File.ReadAllText()`dados da série de tempo JSON com . 

2. Ligue para as funções de deteção de anomalias criadas acima. Utilize `System.Console.ReadKey()` para manter a janela da consola aberta depois de executar a aplicação.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem sucedida é devolvida em formato JSON. Clique nos links abaixo para ver a resposta JSON no GitHub:
* [Resposta de deteção de lotes de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Resposta de deteção de pontos mais recentes](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
