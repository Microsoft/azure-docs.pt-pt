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
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981241"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para traduzir interativamente a fala de um idioma para o texto em outro idioma. Depois de atender a alguns pré-requisitos, a tradução somente de fala para texto leva cinco etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Atualize o objeto ````SpeechConfig```` para especificar os idiomas de origem e de destino.
> * Crie um objeto ````TranslationRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Usando o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
