---
title: Autenticação
titleSuffix: Azure Cognitive Services
description: 'Existem três formas de autenticar um pedido a um recurso dos Serviços Cognitivos Azure: uma chave de subscrição, um token ao portador ou uma subscrição multi-serviço. Neste artigo, você vai aprender sobre cada método, e como fazer um pedido.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: c7aeb9e9f4de7b4de62f9b5a8da6d997e32a2399
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363328"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Autenticar pedidos aos Serviços Cognitivos da Azure

Cada pedido a um Serviço Cognitivo Azure deve incluir um cabeçalho de autenticação. Este cabeçalho passa ao longo de uma chave de subscrição ou token de acesso, que é usado para validar a sua subscrição para um serviço ou grupo de serviços. Neste artigo, você vai aprender sobre três maneiras de autenticar um pedido e os requisitos para cada um.

* Autenticar com uma chave de subscrição [de serviço único](#authenticate-with-a-single-service-subscription-key) ou [multi-serviço](#authenticate-with-a-multi-service-subscription-key)
* Autenticar com um [símbolo](#authenticate-with-an-authentication-token)
* Autenticar com [Diretório Ativo Azure (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer um pedido, precisa de uma conta Azure e uma subscrição dos Serviços Cognitivos Azure. Se já tem uma conta, vá em frente e salte para a secção seguinte. Se não tiver uma conta, temos um guia para o configurar em minutos: [Crie uma conta de Serviços Cognitivos para o Azure.](cognitive-services-apis-create-account.md)

Pode obter a sua chave de subscrição a partir do [portal Azure](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) depois de [criar a sua conta.](https://azure.microsoft.com/free/cognitive-services/)

## <a name="authentication-headers"></a>Cabeçalhos de autenticação

Vamos rever rapidamente os cabeçalhos de autenticação disponíveis para uso com os Serviços Cognitivos Azure.

| Cabeçalho | Description |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Utilize este cabeçalho para autenticar com uma chave de subscrição para um serviço específico ou uma chave de subscrição multi-serviço. |
| Ocp-Apim-Região de Subscrição | Este cabeçalho só é necessário quando se utiliza uma chave de subscrição multi-serviço com o [serviço Tradutor](./Translator/reference/v3-0-reference.md). Utilize este cabeçalho para especificar a região de subscrição. |
| Autorização | Utilize este cabeçalho se estiver a utilizar um token de autenticação. Os passos para realizar uma troca de fichas são detalhados nas seguintes secções. O valor fornecido segue este formato: `Bearer <TOKEN>` . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Autenticar com uma chave de subscrição de serviço único

A primeira opção é autenticar um pedido com uma chave de subscrição para um serviço específico, como o Tradutor. As chaves estão disponíveis no portal Azure para cada recurso que criou. Para utilizar uma chave de subscrição para autenticar um pedido, deve ser transmitida como `Ocp-Apim-Subscription-Key` cabeçalho.

Estes pedidos de amostra demonstram como usar o `Ocp-Apim-Subscription-Key` cabeçalho. Tenha em mente que ao utilizar esta amostra terá de incluir uma chave de subscrição válida.

Esta é uma chamada de amostra para a API de Pesquisa web de Bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta é uma chamada de amostra para o serviço de Tradutor:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

O vídeo que se segue demonstra a utilização de uma chave de Serviços Cognitivos.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Autenticar com uma chave de subscrição multi-serviço

>[!WARNING]
> Neste momento, estes serviços **não** suportam chaves multi-serviços: QnA Maker, Speech Services, Custom Vision e Anomaly Detetor.

Esta opção também utiliza uma chave de subscrição para autenticar pedidos. A principal diferença é que uma chave de subscrição não está ligada a um serviço específico, pelo contrário, uma única chave pode ser usada para autenticar pedidos de múltiplos Serviços Cognitivos. Consulte [os preços dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter informações sobre disponibilidade regional, funcionalidades suportadas e preços.

A chave de subscrição é fornecida em cada pedido como `Ocp-Apim-Subscription-Key` cabeçalho.

[![Demonstração chave de subscrição de vários serviços para serviços cognitivos](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Regiões suportadas

Ao utilizar a chave de subscrição multi-serviço para fazer um pedido `api.cognitive.microsoft.com` para, deve incluir a região no URL. Por exemplo: `westus.api.cognitive.microsoft.com`.

Ao utilizar a chave de subscrição multi-serviço com o serviço Tradutor, deve especificar a região de subscrição com o `Ocp-Apim-Subscription-Region` cabeçalho.

A autenticação multi-serviço é suportada nestas regiões:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

### <a name="sample-requests"></a>Pedidos de amostra

Esta é uma chamada de amostra para a API de Pesquisa web de Bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta é uma chamada de amostra para o serviço de Tradutor:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Autenticar com um token de autenticação

Alguns Serviços Cognitivos Azure aceitam, e em alguns casos exigem, um token de autenticação. Atualmente, estes serviços suportam fichas de autenticação:

* API de Tradução de Texto
* Serviços de Fala: API REST de voz em texto
* Serviços de fala: API DE REPOUSO TEXT-to-speech

>[!NOTE]
> O Fabricante QnA também usa o cabeçalho de autorização, mas requer uma chave de ponto final. Para obter mais informações, consulte [o Fabricante QnA: Obtenha resposta a partir da base de conhecimento.](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

>[!WARNING]
> Os serviços que suportam fichas de autenticação podem ser alterados ao longo do tempo, verifique a referência API para um serviço antes de utilizar este método de autenticação.

As chaves de subscrição de serviço único e multi-serviço podem ser trocadas por fichas de autenticação. As fichas de autenticação são válidas por 10 minutos.

As fichas de autenticação estão incluídas num pedido como `Authorization` cabeçalho. O valor simbólico fornecido deve ser precedido `Bearer` por, por exemplo: `Bearer YOUR_AUTH_TOKEN` .

### <a name="sample-requests"></a>Pedidos de amostra

Utilize este URL para trocar uma chave de subscrição para um token de autenticação: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken` .

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Estas regiões multi-serviços apoiam o intercâmbio de fichas:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

Depois de obter um sinal de autenticação, terá de o passar em cada pedido como `Authorization` cabeçalho. Esta é uma chamada de amostra para o serviço de Tradutor:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Ver também

* [O que são os Serviços Cognitivos?](./what-are-cognitive-services.md)
* [Preços dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Subdomínios personalizados](cognitive-services-custom-subdomains.md)