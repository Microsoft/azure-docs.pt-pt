---
title: 'Início rápido: reconhecer a fala de um arquivo de áudio-serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 2f7ec24667514fb131af29321a53f97210e58fc9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819270"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer a fala de um arquivo de áudio. Depois de atender a alguns pré-requisitos, reconhecer a fala de um arquivo usa apenas cinco etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Crie um objeto ````AudioConfig```` que especifica o. Nome do arquivo WAV.
> * Crie um objeto ````SpeechRecognizer```` usando os objetos ````SpeechConfig```` e ````AudioConfig```` acima.
> * Usando o objeto ````SpeechRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````SpeechRecognitionResult```` retornado.
