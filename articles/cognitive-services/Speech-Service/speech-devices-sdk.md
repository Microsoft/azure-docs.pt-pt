---
title: Dispositivos de fala SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Começa com os Dispositivos de Fala SDK. O serviço Speech funciona com uma grande variedade de dispositivos e fontes de áudio. O Speech Devices SDK é uma biblioteca pré-afinada que é emparelhada com kits de desenvolvimento de matriz de microfones construídos de propósito.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: 0309329b37436022dc88f8951853c4bb09a2c080
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015278"
---
# <a name="what-is-the-speech-devices-sdk"></a>O que é o SDK dos Dispositivos de Fala?

O [serviço Speech](overview.md) funciona com uma grande variedade de dispositivos e fontes de áudio. Agora, pode levar as suas aplicações de discurso para o próximo nível com hardware e software combinados. O Speech Devices SDK é uma biblioteca pré-afinada que é emparelhada com kits de desenvolvimento de matriz de microfones construídos de propósito.

O SDK de Dispositivos de Fala pode ajudá-lo:

- Teste rapidamente novos cenários de voz.
- Integrar mais facilmente o serviço de fala baseado na nuvem no seu dispositivo.
- Crie uma experiência de utilizador excecional para os seus clientes.

O Dispositivo de Fala SDK consome o [SDK da fala](speech-sdk.md). Utilizando os nossos algoritmos avançados de processamento de áudio com a matriz de microfone do dispositivo para enviar o áudio para o [serviço Speech](overview.md). Proporciona [um reconhecimento](speech-to-text.md) preciso da fala de campo distante através da supressão de ruído, cancelamento de eco, formação de feixes e dereverberação.

Também pode utilizar o Speech Devices SDK para construir dispositivos ambiente que tenham a sua própria [palavra-chave personalizada.](./custom-keyword-basics.md) Uma palavra-chave personalizada fornece uma sugestão que inicia uma interação do utilizador que é única para a sua marca.

O Speech Devices SDK permite uma variedade de cenários ativados por voz, tais como assistentes de [voz, sistemas](./voice-assistants.md)de encomenda drive-thru, [transcrição de conversação](./conversation-transcription.md)e colunas inteligentes. Pode responder aos utilizadores com texto, responder-lhes numa voz padrão ou [personalizada,](./how-to-custom-voice-create-voice.md)fornecer resultados de pesquisa, [traduzir](speech-translation.md) para outros idiomas, e muito mais. Estamos ansiosos para ver o que constrói!

## <a name="get-the-speech-devices-sdk"></a>Obter o SDK de Dispositivos de Voz

### <a name="android"></a>Android

O Speech Devices SDK para Android suporta os dispositivos [Roobo v1](speech-devices-sdk-roobo-v1.md) e equivalentes, para estes descarregar a versão mais recente do [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android).


Se tiver um dispositivo Android diferente, como um telemóvel ou telemóvel, comece com o [Android Speech SDK](speech-sdk.md)


### <a name="windows"></a>Windows

Para o Windows, a aplicação da amostra é fornecida como uma aplicação Java de plataforma cruzada. Descarregue a versão mais recente do [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
A aplicação é construída com o pacote Speech SDK e o Eclipse Java IDE (v4) em Windows de 64 bits. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

### <a name="linux"></a>Linux

Para o Linux, a aplicação da amostra é fornecida como uma aplicação Java de plataforma cruzada. Descarregue a versão mais recente do [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
A aplicação é construída com o pacote Speech SDK, e o Eclipse Java IDE (v4) em Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

Binários adicionais são fornecidos para suportar os dispositivos futuros, [Roobo v2 DDK,](https://aka.ms/sdsdk-download-roobov2) [Urbetter DDK,](https://aka.ms/sdsdk-download-urbetter) [GGEC Speaker,](https://aka.ms/sdsdk-download-speaker) [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32), e [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Escolha o seu dispositivo de fala](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)