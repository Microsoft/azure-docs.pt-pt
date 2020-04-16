---
title: 'Quickstart: Reconhecer o discurso de um ficheiro áudio - Serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400602"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para reconhecer o discurso a partir de um ficheiro áudio. Depois de satisfazer alguns pré-requisitos, reconhecer o discurso de um ficheiro só dá alguns passos:
> [!div class="checklist"]
> * Crie `SpeechConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie `AudioConfig` um objeto que especifique o . Nome de ficheiro wav.
> * Crie `SpeechRecognizer` um `SpeechConfig` objeto `AudioConfig` utilizando os objetos e objetos de cima.
> * Utilizando `SpeechRecognizer` o objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione `SpeechRecognitionResult` os devolvidos.
