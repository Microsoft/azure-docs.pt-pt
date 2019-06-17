---
title: 'Início rápido: Detetar anomalias nos seus dados de série de tempo com a API de REST de detetor de anomalias e o Java | Documentos da Microsoft'
description: Utilize a API de detetor de anomalias para detetar anomalias na sua série de dados como um lote ou nos dados de transmissão em fluxo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 04ace16559a6f5b747bc735aa89265d2962a32b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073227"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Início rápido: Detetar anomalias nos seus dados de séries de tempo com a API de REST de detetor de anomalias e Java

Utilize este guia de introdução para começar a utilizar dois modos de deteção a API de detetor de anomalias para detetar anomalias nos seus dados de séries de tempo. Esta aplicação de Java envia dois pedidos de API que contém dados de séries de tempo de formato JSON e obtém as respostas.

| Pedido de API                                        | Saída da aplicação                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detetar anomalias como um lote                        | A resposta JSON que contém o estado de anomalias (e outros dados) para cada ponto de dados em dados de séries de tempo e as posições de quaisquer anomalias detetadas. |
| Detectar o status de anomalias do ponto de dados mais recentes | A resposta JSON que contém o estado de anomalias (e outros dados) para o ponto de dados mais recentes nos dados de séries de tempo.                                                                                                                                         |

 Embora esse aplicativo é escrito em Java, a API é um serviço RESTful web compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- O [Java&trade; desenvolvimento Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.

- Estas bibliotecas de importação do repositório Maven
    - [JSON em Java](https://mvnrepository.com/artifact/org.json/json) pacote
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) pacote

- Pontos de dados de séries de tempo que contém um JSON ficheiro. Os dados de exemplo para este início rápido, podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

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

2. Crie variáveis para a chave de subscrição e o ponto final. Abaixo estão os URIs que pode utilizar para a deteção de anomalias. Estes serão anexados ao seu ponto final de serviço mais tarde para criar a API de URLs de pedido.

    |Método de deteção  |URI  |
    |---------|---------|
    |Deteção de batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Deteção no ponto de dados mais recentes     | `/anomalydetector/v1.0/timeseries/last/detect`        |

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

3. Ler o arquivo de dados JSON.

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Criar uma nova função chamada `sendRequest()` que usa as variáveis criadas acima. Em seguida, execute os seguintes passos.

2. Criar um `CloseableHttpClient` objeto que pode enviar pedidos para a API. Enviar o pedido para um `HttpPost` objeto de solicitação, combinando o ponto final e um URL de detetor de anomalias.

3. Utilizar a solicitação `setHeader()` função para definir o `Content-Type` cabeçalho para `application/json`e adicione a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho.

4. Utilize o pedido `setEntity()` função para os dados a serem enviados.

5. Utilizar o cliente `execute()` funcionar para enviar o pedido e guardá-lo para um `CloseableHttpResponse` objeto.

6. Criar um `HttpEntity` objeto para armazenar o conteúdo de resposta. Obter o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorná-lo.

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

## <a name="detect-anomalies-as-a-batch"></a>Detetar anomalias como um lote

1. Criar um método chamado `detectAnomaliesBatch()` para detetar anomalias em todo os dados como um lote. Chamar o `sendRequest()` método criado acima com o seu ponto final, o url, a chave de subscrição e a dados json. Obter o resultado e imprimi-lo na consola.

2. Se a resposta contém `code` campo, o código de erro e a mensagem de erro de impressão.

3. Caso contrário, encontre as posições de anomalias no conjunto de dados. A resposta `isAnomaly` campo contém um valor booleano relacionados ao se um ponto de dados fornecida é uma anomalia. Obter a matriz JSON e iterar através do mesmo, para imprimir o índice de qualquer `true` valores. Esses valores correspondem ao índice de pontos de dados anómalas, se for detetada qualquer.

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

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do ponto de dados mais recentes

* Criar um método chamado `detectAnomaliesLatest()` para detectar o status de anomalias do último ponto de dados no conjunto de dados. Chamar o `sendRequest()` método criado acima com o seu ponto final, o url, a chave de subscrição e a dados json. Obter o resultado e imprimi-lo na consola.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregue os dados de séries de tempo e enviar o pedido

1. No método principal do seu aplicativo, o ficheiro JSON que contém os dados que serão adicionados aos pedidos de leitura.

2. Chame as funções de deteção de dois anomalias criadas acima.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Resposta de exemplo

Uma resposta com êxito, é devolvida no formato JSON. Clique nos links abaixo para ver a resposta JSON no GitHub:
* [Resposta de deteção de batch de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Resposta de deteção de ponto mais recente do exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
