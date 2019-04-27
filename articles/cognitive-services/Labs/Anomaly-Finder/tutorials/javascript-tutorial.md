---
title: 'Tutorial: Deteção de anomalias com Javascript'
titlesuffix: Azure Cognitive Services
description: Explore uma aplicação Web de Javascript que utilize a API de Deteção de Anomalias. Envie pontos de dados originais à API e obtenha o valor esperado e os pontos de anomalias.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 9e66b24987b2318f3022404d951fbb911e7b592d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709873"
---
# <a name="tutorial-anomaly-detection-with-javascript-application"></a>Tutorial: Deteção de anomalias com a aplicação do Javascript

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Explore uma aplicação Web que utilize a API REST de Deteção de Anomalias para detetar uma anomalia. O exemplo envia os dados de série de tempo para a API de Deteção de Anomalias com a sua chave de subscrição e obtém todos os pontos de anomalias e o valor esperado para cada ponto de dados da API.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Este tutorial foi desenvolvido com um editor de texto simples.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subscrever a Deteção de Anomalias e obter uma chave de subscrição 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Obter e utilizar o exemplo

Este tutorial fornece dois cenários para deteção de anomalias de dados de série de tempo. Vamos começar.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Transferir o projeto de tutorial

Clone o [Tutorial de Deteção de Anomalias de JavaScript dos Serviços Cognitivos](https://github.com/MicrosoftAnomalyDetection/javascript-sample) ou transfira o ficheiro. zip e extraia-o para um diretório vazio.

<a name="Step2"></a>
### <a name="run-the-example"></a>Executar o exemplo

Existem dois cenários nos quais pode experimentar o exemplo.
1. Coloque a sua **chave de subscrição** no campo Chave de Subscrição da função de deteção no ficheiro anomalydetection.html.
2. Coloque o ponto final da API de Deteção de Anomalias e certifique-se de que está a utilizar a região correta na Região da Subscrição.
3. Abra o ficheiro **anomalydetection.html** num browser.

**Cenário 1 Detetar dados de série de tempo semanais**
1. No campo Período, introduza o período**7**. 
2. Substitua os dados de exemplo pelos seus pontos de dados de série de tempo semanais (Json) no campo Pontos ou utilize diretamente os dados de exemplo.
3. Clique no botão de Deteção de Anomalias e selecione o resultado na caixa de texto de Resposta correta.

**Cenário 2 Detetar os dados de série de tempo sem um período**
1. Deixe o campo Período vazio, partindo do princípio de que desconhece o período da série de tempo.
2. Utilize os mesmos dados de série de tempo do cenário 1.
3. Clique no botão de Deteção de Anomalias e selecione o campo Período na caixa de texto de Resposta correta.

<a name="Step3"></a>
### <a name="read-the-result"></a>Ler o resultado

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Rever e aprender

Agora, tem uma aplicação em execução. Vamos rever como a aplicação de exemplo se integra com a tecnologia de Serviços Cognitivos. Este passo irá permitir que continue a compilar esta aplicação ou que desenvolva a sua própria aplicação através da Deteção de Anomalias da Microsoft.
Esta aplicação de exemplo utiliza o ponto final da API Restful de Deteção de Anomalias.
Ao analisamos a forma como a API Restful é utilizada na aplicação de exemplo, vamos examinar um fragmento de código do ficheiro anomalydetection.html.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
