---
title: Áudio comprimido de código de fluxo com o SDK da fala no iOS
titleSuffix: Azure Cognitive Services
description: Aprenda a transmitir áudio comprimido para o serviço de Fala com o SDK de Fala no iOS.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 5d65ac83fc8a41008971108c5b370c1d0416113b
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331524"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Como: Utilizar a entrada de áudio comprimido codec com o SDK de Fala no iOS

A API de entrada de **áudio comprimido** do Speech SDK fornece uma forma de transmitir áudio comprimido para o serviço de Fala usando um fluxo de puxar ou empurrar.

> [!IMPORTANT]
> A versão 1.7.0 ou superior do Speech SDK é necessária para o streaming de áudio comprimido no iOS. Também é suportado para [ C++, C#e Java em Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)](how-to-use-codec-compressed-audio-input-streams.md) e [Java no Android.](how-to-use-codec-compressed-audio-input-streams-android.md)

Para acenar/PCM consulte a documentação da fala principal. Fora do wav/PCM, são suportados os seguintes formatos de entrada comprimidos codec:

- MP3
- OPUS/OGG
- FLAC
- ALAW em recipiente de awav
- MULAW em recipiente de awav

## <a name="prerequisites"></a>Pré-requisitos

O manuseamento do áudio comprimido é implementado utilizando [gStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, estas funções não podem ser enviadas com o SDK, mas uma biblioteca de invólucros contendo estas funções precisa de ser construída por desenvolvedores de aplicações e enviada com as aplicações usando o SDK.

Para construir esta biblioteca de invólucros, primeiro descarregue e instale o [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Em seguida, descarregue o projeto Xcode para a [biblioteca de invólucros.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)

Abra o projeto em Xcode e construa-o para o alvo **genérico do dispositivo iOS** -- não funcionará para construí-lo para um alvo específico.

O passo de construção gerará um conjunto de quadros dinâmicos com uma biblioteca dinâmica para todas as arquiteturas necessárias com o nome de `GStreamerWrapper.framework`.

Esta estrutura deve ser incluída em todas as aplicações que utilizam fluxos de áudio comprimidos com o serviço de fala SDK.

Aplique as seguintes definições no seu projeto Xcode para o concretizar:

1. Copie tanto o `GStreamerWrapper.framework` que acabou de construir como o quadro do SDK de Discurso dos Serviços Cognitivos, que pode transferir daqui [para](https://aka.ms/csspeech/iosbinary)o diretório que contém o seu projeto de amostra.
1. Ajuste os caminhos para as estruturas nas _Definições_do Projeto .
   1. No separador **Geral** sob o cabeçalho **Embedded Binaries,** adicione a biblioteca SDK como uma estrutura: **Adicione binários incorporados** > **Adicione outros...** > Navegue ao diretório que escolheu e selecione ambos os quadros.
   1. Vá para o separador **Build Settings** (Definições de Compilação) e ative a definição **All** (Tudo).
1. Adicione o diretório `$(SRCROOT)/..` a _Framework Search Paths_ (Caminhos de Pesquisa da Arquitetura) no cabeçalho **Search Paths** (Caminhos de Pesquisa).

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo utilizando entrada de áudio comprimido codec

Para transmitir num formato áudio comprimido para o serviço De Fala, crie uma `SPXPullAudioInputStream` ou `SPXPushAudioInputStream`.

O seguinte corte mostra como criar uma `SPXAudioConfiguration` a partir de um caso de `SPXPushAudioInputStream`, especificando mp3 como o formato de compressão do fluxo.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

O próximo corte mostra como os dados de áudio comprimidos podem ser lidos a partir de um ficheiro e bombeados para o `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como reconhecer o discurso em Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
