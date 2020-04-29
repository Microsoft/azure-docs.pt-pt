---
title: 'Quickstart: Reconheça tinta digital com o Reconhecimento de Tinta REST API e Node.js'
titleSuffix: Azure Cognitive Services
description: Utilize a API do Reconhecimento de Tinta para começar a reconhecer traços de tinta digital neste arranque rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: a37f2b7044fcba04ca18093aa73563961e9e35de
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448131"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Quickstart: Reconheça tinta digital com o Reconhecimento de Tinta REST API e JavaScript

Utilize este arranque rápido para começar a utilizar a API do Reconhecimento de Tinta em traços de tinta digitais. Esta aplicação JavaScript envia um pedido de API contendo dados de traçado de tinta formada jSON e exibe a resposta.

Embora esta aplicação esteja escrita no Javascript e seja executado no seu navegador web, a API é um serviço web RESTful compatível com a maioria dos idiomas de programação.

Normalmente, chamaria a API de uma aplicação de tinta digital. Este quickstart envia dados de traçado de tinta para a seguinte amostra manuscrita de um ficheiro JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código fonte para este arranque rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador web
- Os dados de traçado de tinta exemplo para este arranque rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso de reconhecimento de tinta

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. No seu IDE ou editor `.html` favorito, crie um novo ficheiro. Em seguida, adicione HTML básico para o código que adicionaremos mais tarde.
    
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

2. Dentro `<body>` da etiqueta, adicione o seguinte html:
    1. Duas áreas de texto para exibir o pedido e resposta da JSON.
    2. Um botão `recognizeInk()` para chamar a função que será criada mais tarde.
    
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

## <a name="load-the-example-json-data"></a>Carregue os dados da JSON exemplo

1. Dentro `<script>` da etiqueta, crie uma variável para a amostraJson. Em seguida, crie `openFile()` uma função JavaScript chamada que abra um explorador de ficheiros para que possa selecionar o seu ficheiro JSON. Quando `Recognize ink` o botão estiver clicado, irá ligar para esta função e começar a ler o ficheiro.
2. Utilize `FileReader` a função de `onload()` um objeto para processar o ficheiro assincronicamente. 
    1. Substitua `\n` `\r` qualquer ou caracteres no ficheiro por uma corda vazia. 
    2. Utilizar `JSON.parse()` para converter o texto em JSON válido
    3. Atualize `request` a caixa de texto na aplicação. Utilize `JSON.stringify()` para formatar a corda JSON. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Envie um pedido para a API do Reconhecimento de Tinta

1. Dentro `<script>` da etiqueta, crie uma função chamada `recognizeInk()`. Esta função irá posteriormente ligar para a API e atualizar a página com a resposta. Adicione o código a partir dos seguintes passos dentro desta função. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Crie variáveis para o seu URL de ponto final, chave de subscrição e a amostra JSON. Em seguida, crie um `XMLHttpRequest` objeto para enviar o pedido de API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Crie a função de devolução para o `XMLHttpRequest` objeto. Esta função analisará a resposta da API a partir de um pedido bem sucedido e exibi-la-á na aplicação. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Crie a função de erro para o objeto de pedido. Esta função regista o erro na consola. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Crie uma função para `onreadystatechange` a propriedade do objeto de pedido. Quando o estado de prontidão do objeto de pedido mudar, as funções de devolução e erro acima serão aplicadas.
            
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
    
    5. Envie o pedido da API. Adicione a sua `Ocp-Apim-Subscription-Key` chave de subscrição ao cabeçalho e detetete te `content-type` o`application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Executar a aplicação e ver a resposta

Esta aplicação pode ser executada dentro do seu navegador web. Uma resposta bem sucedida é devolvida em formato JSON. Também pode encontrar a resposta JSON no [GitHub:](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)

Para ver como funciona a API de Reconhecimento de Tinta numa aplicação de tinta digital, veja as seguintes aplicações de amostra no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
