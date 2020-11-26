---
title: 'Quickstart: Criar um assistente de voz personalizado - Serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187688"
---
Neste arranque rápido, você usará o [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) para criar uma aplicação de assistente de voz personalizada que se conecta a um bot que você já autorizou e configurado. Se precisar de criar um bot, consulte [o tutorial relacionado](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) para um guia mais abrangente.

Depois de satisfazer alguns pré-requisitos, ligar o seu assistente de voz personalizado dá apenas alguns passos:
> [!div class="checklist"]
> * Crie um `BotFrameworkConfig` objeto a partir da sua chave de subscrição e região.
> * Crie um `DialogServiceConnector` objeto utilizando o objeto de `BotFrameworkConfig` cima.
> * Utilizando o `DialogServiceConnector` objeto, inicie o processo de audição para uma única expressão.
> * Inspecione o `ActivityReceivedEventArgs` devolvido.
