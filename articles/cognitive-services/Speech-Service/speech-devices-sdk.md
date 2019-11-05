---
title: Sobre o SDK de dispositivos de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Introdução ao SDK de dispositivos de fala. O serviço de fala funciona com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar seus aplicativos de fala para o próximo nível com hardware e software correspondentes. O SDK dos dispositivos de fala é uma biblioteca previamente ajustada que é emparelhada com os kits de desenvolvimento de matriz de microfone criados por finalidade.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 243072477c7d249d7066a7a448061c51a0bd2f34
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468729"
---
# <a name="about-the-speech-devices-sdk"></a>Sobre o SDK de dispositivos de fala

Os [serviços de fala](overview.md) funcionam com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar seus aplicativos de fala para o próximo nível com hardware e software correspondentes. O SDK dos dispositivos de fala é uma biblioteca predefinida que é emparelhada com os kits de desenvolvimento de matriz de microfone criados por finalidade.

O SDK dos dispositivos de fala pode ajudá-lo a:

* Teste rapidamente novos cenários de voz.
* Integre mais facilmente os serviços de fala baseados em nuvem em seu dispositivo.
* Crie uma experiência de usuário excepcional para seus clientes.

O SDK de dispositivos de fala consome o [SDK de fala](speech-sdk.md). Ele usa o SDK de fala para enviar o áudio que é processado pelo nosso algoritmo de processamento de áudio avançado da matriz de microfone do dispositivo para os [serviços de fala](overview.md). Ele usa áudio multicanal para fornecer [reconhecimento de fala](speech-to-text.md) de campo mais preciso por meio de supressão de ruído, cancelamento de eco, beamforming e dereverberation.

Você também pode usar o SDK de dispositivos de fala para criar dispositivos de ambiente que tenham sua própria [palavra-chave personalizada](speech-devices-sdk-create-kws.md) para que a indicação que inicia uma interação do usuário seja exclusiva à sua marca.

O SDK dos dispositivos de fala facilita uma variedade de cenários habilitados para voz, como [assistentes de voz](https://aka.ms/bots/speech/va), sistemas de pedidos de unidade, [transcrição de conversa](conversation-transcription-service.md)e palestrantes inteligentes. Você pode responder a usuários com texto, falar de volta para eles em uma voz padrão ou [personalizada](how-to-customize-voice-font.md), fornecer resultados da pesquisa, [traduzir](speech-translation.md) para outras linguagens e muito mais. Estamos ansiosos para ver o que você cria!

## <a name="get-the-speech-devices-sdk"></a>Obter o SDK de Dispositivos de Voz

### <a name="android"></a>Android

Para dispositivos Android, baixe a versão mais recente do [SDK de dispositivos de fala do Android](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Para o Windows, o aplicativo de exemplo é fornecido como um aplicativo Java de plataforma cruzada. Baixe a versão mais recente do [SDK de dispositivos de fala do JRE](https://aka.ms/sdsdk-download-JRE).
O aplicativo é criado com o pacote do SDK de fala e o Java IDE do Eclipse (v4) no Windows de 64 bits. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

### <a name="linux"></a>Linux

Para o Linux, o aplicativo de exemplo é fornecido como um aplicativo Java de plataforma cruzada. Baixe a versão mais recente do [SDK de dispositivos de fala do JRE](https://aka.ms/sdsdk-download-JRE).
O aplicativo é criado com o pacote do SDK de fala e o eclipse Java IDE (v4) no Linux de 64 bits (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9). É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolha seu dispositivo de fala](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Obtenha uma chave de assinatura de serviços de fala gratuitamente](get-started.md)
