---
title: 'Quickstart: Reconhecer tinta digital com a API de Reconhecimento de Tinta e Java'
titleSuffix: Azure Cognitive Services
description: Utilize a API e a Java do Reconhecimento de Tinta para começar a reconhecer traços de tinta digital neste arranque rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-java
ms.openlocfilehash: dea46a07a7357be6079c52634be8ea2ff79cc8f3
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369125"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Quickstart: Reconhecer tinta digital com a API de Reconhecimento de Tinta e Java

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Utilize este arranque rápido para começar a utilizar a API do Reconhecimento de Tinta em traços de tinta digital. Esta aplicação Java envia um pedido de API contendo dados de traçado de tinta formatados por JSON, e obtém a resposta.

Embora esta aplicação esteja escrita em Java, a API é um serviço web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamaria a API de uma aplicação digital de tinta. Este quickstart envia dados de traçado de tinta para a seguinte amostra manuscrita a partir de um ficheiro JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte para este arranque rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Pré-requisitos

- O [Kit de Desenvolvimento de Java &trade; (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou mais tarde.

- Importe estas bibliotecas do Repositório de Maven
    - [JSON no](https://mvnrepository.com/artifact/org.json/json) pacote Java
    - [Pacote Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Os dados de traçado de tinta exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso de reconhecimento de tinta

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Crie um novo projeto Java no seu IDE ou editor favorito e importe as seguintes bibliotecas.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Crie variáveis para a sua chave de subscrição, ponto final e ficheiro JSON. O ponto final será posteriormente anexado ao URI do reconhecimento de tinta.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar pedidos

1. Criar uma nova função chamada `sendRequest()` que leva as variáveis criadas acima. Em seguida, execute os seguintes passos.

2. Crie um `CloseableHttpClient` objeto que possa enviar pedidos para a API. Envie o pedido para um `HttpPut` objeto de pedido combinando o seu ponto final e o URL do Reconhecimento de Tinta.

3. Utilize a função do pedido `setHeader()` para definir o `Content-Type` cabeçalho para , e adicione a `application/json` sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

4. Utilize a função do pedido `setEntity()` para os dados a enviar.   

5. Use a função do cliente `execute()` para enviar o pedido e guarde-o para um `CloseableHttpResponse` objeto. 

6. Crie um `HttpEntity` objeto para armazenar o conteúdo da resposta. Obtenha o conteúdo com `getEntity()` . Se a resposta não estiver vazia, devolva-a.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Enviar um pedido de reconhecimento de tinta

Crie um método chamado `recognizeInk()` para reconhecer os seus dados de traçado de tinta. Ligue para o `sendRequest()` método acima criado com o seu ponto final, url, chave de subscrição e dados json. Obtenha o resultado e imprima-o na consola.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Carregue os seus dados de tinta digital e envie o pedido

1. No método principal da sua aplicação, leia no ficheiro JSON contendo os dados que serão adicionados aos pedidos.

2. Chame a função de reconhecimento de tinta criada acima.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Executar a aplicação e ver a resposta

Execute a aplicação. Uma resposta bem sucedida é devolvida no formato JSON. Também pode encontrar a resposta JSON no [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)


Para ver como funciona a API de Reconhecimento de Tinta numa aplicação digital de tinta, veja as seguintes aplicações de amostra no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)