---
title: 'Início rápido: reconhecer tinta digital com a API REST do reconhecedor de tinta e o Node. js'
titleSuffix: Azure Cognitive Services
description: Use a API do reconhecedor de tinta para começar a reconhecer os traços de tinta digital neste guia de início rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: a37f2b7044fcba04ca18093aa73563961e9e35de
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448131"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Início rápido: reconhecer tinta digital com a API REST do reconhecedor de tinta e o JavaScript

Use este guia de início rápido para começar a usar a API do reconhecedor de tinta em traços de tinta digital. Esse aplicativo JavaScript envia uma solicitação de API que contém dados de traço de tinta formatados para JSON e exibe a resposta.

Embora esse aplicativo seja escrito em JavaScript e seja executado em seu navegador da Web, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamaria a API de um aplicativo de tinta digital. Este início rápido envia dados de traço de tinta para a seguinte amostra manuscrita de um arquivo JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte para este guia de início rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador da Web
- Os dados de traço de tinta de exemplo para este guia de início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso de reconhecimento de tinta

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Em seu IDE ou editor favorito, crie um novo arquivo de `.html`. Em seguida, adicione o HTML básico a ele para o código que adicionaremos mais tarde.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. Na marca `<body>`, adicione o seguinte HTML:
    1. Duas áreas de texto para exibir a solicitação e a resposta JSON.
    2. Um botão para chamar a função `recognizeInk()` que será criada posteriormente.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Carregar os dados JSON de exemplo

1. Na marca `<script>`, crie uma variável para o sampleJson. Em seguida, crie uma função JavaScript denominada `openFile()` que abre um explorador de arquivos para que você possa selecionar o arquivo JSON. Quando o botão de `Recognize ink` for clicado, ele chamará essa função e começará a ler o arquivo.
2. Use a função `onload()` de um `FileReader` do objeto para processar o arquivo de forma assíncrona. 
    1. Substitua qualquer `\n` ou `\r` caracteres no arquivo por uma cadeia de caracteres vazia. 
    2. Use `JSON.parse()` para converter o texto em JSON válido
    3. Atualize a caixa de texto `request` no aplicativo. Use `JSON.stringify()` para formatar a cadeia de caracteres JSON. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Enviar uma solicitação para a API do reconhecedor de tinta

1. Na marca `<script>`, crie uma função chamada `recognizeInk()`. Essa função chamará posteriormente a API e atualizará a página com a resposta. Adicione o código das etapas a seguir nessa função. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Crie variáveis para a URL do ponto de extremidade, a chave de assinatura e o JSON de exemplo. Em seguida, crie um objeto `XMLHttpRequest` para enviar a solicitação de API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Crie a função de retorno para o objeto `XMLHttpRequest`. Essa função analisará a resposta da API de uma solicitação bem-sucedida e a exibirá no aplicativo. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Crie a função de erro para o objeto de solicitação. Essa função registra o erro no console. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Crie uma função para a propriedade de `onreadystatechange` do objeto de solicitação. Quando o estado de preparação do objeto de solicitação for alterado, as funções de retorno e de erro acima serão aplicadas.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Enviar a solicitação de API. Adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key` e defina o `content-type` como `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e exibir a resposta

Esse aplicativo pode ser executado no navegador da Web. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [REST API reference (Referência da API REST)](https://go.microsoft.com/fwlink/?linkid=2089907)

Para ver como a API de reconhecimento de tinta funciona em um aplicativo de escrita digital, dê uma olhada nos seguintes aplicativos de exemplo no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
