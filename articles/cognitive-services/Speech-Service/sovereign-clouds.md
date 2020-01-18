---
title: Nuvens soberanas-serviço de fala
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
ms.openlocfilehash: 3b96ded52aa2148df9e96d6f1e878d50b821abab
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170470"
---
# <a name="speech-services-with-sovereign-clouds"></a>Serviços de fala com nuvens soberanass

## <a name="azure-government-united-states"></a>Azure governamental (Estados Unidos)

Só os organismos federais, estaduais, locais e tribais dos E.U.A. e respetivos parceiros têm acesso a esta instância dedicada com operações controladas por cidadãos norte-americanos qualificados.
- Regiões: US Gov-Virgínia
- SR em SpeechSDK:*config. FromHost ("WSS://Virginia.STT.Speech.Azure.us", "\<your-key\>");*
- TTS em SpeechSDK: *config. FromHost ("https[]()://Virginia.TTS.Speech.Azure.us", "\<your-key\>");*
- Tokens de autenticação[](): https://Virginia.API.cognitive.Microsoft.us/STS/v1.0/issueToken
- Portal do Azure: https://portal.azure.us  
- Portal de Fala Personalizada: https://virginia.cris.azure.us/Home/CustomSpeech
- SKUs disponíveis: S0
- Recursos com suporte:
  - Voz em Texto
  - Fala Personalizada (adaptação de linguagem/acústica)
  - Texto para Discurso
  - Conversor de fala
- Recursos sem suporte
  - Voz Personalizada
  - Vozes neurais para conversão de texto em fala
- Localidades com suporte: há suporte para localidades para os idiomas a seguir.
  - Árabe (ar-*)
  - Chinês (ZH-*)
  - Inglês (EN-*)
  - Francês (FR-*)
  - Alemão (de-*)
  - Hindi
  - Coreano
  - Russo
  - Espanhol (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure China

Localizada na China, uma data center do Azure com acesso direto à China Mobile, na China Telecom, na China Unicom e outra rede principal de backbone de carros, para que os usuários do chinês forneçam uma experiência de acesso à rede local estável e de alta velocidade.
- Regiões: Leste da China 2 (Xangai)
- SR em SpeechSDK: *config. FromHost ("WSS://chinaeast2.STT.Speech.Azure.cn", "\<your-key\>");*
- TTS em SpeechSDK: *config. FromHost ("https[]()://chinaeast2.TTS.Speech.Azure.cn", "\<your-key\>");*
- Tokens de autenticação[](): https://chinaeast2.API.cognitive.Microsoft.cn/STS/v1.0/issueToken
- Portal do Azure: https://portal.azure.cn
- Portal de Fala Personalizada: https://chinaeast2.cris.azure.cn/Home/CustomSpeech
- SKUs disponíveis: S0
- Recursos com suporte:
  - Voz em Texto
  - Fala Personalizada (adaptação de linguagem/acústica)
  - Texto para Discurso
  - Conversor de fala
- Recursos sem suporte
  - Voz Personalizada
  - Vozes neurais para conversão de texto em fala
- Localidades com suporte: há suporte para localidades para os idiomas a seguir.
  - Árabe (ar-*)
  - Chinês (ZH-*)
  - Inglês (EN-*)
  - Francês (FR-*)
  - Alemão (de-*)
  - Hindi
  - Coreano
  - Russo
  - Espanhol (es-*)

