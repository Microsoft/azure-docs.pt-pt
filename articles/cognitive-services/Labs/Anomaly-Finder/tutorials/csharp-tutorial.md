---
title: 'Tutorial: Deteção de anomaliasC#'
titlesuffix: Azure Cognitive Services
description: Explore uma aplicação C# que utiliza a API de Deteção de Anomalias. Envie pontos de dados originais à API e obtenha o valor esperado e os pontos de anomalias.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 64183ea09203ea965bb889fe4741fdaf4dbd1e02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709942"
---
# <a name="tutorial-anomaly-detection-with-c-application"></a>Tutorial: Deteção de anomalias com C# aplicação

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Explore uma aplicação básica do Windows que utiliza a API de Deteção de Anomalias para detetar anomalias da entrada. O exemplo envia os dados de séries de tempo à API de Deteção de Anomalias com a sua chave de subscrição e obtém todos os pontos de anomalias e o valor esperado para cada ponto de dados da API.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo foi desenvolvido para o .NET Framework com o [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subscrever a Deteção de Anomalias e obter uma chave de subscrição 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Obter e utilizar o exemplo

Pode clonar a aplicação de exemplo de deteção de anomalias para o computador a partir [GitHub](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Instalar o exemplo

No seu ambiente de trabalho do GitHub, abra Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>Compilar o exemplo

Prima Ctrl+Shift+B ou clique em Compilar no menu do friso e, em seguida, selecione Compilar Solução.

<a name="Step3"></a>
### <a name="run-the-example"></a>Executar o exemplo

1. Depois de a compilação estar concluída, prima **F5** ou clique em **Iniciar** no menu do friso para executar o exemplo.
2. Localize a janela da interface de utilizador da Deteção de Anomalias com a caixa de edição de texto com o texto “{a_sua_chave_de_subscrição}”.
3. Substitua o ficheiro request.json, que contém os dados de exemplo, pelos seus dados e, em seguida, clique no botão “Enviar”. A Microsoft recebe os dados que carregar e utiliza-os para detetar quaisquer pontos de anomalias entre eles. Os dados que carregar não permanecerão no servidor da Microsoft. Para detetar novamente o ponto de anomalia, precisa de carregar os dados mais uma vez.
4. Se os dados estiverem corretos, encontrará o resultado da deteção de anomalias no campo “Resposta”. Se ocorrer algum erro, as informações de erro serão mostradas no campo Resposta.

<a name="Review"></a>
### <a name="read-the-result"></a>Ler o resultado

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Rever e aprender

Agora que tem uma aplicação em execução, vamos rever como a aplicação de exemplo se integra na tecnologia de Serviços Cognitivos. Este passo irá permitir que continue a compilar esta aplicação ou que desenvolva a sua própria aplicação através da Deteção de Anomalias da Microsoft.

Esta aplicação de exemplo utiliza o ponto final da API Restful de Deteção de Anomalias.

Ao analisamos a forma como a API Restful é utilizada na aplicação de exemplo, vamos examinar um fragmento de código de **AnomalyDetectionClient.cs**. O ficheiro contém comentários ao código que indicam “KEY SAMPLE CODE STARTS HERE” e “KEY SAMPLE CODE ENDS HERE” para ajudá-lo a localizar os fragmentos de código reproduzidos abaixo.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
### <a name="request"></a>**Pedido**
O fragmento de código abaixo mostra como utilizar o HttpClient para submeter a sua chave de subscrição e os pontos de dados ao ponto final da API de Deteção de Anomalias.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
