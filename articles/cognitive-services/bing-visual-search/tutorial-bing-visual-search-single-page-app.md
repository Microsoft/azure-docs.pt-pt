---
title: " Construa uma aplicação web de uma página única - Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Saiba como integrar a API de pesquisa visual bing numa aplicação web de uma página única.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: 83cdaecfb819fb1f4677b051f87e23e0e03daef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370106"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Tutorial: Criar uma aplicação web de pesquisa visual de uma página

A API de Pesquisa Visual Bing devolve insights para uma imagem. Pode fazer o upload de uma imagem ou fornecer um URL a uma. Insights são imagens visualmente semelhantes, fontes de compras, páginas web que incluem a imagem, e muito mais. Insights devolvidos pela API de Pesquisa Visual bing são semelhantes aos mostrados no Bing.com/images.

Este tutorial explica como estender uma aplicação web de uma página para a API de Pesquisa de Imagem Bing. Para visualizar esse tutorial ou obter o código fonte usado aqui, consulte [Tutorial: Crie uma aplicação de uma página única para a API](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)de Pesquisa de Imagem Bing .

O código fonte completo para esta aplicação (depois de o estender para utilizar a API de Pesquisa Visual Bing), está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Ligue para a API de Pesquisa Visual Bing e lide com a resposta

Editar o tutorial bing Image Search e adicionar `<script>` o seguinte código `</script>` à extremidade do elemento (e antes da etiqueta de fecho). O código seguinte trata de uma resposta de pesquisa visual da API, iterates através dos resultados, e exibe-os:

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

O seguinte código envia um pedido de pesquisa à API, utilizando um ouvinte de eventopara ligar: `handleVisualSearchResponse()`

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

Adicione o seguinte `searchItemsRenderer` código ao objeto. Este código adiciona uma ligação **encontrar semelhante** que chama a função `bingVisualSearch` ao clicar na ligação. A função `imageInsightsToken` recebe como argumento.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Apresentar imagens semelhantes

Adicione o seguinte código HTML na linha 601. Este código de marcação adiciona um elemento para exibir os resultados da chamada Bing Visual Search API:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Com todo o novo código JavaScript e elementos HTML implementados, os resultados da pesquisa são apresentados com uma ligação **encontrar semelhante**. Clique na ligação para preencher a secção **Semelhante** com as imagens semelhantes à que escolheu. Poderá ter de expandir a secção **Semelhante** para mostrar as imagens.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Corte uma imagem com o Bing Visual Search SDK para C #](tutorial-visual-search-crop-area-results.md)
