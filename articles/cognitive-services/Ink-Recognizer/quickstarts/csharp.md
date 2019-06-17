---
title: 'Início rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta eC#'
description: Utilize a API do reconhecedor de tinta para iniciar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: d661d6eca6e4916946944c48cc2e5411aeaf8f14
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060987"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Início rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta eC#

Utilize este guia de introdução para começar a enviar de traços de tinta digital para a API do reconhecedor de tinta. Isso C# aplicação envia um pedido de API que contêm dados de traços de tinta formatada em JSON e obtém a resposta.

Embora esse aplicativo é escrito em C#, a API é um serviço RESTful web compatível com a maioria das linguagens de programação.

Normalmente, chamaria a API de uma aplicação de escrita a tinta digital. Este guia de introdução envia dados de traços de tinta para o seguinte exemplo de manuscrito a partir de um ficheiro JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte para este início rápido, pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Para instalar newtonsoft como um pacote de NuGet no Visual studio:
        1. Clique com o botão direito do rato sobre o **Manager da solução**
        2. Clique em **gerir pacotes NuGet...**
        3. Procure `Newtonsoft.Json` e instalar o pacote
- Se estiver a utilizar Linux/MacOS, esta aplicação pode ser executada usando [Mono](https://www.mono-project.com/).

- Os dados de traços de tinta de exemplo para este início rápido, podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


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
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Crie variáveis para a chave de subscrição e o ponto final. Segue-se o URI que pode utilizar para reconhecimento de tinta. Ele será anexado ao seu ponto final de serviço mais tarde para criar a API do URl do pedido.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Criar uma nova função de async chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e informações de cabeçalho usando um `HttpClient` objeto. Certifique-se de que adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho. Em seguida, crie um `StringContent` objeto para o pedido.
 
3. Enviar o pedido com `PutAsync()`. Se a solicitação for bem-sucedida, devolve a resposta.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Enviar um pedido de reconhecimento de tinta

1. Criar uma nova função chamada `recognizeInk()`. Construa a solicitação e enviá-lo ao chamar o `Request()` função com o ponto de final, a chave de subscrição, o URL para a API e os dados de traços de tinta digital.

2. Anular a serialização do objeto JSON e gravá-lo na consola. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Carregue os dados de tinta digital

Criar uma função chamada `LoadJson()` ao carregar o ficheiro JSON de dados de tinta. Utilize um `StreamReader` e `JsonTextReader` para criar um `JObject` e retorná-lo.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Enviar o pedido de API

1. No método principal da sua aplicação, carregar os dados JSON com a função criada acima. 

2. Chamar o `recognizeInk()` função criada acima. Utilize `System.Console.ReadKey()` para manter a janela da consola aberta após executar a aplicação.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e ver a resposta

Execute a aplicação. Uma resposta com êxito, é devolvida no formato JSON. Também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API de reconhecimento de tinta funciona num aplicativo de escrita a tinta digital, veja as seguintes aplicações de exemplo no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
