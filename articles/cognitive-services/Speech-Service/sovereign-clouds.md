---
title: Nuvens Soberanas - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar nuvens soberanas
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78228093"
---
# <a name="speech-services-with-sovereign-clouds"></a>Serviços de fala com nuvens soberanas

## <a name="azure-government-united-states"></a>Governo de Azure (Estados Unidos)

Apenas os governos federais, estaduais, locais e tribais dos EUA e os seus parceiros têm acesso a este caso dedicado com operações controladas por cidadãos americanos selecionados.
- Regiões: Eua Gov Virginia
- SR em SpeechSDK:*config. FromHost ("wss://virginia.stt.speech.azure.us", \<your-key\> ");*
- TTS em SpeechSDK: *config. FromHost("https://virginia.tts.speech.azure.us", []() \<your-key\> ");*
- Fichas de autenticação: https []() ://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Portal Azure: https://portal.azure.us  
- Portal da fala personalizada: https://virginia.cris.azure.us/Home/CustomSpeech
- SKUs disponíveis: S0
- Características suportadas:
  - Conversão de Voz em Texto
  - Discurso personalizado (adaptação acústica/linguística)
  - Conversão de Texto em Voz
  - Tradutor de Fala
- Funcionalidades não suportadas
  - Voz Personalizada
  - Vozes neurais para texto-a-discurso
- Locais apoiados: Locais para as seguintes línguas são apoiados.
  - Árabe (ar-*)
  - Chinês (zh-*)
  - Inglês (en-*)
  - Francês (fr-*)
  - Alemão (de-*)
  - Hindi
  - Coreano
  - Russo
  - Espanhol (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure China

Localizado na China, um centro de dados Azure com acesso direto à China Mobile, China Telecom, China Unicom e outra rede de espinha dorsal de grandes transportadoras, para que os utilizadores chineses forneçam experiência de acesso à rede local de alta velocidade e estável.
- Regiões: China East 2 (Xangai)
- SR em SpeechSDK: *config. FromHost("wss://chinaeast2.stt.speech.azure.cn", \<your-key\> ");*
- TTS em SpeechSDK:  *config. FromHost("https://chinaeast2.tts.speech.azure.cn", []() \<your-key\> ");*
- Fichas de autenticação: https []() ://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Portal Azure: https://portal.azure.cn
- Portal da fala personalizada: https://speech.azure.cn/CustomSpeech
- SKUs disponíveis: S0
- Características suportadas:
  - Conversão de Voz em Texto
  - Discurso personalizado (adaptação acústica/linguística)
  - Conversão de Texto em Voz
  - Tradutor de Fala
- Funcionalidades não suportadas
  - Voz Personalizada
  - Vozes neurais para texto-a-discurso
- Locais apoiados: Locais para as seguintes línguas são apoiados.
  - Árabe (ar-*)
  - Chinês (zh-*)
  - Inglês (en-*)
  - Francês (fr-*)
  - Alemão (de-*)
  - Hindi
  - Coreano
  - Russo
  - Espanhol (es-*)

