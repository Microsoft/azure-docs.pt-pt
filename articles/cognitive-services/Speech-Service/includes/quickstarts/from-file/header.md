---
title: 'Início rápido: reconhecer a fala de um arquivo de áudio-serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037672"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer a fala de um arquivo de áudio. Depois de atender a alguns pré-requisitos, reconhecer a fala de um arquivo só leva algumas etapas:
> [!div class="checklist"]
> * Crie um objeto de `SpeechConfig` de sua chave de assinatura e região.
> * Crie um objeto `AudioConfig` que especifica o. Nome do arquivo WAV.
> * Crie um objeto `SpeechRecognizer` usando os objetos `SpeechConfig` e `AudioConfig` acima.
> * Usando o objeto `SpeechRecognizer`, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o `SpeechRecognitionResult` retornado.
