---
title: 'Início rápido: detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias eC#'
titleSuffix: Azure Cognitive Services
description: Use a API do detector de anomalias para detectar anormalidades em sua série de dados, seja como um lote ou em dados de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 76308e2167cbedae9572f1fb5037dfb394ce4b17
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483417"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Início rápido: detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias eC# 

Use este guia de início rápido para começar a usar os dois modos de detecção da API do detector de anomalias para detectar anomalias nos dados de série temporal. Esse C# aplicativo envia duas solicitações de API que contêm dados de série temporal formatados por JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON que contém o status de anomalia (e outros dados) para cada ponto de dados nos dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalia do último ponto de dados | A resposta JSON que contém o status de anomalia (e outros dados) para o ponto de dados mais recente nos dados de série temporal.                                        |

 Embora esse aplicativo seja escrito no C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. Você pode encontrar o código-fonte deste guia de início rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/),
- Uma chave de detector de anomalias e um ponto de extremidade
- O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet. Para instalar o Newtonsoft. JSON como um pacote NuGet no Visual Studio:
    
    1. Clique com o botão direito do mouse em seu projeto no **Gerenciador de soluções**.
    2. Selecione **gerenciar pacotes NuGet**.
    3. Procure *Newtonsoft. JSON* e instale o pacote.

- Se você estiver usando o linux/MacOS, esse aplicativo poderá ser executado usando [mono](https://www.mono-project.com/).

- Um arquivo JSON que contém pontos de dados de série temporal. Os dados de exemplo deste guia de início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detector de anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No Visual Studio, crie uma nova solução de console e adicione os pacotes a seguir. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo estão os URIs que você pode usar para detecção de anomalias. Eles serão anexados ao ponto de extremidade de serviço posteriormente para criar as URLs de solicitação de API.

    | Método de detecção                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Detecção de lote                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Detecção no último ponto de dados | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma nova função Async chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e as informações de cabeçalho usando um objeto `HttpClient`. Certifique-se de adicionar sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`. Em seguida, crie um objeto de `StringContent` para a solicitação.

3. Envie a solicitação com `PostAsync()`e, em seguida, retorne a resposta.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Crie uma nova função chamada `detectAnomaliesBatch()`. Construa a solicitação e envie-a chamando a função `Request()` com seu ponto de extremidade, chave de assinatura, a URL para detecção de anomalias do lote e os dados de série temporal.

2. Desserializar o objeto JSON e gravá-lo no console.

3. Se a resposta contiver `code` campo, imprima o código de erro e a mensagem de erro. 

4. Caso contrário, localize as posições de anomalias no conjunto de dados. O campo de `isAnomaly` da resposta contém uma matriz de valores Boolianos, cada um deles indica se um ponto de dados é uma anomalia. Converta-o em uma matriz de cadeia de caracteres com a função de `ToObject<bool[]>()` do objeto de resposta. Itere pela matriz e imprima o índice de quaisquer `true` valores. Esses valores correspondem ao índice de pontos de dados anormais, se algum for encontrado.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalia do último ponto de dados

1. Crie uma nova função chamada `detectAnomaliesLatest()`. Construa a solicitação e envie-a chamando a função `Request()` com seu ponto de extremidade, chave de assinatura, a URL para detecção de anomalias de ponto mais recente e os dados de série temporal.

2. Desserializar o objeto JSON e gravá-lo no console.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar os dados de série temporal e enviar a solicitação

1. No método principal do seu aplicativo, carregue seus dados de série temporal JSON com `File.ReadAllText()`. 

2. Chame as funções de detecção de anomalias criadas acima. Use `System.Console.ReadKey()` para manter a janela do console aberta após a execução do aplicativo.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção de lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo de resposta mais recente de detecção de ponto](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
