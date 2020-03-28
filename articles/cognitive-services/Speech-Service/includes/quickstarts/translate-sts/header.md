---
title: 'Quickstart: Traduzir discurso-a-discurso - Serviço de discurso'
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
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74981264"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para traduzir interativamente o discurso de uma língua para a fala noutra língua. Depois de satisfazer alguns pré-requisitos, traduzir o discurso à fala só dá seis passos:
> [!div class="checklist"]
> * Crie ````SpeechTranslationConfig```` um objeto a partir da sua chave de subscrição e região.
> * Atualize ````SpeechTranslationConfig```` o objeto para especificar as línguas de origem e alvo.
> * Atualize ````SpeechTranslationConfig```` o objeto para especificar o nome de voz da saída da fala.
> * Crie ````TranslationRecognizer```` um ````SpeechTranslationConfig```` objeto utilizando o objeto de cima.
> * Utilizando ````TranslationRecognizer```` o objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione ````TranslationRecognitionResult```` os devolvidos.
