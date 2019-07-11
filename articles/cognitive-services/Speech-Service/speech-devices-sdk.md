---
title: Sobre os dispositivos de voz SDK - serviços de voz
titleSuffix: Azure Cognitive Services
description: Comece com o SDK de dispositivos de voz. Os serviços de voz funcionam com uma ampla variedade de dispositivos e fontes de áudio. Agora, pode tirar seus aplicativos de fala para o próximo nível com correspondentes de hardware e software. O SDK de dispositivos de voz é uma biblioteca previamente atento que se encontra emparelhada com finalidade específica, kits de desenvolvimento de matriz de microfone.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 341f35d04c7f3f591198acbbf05ac32afe84e95c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718541"
---
# <a name="about-the-speech-devices-sdk"></a>Sobre os dispositivos de voz SDK

O [serviços de voz](overview.md) funcionam com uma grande variedade de dispositivos e fontes de áudio. Agora, pode tirar seus aplicativos de fala para o próximo nível com correspondentes de hardware e software. O SDK de dispositivos de voz é uma biblioteca pretuned que se encontra emparelhada com finalidade específica, kits de desenvolvimento de matriz de microfone.

O SDK de dispositivos de voz pode ajudá-lo a:

* Teste rapidamente a novos cenários de voz.
* Integre mais facilmente os serviços de voz com base na cloud no seu dispositivo.
* Crie uma experiência de usuário excecional para os seus clientes.

O SDK de dispositivos de voz consome os [SDK de voz](speech-sdk.md). Ele usa o SDK de voz para enviar o áudio que é processado pelo nosso algoritmo de processamento de áudio avançado da matriz de microfone do dispositivo para o [serviços de voz](overview.md). Ele usa o áudio multicanal para oferecer extremidade campo mais preciso [reconhecimento de fala](speech-to-text.md) através de supressão de ruído, cancelamento de eco, beamforming e dereverberation.

Também pode utilizar o SDK de dispositivos de voz para criar dispositivos de ambiente que têm seu próprio [personalizado do word de reativação](speech-devices-sdk-create-kws.md) , de modo a indicação de que inicia uma interação do utilizador é exclusiva para a sua marca.

O SDK de dispositivos de voz facilita uma variedade de cenários habilitados para voz, como [assistentes de Virtual de voz em primeiro lugar personalizada](https://aka.ms/bots/speech/va), a unidade através da ordenação de sistemas, [transcrição da conversação](conversation-transcription-service.md), inteligente e oradores. Pode responder a utilizadores com texto, falar para si num padrão ou [voz personalizada](how-to-customize-voice-font.md), forneça os resultados da pesquisa, [traduzir](speech-translation.md) para outros idiomas e muito mais. Estamos ansiosos por ver o que cria!

## <a name="get-the-speech-devices-sdk"></a>Obter o SDK de Dispositivos de Voz

### <a name="android"></a>Android

Para Android dispositivos baixar a versão mais recente do [Android SDK de dispositivos de voz](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Para Windows, o aplicativo de exemplo é fornecido como uma aplicação de Java para várias plataformas. Baixe a versão mais recente dos [SDK de dispositivos de voz de JRE](https://aka.ms/sdsdk-download-JRE).
A aplicação baseia-se com o pacote do SDK de voz e o IDE de Java de Eclipse (v4) no Windows de 64 bits. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

### <a name="linux"></a>Linux

Para Linux, o aplicativo de exemplo é fornecido como uma aplicação de Java para várias plataformas. Baixe a versão mais recente dos [SDK de dispositivos de voz de JRE](https://aka.ms/sdsdk-download-JRE).
A aplicação baseia-se com o pacote do SDK de voz e o IDE de Java de Eclipse (v4) no Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9). É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Escolha o seu dispositivo de voz](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Obtenha gratuitamente uma chave de subscrição de serviços de voz](get-started.md)
