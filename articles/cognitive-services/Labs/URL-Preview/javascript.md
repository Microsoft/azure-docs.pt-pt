---
title: 'Início rápido: Versão prévia da URL do projeto, JavaScript'
titlesuffix: Azure Cognitive Services
description: Exemplo de script para começar a utilizar rapidamente a API de Pré-visualização de URL com o JavaScript.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: ec8a7ceef8cd938984b8eedc8397f6f67294c990
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706935"
---
# <a name="quickstart-url-preview-in-javascript"></a>Início rápido: Visualização de URL em JavaScript 

A seguinte aplicação de página única utiliza JavaScript para criar uma Pré-visualização de URL para o site da SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Pré-requisitos

Obter uma chave de acesso da avaliação gratuita dos [Laboratórios dos Serviços Cognitivos](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Cenário de código
O exemplo seguinte em JavaScript inclui um objeto de entrada de caixa de texto, na qual o utilizador introduz o URL para pré-visualização.  Quando o utilizador clica no botão **Pré-visualização**, o método OnClick encaminha para `getPreview`, em que o código gera um pedido Web para o ponto final **UrlPreview**.

O código cria um *XMLHttpRequest*, adiciona o cabeçalho e a chave *Ocp-Apim-Subscription-Key*, e envia o pedido.  Adiciona um processador de eventos assíncronos para processar a resposta.

Se a resposta for devolvida com êxito, o processador atribui o texto JSON da resposta ao parágrafo `demo` na página. Outros elementos de resposta estão definidos para apresentação pelos parágrafos seguintes.

**Resposta JSON não processada**

```
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

**A demonstração em execução**

![Exemplo de Pré-visualização de Url em JavaScript](./media/java-script-demo.png)

## <a name="running-the-application"></a>Executar a aplicação

Para executar a aplicação:

1. Substitua o valor `YOUR-SUBSCRIPTION-KEY` por uma chave de acesso válida para a sua subscrição.
2. Guarde o HTML e o script num ficheiro com a extensão .HTML.
3. Execute a página Web num browser.
4. Utilize o URL existente ou introduza outro na caixa de texto.
5. Clique no botão **Pré-visualização**.

**Código fonte:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido do C#](csharp.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início rápido de Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
