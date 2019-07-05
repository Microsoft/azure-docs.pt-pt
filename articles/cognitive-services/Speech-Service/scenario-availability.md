---
title: Disponibilidade do cenário - serviços de voz
titlesuffix: Azure Cognitive Services
description: Referência para as regiões do serviço de voz.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: 7aa2c72a01f1887ea9680f8d5706b825a49039a1
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561276"
---
# <a name="scenario-availability"></a>Disponibilidade do cenário

O SDK do serviço de voz apresenta muitos cenários numa ampla variedade de linguagens e ambientes de programação.  Nem todos os cenários ainda estão atualmente disponíveis em todas as linguagens de programação ou em todos os ambientes.  Abaixo encontra a disponibilidade de cada cenário.

- **Reconhecimento de fala (SR), a lista de frase, objetivo, tradução e no local contentores**
  - Todas as linguagens de programação/ambientes onde existe uma ligação da seta <img src="media/index/link.jpg" height="15" width="15"></img> na tabela de início rápido [aqui](https://aka.ms/csspeech).
- **Voz (TTS)**
  - C++/ Windows e Linux
  - C#/ Windows, UWP & Unity
  - API de REST de TTS pode ser utilizado em todas as outras situações.
- **Palavra de reativação (palavra-chave Spotter/KWS)**
  - C++/ Windows e Linux
  - C#/ Windows e Linux
  - Python/Windows e Linux
  - Java/Windows, Linux & Android (dispositivos SDK de voz)
  - Funcionalidade do Word (palavra-chave Spotter/KWS) de reativação pode funcionar com qualquer tipo de microfone, oficial KWS suportar, no entanto, está atualmente limitado para as matrizes de microfone encontrado no hardware de Azure Kinect DK ou o SDK de dispositivos de voz
- **Assistente de voz em primeiro lugar Virtual**
  - C++/ Windows e Linux e macOS
  - C#/Windows
  - Java/Windows, Linux e macOS & Android (SDK de dispositivos de voz)
- **Transcrição da conversação**
  - C++/ Windows e Linux
  - C#(Framework e .NET Core) / Windows, UWP & Linux
  - Java/Windows, Linux & Android (dispositivos SDK de voz)
- **Transcrição da central de atendimento**
  - API de REST e pode ser utilizado em qualquer situação
- **Entrada de áudio de comprimidos do codec**
  - C++/Linux
  - C#/Linux
  - Java/Linux e Android
