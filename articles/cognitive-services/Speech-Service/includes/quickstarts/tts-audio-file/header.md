---
title: 'Início rápido: sintetizar a fala em arquivo de áudio-serviço de fala'
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
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502948"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada em um arquivo de áudio. Depois de atender a alguns pré-requisitos, o sintetizamento de fala em um arquivo usa apenas cinco etapas:
> [!div class="checklist"]
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Crie um objeto de configuração de áudio que especifique o. Nome do arquivo WAV.
> * Crie um objeto de ````SpeechSynthesizer```` usando os objetos de configuração acima.
> * Usando o objeto ````SpeechSynthesizer````, converta o texto em fala sintetizada, salvando-o no arquivo de áudio especificado.
> * Inspecione o ````SpeechSynthesizer```` retornado para erros.
