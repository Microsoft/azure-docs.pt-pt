---
title: 'Quickstart: Reconhecer tinta digital com a API e C do Reconhecimento de Tinta REST #'
titleSuffix: Azure Cognitive Services
description: Este quickstart mostra como usar a API e c# do Reconhecimento de Tinta para começar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 13a2d887b17ff319fb3a0f2bb0d5d0ff04629088
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369108"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Quickstart: Reconhecer tinta digital com a API e C do Reconhecimento de Tinta REST #

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Utilize este quickstart para começar a enviar traços de tinta digital para a API do Reconhecimento de Tinta. Esta aplicação C# envia um pedido de API contendo dados de traçado de tinta com formato JSON, e obtém a resposta.

Enquanto esta aplicação está escrita em C#, a API é um serviço web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamaria a API de uma aplicação digital de tinta. Este quickstart envia dados de traçado de tinta para a seguinte amostra manuscrita a partir de um ficheiro JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte para este arranque rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Para instalar Newtonsoft.Jscomo um pacote NuGet no estúdio Visual:
        1. Clique no gestor de **solução**
        2. Clique **em Gerir Pacotes NuGet...**
        3. Procurar `Newtonsoft.Json` e instalar o pacote
- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada utilizando [o Mono.](https://www.mono-project.com/)

- Os dados de traçado de tinta exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso de reconhecimento de tinta

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No Visual Studio, crie uma nova solução de consola e adicione os seguintes pacotes. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Crie variáveis para a sua chave de subscrição e ponto final, e o ficheiro JSON exemplo. O ponto final será posteriormente combinado com `inkRecognitionUrl` o acesso à API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Crie uma nova função async chamada `Request` que leva as variáveis criadas acima.

2. Desajei o protocolo de segurança do cliente e informações de cabeçalho usando um `HttpClient` objeto. Certifique-se de adicionar a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho. Em seguida, crie um `StringContent` objeto para o pedido.
 
3. Envie o pedido `PutAsync()` com. Se o pedido for bem sucedido, devolva a resposta.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Enviar um pedido de reconhecimento de tinta

1. Criar uma nova função chamada `recognizeInk()` . Construa o pedido e envie-o chamando a função com o `Request()` seu ponto final, chave de subscrição, o URL para a API e os dados digitais do traçado de tinta.

2. Deserialize o objeto JSON e escreva-o para a consola. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Carregue os seus dados de tinta digital

Crie uma função chamada `LoadJson()` para carregar o ficheiro JSON de dados de tinta. Use um `StreamReader` e para criar um e `JsonTextReader` `JObject` devolvê-lo.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Enviar o pedido da API

1. No método principal da sua aplicação, carregue os seus dados JSON com a função acima criada. 

2. Ligue para a `recognizeInk()` função acima criada. Utilize `System.Console.ReadKey()` para manter a janela da consola aberta depois de executar a aplicação.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Executar a aplicação e ver a resposta

Execute a aplicação. Uma resposta bem sucedida é devolvida no formato JSON. Também pode encontrar a resposta JSON no [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)


Para ver como funciona a API de Reconhecimento de Tinta numa aplicação digital de tinta, veja as seguintes aplicações de amostra no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)