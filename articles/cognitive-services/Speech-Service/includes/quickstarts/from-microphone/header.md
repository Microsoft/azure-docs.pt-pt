---
title: 'Quickstart: Reconhecer o discurso a partir de um microfone - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468716"
---
Neste arranque rápido, você usará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do discurso para reconhecer interativamente o discurso a partir de uma entrada de microfone, e obter a transcrição de texto a partir de áudio capturado. É fácil integrar esta funcionalidade nas suas apps ou dispositivos para tarefas comuns de reconhecimento, como a transcrição de conversas. Também pode ser usado para integrações mais complexas, como usar o Quadro Bot com o SDK de Fala para construir assistentes de voz.

Depois de satisfazer alguns pré-requisitos, reconhecer o discurso de um microfone só dá quatro passos:

> [!div class="checklist"]
> * Crie `SpeechConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie `SpeechRecognizer` um `SpeechConfig` objeto utilizando o objeto de cima.
> * Utilizando `SpeechRecognizer` o objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione `SpeechRecognitionResult` os devolvidos.
