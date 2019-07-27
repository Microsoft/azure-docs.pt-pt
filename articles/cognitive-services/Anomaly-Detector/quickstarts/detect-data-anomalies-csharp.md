---
title: 'Início rápido: Detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias eC#'
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
ms.openlocfilehash: 97efa5cd91646809178d685ca51e29ef2fda7c0d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564730"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Início rápido: Detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias eC# 

Use este guia de início rápido para começar a usar os dois modos de detecção da API do detector de anomalias para detectar anomalias nos dados de série temporal. Esse C# aplicativo envia duas solicitações de API que contêm dados de série temporal formatados por JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON que contém o status de anomalia (e outros dados) para cada ponto de dados nos dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalia do último ponto de dados | A resposta JSON que contém o status de anomalia (e outros dados) para o ponto de dados mais recente nos dados de série temporal.                                                                                                                                         |

 Embora esse aplicativo seja escrito no C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/),

- O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet. Para instalar o Newtonsoft. JSON como um pacote NuGet no Visual Studio:
    
    1. Clique com o botão direito do mouse em seu projeto no **Gerenciador de soluções**.
    2. Selecione **gerenciar pacotes NuGet**.
    3. Procure *Newtonsoft. JSON* e instale o pacote.

- Se você estiver usando o linux/MacOS, esse aplicativo poderá ser executado usando [mono](https://www.mono-project.com/).

- Um arquivo JSON que contém pontos de dados de série temporal. Os dados de exemplo deste guia de início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No Visual Studio, crie uma nova solução de console e adicione os pacotes a seguir. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo estão os URIs que você pode usar para detecção de anomalias. Eles serão anexados ao ponto de extremidade de serviço posteriormente para criar as URLs de solicitação de API.

    |Método de detecção  |URI  |
    |---------|---------|
    |Detecção de lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no último ponto de dados     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma nova função Async chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e as informações de `HttpClient` cabeçalho usando um objeto. Certifique-se de adicionar sua chave de assinatura `Ocp-Apim-Subscription-Key` ao cabeçalho. Em seguida, `StringContent` crie um objeto para a solicitação.

3. Envie a solicitação com `PostAsync()`e, em seguida, retorne a resposta.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Crie uma nova função chamada `detectAnomaliesBatch()`. Construa a solicitação e envie-a chamando a `Request()` função com seu ponto de extremidade, chave de assinatura, a URL para detecção de anomalias do lote e os dados de série temporal.

2. Desserializar o objeto JSON e gravá-lo no console.

3. Se a resposta contiver `code` o campo, imprima o código de erro e a mensagem de erro. 

4. Caso contrário, localize as posições de anomalias no conjunto de dados. O campo da `isAnomaly` resposta contém uma matriz de valores Boolianos, cada um deles indica se um ponto de dados é uma anomalia. Converta-o em uma matriz de cadeia de caracteres `ToObject<bool[]>()` com a função do objeto de resposta. Itere pela matriz e imprima o índice de quaisquer `true` valores. Esses valores correspondem ao índice de pontos de dados anormais, se algum for encontrado.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalia do último ponto de dados

1. Crie uma nova função chamada `detectAnomaliesLatest()`. Construa a solicitação e envie-a chamando a `Request()` função com seu ponto de extremidade, chave de assinatura, a URL para detecção de anomalias de ponto mais recente e os dados de série temporal.

2. Desserializar o objeto JSON e gravá-lo no console.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar os dados de série temporal e enviar a solicitação

1. No método principal do seu aplicativo, carregue seus dados de série temporal JSON com `File.ReadAllText()`. 

2. Chame as funções de detecção de anomalias criadas acima. Use `System.Console.ReadKey()` para manter a janela do console aberta depois de executar o aplicativo.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção de lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo de resposta mais recente de detecção de ponto](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
