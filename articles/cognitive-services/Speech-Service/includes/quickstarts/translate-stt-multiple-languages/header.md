---
title: 'Início rápido: traduzir a fala para vários idiomas-serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 6c65fd9a504b5f399afa99280ca2a75b476c750c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502815"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter interativamente a fala de um idioma para a fala em outro idioma. Depois de satisfazer alguns pré-requisitos, a tradução de fala para texto em vários idiomas usa apenas seis etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechTranslationConfig```` de sua chave de assinatura e região.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar o idioma de origem do reconhecimento de fala.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar vários idiomas de destino de tradução.
> * Crie um objeto ````TranslationRecognizer```` usando o objeto ````SpeechTranslationConfig```` acima.
> * Usando o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
