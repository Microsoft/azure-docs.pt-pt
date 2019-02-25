---
title: Comece com a API de reconhecimento de voz do Bing em JavaScript | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Utilize a API de reconhecimento de voz do Bing nos serviços cognitivos para desenvolver aplicações que continuamente converter áudio falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17901ad40a48e9ee8d1a8b872b04ad52b75b3a52
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672411"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Comece com a API de reconhecimento de voz em JavaScript

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Pode desenvolver aplicações que converta áudio falado em texto ao utilizar a API de reconhecimento de voz. A biblioteca de cliente JavaScript utiliza a [protocolo WebSocket do serviço de voz](../API-Reference-REST/websocketprotocol.md), que permite-lhe falar e receber transcrito texto em simultâneo. Este artigo ajuda-o a começar com a API de reconhecimento de voz em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subscrever a API de reconhecimento de voz e obter uma chave de subscrição de avaliação gratuita

A API de voz faz parte dos serviços cognitivos. Pode obter chaves de subscrição de avaliação gratuita do [subscrição dos serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/) página. Depois de selecionar a API de voz, selecione **obter a chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão associadas para a mesma cota, pelo que pode utilizar qualquer uma das chaves.

> [!IMPORTANT]
> Obter uma chave de subscrição. Antes de poder utilizar bibliotecas de cliente de voz, tem de ter uma [chave de subscrição](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Introdução

Nesta secção, irá guiá-lo pelos passos necessários para carregar uma página HTML de exemplo. O exemplo está localizado no nosso [repositório do GitHub](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Pode **abra o exemplo diretamente** do repositório, ou **abra o exemplo a partir de uma cópia local** do repositório.

> [!NOTE]
> Alguns browsers bloquear o acesso ao microfone na origem não segura. Por isso, recomenda-se para alojar o "exemplo" / "a aplicação" em https para que isso funcione em todos os browsers suportados.

### <a name="open-the-sample-directly"></a>Abra o exemplo diretamente

Adquira uma chave de subscrição, conforme descrito acima. Em seguida, abra a [ligação para o exemplo](https://htmlpreview.github.io/? https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Isso irá carregar a página no seu browser predefinido (processado usando [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Abra o exemplo a partir de uma cópia local

Para experimentar o exemplo localmente, este repositório do clone:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Compilar as origens do TypeScript e agrupá-los num único arquivo JavaScript ([npm](https://www.npmjs.com/) tem de ser instalado no seu computador). Alterar para a raiz do repositório clonado e execute os comandos:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Abra `samples\browser\Sample.html` no seu browser favorito.

## <a name="next-steps"></a>Passos Seguintes

Estão disponíveis mais informações sobre como incluir o SDK na sua própria página da Web [aqui](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Observações

- A API de reconhecimento de voz oferece suporte a três [modos de reconhecimento](../concepts.md#recognition-modes). Pode alternar o modo ao atualizar o **Setup()** função encontrada no arquivo Sample.html. O exemplo define o modo como `Interactive` por predefinição. Para alterar o modo, atualize o parâmetro `SR.RecognitionMode.Interactive` para o outro modo. Por exemplo, altere o parâmetro para `SR.RecognitionMode.Conversation`.
- Para obter uma lista completa dos idiomas suportados, consulte [idiomas suportados](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Tópicos relacionados

- [Repositório de exemplo da API de reconhecimento de voz de JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Comece com a API REST](GetStartedREST.md)
