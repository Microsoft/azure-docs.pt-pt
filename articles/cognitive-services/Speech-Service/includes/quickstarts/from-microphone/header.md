---
title: 'Início rápido: reconhecer a fala de um serviço de fala de microfone'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: a72597163a8c11cd8b515d052dc69992784e61e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818686"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de forma interativa a fala de uma entrada de microfone e obterá a transcrição de texto do áudio capturado. É fácil integrar esse recurso em seus aplicativos ou dispositivos para tarefas de reconhecimento comuns, como transcrever conversas. Ele também pode ser usado para integrações mais complexas, como usar o bot Framework com o SDK de fala para criar assistentes de voz.

Depois de satisfazer alguns pré-requisitos, reconhecer a fala de um microfone usa apenas quatro etapas:

> [!div class="checklist"]
> * Crie um objeto de `SpeechConfig` de sua chave de assinatura e região.
> * Crie um objeto `SpeechRecognizer` usando o objeto `SpeechConfig` acima.
> * Usando o objeto `SpeechRecognizer`, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o `SpeechRecognitionResult` retornado.
