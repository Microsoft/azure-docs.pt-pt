---
title: 'Início rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta e o Java'
description: Utilize a API do reconhecedor de tinta para iniciar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 04f2ac17871bbaf0506fe18122507167b23869a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060949"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Início rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta e o Java

Utilize este guia de introdução para começar a utilizar a API do reconhecedor de tinta em traços de tinta digital. Esta aplicação de Java envia um pedido de API que contêm dados de traços de tinta formatada em JSON e obtém a resposta.

Embora esse aplicativo é escrito em Java, a API é um serviço RESTful web compatível com a maioria das linguagens de programação.

Normalmente, chamaria a API de uma aplicação de escrita a tinta digital. Este guia de introdução envia dados de traços de tinta para o seguinte exemplo de manuscrito a partir de um ficheiro JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte para este início rápido, pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Pré-requisitos

- O [Java&trade; desenvolvimento Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.

- Estas bibliotecas de importação do repositório Maven
    - [JSON em Java](https://mvnrepository.com/artifact/org.json/json) pacote
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) pacote

- Os dados de traços de tinta de exemplo para este início rápido, podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

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
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Crie variáveis para a chave de subscrição e o ponto final. Segue-se o URI que pode utilizar para reconhecimento de tinta. Ele será anexado ao seu ponto final de serviço mais tarde para criar o URL do pedido de API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Criar uma nova função chamada `sendRequest()` que usa as variáveis criadas acima. Em seguida, execute os seguintes passos.

2. Criar um `CloseableHttpClient` objeto que pode enviar pedidos para a API. Enviar o pedido para um `HttpPut` objeto de solicitação, combinando o ponto final e o URL do reconhecedor de tinta.

3. Utilizar a solicitação `setHeader()` função para definir o `Content-Type` cabeçalho para `application/json`e adicione a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho.

4. Utilize o pedido `setEntity()` função para os dados a serem enviados.   

5. Utilizar o cliente `execute()` funcionar para enviar o pedido e guardá-lo para um `CloseableHttpResponse` objeto. 

6. Criar um `HttpEntity` objeto para armazenar o conteúdo de resposta. Obter o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorná-lo.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
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
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Enviar um pedido de reconhecimento de tinta

Criar um método chamado `recognizeInk()` reconhecer os dados de traços de tinta. Chamar o `sendRequest()` método criado acima com o seu ponto final, o url, a chave de subscrição e a dados json. Obter o resultado e imprimi-lo na consola.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Carregar os dados de tinta digital e enviar o pedido

1. No método principal do seu aplicativo, o ficheiro JSON que contém os dados que serão adicionados aos pedidos de leitura.

2. Chame a função de reconhecimento de tinta criada acima.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e ver a resposta

Execute a aplicação. Uma resposta com êxito, é devolvida no formato JSON. Também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API de reconhecimento de tinta funciona num aplicativo de escrita a tinta digital, veja as seguintes aplicações de exemplo no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
