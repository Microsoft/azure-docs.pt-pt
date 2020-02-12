---
title: 'Quickstart: Reconhecer tinta digital com a API REST Reconhecedor de Tinta eC#'
titleSuffix: Azure Cognitive Services
description: Este quickstart mostra como usar a API do Reconhecimento de Tinta para começar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 1cf519d8d8c25877b49bf14aefd1c0be3afa8023
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137886"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Quickstart: Reconhecer tinta digital com a API REST Reconhecedor de Tinta eC#

Utilize este arranque rápido para começar a enviar tacadas de tinta digital para a API do Reconhecimento de Tinta. Esta C# aplicação envia um pedido de API contendo dados de traçado de tinta formada jSON, e obtém a resposta.

Embora esta aplicação C#esteja escrita, a API é um serviço web RESTful compatível com a maioria dos idiomas de programação.

Normalmente, chamaria a API de uma aplicação de tinta digital. Este quickstart envia dados de traçado de tinta para a seguinte amostra manuscrita de um ficheiro JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código fonte para este arranque rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Para instalar newtonsoft.Json como um pacote NuGet em estúdio Visual:
        1. Clique certo no **Gestor de Soluções**
        2. Clique em **gerir pacotes NuGet...**
        3. Procure `Newtonsoft.Json` e instale o pacote
- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser recorreu utilizando [o Mono](https://www.mono-project.com/).

- Os dados de traçado de tinta exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso de reconhecimento de tinta

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No Visual Studio, crie uma nova solução de consola e adicione os seguintes pacotes. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Crie variáveis para a sua chave de subscrição e ponto final, e o exemplo do ficheiro JSON. O ponto final será mais tarde combinado com `inkRecognitionUrl` de acesso à API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Crie uma nova função de sincronia chamada `Request` que leve as variáveis criadas acima.

2. Detete o protocolo de segurança do cliente e a informação do cabeçalho usando um objeto `HttpClient`. Certifique-se de adicionar a sua chave de subscrição ao cabeçalho `Ocp-Apim-Subscription-Key`. Em seguida, crie um objeto `StringContent` para o pedido.
 
3. Envie o pedido com `PutAsync()`. Se o pedido for bem sucedido, devolva a resposta.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Envie um pedido de reconhecimento de tinta

1. Criar uma nova função chamada `recognizeInk()`. Construa o pedido e envie-o chamando a função `Request()` com o seu ponto final, chave de subscrição, o URL para a API e os dados de traçado de tinta digital.

2. Desserialize o objeto JSON e escreva-o para a consola. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Carregue os seus dados de tinta digital

Crie uma função chamada `LoadJson()` para carregar o ficheiro JSON dos dados de tinta. Use um `StreamReader` e `JsonTextReader` para criar uma `JObject` e devolvê-la.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Enviar o pedido da API

1. No método principal da sua aplicação, carregue os seus dados JSON com a função acima criada. 

2. Ligue para a função `recognizeInk()` criada acima. Utilize `System.Console.ReadKey()` para manter a janela da consola aberta depois de executar a aplicação.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Executar a aplicação e ver a resposta

Execute a aplicação. Uma resposta bem sucedida é devolvida em formato JSON. Também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [REST API reference (Referência da API REST)](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como funciona a API de Reconhecimento de Tinta numa aplicação de tinta digital, veja as seguintes aplicações de amostra no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
