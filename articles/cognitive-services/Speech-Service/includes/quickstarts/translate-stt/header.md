---
title: 'Início rápido: traduzir o serviço de fala em texto de fala'
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
ms.openlocfilehash: c45e75038d1731c933ccf8bf26f9de573e409292
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502787"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para traduzir interativamente a fala de um idioma para o texto em outro idioma. Depois de atender a alguns pré-requisitos, a tradução somente de fala para texto leva cinco etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Atualize o objeto ````SpeechConfig```` para especificar os idiomas de origem e de destino.
> * Crie um objeto ````TranslationRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Usando o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
