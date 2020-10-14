---
title: 'Quickstart: Detete anomalias nos dados da sua série de tempo usando o Detetor de AnomaliaS REST API e C #'
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar os dois modos de deteção da API do Detetor de Anomalias para detetar anomalias nos dados da série de tempo.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: mbullwin
ms.custom: devx-track-csharp
ms.openlocfilehash: 919d608bda4b0a288996ad9d8d7908e5eb5ace23
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019703"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Quickstart: Detete anomalias nos dados da sua série de tempo usando o Detetor de AnomaliaS REST API e C #

Utilize este arranque rápido para começar a utilizar a API do Detetor de Anomalias para detetar anomalias nos dados da série de tempo. Esta aplicação C# envia pedidos de API contendo dados de séries de tempo formatadas com JSON e obtém as respostas.

| Pedido da API                                        | Saída da aplicação                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Detetar anomalias como um lote                        | A resposta JSON contendo o estado de anomalia (e outros dados) para cada ponto de dados nos dados da série de tempo, e as posições de quaisquer anomalias detetadas. |
| Detetar o estado de anomalia do último ponto de dados | A resposta JSON contendo o estado de anomalia (e outros dados) para o último ponto de dados nos dados da série de tempo. |
| Detetar pontos de mudança que marcam novas tendências de dados | A resposta JSON que contém os pontos de alteração detetados nos dados da série de tempo. |

Enquanto esta aplicação está escrita em C#, a API é um serviço web RESTful compatível com a maioria das linguagens de programação. Pode encontrar o código-fonte para este arranque rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
- Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Anomalias no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    - Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
- Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/)
- O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet. Para instalar Newtonsoft.Jscomo um pacote NuGet em Visual Studio:

    1. Clique em clique direito no seu projeto no **Solution Explorer.**
    2. **Selecione Gerir pacotes nuget**.
    3. Procure *Newtonsoft.Js* e instale o pacote.

- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada utilizando [o Mono.](https://www.mono-project.com/)

- Um ficheiro JSON contendo pontos de dados da série de tempo. Os dados de exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No Visual Studio, crie uma nova solução de consola e adicione os seguintes pacotes.

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Crie variáveis para a sua chave de subscrição e para o seu ponto final. Abaixo estão os URIs que pode usar para deteção de anomalias. Estes serão anexados ao seu ponto final de serviço mais tarde para criar os URLs de pedido de API.

    | Método de deteção                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Deteção de lotes                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Deteção no último ponto de dados | `/anomalydetector/v1.0/timeseries/last/detect`   |
    | Deteção de pontos de alteração | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Crie uma nova função async chamada `Request` que leva as variáveis criadas acima.

2. Desajei o protocolo de segurança do cliente e informações de cabeçalho usando um `HttpClient` objeto. Certifique-se de adicionar a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho. Em seguida, crie um `StringContent` objeto para o pedido.

3. Envie o pedido com `PostAsync()` , e, em seguida, devolva a resposta.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Detetar anomalias como um lote

1. Criar uma nova função chamada `detectAnomaliesBatch()` . Construa o pedido e envie-o chamando a função com o `Request()` seu ponto final, chave de subscrição, o URL para deteção de anomalias de lote e os dados da série de tempo.

2. Deserialize o objeto JSON e escreva-o para a consola.

3. Se a resposta `code` contiver campo, imprima o código de erro e a mensagem de erro.

4. Caso contrário, encontre as posições de anomalias no conjunto de dados. O campo da resposta `isAnomaly` contém uma matriz de valores boolean, cada um dos quais indica se um ponto de dados é uma anomalia. Converta isto numa matriz de cordas com a função do objeto de `ToObject<bool[]>()` resposta. Iterar através da matriz, e imprimir o índice de quaisquer `true` valores. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontrem.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detetar o estado de anomalia do último ponto de dados

1. Criar uma nova função chamada `detectAnomaliesLatest()` . Construa o pedido e envie-o chamando a função com o `Request()` seu ponto final, a chave de subscrição, o URL para a deteção de anomalias de ponto mais recente e os dados da série de tempo.

2. Deserialize o objeto JSON e escreva-o para a consola.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="detect-change-points-in-the-data"></a>Detetar pontos de alteração nos dados

1. Criar uma nova função chamada `detectChangePoints()` . Construa o pedido e envie-o chamando a função com o `Request()` seu ponto final, o URL para deteção de anomalias de lote, a sua chave de subscrição e os dados da série de tempo.

2. Deserialize o objeto JSON e escreva-o para a consola.

3. Se a resposta contiver um `code` campo, imprima o código de erro e a mensagem de erro.

4. Caso contrário, encontre as posições dos pontos de alteração no conjunto de dados. O campo de resposta `isChangePoint` contém uma matriz de valores boolean, cada um dos quais indica se um ponto de dados foi identificado como um ponto de mudança. Converta isto numa matriz de cordas com a função do objeto de `ToObject<bool[]>()` resposta. Iterar através da matriz, e imprimir o índice de quaisquer `true` valores. Estes valores correspondem aos índices de variação de tendência, se algum for encontrado.

    [!code-csharp[Detect change points](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectChangePoints)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregue os dados da série de tempo e envie o pedido

1. No método principal da sua aplicação, carregue os dados da série de tempo JSON com `File.ReadAllText()` .

2. Ligue para as funções de deteção de anomalias criadas acima. Utilize `System.Console.ReadKey()` para manter a janela da consola aberta depois de executar a aplicação.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem sucedida é devolvida no formato JSON. Clique nos links abaixo para ver a resposta JSON no GitHub:
* [Resposta de deteção de lote de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo da resposta de deteção de pontos mais recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Exemplo de resposta de deteção de ponto de alteração](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
