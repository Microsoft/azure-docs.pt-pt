---
title: Authentication
titleSuffix: Cognitive Services - Azure
description: 'Existem três formas de autenticar um pedido a um recurso de serviços cognitivos do Azure: uma chave de subscrição, um token de portador ou uma subscrição de múltiplos serviço. Neste artigo, irá aprender sobre cada método e como fazer um pedido.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: erhopf
ms.openlocfilehash: 90bc2bf4c207f3bb2727d76c2e6b4fd5597539b1
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240767"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Autenticar pedidos para os serviços cognitivos do Azure

Cada pedido num serviço cognitivos do Azure tem de incluir um cabeçalho de autenticação. Este cabeçalho transmite uma chave de subscrição ou acesso token, que é usado para validar a sua subscrição para um serviço ou grupo de serviços. Neste artigo, aprenderá a cerca de três formas de autenticar um pedido e os requisitos para cada.

* [Autenticar com uma chave de subscrição de serviço único](#authenticate-with-a-single-service-subscription-key)
* [Autenticar com uma chave de subscrição de múltiplos serviços](#authenticate-with-a-multi-service-subscription-key)
* [Autenticar com um token](#authenticate-with-an-authentication-token)

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer um pedido, terá de uma conta do Azure e uma subscrição de serviços cognitivos do Azure. Se já tiver uma conta, vá em frente e avançar para a secção seguinte. Se não tiver uma conta, temos um guia para ajudá-lo a configurar em minutos: [Criar uma conta dos serviços cognitivos para o Azure](cognitive-services-apis-create-account.md).

Pode obter a chave de subscrição a partir do [portal do Azure](cognitive-services-apis-create-account.md#access-your-resource) depois de criar a sua conta ou ativar um [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/my-apis).
 
## <a name="authentication-headers"></a>Cabeçalhos de autenticação

Vamos analisar rapidamente os cabeçalhos de autenticação disponíveis para utilização com os serviços cognitivos do Azure.

| Cabeçalho | Descrição |
|--------|-------------|
| OCP-Apim-Subscription-Key | Utilize este cabeçalho para autenticar com uma chave de subscrição para um serviço específico ou uma chave de subscrição de múltiplos serviços. |
| Ocp-Apim-Subscription-Region | Este cabeçalho só é necessário quando utilizar uma chave de subscrição de múltiplos serviços com o [API de texto do Translator](./Translator/reference/v3-0-reference.md). Utilize este cabeçalho para especificar a região de subscrição. |
| Autorização | Utilize este cabeçalho se estiver a utilizar um token de autenticação. Os passos para efetuar uma troca de tokens são detalhados nas seções a seguir. O valor fornecido segue este formato: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Autenticar com uma chave de subscrição de serviço único

A primeira opção é autenticar um pedido com uma chave de subscrição para um serviço específico, como o texto do Translator. As chaves estão disponíveis no portal do Azure para cada recurso que criou. Para utilizar uma chave de subscrição para autenticar um pedido, ele deve ser passado juntamente como o `Ocp-Apim-Subscription-Key` cabeçalho.

Estes pedidos de exemplo demonstra como utilizar o `Ocp-Apim-Subscription-Key` cabeçalho. Tenha em mente, ao utilizar este exemplo, que precisará incluir uma chave de subscrição válido.

Esta é uma chamada de exemplo para a API de pesquisa Web Bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta é uma chamada de exemplo para a API de texto do Translator:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

O vídeo seguinte demonstra como utilizar uma chave de serviços cognitivos.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Autenticar com uma chave de subscrição de múltiplos serviços

>[!WARNING]
> Neste momento, estes serviços **não** chaves múltiplos serviços de suporte: A ferramenta QnA Maker, serviços de voz e visão personalizada.

Esta opção também utiliza uma chave de subscrição para autenticar pedidos. A principal diferença é que uma chave de subscrição não está associada a um serviço específico, em vez disso, uma única chave pode ser utilizada para autenticar pedidos para vários serviços cognitivos. Ver [preços dos serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter informações sobre a disponibilidade regional, funcionalidades suportadas e preços.

A chave de subscrição é fornecida em cada pedido como o `Ocp-Apim-Subscription-Key` cabeçalho.

[![Demonstração de chave de subscrição de múltiplos serviços para os serviços cognitivos](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Regiões suportadas

Ao utilizar a chave de subscrição de múltiplos serviços para fazer um pedido para `api.cognitive.microsoft.com`, tem de incluir a região no URL. Por exemplo: `westus.api.cognitive.microsoft.com`.

Ao utilizar a chave de subscrição de múltiplos serviços com a API de texto do Translator, tem de especificar a região de subscrição com o `Ocp-Apim-Subscription-Region` cabeçalho.

Autenticação de múltiplos serviço é suportada nestas regiões:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Pedidos de exemplo

Esta é uma chamada de exemplo para a API de pesquisa Web Bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta é uma chamada de exemplo para a API de texto do Translator:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Autenticar com um token de autenticação

Alguns serviços cognitivos do Azure aceite e, em alguns casos exigir, um token de autenticação. Atualmente, esses serviços oferecem suporte a tokens de autenticação:

* API de tradução de texto
* Serviços de voz: API REST de voz em texto
* Serviços de voz: API REST de texto para discurso

>[!NOTE]
> A ferramenta QnA Maker também usa o cabeçalho de autorização, mas requer uma chave de ponto final. Para obter mais informações, consulte [QnA Maker: Receber resposta da base de dados de conhecimento](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md).

>[!WARNING]
> Os serviços que suportam tokens de autenticação podem ser alterados ao longo do tempo, volte a API de verificação de referência para um serviço antes de utilizar este método de autenticação.

Ambos único serviço e chaves de subscrição de múltiplos serviços podem ser trocadas para tokens de autenticação. Tokens de autenticação são válidos durante 10 minutos.

Tokens de autenticação estão incluídos num pedido como o `Authorization` cabeçalho. O valor de token fornecido tem de ser precedido pelo `Bearer`, por exemplo: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Pedidos de exemplo

Utilize este URL para trocar uma chave de subscrição para um token de autenticação: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Estas regiões múltiplos serviços suportam a troca de token:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Depois de obter um token de autenticação, precisará passá-lo em cada pedido como o `Authorization` cabeçalho. Esta é uma chamada de exemplo para a API de texto do Translator:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="see-also"></a>Consulte também

* [O que são os Serviços Cognitivos?](welcome.md)
* [Preços dos serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Criar uma conta](cognitive-services-apis-create-account.md)
