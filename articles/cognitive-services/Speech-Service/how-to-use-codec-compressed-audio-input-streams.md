---
title: Áudio comprimido de código stream com o Serviço Speech SDK - Speech
titleSuffix: Azure Cognitive Services
description: Aprenda a transmitir áudio comprimido para o serviço de Fala com o SDK de Fala. Disponível para C++, C#, e Java para Linux, Java em Android e Objective-C no iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: bd6b500a823bde654da4442704b75451806d6a46
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80637279"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Utilize a entrada de áudio comprimido codec com o SDK do Discurso

O serviço de fala SDK **Comprime Audio Input Stream** API fornece uma `PullStream` forma `PushStream`de transmitir áudio comprimido para o serviço de Fala usando um ou .

O streaming de áudio de entrada comprimido é atualmente suportado para C#, C++, Java no Windows (as aplicações UWP não são suportadas) e Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Também é suportado para Java em Android e Objective-C na plataforma iOS.
* A versão 1.10.0 ou posterior do Discurso SDK é necessária para RHEL 8 e CentOS 8
* A versão 1.11.0 ou posterior do Speech SDK é necessária para windows.

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

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo utilizando entrada de áudio comprimido codec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aprenda a reconhecer a fala](quickstarts/speech-to-text-from-microphone.md)
