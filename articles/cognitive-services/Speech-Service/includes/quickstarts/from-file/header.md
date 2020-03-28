---
title: 'Quickstart: Reconhecer o discurso de um ficheiro áudio - Serviço de fala'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037672"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para reconhecer o discurso a partir de um ficheiro áudio. Depois de satisfazer alguns pré-requisitos, reconhecer o discurso de um ficheiro só dá alguns passos:
> [!div class="checklist"]
> * Crie `SpeechConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie `AudioConfig` um objeto que especifique o . Nome de ficheiro wav.
> * Crie `SpeechRecognizer` um `SpeechConfig` objeto `AudioConfig` utilizando os objetos e objetos de cima.
> * Utilizando `SpeechRecognizer` o objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione `SpeechRecognitionResult` os devolvidos.
