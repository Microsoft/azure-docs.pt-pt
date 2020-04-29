---
title: 'Quickstart: Traduzir o discurso para várias línguas - Serviço de fala'
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
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980475"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para traduzir interativamente o discurso de uma língua para a fala noutra língua. Depois de satisfazer alguns pré-requisitos, traduzir a fala para texto em várias línguas só dá seis passos:
> [!div class="checklist"]
> * Crie ````SpeechTranslationConfig```` um objeto a partir da sua chave de subscrição e região.
> * Atualize ````SpeechTranslationConfig```` o objeto para especificar a linguagem fonte de reconhecimento da fala.
> * Atualize ````SpeechTranslationConfig```` o objeto para especificar vários idiomas-alvo de tradução.
> * Crie ````TranslationRecognizer```` um ````SpeechTranslationConfig```` objeto utilizando o objeto de cima.
> * Utilizando ````TranslationRecognizer```` o objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione ````TranslationRecognitionResult```` os devolvidos.
