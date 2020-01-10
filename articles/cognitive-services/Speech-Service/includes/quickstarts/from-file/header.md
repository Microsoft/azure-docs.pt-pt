---
title: 'Início rápido: reconhecer a fala de um arquivo de áudio-serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466680"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer a fala de um arquivo de áudio. Depois de atender a alguns pré-requisitos, reconhecer a fala de um arquivo usa apenas cinco etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Crie um objeto ````AudioConfig```` que especifica o. Nome do arquivo WAV.
> * Crie um objeto ````SpeechRecognizer```` usando os objetos ````SpeechConfig```` e ````AudioConfig```` acima.
> * Usando o objeto ````SpeechRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````SpeechRecognitionResult```` retornado.
