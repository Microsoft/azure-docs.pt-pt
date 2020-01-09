---
title: 'Início rápido: sintetizar a fala em arquivo de áudio-serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468969"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada em um arquivo de áudio. Depois de atender a alguns pré-requisitos, o sintetizamento de fala em um arquivo usa apenas cinco etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Crie um objeto de configuração de áudio que especifique o. Nome do arquivo WAV.
> * Crie um objeto de ````SpeechSynthesizer```` usando os objetos de configuração acima.
> * Usando o objeto ````SpeechSynthesizer````, converta o texto em fala sintetizada, salvando-o no arquivo de áudio especificado.
> * Inspecione o ````SpeechSynthesizer```` retornado para erros.
