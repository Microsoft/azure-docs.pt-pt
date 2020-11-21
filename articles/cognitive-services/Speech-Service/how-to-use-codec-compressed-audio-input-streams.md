---
title: Streaming codec comprimido áudio com o Serviço de Discurso SDK - Speech
titleSuffix: Azure Cognitive Services
description: Aprenda a transmitir áudio comprimido para o serviço de Discurso com o SDK do discurso. Disponível para C++, C#e Java para Linux, Java no Android e Objective-C no iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: ace584b51f0aa457e30d8e48d97834aa735a1a57
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026578"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Utilize entrada de áudio comprimido codec com o SDK de fala

O serviço de voz SDK **Comprimida Transmissão de Entrada Sonora** API fornece uma forma de transmitir áudio comprimido para o serviço Dea falar utilizando um `PullStream` ou `PushStream` .

Plataforma | Idiomas | Versão GStreamer suportada
| :--- | ---: | :---:
Janelas (excluindo UWP)  | C++, C#, Java, Python | [1.15.1](https://gstreamer.freedesktop.org/data/pkg/windows/1.15.1/)
Linux  | C++, C#, Java, Python | [apoiou distribuição de Linux e arquiteturas-alvo](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Versão SDK de fala necessária para entrada de áudio comprimido
* A versão SDK de fala 1.10.0 ou posterior é necessária para RHEL 8 e CentOS 8
* A versão SDK do discurso 1.11.0 ou posterior é necessária para o Windows.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo usando entrada de áudio comprimido codec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Aprenda a reconhecer a fala](./get-started-speech-to-text.md)