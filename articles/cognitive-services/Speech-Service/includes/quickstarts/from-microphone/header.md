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
ms.date: 11/07/2019
ms.author: erhopf
ms.openlocfilehash: f3f28cdd8c3c77a5cde2ead86c49b41d54bab5d7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961176"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de forma interativa a fala de dados de áudio capturados de um microfone. Depois de satisfazer alguns pré-requisitos, reconhecer a fala de um microfone usa apenas quatro etapas:

> [!div class="checklist"]
> * Crie um objeto de `SpeechConfig` de sua chave de assinatura e região.
> * Crie um objeto `SpeechRecognizer` usando o objeto `SpeechConfig` acima.
> * Usando o objeto `SpeechRecognizer`, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o `SpeechRecognitionResult` retornado.
