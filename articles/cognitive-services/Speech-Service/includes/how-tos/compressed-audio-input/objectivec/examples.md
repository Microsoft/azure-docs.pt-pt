---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943742"
---
Para transmitir num formato áudio comprimido para `SPXPullAudioInputStream` o `SPXPushAudioInputStream`serviço De Fala, crie um ou .

O seguinte corte mostra como `SPXAudioConfiguration` criar um a `SPXPushAudioInputStream`partir de um exemplo de a , especificando um MP3 como o formato de compressão do fluxo.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

O próximo corte mostra como os dados de áudio comprimidos podem `SPXPushAudioInputStream`ser lidos a partir de um ficheiro e bombeados para o .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
