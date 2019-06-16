---
title: Transcrição da conversação - serviços de voz
titleSuffix: Azure Cognitive Services
description: Transcrição da conversação é uma funcionalidade avançada dos serviços de voz que combina o reconhecimento de voz em tempo real, identificação de orador e diarization. Transcrição da conversação é perfeita para fotografar reuniões presenciais, com a capacidade de distinguir oradores, ele permite-lhe saber quem disse que o quê e quando, permitindo que os participantes para se concentrar na reunião e rapidamente dar seguimento passos seguintes. Esta funcionalidade melhora também a acessibilidade. Com transcrição, pode interagir ativamente com os participantes com deficiências auditivas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: a31921e229a4bbfccd6fdd871666aad1eeef3232
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243846"
---
# <a name="what-is-conversation-transcription"></a>O que é a transcrição da conversação?

Transcrição da conversação é uma funcionalidade avançada dos serviços de voz que combina o reconhecimento de voz em tempo real, identificação de orador e diarization. Transcrição da conversação é perfeita para fotografar reuniões presenciais, com a capacidade de distinguir oradores, ele permite-lhe saber quem disse que o quê e quando, permitindo que os participantes para se concentrar na reunião e rapidamente dar seguimento passos seguintes. Esta funcionalidade melhora também a acessibilidade. Com transcrição, pode interagir ativamente com os participantes com deficiências auditivas.   

Transcrição da conversação proporciona reconhecimento preciso com modelos de voz personalizáveis que poderá adaptar para compreender o setor e o vocabulário de específicas da empresa. Além disso, pode emparelhar transcrição da conversação com o SDK de dispositivos de voz para otimizar a experiência para microfone multi dispositivos.

>[!NOTE]
> Atualmente, transcrição da conversação é recomendada para pequenas reuniões. Se gostaria de estender a transcrição da conversação para reuniões de grandes escala, entre em contato conosco.

Este diagrama ilustra o hardware, software e serviços que funcionam em conjunto com a transcrição da conversação.

![O diagrama de transcrição da conversação de importação](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Uma matriz de sete microfone circular com a configuração de geometry específico é necessária. Para obter detalhes de especificação e design, consulte [microfone de SDK de dispositivo de voz de Microsoft](https://aka.ms/cts/microphone). Para obter mais informações ou comprar um kit de desenvolvimento, consulte [obter SDK de dispositivo de voz de Microsoft](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Introdução ao transcrição da conversação

Existem três passos que terá de seguir para obter uma introdução transcrição da conversação.

1. Recolha exemplos de voz de utilizadores.
2. Gerar perfis de utilizador a utilizar os exemplos de voz do utilizador
3. Utilizar o SDK de voz para identificar os utilizadores (alto-falantes) e transcrição de voz

## <a name="collect-user-voice-samples"></a>Recolher exemplos de voz do utilizador

A primeira etapa é coletar gravações de áudio de cada utilizador. Voz do utilizador deve ser registrado num ambiente silencioso sem barulho de fundo. O comprimento recomendado para cada amostra de áudio é entre 30 segundos e dois minutos. Mais exemplos de áudio resultarão em maior exatidão ao identificar oradores. Áudio tem de ser mono canal com uma taxa de amostragem de 16 KHz.

Para além das orientações mencionadas anteriormente, como áudio é registrado e armazenados cabe a – base de dados segura é recomendado. Na próxima seção, vamos rever como este áudio é usado para gerar os perfis de utilizador que são utilizados com o SDK de voz para reconhecer oradores.

## <a name="generate-user-profiles"></a>Gerar perfis de utilizador

Em seguida, terá de enviar as gravações de áudio já recolhidos para o serviço de geração de assinaturas para validar o áudio e gerar perfis de utilizador. O [serviço de geração de assinaturas](https://aka.ms/cts/signaturegenservice) é um conjunto de APIs REST, que permitem gerar e obter os perfis de utilizador.

Para criar um perfil de utilizador, terá de utilizar o `GenerateVoiceSignature` API. Detalhes da especificação e código de exemplo estão disponíveis:

> [!NOTE]
> Transcrição da conversação está atualmente disponível em "en-US" e "zh-CN" nas seguintes regiões: `centralus` e `eastasia`.

* [Especificação de REST](https://aka.ms/cts/signaturegenservice)
* [Como utilizar a transcrição da conversação](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transcreva e identificar oradores

Transcrição da conversação espera multicanais fluxos de áudio e perfis de utilizador como entradas para gerar transcrições e identificar oradores. Dados de perfil de utilizador e de áudio são enviados ao serviço de transcrição de conversação com o SDK de dispositivos de voz. Como mencionado anteriormente, uma matriz de sete microfone circular e o SDK de dispositivos de voz são necessários para utilizar a transcrição da conversação.

>[!NOTE]
> Para obter detalhes de especificação e design, consulte [microfone de SDK de dispositivo de voz de Microsoft](https://aka.ms/cts/microphone). Para obter mais informações ou comprar um kit de desenvolvimento, consulte [obter SDK de dispositivo de voz de Microsoft](https://aka.ms/cts/getsdk).

Para saber como utilizar a transcrição de conversação com o SDK de dispositivos de voz, veja [como utilizar a transcrição da conversação](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>Guia de introdução com uma aplicação de exemplo

SDK de dispositivo de voz de Microsoft tem uma aplicação de exemplo de início rápido para todos os exemplos relacionados com o dispositivo. Transcrição da conversação é uma delas. Pode encontrá-lo na [guia de introdução de android SDK de dispositivo de voz](https://aka.ms/sdsdk-quickstart) com a aplicação de exemplo e seu código-fonte para sua referência.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK de dispositivos de voz](speech-devices-sdk.md)
