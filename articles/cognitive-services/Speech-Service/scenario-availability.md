---
title: Disponibilidade do cenário-serviço de fala
titleSuffix: Azure Cognitive Services
description: Referência para regiões do serviço de fala.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: 6ec31df7cef8391728eae7845f64f55bb1c6466a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491340"
---
# <a name="scenario-availability"></a>Disponibilidade do cenário

O SDK do serviço de fala apresenta muitos cenários em uma ampla variedade de linguagens de programação e ambientes.  Nem todos os cenários estão atualmente disponíveis em todas as linguagens de programação ou todos os ambientes ainda.  Listada abaixo está a disponibilidade de cada cenário.

- **Reconhecimento de fala (SR), lista de frases, intenção, tradução e contêineres locais**
  - Todas as linguagens de programação/ambientes em que há um link de seta <img src="media/index/link.jpg" height="15" width="15"></img> na tabela de início rápido [aqui](https://aka.ms/csspeech).
- **Conversão de texto em fala (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE e Android)
  - Python
  - Swift
  - Objective-C
  - A API REST de TTS pode ser usada em todas as outras situações.
- **KWS (palavra-chave parando)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (SDK de dispositivos de fala)
  - A funcionalidade de KWS (palavra-chave) pode funcionar com qualquer tipo de microfone, o suporte oficial do KWS, no entanto, está limitado atualmente às matrizes de microfone encontradas no hardware do Azure Kinect DK ou no SDK dos dispositivos de fala
- **Assistentes de voz**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (SDK de dispositivos de fala)
- **Transcrição de conversa**
  - C++/Windows & Linux
  - C#(Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (SDK de dispositivos de fala)
- **Transcrição do Call Center**
  - API REST e pode ser usada em qualquer situação
- **Entrada de áudio compactada por codec**
  - C++/Linux
  - C#/Linux
  - Java/Linux, Android e iOS
