---
title: 'Início rápido: sintetizando o serviço de fala-fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar a fala usando o SDK de fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503081"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada. Depois de satisfazer alguns pré-requisitos, a renderização de fala sintetizada para os alto-falantes padrão usa apenas quatro etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Crie um objeto ````SpeechSynthesizer```` usando o objeto ````SpeechConfig```` acima.
> * Usando o objeto ````SpeechSynthesizer```` para falar o texto.
> * Verifique o ````SpeechSynthesisResult```` retornado para erros.
