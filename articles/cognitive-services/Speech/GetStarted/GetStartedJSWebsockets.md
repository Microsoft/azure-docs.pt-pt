---
title: Introdução à API de reconhecimento de Fala do Bing em JavaScript | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use a API de reconhecimento de Fala do Bing em serviços cognitivas para desenvolver aplicativos que convertam continuamente áudio falado em texto.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: eef1a4885b77ae94f11d3d5bda5ded9b70ed63a4
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965817"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Introdução à API de reconhecimento de fala em JavaScript

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Você pode desenvolver aplicativos que convertem áudio falado em texto usando a API de reconhecimento de fala. A biblioteca de cliente do JavaScript usa o [protocolo WebSocket do serviço de fala](../API-Reference-REST/websocketprotocol.md), que permite que você fale e receba o texto transcrevedo simultaneamente. Este artigo ajuda você a começar a usar a API de reconhecimento de fala em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Assine a API de reconhecimento de fala e obtenha uma chave de assinatura de avaliação gratuita

O Speech API é parte dos serviços cognitivas. Você pode obter chaves de assinatura de avaliação gratuita da página de [assinatura de serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/) . Depois de selecionar a Speech API, selecione **obter chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, para que você possa usar qualquer chave.

> [!IMPORTANT]
> Obtenha uma chave de assinatura. Antes de poder usar as bibliotecas de cliente de fala, você deve ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Introdução

Nesta seção, descreveremos as etapas necessárias para carregar uma página HTML de exemplo. O exemplo está localizado em nosso [repositório GitHub](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Você pode **abrir o exemplo diretamente** do repositório ou **abrir o exemplo de uma cópia local** do repositório.

> [!NOTE]
> Alguns navegadores bloqueiam o acesso ao microfone na origem não segura. Portanto, é recomendável hospedar o "exemplo"/"seu aplicativo" em https para fazê-lo funcionar em todos os navegadores com suporte.

### <a name="open-the-sample-directly"></a>Abrir o exemplo diretamente

Adquira uma chave de assinatura conforme descrito acima. Em seguida, abra o [link para o exemplo](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Isso carregará a página no navegador padrão (renderizado usando [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Abrir o exemplo de uma cópia local

Para testar o exemplo localmente, clone este repositório:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Compile as fontes TypeScript e agrupe-as em um único arquivo JavaScript (o[NPM](https://www.npmjs.com/) precisa ser instalado em seu computador). Altere para a raiz do repositório clonado e execute os comandos:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Abra `samples\browser\Sample.html` em seu navegador favorito.

## <a name="next-steps"></a>Passos seguintes

Mais informações sobre como incluir o SDK em sua própria página da Web estão disponíveis [aqui](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Observações

- A API de reconhecimento de fala dá suporte a três [modos de reconhecimento](../concepts.md#recognition-modes). Você pode alternar o modo atualizando a função **Setup ()** encontrada no arquivo Sample. html. O exemplo define o modo como `Interactive` por padrão. Para alterar o modo, atualize o parâmetro `SR.RecognitionMode.Interactive` para outro modo. Por exemplo, altere o parâmetro para `SR.RecognitionMode.Conversation`.
- Para obter uma lista completa dos idiomas com suporte, consulte [idiomas com suporte](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Tópicos relacionados

- [Repositório de exemplo da API de reconhecimento de fala do JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Introdução à API REST](GetStartedREST.md)
