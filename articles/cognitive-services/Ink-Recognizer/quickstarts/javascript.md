---
title: 'Início rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta e o node. js'
titleSuffix: Azure Cognitive Services
description: Utilize a API do reconhecedor de tinta para iniciar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 7e158b0ae27780eeecb1ee7948087bf59b1502e1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721263"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Início rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta e o JavaScript

Utilize este guia de introdução para começar a utilizar a API do reconhecedor de tinta em traços de tinta digital. Esta aplicação JavaScript envia um pedido de API que contêm dados de traços de tinta formatada em JSON e exibe a resposta.

Embora esse aplicativo é escrito em Javascript e é executado no seu navegador da web, a API é um serviço RESTful web compatível com a maioria das linguagens de programação.

Normalmente, chamaria a API de uma aplicação de escrita a tinta digital. Este guia de introdução envia dados de traços de tinta para o seguinte exemplo de manuscrito a partir de um ficheiro JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte para este início rápido, pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador da web
- Os dados de traços de tinta de exemplo para este início rápido, podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No seu IDE favorito ou um editor, criar um novo `.html` ficheiro. Em seguida, adicione o HTML básico para o mesmo para o código, que vamos adicionar mais tarde.
    
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

2. Dentro do `<body>` etiqueta, adicione o seguinte html:
    1. Duas áreas de texto para exibir o JSON de solicitação e resposta.
    2. Um botão para chamar o `recognizeInk()` função que será possível criar mais tarde.
    
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

## <a name="load-the-example-json-data"></a>Carregar o dados JSON de exemplo

1. Dentro do `<script>` Etiquetar, crie uma variável para o sampleJson. Em seguida, crie uma função JavaScript chamada `openFile()` que abre um Explorador de ficheiros, pelo que pode selecionar o ficheiro JSON. Quando o `Recognize ink` botão é clicado, irá chamar essa função e começar a ler o ficheiro.
2. Utilize um `FileReader` do objeto `onload()` função para processar o ficheiro de forma assíncrona. 
    1. Substituir alguma `\n` ou `\r` carateres no ficheiro com uma cadeia vazia. 
    2. Utilize `JSON.parse()` para converter o texto JSON válido
    3. Atualização do `request` caixa de texto no aplicativo. Utilize `JSON.stringify()` para formatar a cadeia de caracteres do JSON. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Enviar um pedido para a API do reconhecedor de tinta

1. Dentro de `<script>` Etiquetar, crie uma função chamada `recognizeInk()`. Esta função será mais tarde chamar a API e atualizar a página com a resposta. Adicione o código dos seguintes passos dentro desta função. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Crie variáveis para o URL de ponto final, a chave de subscrição e JSON de exemplo. Em seguida, crie um `XMLHttpRequest` objeto para enviar o pedido de API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Criar a função de retorno para o `XMLHttpRequest` objeto. Esta função irá analisar a resposta de API a partir de um pedido com êxito e apresentá-lo no aplicativo. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Crie a função de erro para o objeto de solicitação. Esta função regista o erro para a consola. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Criar uma função para o objeto de solicitação `onreadystatechange` propriedade. Quando altera o estado de preparação para o objeto de solicitação, as funções de retorno e o erro acima serão aplicadas.
            
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
    
    5. Envie o pedido de API. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho e defina o `content-type` para `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e ver a resposta

Esta aplicação pode ser executada dentro de seu navegador da web. Uma resposta com êxito, é devolvida no formato JSON. Também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)

Para ver como a API de reconhecimento de tinta funciona num aplicativo de escrita a tinta digital, veja as seguintes aplicações de exemplo no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
