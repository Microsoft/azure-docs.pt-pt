---
title: Efetuar operações de imagem - JavaScript
titlesuffix: Azure Cognitive Services
description: Explore uma aplicação JavaScript básica que utiliza a API de Imagem Digitalizada nos Serviços Cognitivos do Azure. Efetue o OCR, crie miniaturas e trabalhe com funcionalidades visuais numa imagem.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 91af70406590ab8e65a5d4a4b53835e9e4d4ed2a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231657"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-javascript"></a>Utilizar as funcionalidades de imagem digitalizada com a REST API e JavaScript

Este guia mostra os recursos das Azure cognitivos serviços REST API de imagem digitalizada.

Explore uma aplicação JavaScript que utilize a API REST de Imagem Digitalizada para realizar o reconhecimento ótico de carateres (OCR), criar miniaturas com recorte inteligente, além de detetar, categorizar, etiquetar e descrever funcionalidades visuais, incluindo rostos, numa imagem. Este exemplo permite enviar um URL de imagem para análise ou processamento. Pode utilizar este exemplo de open source aberto como um modelo para criar a sua própria aplicação JavaScript para utilizar a API REST de Imagem Digitalizada.

A aplicação de formulário JavaScript já foi escrita, mas não tem a funcionalidade de Imagem Digitalizada. Neste guia, é possível adicionar o código específico para a API de REST de visão do computador para concluir a funcionalidade do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Pode seguir os passos deste guia usando um editor de texto simples.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Subscrever a API de Imagem Digitalizada e obter uma chave de subscrição

Antes de criar o exemplo, tem de subscrever a API de Imagem Digitalizada que faz parte dos Serviços Cognitivos do Azure. Para obter os detalhes da subscrição e da gestão de chaves, veja [Subscrições](https://azure.microsoft.com/try/cognitive-services/). As chaves primárias e secundárias são válidas para utilizar neste guia.

## <a name="acquire-incomplete-tutorial-project"></a>Adquirir o projeto de tutorial incompleto

### <a name="download-the-project"></a>Transfira o projeto

Clone o [Cognitive Services JavaScript Computer Vision Tutorial](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial) (Tutorial de Imagem Digitalizada de JavaScript dos Serviços Cognitivos) ou transfira o ficheiro. zip e extraia-o para um diretório vazio.

Se preferir utilizar o projeto concluído com o código do tutorial todos os adicionado, pode utilizar os ficheiros a **concluído** pasta.

## <a name="add-tutorial-code-to-the-project"></a>Adicione o código do tutorial ao projeto

A aplicação do JavaScript é configurada com seis .HTML os ficheiros, um para cada funcionalidade. Cada ficheiro demonstra outra função de imagem digitalizada (analisar, OCR, etc.). As secções de seis não têm interdependências, para que pode adicionar o código do tutorial para um ficheiro, todos os seis ficheiros ou apenas dois arquivos. Também pode adicionar o código do tutorial aos ficheiros em qualquer ordem.

### <a name="analyze-an-image"></a>Analisar uma imagem

A funcionalidade de análise de imagem digitalizada analisa uma imagem para milhares de objetos reconhecíveis, vivos, paisagens e ações. Quando a análise estiver concluída, a funcionalidade Analyze (Analisar) devolve um objeto JSON que descreve a imagem com etiquetas descritivas, análise de cores, legendas e mais.

Para concluir a funcionalidade de análise da aplicação, execute os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>Adicione o código do manipulador de eventos para o botão de analisar

Abra o ficheiro **analyze.html** num editor de texto e localize a função **analyzeButtonClick** junto à parte inferior do ficheiro.

A função do processador de eventos **analyzeButtonClick** limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama a função **AnalyzeImage** para analisar a imagem. Copie e cole o seguinte código na função **analyzeButtonClick**.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **AnalyzeImage** encapsula a chamada à API REST num wrapper para analisar uma imagem. Após uma devolução com êxito, a análise JSON formatada será apresentada na área de texto especificada e a legenda será apresentada no intervalo especificado.

Copie e cole o código da função **AnalyzeImage** por baixo da função **analyzeButtonClick**.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-analyze-function"></a>Executar a função de análise

Guarde o ficheiro **analyze.html** e abra-o num browser. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Introduza um URL para uma imagem a analisar e, em seguida, clique no botão **Analyze Image** (Analisar Imagem) para analisar uma imagem e ver o resultado.

### <a name="recognize-a-landmark"></a>Reconhecer um marco

A funcionalidade Landmark (Marco) da Imagem Digitalizada analisa uma imagem relativamente a marcos naturais e artificiais, como montanhas ou edifícios famosos. Quando a análise estiver concluída, a funcionalidade Landmark (Marco) devolve um objeto JSON que identifica os marcos encontrados na imagem.

Para concluir o recurso de ponto de referência do aplicativo, execute os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-landmark-button"></a>Adicione o código do manipulador de eventos para o botão de ponto de referência

Abra o ficheiro **landmark.html** num editor de texto e localize a função **landmarkButtonClick** junto à parte inferior do ficheiro.

A função do processador de eventos **landmarkButtonClick** limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama a função **IdentifyLandmarks** para analisar a imagem. Copie e cole o seguinte código na função **landmarkButtonClick**.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **IdentifyLandmarks** encapsula a chamada à API REST num wrapper para analisar uma imagem. Após uma devolução com êxito, a análise JSON formatada será apresentada na área de texto especificada e a legenda será apresentada no intervalo especificado.

Copie e cole o código da função **IdentifyLandmarks** por baixo da função **landmarkButtonClick**.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-landmark-function"></a>Executar a função de ponto de referência

Guarde o ficheiro **landmark.html** e abra-o num browser. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Introduza um URL para uma imagem a analisar e, em seguida, clique no botão **Analyze Image** (Analisar Imagem) para analisar uma imagem e ver o resultado.

### <a name="recognize-celebrities"></a>Reconhecer celebridades

A funcionalidade Celebrities (Celebridades) da Imagem Digitalizada analisa uma imagem relativamente a pessoas famosas. Quando a análise estiver concluída, a funcionalidade Celebrities (Celebridades) devolve um objeto JSON que identifica as celebridades encontradas na imagem.

Para concluir a funcionalidade de celebridades do aplicativo, execute os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>Adicione o código do manipulador de eventos para o botão de celebridades

Abra o ficheiro **celebrities.html** num editor de texto e localize a função **celebritiesButtonClick** junto à parte inferior do ficheiro.

A função do processador de eventos **celebritiesButtonClick** limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama a função **IdentifyCelebrities** para analisar a imagem. Copie e cole o seguinte código na função **celebritiesButtonClick**.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-celebrities-function"></a>Executar a função de celebridades

Guarde o ficheiro **celebrities.html** e abra-o num browser. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Introduza um URL para uma imagem a analisar e, em seguida, clique no botão **Analyze Image** (Analisar Imagem) para analisar uma imagem e ver o resultado.

### <a name="intelligently-generate-a-thumbnail"></a>Gerar uma miniatura de forma inteligente

A funcionalidade Thumbnail (Miniatura) da Imagem Digitalizada gera uma miniatura a partir de uma imagem. Ao utilizar a funcionalidade **Smart Crop** (Recorte Inteligente), a funcionalidade Thumbnail (Miniatura) identificará a área de interesse numa imagem e centrará a miniatura nessa área, para gerar imagens em miniatura mais agradáveis esteticamente.

Para concluir a funcionalidade de miniatura do aplicativo, execute os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>Adicione o código do manipulador de eventos do botão em miniatura

Abra o ficheiro **thumbnail.html** num editor de texto e localize a função **thumbnailButtonClick** junto à parte inferior do ficheiro.

A função do processador de eventos **thumbnailButtonClick** limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama a função **getThumbnail** duas vezes para criar duas miniaturas, uma com recorte inteligente e outra sem. Copie e cole o seguinte código na função **thumbnailButtonClick**.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **getThumbnail** encapsula a chamada à API REST num wrapper para analisar uma imagem. Após uma devolução com êxito, a miniatura será apresentada no elemento da imagem especificado.

Copie e cole a seguinte função **getThumbnail** por baixo da função **thumbnailButtonClick**.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumbnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-thumbnail-function"></a>Executar a função em miniatura

Guarde o ficheiro **thumbnail.html** e abra-o num browser. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Introduza um URL para uma imagem a analisar e, em seguida, clique no botão **Generate Thumbnails** (Gerar Miniaturas) para analisar uma imagem e ver o resultado.

### <a name="read-printed-text-ocr"></a>Ler texto impresso (OCR)

A funcionalidade Optical Character Recognition (Reconhecimento Ótico de Carateres – OCR) da Imagem Digitalizada analisa uma imagem de texto impresso. Quando a análise estiver concluída, a funcionalidade OCR devolve um objeto JSON que contém o texto e a localização do texto na imagem.

Para concluir a funcionalidade de OCR do aplicativo, execute os seguintes passos:

### <a name="add-the-event-handler-code-for-the-ocr-button"></a>Adicione o código do manipulador de eventos para o botão de OCR

Abra o ficheiro **ocr.html** num editor de texto e localize a função **ocrButtonClick** junto à parte inferior do ficheiro.

A função do processador de eventos **ocrButtonClick** limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama a função **ReadOcrImage** para analisar a imagem. Copie e cole o seguinte código na função **ocrButtonClick**.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **ReadOcrImage** encapsula a chamada à API REST num wrapper para analisar uma imagem. Após uma devolução com êxito, o JSON formatado que descreve o texto e a localização do texto serão apresentados na área de texto especificada.

Copie e cole a seguinte função **ReadOcrImage** por baixo da função **ocrButtonClick**.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-ocr-function"></a>Executar a função de OCR

Guarde o ficheiro **ocr.html** e abra-o num browser. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Introduza um URL para uma imagem de texto a ler e, em seguida, clique no botão **Read Imagem** (Ler Imagem) para analisar a imagem e ver o resultado.

### <a name="read-handwritten-text-handwriting-recognition"></a>Ler texto manuscrito (reconhecimento de escrita manual)

A funcionalidade Handwriting Recognition (Reconhecimento de Escrita Manual) da Imagem Digitalizada analisa uma imagem de texto manuscrito. Quando a análise estiver concluída, a funcionalidade Handwriting Recognition (Reconhecimento de Escrita Manual) devolve um objeto JSON que contém o texto e a localização do texto na imagem.

Para concluir a funcionalidade de reconhecimento de manuscrito do aplicativo, execute os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>Adicione o código do manipulador de eventos para o botão de escrita manual

Abra o ficheiro **handwriting.html** num editor de texto e localize a função **handwritingButtonClick** junto à parte inferior do ficheiro.

A função do processador de eventos **handwritingButtonClick** limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama a função **HandwritingImage** para analisar a imagem.

Copie e cole o seguinte código na função **handwritingButtonClick**.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **ReadHandwrittenImage** encapsula as duas chamadas à API REST necessárias num wrapper para analisar uma imagem. Como o Handwriting Recognition (Reconhecimento de Escrita Manual) é um processo demorado, é utilizado um processo em duas etapas. A primeira chamada submete a imagem para processamento; a segunda chamada obtém o texto detetado quando o processamento está concluído.

Após o texto ser devolvido, o JSON formatado que descreve o texto e a localização do texto serão apresentados na área de texto especificada.

Copie e cole a seguinte função **ReadHandwrittenImage** por baixo da função **handwritingButtonClick**.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-handwriting-function"></a>Executar a função de manuscrito

Guarde o ficheiro **handwriting.html** e abra-o num browser. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Introduza um URL para uma imagem de texto a ler e, em seguida, clique no botão **Read Imagem** (Ler Imagem) para analisar a imagem e ver o resultado.

## <a name="next-steps"></a>Passos Seguintes

Neste guia, utilizada a API de REST de visão do computador com o JavaScript para testar muitas das funcionalidades de análise de imagem disponíveis. Em seguida, consulte a documentação de referência para saber que mais sobre as APIs envolvidos.

- [API de REST de imagem digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
