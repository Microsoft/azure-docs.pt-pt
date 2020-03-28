---
title: 'Quickstart: Traduzir discurso-texto - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74981241"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para traduzir interativamente o discurso de uma língua para texto noutra língua. Depois de satisfazer alguns pré-requisitos, traduzir o discurso ao texto só dá cinco passos:
> [!div class="checklist"]
> * Crie ````SpeechConfig```` um objeto a partir da sua chave de subscrição e região.
> * Atualize ````SpeechConfig```` o objeto para especificar as línguas de origem e alvo.
> * Crie ````TranslationRecognizer```` um ````SpeechConfig```` objeto utilizando o objeto de cima.
> * Utilizando ````TranslationRecognizer```` o objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione ````TranslationRecognitionResult```` os devolvidos.
