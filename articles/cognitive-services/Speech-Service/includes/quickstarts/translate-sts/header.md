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
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 805193c08e57ddc18dfca0a78c6b58cf895baec1
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817601"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter interativamente a fala de um idioma para a fala em outro idioma. Depois de satisfazer alguns pré-requisitos, a tradução da conversão de fala em fala usa apenas seis etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechTranslationConfig```` de sua chave de assinatura e região.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar os idiomas de origem e de destino.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar o nome da voz de saída de fala.
> * Crie um objeto ````TranslationRecognizer```` usando o objeto ````SpeechTranslationConfig```` acima.
> * Usando o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
