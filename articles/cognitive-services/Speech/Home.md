---
title: Serviço de Fala do Bing da Microsoft | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use o Microsoft Speech API para adicionar ações controladas por fala aos seus aplicativos, incluindo a interação em tempo real com os usuários.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff9de2557583eecd5ddb2acd97b445a93abc5fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966653"
---
# <a name="what-is-bing-speech"></a>O que é Fala do Bing?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

O Microsoft API de Fala do Bing baseado em nuvem fornece aos desenvolvedores uma maneira fácil de criar recursos avançados habilitados para fala em seus aplicativos, como controle de comando de voz, caixa de diálogo de usuário usando conversa de fala natural e transcrição de fala e ditado. O Microsoft Speech API dá suporte à conversão *de fala em texto* e *conversão de texto em fala* .

- **Fala para texto** A API converte a fala humana em texto que pode ser usada como entrada ou comandos para controlar seu aplicativo.
- **Conversão de texto em fala** A API converte texto em fluxos de áudio que podem ser reproduzidos para o usuário do seu aplicativo.

## <a name="speech-to-text-speech-recognition"></a>Fala para texto (reconhecimento de fala)

A API de reconhecimento de fala da Microsoft *transcreve* fluxos de áudio em texto que seu aplicativo pode exibir para o usuário ou agir como entrada de comando. Ele fornece duas maneiras para os desenvolvedores adicionarem fala aos seus aplicativos: APIs REST **ou** bibliotecas de cliente baseadas em WebSocket.

- [APIs REST](GetStarted/GetStartedREST.md): Os desenvolvedores podem usar chamadas HTTP de seus aplicativos para o serviço para reconhecimento de fala.
- [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md): Para recursos avançados, os desenvolvedores podem baixar as bibliotecas de cliente do Microsoft Speech e vinculá-las a seus aplicativos.  As bibliotecas de cliente estão disponíveis em várias plataformas (Windows, Android, iOS) usando diferentes idiomasC#(, Java, JavaScript, ObjectiveC). Ao contrário das APIs REST, as bibliotecas de cliente utilizam o protocolo baseado em WebSocket.

| Casos de utilização | [APIs REST](GetStarted/GetStartedREST.md) | [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Converter um áudio falado curto, por exemplo, comandos (comprimento de áudio < 15 s) sem resultados provisórios | Sim | Sim |
| Converter um áudio longo (> 15 s) | Não | Sim |
| Transmitir áudio com resultados provisórios desejados | Não | Sim |
| Entender o texto convertido de áudio usando LUIS | Não | Sim |

Seja qual for a abordagem que os desenvolvedores escolhem (APIs REST ou bibliotecas de cliente), o serviço de fala da Microsoft oferece suporte ao seguinte:

- Tecnologias avançadas de reconhecimento de fala da Microsoft que são usadas pela Cortana, o ditado do Office, o Office Translator e outros produtos da Microsoft.
- Reconhecimento contínuo em tempo real. A API de reconhecimento de fala permite aos usuários transcrever áudio em texto em tempo real e dá suporte para receber os resultados intermediários das palavras que foram reconhecidas até o momento. O serviço de fala também oferece suporte à detecção de fim de fala. Além disso, os usuários podem escolher recursos de formatação adicionais, como maiúsculas e minúsculas, mascaramento de profanação e normalização de texto.
- Dá suporte a resultados de reconhecimento de fala otimizados para cenários *interativos*, de *conversa*e de *ditado* . Para cenários de usuário que exigem modelos de linguagem e modelos acústicos personalizados, [fala personalizada serviço](../custom-speech-service/cognitive-services-custom-speech-home.md) permite que você crie modelos de fala personalizados para seu aplicativo e seus usuários.
- Suporte a muitos idiomas falados em vários dialetos. Para obter a lista completa de idiomas com suporte em cada modo de reconhecimento, consulte [linguagens de reconhecimento](api-reference-rest/supportedlanguages.md).
- Integração com o reconhecimento de linguagem. Além de converter o áudio de entrada em texto, a *fala em texto* fornece aos aplicativos um recurso adicional para entender o que significa o texto. Ele usa o [Luis (serviço inteligente reconhecimento vocal)](../LUIS/what-is-luis.md) para extrair intenções e entidades do texto reconhecido.

### <a name="next-steps"></a>Passos seguintes

- Comece a usar o serviço de reconhecimento de fala da Microsoft com [APIs REST](GetStarted/GetStartedREST.md) ou [bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md).
- Confira os [aplicativos de exemplo](samples.md) em sua linguagem de programação preferida.
- Vá para a seção de referência para localizar detalhes [do Microsoft Speech Protocol](API-Reference-REST/websocketprotocol.md) e referências de API.

## <a name="text-to-speech-speech-synthesis"></a>Conversão de texto em fala (síntese de fala)

*Conversão de texto em fala* As APIs usam REST para converter texto estruturado em um fluxo de áudio. As APIs fornecem conversão rápida de texto em fala em várias vozes e linguagens. Além disso, os usuários também têm a capacidade de alterar características de áudio, como pronúncia, volume, pitch, etc. usando marcas SSML.

### <a name="next-steps"></a>Passos Seguintes

- Introdução ao uso do Microsoft Text to Speech Service: [Referência de API de conversão de texto em fala](api-reference-rest/bingvoiceoutput.md). Para obter a lista completa de idiomas e vozes com suporte pelo Conversão de Texto em Fala, consulte [localidades com suporte e fontes de voz](api-reference-rest/bingvoiceoutput.md#SupLocales).
