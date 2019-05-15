---
title: 'Início rápido: Detetar anomalias nos seus dados de séries de tempo com a API de REST de detetor de anomalias e C# | Documentos da Microsoft'
description: Utilize a API de detetor de anomalias para detetar anomalias na sua série de dados como um lote ou nos dados de transmissão em fluxo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 04b331f3b63ad6400b4bb8efcd053d04ac88989b
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595837"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Início rápido: Detetar anomalias nos seus dados de séries de tempo com a API de REST de detetor de anomalias eC# 

Utilize este guia de introdução para começar a utilizar dois modos de deteção a API de detetor de anomalias para detetar anomalias nos seus dados de séries de tempo. Isso C# aplicação envia dois pedidos de API que contém dados de séries de tempo de formato JSON e obtém as respostas.

| Pedido de API                                        | Saída da aplicação                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detetar anomalias como um lote                        | A resposta JSON que contém o estado de anomalias (e outros dados) para cada ponto de dados em dados de séries de tempo e as posições de quaisquer anomalias detetadas. |
| Detectar o status de anomalias do ponto de dados mais recentes | A resposta JSON que contém o estado de anomalias (e outros dados) para o ponto de dados mais recentes nos dados de séries de tempo.                                                                                                                                         |

 Embora esse aplicativo é escrito em C#, a API é um serviço RESTful web compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição dos [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/),

- O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet. Para instalar newtonsoft como um pacote de NuGet no Visual Studio:
    
    1. Clique com o botão direito do rato no **Explorador de soluções**.
    2. Selecione **gerir pacotes NuGet**.
    3. Procure *newtonsoft* e instalar o pacote.

- Se estiver a utilizar Linux/MacOS, esta aplicação pode ser executada usando [Mono](https://www.mono-project.com/).

- Pontos de dados de séries de tempo que contém um JSON ficheiro. Os dados de exemplo para este início rápido, podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No Visual Studio, crie uma nova solução de consola e adicione os seguintes pacotes. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Crie variáveis para a chave de subscrição e o ponto final. Abaixo estão os URIs que pode utilizar para a deteção de anomalias. Estes serão anexados ao seu ponto final de serviço mais tarde para criar a API de URLs de pedido.

    |Método de deteção  |URI  |
    |---------|---------|
    |Deteção de batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Deteção no ponto de dados mais recentes     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
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

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Criar uma nova função de async chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e informações de cabeçalho usando um `HttpClient` objeto. Certifique-se de que adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho. Em seguida, crie um `StringContent` objeto para o pedido.

3. Enviar o pedido com `PostAsync()`e, em seguida, retornar a resposta.

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

## <a name="detect-anomalies-as-a-batch"></a>Detetar anomalias como um lote

1. Criar uma nova função chamada `detectAnomaliesBatch()`. Construa a solicitação e enviá-lo ao chamar o `Request()` função com o ponto final, a chave de subscrição, o URL de deteção de anomalias de batch e os dados de séries de tempo.

2. Anular a serialização do objeto JSON e gravá-lo na consola.

3. Se a resposta contém `code` campo, o código de erro e a mensagem de erro de impressão. 

4. Caso contrário, encontre as posições de anomalias no conjunto de dados. A resposta `isAnomaly` campo contém uma matriz de valores booleanos, cada qual indicando se a um ponto de dados é uma anomalia. Converter para uma matriz de cadeia com o objeto de resposta `ToObject<bool[]>()` função. Iterar por meio da matriz e imprimir o índice de qualquer `true` valores. Esses valores correspondem ao índice de pontos de dados anómalas, se for detetada qualquer.

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

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do ponto de dados mais recentes

1. Criar uma nova função chamada `detectAnomaliesLatest()`. Construa a solicitação e enviá-lo ao chamar o `Request()` função com o ponto final, a chave de subscrição, o URL de deteção de anomalias do ponto mais recente e os dados de séries de tempo.

2. Anular a serialização do objeto JSON e gravá-lo na consola.

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

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregue os dados de séries de tempo e enviar o pedido

1. No método principal de seu aplicativo, carregue os dados de série de tempo JSON com `File.ReadAllText()`. 

2. Chame as funções de deteção de anomalias criadas acima. Utilize `System.Console.ReadKey()` para manter a janela da consola aberta após executar a aplicação.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Resposta de exemplo

Uma resposta com êxito, é devolvida no formato JSON. Clique nos links abaixo para ver a resposta JSON no GitHub:
* [Resposta de deteção de batch de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Resposta de deteção de ponto mais recente do exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
