---
title: 'Início rápido: traduzir o serviço de fala para fala-fala'
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981264"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter interativamente a fala de um idioma para a fala em outro idioma. Depois de satisfazer alguns pré-requisitos, a tradução da conversão de fala em fala usa apenas seis etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechTranslationConfig```` de sua chave de assinatura e região.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar os idiomas de origem e de destino.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar o nome da voz de saída de fala.
> * Crie um objeto ````TranslationRecognizer```` usando o objeto ````SpeechTranslationConfig```` acima.
> * Usando o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
