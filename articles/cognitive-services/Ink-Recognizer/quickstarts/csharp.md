---
title: 'Início rápido: reconhecer tinta digital com a API REST do reconhecedor de tinta eC#'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido mostra como usar a API do reconhecedor de tinta para começar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: c5379452449188f17b75036eb09c3ca15bae0c2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448171"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Início rápido: reconhecer tinta digital com a API REST do reconhecedor de tinta eC#

Use este guia de início rápido para começar a enviar traços de tinta digital para a API do reconhecedor de tinta. Esse C# aplicativo envia uma solicitação de API que contém dados de traço de tinta formatados para JSON e obtém a resposta.

Embora esse aplicativo seja escrito no C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamaria a API de um aplicativo de tinta digital. Este início rápido envia dados de traço de tinta para a seguinte amostra manuscrita de um arquivo JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte para este guia de início rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Para instalar o Newtonsoft. JSON como um pacote NuGet no Visual Studio:
        1. Clique com o botão direito do mouse no **Gerenciador de soluções**
        2. Clique em **gerenciar pacotes NuGet...**
        3. Pesquisar `Newtonsoft.Json` e instalar o pacote
- Se você estiver usando o linux/MacOS, esse aplicativo poderá ser executado usando o [mono](https://www.mono-project.com/).

- Os dados de traço de tinta de exemplo para este guia de início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso de reconhecimento de tinta

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No Visual Studio, crie uma nova solução de console e adicione os pacotes a seguir. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Crie variáveis para sua chave de assinatura e ponto de extremidade e o exemplo de arquivo JSON. O ponto de extremidade posteriormente será combinado com `inkRecognitionUrl` para acessar a API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma nova função Async chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e as informações de cabeçalho usando um objeto `HttpClient`. Certifique-se de adicionar sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`. Em seguida, crie um objeto de `StringContent` para a solicitação.
 
3. Envie a solicitação com `PutAsync()`. Se a solicitação for bem-sucedida, retorne a resposta.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Enviar uma solicitação de reconhecimento de tinta

1. Crie uma nova função chamada `recognizeInk()`. Construa a solicitação e envie-a chamando a função `Request()` com o ponto de extremidade, a chave de assinatura, a URL da API e os dados de traço de tinta digital.

2. Desserializar o objeto JSON e gravá-lo no console. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Carregue seus dados de tinta digital

Crie uma função chamada `LoadJson()` para carregar o arquivo JSON de dados de tinta. Use um `StreamReader` e `JsonTextReader` para criar um `JObject` e retorná-lo.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Enviar a solicitação de API

1. No método principal do seu aplicativo, carregue os dados JSON com a função criada acima. 

2. Chame a função `recognizeInk()` criada acima. Use `System.Console.ReadKey()` para manter a janela do console aberta após a execução do aplicativo.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e exibir a resposta

Execute a aplicação. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [REST API reference (Referência da API REST)](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API de reconhecimento de tinta funciona em um aplicativo de escrita digital, dê uma olhada nos seguintes aplicativos de exemplo no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
