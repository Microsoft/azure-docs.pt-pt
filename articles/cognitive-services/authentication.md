---
title: Autenticação
titleSuffix: Azure Cognitive Services
description: 'Há três maneiras de autenticar uma solicitação para um recurso de serviços cognitivas do Azure: uma chave de assinatura, um token de portador ou uma assinatura de vários serviços. Neste artigo, você aprenderá sobre cada método e como fazer uma solicitação.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423939"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Autenticar solicitações para serviços cognitivas do Azure

Cada solicitação para um serviço de cognitiva do Azure deve incluir um cabeçalho de autenticação. Esse cabeçalho passa uma chave de assinatura ou um token de acesso, que é usado para validar sua assinatura para um serviço ou grupo de serviços. Neste artigo, você aprenderá sobre três maneiras de autenticar uma solicitação e os requisitos para cada uma delas.

* [Autenticar com uma chave de assinatura de serviço único](#authenticate-with-a-single-service-subscription-key)
* [Autenticar com uma chave de assinatura de vários serviços](#authenticate-with-a-multi-service-subscription-key)
* [Autenticar com um token](#authenticate-with-an-authentication-token)
* [Autenticar com Azure Active Directory (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer uma solicitação, você precisa de uma conta do Azure e uma assinatura de serviços cognitivas do Azure. Se você já tiver uma conta, vá em frente e pule para a próxima seção. Se você não tiver uma conta, temos um guia para configurá-lo em minutos: [criar uma conta de serviços cognitivas para o Azure](cognitive-services-apis-create-account.md).

Você pode obter sua chave de assinatura do [portal do Azure](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) depois de criar sua conta ou ativar uma [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/my-apis).

## <a name="authentication-headers"></a>Cabeçalhos de autenticação

Vamos examinar rapidamente os cabeçalhos de autenticação disponíveis para uso com os serviços cognitivas do Azure.

| Cabeçalho | Descrição |
|--------|-------------|
| OCP-Apim-Subscription-Key | Use esse cabeçalho para autenticar com uma chave de assinatura para um serviço específico ou uma chave de assinatura de vários serviços. |
| OCP-APIM-Subscription – região | Esse cabeçalho só é necessário ao usar uma chave de assinatura de vários serviços com o [API de tradução de texto](./Translator/reference/v3-0-reference.md). Use esse cabeçalho para especificar a região da assinatura. |
| Autorização | Use esse cabeçalho se você estiver usando um token de autenticação. As etapas para executar uma troca de token são detalhadas nas seções a seguir. O valor fornecido segue este formato: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Autenticar com uma chave de assinatura de serviço único

A primeira opção é autenticar uma solicitação com uma chave de assinatura para um serviço específico, como Tradução de Texto. As chaves estão disponíveis no portal do Azure para cada recurso que você criou. Para usar uma chave de assinatura para autenticar uma solicitação, ela deve ser passada como o cabeçalho de `Ocp-Apim-Subscription-Key`.

Essas solicitações de exemplo demonstram como usar o cabeçalho `Ocp-Apim-Subscription-Key`. Tenha em mente, ao usar este exemplo, você precisará incluir uma chave de assinatura válida.

Esta é uma chamada de exemplo para o API de Pesquisa na Web do Bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta é uma chamada de exemplo para o API de Tradução de Texto:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

O vídeo a seguir demonstra como usar uma chave de serviços cognitivas.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Autenticar com uma chave de assinatura de vários serviços

>[!WARNING]
> Neste momento, esses serviços **não** dão suporte a chaves de vários serviços: QnA Maker, serviços de fala, visão personalizada e detector de anomalias.

Essa opção também usa uma chave de assinatura para autenticar solicitações. A principal diferença é que uma chave de assinatura não está vinculada a um serviço específico, em vez disso, uma única chave pode ser usada para autenticar solicitações para vários serviços cognitivas. Consulte [preços de serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter informações sobre disponibilidade regional, recursos com suporte e preços.

A chave de assinatura é fornecida em cada solicitação como o cabeçalho de `Ocp-Apim-Subscription-Key`.

[demonstração da chave de assinatura de vários serviços ![para serviços cognitivas](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Regiões suportadas

Ao usar a chave de assinatura de vários serviços para fazer uma solicitação para `api.cognitive.microsoft.com`, você deve incluir a região na URL. Por exemplo: `westus.api.cognitive.microsoft.com`.

Ao usar a chave de assinatura de vários serviços com o API de Tradução de Texto, você deve especificar a região da assinatura com o cabeçalho `Ocp-Apim-Subscription-Region`.

A autenticação de vários serviços tem suporte nessas regiões:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Solicitações de amostra

Esta é uma chamada de exemplo para o API de Pesquisa na Web do Bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta é uma chamada de exemplo para o API de Tradução de Texto:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Autenticar com um token de autenticação

Alguns serviços cognitivas do Azure aceitam e, em alguns casos, exigem um token de autenticação. Atualmente, esses serviços dão suporte a tokens de autenticação:

* API de tradução de texto
* Serviços de fala: API REST de fala em texto
* Serviços de fala: API REST de conversão de texto em fala

>[!NOTE]
> QnA Maker também usa o cabeçalho Authorization, mas requer uma chave de ponto de extremidade. Para obter mais informações, consulte [QnA Maker: obter resposta da base de dados de conhecimento](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> Os serviços que oferecem suporte a tokens de autenticação podem mudar com o tempo, verifique a referência de API para um serviço antes de usar esse método de autenticação.

As chaves de assinatura de serviço único e de vários serviços podem ser trocadas para tokens de autenticação. Os tokens de autenticação são válidos por 10 minutos.

Os tokens de autenticação são incluídos em uma solicitação como o cabeçalho de `Authorization`. O valor do token fornecido deve ser precedido por `Bearer`, por exemplo: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Solicitações de amostra

Use esta URL para trocar uma chave de assinatura para um token de autenticação: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Essas regiões de vários serviços oferecem suporte à troca de tokens:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Depois de obter um token de autenticação, você precisará passá-lo em cada solicitação como o cabeçalho de `Authorization`. Esta é uma chamada de exemplo para o API de Tradução de Texto:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Consulte também

* [O que são os Serviços Cognitivos?](welcome.md)
* [Preços dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Subdomínios personalizados](cognitive-services-custom-subdomains.md)
