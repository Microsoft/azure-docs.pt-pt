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
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 5f3b0094834b1ca547ba5ddf9726068c9ea36079
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409091"
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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Escolha o seu dispositivo de voz](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Obtenha gratuitamente uma chave de subscrição de serviços de voz](get-started.md)
