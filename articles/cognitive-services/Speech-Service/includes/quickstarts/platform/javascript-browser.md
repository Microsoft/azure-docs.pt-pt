---
title: 'Quickstart: Speech SDK for JavaScript (Browser) platform setup - Speech service'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para utilizar o JavaScript (Browser) com o serviço de Fala SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: fa8bf79c047911ca283bf60261bba0cbdd6816a7
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980180"
---
Este guia mostra como instalar o [SDK de Discurso](~/articles/cognitive-services/speech-service/speech-sdk.md) para JavaScript para utilização com uma página web.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Criar uma nova pasta de sites

Crie uma nova pasta vazia. No caso de querer alojar o exemplo num servidor Web, certifique-se de que este pode aceder à pasta.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Descompactar o SDK de Voz para JavaScript nessa pasta

Transfira o SDK de Voz como um [pacote .zip](https://aka.ms/csspeech/jsbrowserpackage) e descompacte-o para a pasta recentemente criada. Isto resulta em quatro ficheiros sendo desembalados:
* `microsoft.cognitiveservices.speech.sdk.bundle.js`Uma versão humana legível do SDK do Discurso.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map`Um ficheiro de mapa usado para depurar o código SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts`Definições de objetopara utilização com TypeScript
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js`Uma versão minificada do SDK do Discurso.

## <a name="create-an-indexhtml-page"></a>Criar uma página index.html

Crie um novo ficheiro na pasta, com o nome `index.html`, e abra este ficheiro com um editor de texto.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]