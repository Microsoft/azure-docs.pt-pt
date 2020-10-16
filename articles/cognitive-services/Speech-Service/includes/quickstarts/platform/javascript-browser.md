---
title: 'Quickstart: Speech SDK for JavaScript (Browser) platform setup - Speech service'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para utilizar o JavaScript (Browser) com o serviço de voz SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: ec73394e184e3c16fbc4eebf2a1090c9e52ddee4
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097053"
---
Este guia mostra como instalar o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) de discurso para JavaScript para utilização com uma página web.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Criar uma nova pasta de sites

Crie uma nova pasta vazia. No caso de querer alojar o exemplo num servidor Web, certifique-se de que este pode aceder à pasta.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Descompactar o SDK de Voz para JavaScript nessa pasta

Transfira o SDK de Voz como um [pacote .zip](https://aka.ms/csspeech/jsbrowserpackage) e descompacte-o para a pasta recentemente criada. Isto resulta em cinco ficheiros a serem desembalados:
* `microsoft.cognitiveservices.speech.sdk.bundle.js` Uma versão legível humana do Speech SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` Um ficheiro de mapa usado para depurar código SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` Definições de objeto para utilização com TypeScript
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` Uma versão minificada do SDK do Discurso.
* `speech-processor.js` Código para melhorar o desempenho em alguns navegadores.

## <a name="create-an-indexhtml-page"></a>Criar uma página index.html

Crie um novo ficheiro na pasta, com o nome `index.html`, e abra este ficheiro com um editor de texto.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]