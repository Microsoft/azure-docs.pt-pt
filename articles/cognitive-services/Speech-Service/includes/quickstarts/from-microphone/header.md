---
title: 'Quickstart: Recognize speech from a microphone - Speech Service'
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
ms.openlocfilehash: 561acc798df475b9cb7f451163016b1b6abe83cf
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74451441"
---
In this quickstart, you'll use the [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) to interactively recognize speech from a microphone input, and get the text transcription from captured audio. It's easy to integrate this feature into your apps or and devices for common recognition tasks, such as transcribing conversations. It can also be used for more complex integrations, like using the Bot Framework with the Speech SDK to build voice assistants.

After satisfying a few prerequisites, recognizing speech from a microphone only takes four steps:

> [!div class="checklist"]
> * Create a `SpeechConfig` object from your subscription key and region.
> * Create a `SpeechRecognizer` object using the `SpeechConfig` object from above.
> * Using the `SpeechRecognizer` object, start the recognition process for a single utterance.
> * Inspect the `SpeechRecognitionResult` returned.
