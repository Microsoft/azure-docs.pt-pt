---
title: 'Quickstart: Reconhecer tinta digital com o Reconhecimento de Tinta REST API e C #'
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
ms.openlocfilehash: c24d055f1904453d2f512a278f00e23c6fea1d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371382"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Quickstart: Reconhecer tinta digital com o Reconhecimento de Tinta REST API e C #

Utilize este arranque rápido para começar a enviar tacadas de tinta digital para a API do Reconhecimento de Tinta. Esta aplicação C# envia um pedido de API contendo dados de traçado de tinta formada jSON, e obtém a resposta.

Embora esta aplicação esteja escrita em C#, a API é um serviço web RESTful compatível com a maioria dos idiomas de programação.

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

- Os dados de traçado de tinta exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso de reconhecimento de tinta

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No Visual Studio, crie uma nova solução de consola e adicione os seguintes pacotes. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Crie variáveis para a sua chave de subscrição e ponto final, e o exemplo do ficheiro JSON. O ponto final será `inkRecognitionUrl` mais tarde combinado com o acesso à API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Crie uma nova `Request` função de assinos chamada que leve as variáveis criadas acima.

2. Detete o protocolo de segurança `HttpClient` do cliente e a informação do cabeçalho usando um objeto. Certifique-se de adicionar a `Ocp-Apim-Subscription-Key` sua chave de subscrição ao cabeçalho. Em seguida, crie um `StringContent` objeto para o pedido.
 
3. Envie o `PutAsync()`pedido com . Se o pedido for bem sucedido, devolva a resposta.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Envie um pedido de reconhecimento de tinta

1. Criar uma nova `recognizeInk()`função chamada . Construa o pedido e `Request()` envie-o chamando a função com o seu ponto final, chave de subscrição, o URL para a API e os dados de traçado de tinta digital.

2. Desserialize o objeto JSON e escreva-o para a consola. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Carregue os seus dados de tinta digital

Crie uma `LoadJson()` função chamada para carregar o ficheiro JSON dos dados de tinta. Use `StreamReader` um `JsonTextReader` e `JObject` para criar um e devolvê-lo.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Enviar o pedido da API

1. No método principal da sua aplicação, carregue os seus dados JSON com a função acima criada. 

2. Ligue `recognizeInk()` para a função criada acima. Utilize `System.Console.ReadKey()` para manter a janela da consola aberta depois de executar a aplicação.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Executar a aplicação e ver a resposta

Execute a aplicação. Uma resposta bem sucedida é devolvida em formato JSON. Também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como funciona a API de Reconhecimento de Tinta numa aplicação de tinta digital, veja as seguintes aplicações de amostra no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
