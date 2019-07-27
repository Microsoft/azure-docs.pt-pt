---
title: 'Início rápido: Detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias e o Java'
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
ms.openlocfilehash: 001d53cbd7e2a57615ea3da71d128bd210a79921
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565860"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Início rápido: Detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias e o Java

Use este guia de início rápido para começar a usar os dois modos de detecção da API do detector de anomalias para detectar anomalias nos dados de série temporal. Esse aplicativo Java envia duas solicitações de API que contêm dados de série temporal formatados por JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON que contém o status de anomalia (e outros dados) para cada ponto de dados nos dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalia do último ponto de dados | A resposta JSON que contém o status de anomalia (e outros dados) para o ponto de dados mais recente nos dados de série temporal.                                                                                                                                         |

 Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- O [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.

- Importar essas bibliotecas do repositório Maven
    - [JSON no pacote Java](https://mvnrepository.com/artifact/org.json/json)
    - Pacote [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Um arquivo JSON que contém pontos de dados de série temporal. Os dados de exemplo deste guia de início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Crie um novo projeto Java no seu IDE ou editor favorito e importe as seguintes bibliotecas.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo estão os URIs que você pode usar para detecção de anomalias. Eles serão anexados ao ponto de extremidade de serviço posteriormente para criar as URLs de solicitação de API.

    |Método de detecção  |URI  |
    |---------|---------|
    |Detecção de lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no último ponto de dados     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Ler no arquivo de dados JSON

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma nova função chamada `sendRequest()` que usa as variáveis criadas acima. Em seguida, execute as etapas a seguir.

2. Crie um `CloseableHttpClient` objeto que possa enviar solicitações para a API. Envie a solicitação a um `HttpPost` objeto de solicitação combinando seu ponto de extremidade e uma URL de detector de anomalias.

3. Use a função da `setHeader()` solicitação para definir o `Content-Type` cabeçalho como `application/json`e adicione sua chave de assinatura ao `Ocp-Apim-Subscription-Key` cabeçalho.

4. Use a função da `setEntity()` solicitação para os dados a serem enviados.

5. Use a função do `execute()` cliente para enviar a solicitação e salvá-la em um `CloseableHttpResponse` objeto.

6. Crie um `HttpEntity` objeto para armazenar o conteúdo da resposta. Obtenha o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorne-a.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        request.setEntity(new StringEntity(requestData));
        try (CloseableHttpResponse response = client.execute(request)) {
            HttpEntity respEntity = response.getEntity();
            if (respEntity != null) {
                return EntityUtils.toString(respEntity, "utf-8");
            }
        } catch (Exception respEx) {
            respEx.printStackTrace();
        }
    } catch (IOException ex) {
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Crie um método chamado `detectAnomaliesBatch()` para detectar anomalias em todos os dados como um lote. Chame o `sendRequest()` método criado acima com seu ponto de extremidade, URL, chave de assinatura e dados JSON. Obtenha o resultado e imprima-o no console do.

2. Se a resposta contiver `code` o campo, imprima o código de erro e a mensagem de erro.

3. Caso contrário, localize as posições de anomalias no conjunto de dados. O campo da `isAnomaly` resposta contém um valor booliano relacionado a se um determinado ponto de dados é uma anomalia. Obtenha a matriz JSON e itere-a, imprimindo o índice de `true` quaisquer valores. Esses valores correspondem ao índice de pontos de dados anormais, se algum for encontrado.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalia do último ponto de dados

* Crie um método chamado `detectAnomaliesLatest()` para detectar o status de anomalia do último ponto de dados no conjunto de dados. Chame o `sendRequest()` método criado acima com seu ponto de extremidade, URL, chave de assinatura e dados JSON. Obtenha o resultado e imprima-o no console do.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar os dados de série temporal e enviar a solicitação

1. No método principal do seu aplicativo, leia no arquivo JSON que contém os dados que serão adicionados às solicitações.

2. Chame as duas funções de detecção de anomalias criadas acima.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Resposta de exemplo

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção de lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo de resposta mais recente de detecção de ponto](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
