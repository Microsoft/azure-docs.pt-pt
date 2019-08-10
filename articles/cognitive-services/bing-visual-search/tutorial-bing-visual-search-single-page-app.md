---
title: " Criar um aplicativo Web de página única-Pesquisa Visual do Bing"
titleSuffix: Azure Cognitive Services
description: Saiba como integrar o API da Pesquisa Visual do Bing a um aplicativo Web de página única.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: aahi
ms.openlocfilehash: e0370be1c10bc0f5813bec833be78ad31a3d61a7
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880649"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Criar um aplicativo Web de página única Pesquisa Visual

O API da Pesquisa Visual do Bing retorna informações para uma imagem. Você pode carregar uma imagem ou fornecer uma URL para uma. As informações são imagens visualmente semelhantes, fontes de compras, páginas da Web que incluem a imagem e muito mais. As informações retornadas pelo API da Pesquisa Visual do Bing são semelhantes àquelas mostradas em Bing.com/images.

Este tutorial explica como estender um aplicativo Web de página única para o API de Pesquisa de Imagem do Bing. Para exibir esse tutorial ou obter o código-fonte usado aqui, [consulte o tutorial: Crie um aplicativo de página única para o API de Pesquisa de Imagem do Bing](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

O código-fonte completo para esse aplicativo (depois de estendê-lo para usar o API da Pesquisa Visual do Bing), está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Chamar o API da Pesquisa Visual do Bing e manipular a resposta

Edite o tutorial de pesquisa de imagem do Bing e adicione o código a seguir ao final `<script>` do elemento (e antes da `</script>` marca de fechamento). O código a seguir manipula uma resposta de pesquisa visual da API, itera os resultados e os exibe:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

O código a seguir envia uma solicitação de pesquisa para a API, usando um ouvinte `handleVisualSearchResponse()`de eventos para chamar:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Recolha do token de informações

Adicione o código a seguir ao `searchItemsRenderer` objeto. Este código adiciona uma ligação **encontrar semelhante** que chama a função `bingVisualSearch` ao clicar na ligação. A função recebe o `imageInsightsToken` como um argumento.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Apresentar imagens semelhantes

Adicione o seguinte código HTML na linha 601. Esse código de marcação adiciona um elemento para exibir os resultados da chamada API da Pesquisa Visual do Bing:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Com todo o novo código JavaScript e elementos HTML implementados, os resultados da pesquisa são apresentados com uma ligação **encontrar semelhante**. Clique na ligação para preencher a secção **Semelhante** com as imagens semelhantes à que escolheu. Poderá ter de expandir a secção **Semelhante** para mostrar as imagens.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Cortar uma imagem com o SDK do Pesquisa Visual do Bing paraC#](tutorial-visual-search-crop-area-results.md)
