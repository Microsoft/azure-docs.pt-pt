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
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504138"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer a fala de um arquivo de áudio. Depois de atender a alguns pré-requisitos, reconhecer a fala de um arquivo usa apenas cinco etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Crie um objeto ````AudioConfig```` que especifica o. Nome do arquivo WAV.
> * Crie um objeto ````SpeechRecognizer```` usando os objetos ````SpeechConfig```` e ````AudioConfig```` acima.
> * Usando o objeto ````SpeechRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````SpeechRecognitionResult```` retornado.
