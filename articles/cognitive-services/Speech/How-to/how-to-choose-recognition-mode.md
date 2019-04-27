---
title: Como escolher o modo de reconhecimento de voz do Bing | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Como escolher o melhor modo de reconhecimento de voz do Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 99e69691d9904285ecff356523813b426c98fca2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515024"
---
# <a name="bing-speech-recognition-modes"></a>Modos de reconhecimento de voz do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A voz do Bing às APIs de texto suportam vários modos de reconhecimento de fala. Escolha o modo que produz os melhores resultados de reconhecimento para a sua aplicação.

| Modo | Descrição |
|---|---|
| *interativo* | Reconhecimento de "Comando e controlo" para cenários de aplicações do utilizador interativo. Os utilizadores falam frases curtas que se destina a ser comandos para um aplicativo. |
| *dictation* | Reconhecimento contínuo para cenários de ditado. Os utilizadores falam frases mais tempo do que são apresentadas como texto. Os utilizadores adotam um estilo de fala mais formal. |
| *conversation* | Reconhecimento contínuo para fotografar conversações entre seres humanos. Os utilizadores adotam um estilo de fala menos formal e podem se alternam entre frases mais tempo e frases mais curtos.

> [!NOTE]
> Esses modos são aplicáveis quando utiliza a [REST APIs](../GetStarted/GetStartedREST.md). O [bibliotecas de cliente](../GetStarted/GetStartedClientLibraries.md) utilizar parâmetros diferentes para especificar o modo de reconhecimento. Para obter mais informações, consulte a biblioteca de cliente da sua preferência.

Para obter mais informações, consulte a [modos de reconhecimento](../concepts.md#recognition-modes) página.
