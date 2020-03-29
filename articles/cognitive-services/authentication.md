---
title: Autenticação
titleSuffix: Azure Cognitive Services
description: 'Existem três formas de autenticar um pedido a um recurso dos Serviços Cognitivos Azure: uma chave de subscrição, um símbolo do portador ou uma subscrição multi-serviço. Neste artigo, você vai aprender sobre cada método, e como fazer um pedido.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74423939"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Autenticação de pedidos aos Serviços Cognitivos Azure

Cada pedido a um Serviço Cognitivo Azure deve incluir um cabeçalho de autenticação. Este cabeçalho passa ao longo de uma chave de subscrição ou ficha de acesso, que é usada para validar a sua subscrição para um serviço ou grupo de serviços. Neste artigo, você aprenderá sobre três formas de autenticar um pedido e os requisitos para cada um.

* [Autenticar com uma chave de subscrição de um único serviço](#authenticate-with-a-single-service-subscription-key)
* [Autenticar com uma chave de subscrição multi-serviço](#authenticate-with-a-multi-service-subscription-key)
* [Autenticar com um símbolo](#authenticate-with-an-authentication-token)
* [Autenticação com Diretório Ativo Azure (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer um pedido, precisa de uma conta Azure e de uma subscrição do Serviço Cognitivo Azure. Se já tem uma conta, vá em frente e salte para a próxima secção. Se não tiver uma conta, temos um guia para o configurar em minutos: Criar uma conta de [Serviços Cognitivos para o Azure](cognitive-services-apis-create-account.md).

Pode obter a sua chave de subscrição no [portal Azure](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) depois de criar a sua conta, ou ativar um [teste gratuito.](https://azure.microsoft.com/try/cognitive-services/my-apis)

## <a name="authentication-headers"></a>Cabeçalhos de autenticação

Vamos rever rapidamente os cabeçalhos de autenticação disponíveis para utilização com os Serviços Cognitivos Azure.

| Cabeçalho | Descrição |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Utilize este cabeçalho para autenticar com uma chave de subscrição para um serviço específico ou uma chave de subscrição de vários serviços. |
| Ocp-Apim-Região de Assinatura | Este cabeçalho só é necessário quando utilizar uma chave de subscrição multi-serviço com a [API de Texto](./Translator/reference/v3-0-reference.md)tradutor . Utilize este cabeçalho para especificar a região de subscrição. |
| Autorização | Utilize este cabeçalho se estiver a utilizar um símbolo de autenticação. As etapas para realizar uma troca simbólica são detalhadas nas seguintes secções. O valor fornecido segue `Bearer <TOKEN>`este formato: . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Autenticar com uma chave de subscrição de um único serviço

A primeira opção é autenticar um pedido com uma chave de subscrição para um serviço específico, como o Texto tradutor. As chaves estão disponíveis no portal Azure para cada recurso que criou. Para utilizar uma chave de subscrição para autenticar um `Ocp-Apim-Subscription-Key` pedido, deve ser transmitido como cabeçalho.

Estes pedidos de amostra demonstram `Ocp-Apim-Subscription-Key` como usar o cabeçalho. Tenha em mente que, ao utilizar esta amostra, terá de incluir uma chave de subscrição válida.

Esta é uma chamada de amostra para a API de pesquisa web bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta é uma chamada de amostra para a API de Texto tradutor:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

O vídeo seguinte demonstra o uso de uma chave De Serviços Cognitivos.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Autenticar com uma chave de subscrição multi-serviço

>[!WARNING]
> Neste momento, estes serviços **não** suportam chaves multi-serviço: QnA Maker, Speech Services, Custom Vision e Anomaly Detetor.

Esta opção também utiliza uma chave de subscrição para autenticar pedidos. A principal diferença é que uma chave de subscrição não está ligada a um serviço específico, pelo contrário, uma única chave pode ser usada para autenticar pedidos de múltiplos Serviços Cognitivos. Consulte os preços dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter informações sobre disponibilidade regional, funcionalidades suportadas e preços.

A chave de subscrição é `Ocp-Apim-Subscription-Key` fornecida em cada pedido como cabeçalho.

[![Demonstração chave de subscrição de vários serviços para serviços cognitivos](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Regiões suportadas

Ao utilizar a chave de subscrição `api.cognitive.microsoft.com`multi-serviço para fazer um pedido, deve incluir a região no URL. Por exemplo: `westus.api.cognitive.microsoft.com`.

Ao utilizar a chave de subscrição de vários serviços com `Ocp-Apim-Subscription-Region` a API de Texto tradutor, deve especificar a região de subscrição com o cabeçalho.

A autenticação multi-serviço é suportada nestas regiões:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Pedidos de amostra

Esta é uma chamada de amostra para a API de pesquisa web bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta é uma chamada de amostra para a API de Texto tradutor:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Autenticar com um símbolo de autenticação

Alguns Serviços Cognitivos Azure aceitam, e em alguns casos exigem, um símbolo de autenticação. Atualmente, estes serviços suportam fichas de autenticação:

* API de tradução de texto
* Serviços de Discurso: Discurso-a-texto REST API
* Serviços de Fala: Texto-a-discurso REST API

>[!NOTE]
> O Fabricante QnA também usa o cabeçalho de autorização, mas requer uma chave de ponto final. Para mais informações, consulte [QnA Maker: Obtenha resposta da base de conhecimento](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> Os serviços que suportam fichas de autenticação podem ser alterados ao longo do tempo, verifique a referência DaPI para um serviço antes de utilizar este método de autenticação.

Tanto as teclas de subscrição de serviço único como as chaves de subscrição multi-serviço podem ser trocadas por fichas de autenticação. Os tokens de autenticação são válidos por 10 minutos.

Os tokens de autenticação estão `Authorization` incluídos num pedido como cabeçalho. O valor simbólico fornecido deve `Bearer`ser precedido, por exemplo: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Pedidos de amostra

Utilize este URL para trocar uma chave de `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`subscrição por um símbolo de autenticação: .

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Estas regiões multi-serviços apoiam a troca de fichas:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Depois de obter um símbolo de autenticação, terá de passá-lo em cada pedido como `Authorization` cabeçalho. Esta é uma chamada de amostra para a API de Texto tradutor:

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
