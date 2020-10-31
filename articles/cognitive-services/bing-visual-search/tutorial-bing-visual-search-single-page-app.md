---
title: " Construir uma aplicação Web de uma página única - Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Saiba como integrar a API de Pesquisa Visual Bing numa aplicação Web de uma só página.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b9458f6080759f0948d103f7fe3d131f6a4c4e37
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092726"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Tutorial: Criar uma aplicação web de pesquisa visual de uma página

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

A API de Pesquisa Visual Bing retorna insights para uma imagem. Pode fazer upload de uma imagem ou fornecer um URL a uma. Os insights são imagens visualmente semelhantes, fontes de compras, páginas web que incluem a imagem, e muito mais. Os insights devolvidos pela API de Pesquisa Visual Bing são semelhantes aos apresentados no Bing.com/images.

Este tutorial explica como estender uma aplicação web de uma página para a API de Pesquisa de Imagem de Bing. Para ver esse tutorial ou obter o código fonte utilizado aqui, consulte [Tutorial: Crie uma aplicação de uma página para a API de Pesquisa de Imagem Bing](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

O código fonte completo desta aplicação (depois de a ter alargado para utilizar a API de Pesquisa Visual Bing), está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Ligue para a API de Pesquisa Visual Bing e trate da resposta

Edite o tutorial de Pesquisa de Imagem Bing e adicione o seguinte código ao fim do `<script>` elemento (e antes da etiqueta de `</script>` fecho). O código a seguir trata de uma resposta de pesquisa visual da API, itera através dos resultados e apresenta-os:

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

O seguinte código envia um pedido de pesquisa à API, utilizando um ouvinte do evento para `handleVisualSearchResponse()` ligar:

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

Adicione o seguinte código ao `searchItemsRenderer` objeto. Este código adiciona uma ligação **encontrar semelhante** que chama a função `bingVisualSearch` ao clicar na ligação. A função recebe o `imageInsightsToken` como argumento.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Apresentar imagens semelhantes

Adicione o seguinte código HTML na linha 601. Este código de marcação adiciona um elemento para exibir os resultados da chamada de API de Pesquisa Visual Bing:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Com todo o novo código JavaScript e elementos HTML implementados, os resultados da pesquisa são apresentados com uma ligação **encontrar semelhante** . Clique na ligação para preencher a secção **Semelhante** com as imagens semelhantes à que escolheu. Poderá ter de expandir a secção **Semelhante** para mostrar as imagens.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Corte uma imagem com o Bing Visual Search SDK para C #](tutorial-visual-search-crop-area-results.md)
