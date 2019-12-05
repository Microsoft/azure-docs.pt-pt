---
title: Sobre o SDK de dispositivos de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Comece com o SDK de dispositivos de voz. O serviço de fala funciona com uma ampla variedade de dispositivos e fontes de áudio. O SDK de dispositivos de voz é uma biblioteca previamente atento que se encontra emparelhada com finalidade específica, kits de desenvolvimento de matriz de microfone.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 7f6793cab3d2d7cc54252bae3a7393f836256bdd
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815527"
---
# <a name="about-the-speech-devices-sdk"></a>Acerca do SDK de Dispositivos de Voz

O [serviço de fala](overview.md) funciona com uma ampla variedade de dispositivos e fontes de áudio. Agora, pode tirar seus aplicativos de fala para o próximo nível com correspondentes de hardware e software. O SDK de dispositivos de voz é uma biblioteca pretuned que se encontra emparelhada com finalidade específica, kits de desenvolvimento de matriz de microfone.

O SDK de dispositivos de voz pode ajudá-lo a:

- Teste rapidamente a novos cenários de voz.
- Integre mais facilmente o serviço de voz com base na cloud no seu dispositivo.
- Crie uma experiência de usuário excecional para os seus clientes.

O SDK de dispositivos de voz consome os [SDK de voz](speech-sdk.md). Ele usa o SDK de voz para enviar o áudio que é processado pelo nosso algoritmo de processamento de áudio avançado da matriz de microfone do dispositivo para o [serviço de voz](overview.md). Ele usa o áudio multicanal para oferecer extremidade campo mais preciso [reconhecimento de fala](speech-to-text.md) através de supressão de ruído, cancelamento de eco, beamforming e dereverberation.

Você também pode usar o SDK de dispositivos de fala para criar dispositivos de ambiente que tenham sua própria [palavra-chave personalizada](speech-devices-sdk-create-kws.md) para que a indicação que inicia uma interação do usuário seja exclusiva à sua marca.

O SDK dos dispositivos de fala facilita uma variedade de cenários habilitados para voz, como [assistentes de voz](https://aka.ms/bots/speech/va), sistemas de pedidos de unidade, [transcrição de conversa](conversation-transcription-service.md)e palestrantes inteligentes. Pode responder a utilizadores com texto, falar para si num padrão ou [voz personalizada](how-to-customize-voice-font.md), forneça os resultados da pesquisa, [traduzir](speech-translation.md) para outros idiomas e muito mais. Estamos ansiosos por ver o que cria!

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
> [!div class="nextstepaction"]
> [Obtenha uma chave de assinatura do serviço de fala gratuitamente](get-started.md)
