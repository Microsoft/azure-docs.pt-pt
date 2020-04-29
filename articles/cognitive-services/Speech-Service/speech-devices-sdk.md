---
title: Dispositivos de Fala SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Começa com o SDK dos Dispositivos de Fala. O serviço Speech funciona com uma grande variedade de dispositivos e fontes de áudio. O SDK de Dispositivos de Fala é uma biblioteca pré-afinada que é emparelhada com kits de desenvolvimento de matrizes de microfone sintetizados de propósito.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79370649"
---
# <a name="what-is-the-speech-devices-sdk"></a>O que é o SDK dos Dispositivos de Fala?

O [serviço Speech](overview.md) funciona com uma grande variedade de dispositivos e fontes de áudio. Agora, pode levar as suas aplicações de fala para o próximo nível com hardware e software combinados. O SDK de Dispositivos de Fala é uma biblioteca pré-afinada que é emparelhada com kits de desenvolvimento de matrizes de microfone sintetizados de propósito.

O SDK dispositivos de fala pode ajudá-lo:

- Teste rapidamente novos cenários de voz.
- Integre mais facilmente o serviço de Fala baseado na nuvem no seu dispositivo.
- Crie uma experiência excecional de utilizador para os seus clientes.

O SDK dispositivos de fala consome o [SDK](speech-sdk.md)da Fala . Utilizando os nossos algoritmos avançados de processamento de áudio com a matriz do microfone do dispositivo para enviar o áudio para o [serviço De Fala](overview.md). Proporciona [um reconhecimento](speech-to-text.md) preciso da fala no campo através da supressão de ruído, cancelamento de eco, feixe e dereverberação.

Também pode utilizar o SDK dos Dispositivos de Fala para construir dispositivos ambiente que tenham a sua própria [palavra-chave personalizada](speech-devices-sdk-create-kws.md). Uma palavra-chave personalizada fornece uma deixa que inicia uma interação do utilizador que é única para a sua marca.

O SDK dos Dispositivos de Fala permite uma variedade de cenários ativados por voz, tais como assistentes de [voz, sistemas](https://aka.ms/bots/speech/va)de encomenda drive-thru, [transcrição](conversation-transcription-service.md)de conversas e colunas inteligentes. Pode responder aos utilizadores com texto, falar-lhes com uma voz por defeito ou [personalizada,](how-to-customize-voice-font.md)fornecer resultados de pesquisa, [traduzir](speech-translation.md) para outros idiomas, e muito mais. Estamos ansiosos para ver o que constrói!

## <a name="get-the-speech-devices-sdk"></a>Obter o SDK de Dispositivos de Voz

### <a name="android"></a>Android

Para Android, os dispositivos descarregam a versão mais recente do [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Para o Windows, a aplicação da amostra é fornecida como uma aplicação java transversal. Descarregue a versão mais recente do [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
A aplicação é construída com o pacote Speech SDK, e o Eclipse Java IDE (v4) em Windows de 64 bits. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

### <a name="linux"></a>Linux

Para o Linux, a aplicação da amostra é fornecida como uma aplicação java transversal. Descarregue a versão mais recente do [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
A aplicação é construída com o pacote Speech SDK, e o Eclipse Java IDE (v4) em 64 bits Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

Binários adicionais são fornecidos para suportar os próximos dispositivos, [Roobo v2 DDK,](https://aka.ms/sdsdk-download-roobov2) [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)e [ALTIFALANTE GGEC](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolha o seu dispositivo de fala](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
