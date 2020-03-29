---
title: Nuvens Soberanas - Serviço de discurso
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228093"
---
# <a name="speech-services-with-sovereign-clouds"></a>Serviços de fala com nuvens soberanas

## <a name="azure-government-united-states"></a>Governo azure (Estados Unidos)

Apenas os governos federais, estaduais, locais e tribais dos EUA e os seus parceiros têm acesso a esta instância dedicada com operações controladas por cidadãos norte-americanos.
- Regiões: EUA Gov Virginia
- SR em SpeechSDK:*config. FromHost ("wss://virginia.stt.speech.azure.us",\<"your-key");\>*
- TTS em SpeechSDK: *config. FromHost("https://virginia.tts.speech.azure.us",[]()\<"a\>sua chave");*
- Tokens de autenticação: https://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken[]()
- Portal Azure:https://portal.azure.us  
- Portal de Discurso personalizado:https://virginia.cris.azure.us/Home/CustomSpeech
- SKUs disponível: S0
- Características suportadas:
  - Voz em Texto
  - Discurso Personalizado (adaptação acústica/linguagem)
  - Texto em Voz
  - Tradutor de Discurso
- Características não suportadas
  - Voz Personalizada
  - Vozes neurais para texto-a-fala
- Locais apoiados: Os locais para as seguintes línguas são apoiados.
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

Localizado na China, um centro de dados Azure com acesso direto à China Mobile, China Telecom, China Unicom e outra rede de espinha dorsal de porta-redes, para que os utilizadores chineses forneçam experiência de acesso à rede local de alta velocidade e estável.
- Regiões: China East 2 (Xangai)
- SR em SpeechSDK: *config. FromHost ("wss://chinaeast2.stt.speech.azure.cn",\<"your-key");\>*
- TTS em SpeechSDK: *config. FromHost("https://chinaeast2.tts.speech.azure.cn",[]()\<"a\>sua chave");*
- Tokens de autenticação: https://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken[]()
- Portal Azure:https://portal.azure.cn
- Portal de Discurso personalizado:https://speech.azure.cn/CustomSpeech
- SKUs disponível: S0
- Características suportadas:
  - Voz em Texto
  - Discurso Personalizado (adaptação acústica/linguagem)
  - Texto em Voz
  - Tradutor de Discurso
- Características não suportadas
  - Voz Personalizada
  - Vozes neurais para texto-a-fala
- Locais apoiados: Os locais para as seguintes línguas são apoiados.
  - Árabe (ar-*)
  - Chinês (zh-*)
  - Inglês (en-*)
  - Francês (fr-*)
  - Alemão (de-*)
  - Hindi
  - Coreano
  - Russo
  - Espanhol (es-*)

