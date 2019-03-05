---
title: " Criar uma aplicação de Web de página única - pesquisa Visual do Bing"
titleSuffix: Azure Cognitive Services
description: Saiba como integrar a API de pesquisa Visual do Bing num aplicativo da Web de página única.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e06fd7a4b2d072e5528643c2c8517d7545c36ef3
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338659"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Criar uma aplicação de web de página única de pesquisa Visual 

A API de pesquisa Visual do Bing fornece uma experiência semelhante à apresentada na Bing.com/images os detalhes da imagem. Com a Pesquisa Visual, pode especificar uma imagem e receber informações sobre a mesma, como imagens visualmente parecidas, origens de compras, páginas Web que incluem a imagem, entre outras. 

Este artigo explica como expandir uma aplicação web de página única para a API de pesquisa de imagens do Bing. Para ver esse tutorial ou obter o código-fonte usado aqui, consulte [Tutorial: Criar uma aplicação de página única para a API de pesquisa de imagens do Bing](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md). 

O código-fonte completo para esta aplicação (após expandir para utilizar a API de pesquisa Visual do Bing), está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Chamar a API de pesquisa Visual do Bing e tratar da resposta

Editar o tutorial de pesquisa de imagens do Bing e adicione o seguinte código ao final dos `<script>` elemento (e antes do fechar `</script>` marca). O código a seguir processa uma resposta de pesquisa visual da API, itera através de resultados e apresenta-os.

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

O código a seguir envia um pedido de pesquisa para a API, usando um serviço de escuta de eventos para chamar `handleVisualSearchResponse()`.


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

Adicione o seguinte código para o `searchItemsRenderer` objeto. Este código adiciona uma ligação **encontrar semelhante** que chama a função `bingVisualSearch` ao clicar na ligação. A função recebe o imageInsightsToken como um argumento.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Apresentar imagens semelhantes

Adicione o seguinte código HTML na linha 601. Este código de marcação adiciona um elemento utilizado para apresentar os resultados da chamada à API de Pesquisa Visual do Bing.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Com todo o novo código JavaScript e elementos HTML implementados, os resultados da pesquisa são apresentados com uma ligação **encontrar semelhante**. Clique na ligação para preencher a secção **Semelhante** com as imagens semelhantes à que escolheu. Poderá ter de expandir a secção **Semelhante** para mostrar as imagens.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Recortar e carregar uma imagem](tutorial-visual-search-crop-area-results.md)
