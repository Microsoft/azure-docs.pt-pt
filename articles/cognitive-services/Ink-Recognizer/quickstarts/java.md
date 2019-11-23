---
title: 'Início rápido: reconhecer tinta digital com a API REST do reconhecedor de tinta e o Java'
titleSuffix: Azure Cognitive Services
description: Use a API do reconhecedor de tinta para começar a reconhecer os traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: e8cd6a4acbd1492bba1c9e88b523a7c44a44f009
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996838"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Início rápido: reconhecer tinta digital com a API REST do reconhecedor de tinta e o Java

Use este guia de início rápido para começar a usar a API do reconhecedor de tinta em traços de tinta digital. Esse aplicativo Java envia uma solicitação de API que contém dados de traço de tinta formatados para JSON e obtém a resposta.

Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamaria a API de um aplicativo de tinta digital. Este início rápido envia dados de traço de tinta para a seguinte amostra manuscrita de um arquivo JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte para este guia de início rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Pré-requisitos

- O [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.

- Importar essas bibliotecas do repositório Maven
    - [JSON no pacote Java](https://mvnrepository.com/artifact/org.json/json)
    - Pacote [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Os dados de traço de tinta de exemplo para este guia de início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso de reconhecimento de tinta

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Crie um novo projeto Java no seu IDE ou editor favorito e importe as seguintes bibliotecas.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Crie variáveis para a chave de assinatura, o ponto de extremidade e o arquivo JSON. O ponto de extremidade será acrescentado posteriormente ao URI do reconhecedor de tinta.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma nova função chamada `sendRequest()` que usa as variáveis criadas acima. Em seguida, execute as etapas a seguir.

2. Crie um objeto `CloseableHttpClient` que possa enviar solicitações para a API. Envie a solicitação para um objeto de solicitação de `HttpPut` combinando seu ponto de extremidade e a URL do reconhecedor de tinta.

3. Use a função `setHeader()` da solicitação para definir o cabeçalho de `Content-Type` como `application/json`e adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

4. Use a função `setEntity()` da solicitação para os dados a serem enviados.   

5. Use a função `execute()` do cliente para enviar a solicitação e salvá-la em um objeto `CloseableHttpResponse`. 

6. Crie um objeto `HttpEntity` para armazenar o conteúdo da resposta. Obtenha o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorne-a.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Enviar uma solicitação de reconhecimento de tinta

Crie um método chamado `recognizeInk()` para reconhecer seus dados de traço de tinta. Chame o método de `sendRequest()` criado acima com seu ponto de extremidade, URL, chave de assinatura e dados JSON. Obtenha o resultado e imprima-o no console do.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Carregar seus dados de tinta digital e enviar a solicitação

1. No método principal do seu aplicativo, leia no arquivo JSON que contém os dados que serão adicionados às solicitações.

2. Chame a função de reconhecimento de tinta criada acima.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e exibir a resposta

Execute a aplicação. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API de reconhecimento de tinta funciona em um aplicativo de escrita digital, dê uma olhada nos seguintes aplicativos de exemplo no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
