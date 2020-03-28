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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241144"
---
Neste arranque rápido, utilizará o [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) para criar uma aplicação personalizada de assistente de voz que se conecta a um bot que já autorizou e configurado. Se precisar de criar um bot, consulte [o tutorial relacionado](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) para um guia mais abrangente.

Depois de satisfazer alguns pré-requisitos, ligar o seu assistente de voz personalizado dá apenas alguns passos:
> [!div class="checklist"]
> * Crie `BotFrameworkConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie `DialogServiceConnector` um `BotFrameworkConfig` objeto utilizando o objeto de cima.
> * Utilizando `DialogServiceConnector` o objeto, inicie o processo de escuta para uma única expressão.
> * Inspecione `ActivityReceivedEventArgs` os devolvidos.
