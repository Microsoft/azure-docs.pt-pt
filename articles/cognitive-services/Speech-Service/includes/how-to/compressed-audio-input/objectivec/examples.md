---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422372"
---
Para transmitir num formato áudio comprimido para `SPXPullAudioInputStream` o `SPXPushAudioInputStream`serviço De Fala, crie um ou .

O seguinte corte mostra como `SPXAudioConfiguration` criar um a `SPXPushAudioInputStream`partir de um exemplo de a , especificando um MP3 como o formato de compressão do fluxo.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

O próximo corte mostra como os dados de áudio comprimidos podem `SPXPushAudioInputStream`ser lidos a partir de um ficheiro e bombeados para o .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
