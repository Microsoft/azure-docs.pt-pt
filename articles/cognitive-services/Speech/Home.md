---
title: Serviço de voz do Bing do Microsoft | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Utilize a API de voz de Microsoft para adicionar ações orientadas por voz às suas aplicações, incluindo interação em tempo real com os utilizadores.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d2c7211831658a18e65e04aa753607f4eb22dac8
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673193"
---
# <a name="what-is-bing-speech"></a>O que é de voz do Bing?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

API de voz o Bing de Microsoft baseado na nuvem fornece aos desenvolvedores uma forma fácil de criar funcionalidades poderosas com funcionalidade de voz em seus aplicativos, como controle de comandos de voz, caixa de diálogo de utilizador com conversa natural de voz e transcrição de voz e ditado. A API de voz de Microsoft oferece suporte a ambos *conversão de voz em texto* e *texto em voz* conversão.

- **Conversão de voz em texto** API converte voz humana em texto que pode ser utilizado como entrada ou de comandos para controlar a sua aplicação.
- **Texto em voz** API converte texto em fluxos de áudio que podem ser reproduzidos para o utilizador da sua aplicação.

## <a name="speech-to-text-speech-recognition"></a>Conversão de voz em texto (reconhecimento de voz)

API de reconhecimento de voz de Microsoft *converte* fluxos de áudio em texto que seu aplicativo pode apresentar ao utilizador ou agir como comando de entrada. Ele fornece duas formas para os programadores adicionarem voz às suas aplicações: REST APIs **ou** bibliotecas de clientes baseada na Websocket.

- [REST APIs](GetStarted/GetStartedREST.md): Os desenvolvedores podem usar chamadas HTTP a partir das suas aplicações para o serviço para o reconhecimento de fala.
- [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md): Para recursos avançados, os desenvolvedores podem transferir as bibliotecas de cliente do Microsoft Speech e vincular a seus aplicativos.  As bibliotecas de cliente estão disponíveis em várias plataformas (Windows, Android, iOS) usando linguagens diferentes (c#, Java, JavaScript, ObjectiveC). Ao contrário das REST APIs, as bibliotecas de cliente utilizam o protocolo baseado em Websocket.

| Casos de utilização | [APIs REST](GetStarted/GetStartedREST.md) | [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Converter um áudio falado resumo, por exemplo, os comandos (comprimento áudio < 15 s) sem resultados intermediárias | Sim | Sim |
| Converta um áudio longo (> 15 s) | Não | Sim |
| Áudio de Stream com resultados provisórias pretendido | Não | Sim |
| Compreender o texto convertido de áudio a utilizar o LUIS | Não | Sim |

Qualquer que seja desenvolvedores abordagem escolha (REST APIs ou bibliotecas de cliente), o serviço de voz de Microsoft suporta o seguinte:

- Avançadas tecnologias de reconhecimento de voz da Microsoft que são utilizadas pelo Cortana, ditado do Office, Translator do Office e outros produtos da Microsoft.
- Reconhecimento contínuo em tempo real. A API de reconhecimento de fala permite que os usuários a transcrição de áudio em texto em tempo real e oferece suporte para receber os resultados intermediários das palavras que tem sido reconhecidos até agora. O serviço de voz também suporta a deteção de fim da voz. Além disso, os utilizadores podem escolher capacidades de formatação adicionais, como o uso e pontuação, linguagem inapropriada da máscara e normalização do texto.
- Suporta com resultados de reconhecimento de voz para otimização *interativo*, *conversação*, e *ditado* cenários. Para cenários de utilizador que necessitam de modelos de linguagem personalizados e modelos acústicos, [serviço de voz personalizada](../custom-speech-service/cognitive-services-custom-speech-home.md) permite-lhe criar modelos de voz adaptadas às suas aplicações e os seus utilizadores.
- Suporta muitos idiomas em múltiplos dialetos. Para obter a lista completa das linguagens suportadas em cada modo de reconhecimento, consulte [idiomas de reconhecimento](api-reference-rest/supportedlanguages.md).
- Integração com compreensão de idiomas. Além de converter o entrada de áudio em texto, o *conversão de voz em texto* fornece aos aplicativos uma capacidade adicional para compreender o que significa que o texto. Ele usa o [Service(LUIS) inteligente de compreensão de linguagem](../LUIS/what-is-luis.md) para extrair intenções e entidades do texto reconhecido.

### <a name="next-steps"></a>Passos Seguintes

- Começar a utilizar o serviço de reconhecimento de voz de Microsoft com [REST APIs](GetStarted/GetStartedREST.md) ou [bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md).
- Confira [aplicações de exemplo](samples.md) sua preferencial na linguagem de programação.
- Vá para a secção de referência para encontrar [protocolo de voz de Microsoft](API-Reference-REST/websocketprotocol.md) detalhes e referências de API.

## <a name="text-to-speech-speech-synthesis"></a>Texto em fala (sintetização de voz)

*Texto em voz* APIs utilizam REST para converter o texto estruturado numa transmissão de áudio. As APIs oferecem conversão de texto em voz rápida em várias vozes e linguagens. Além dos utilizadores também têm a capacidade de alterar as características de áudio como pronúncia, o volume, o argumento de venda etc. as etiquetas SSML a utilizar.

### <a name="next-steps"></a>Passos Seguintes

- Introdução ao utilizar o serviço de texto em voz da Microsoft: [Referência da API de texto em voz](api-reference-rest/bingvoiceoutput.md). Para obter a lista completa de linguagens e vozes suportados pelo texto em voz, consulte [localidades suportados e tipos de voz](api-reference-rest/bingvoiceoutput.md#SupLocales).
