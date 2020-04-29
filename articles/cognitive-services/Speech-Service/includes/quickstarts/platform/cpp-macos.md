---
title: 'Quickstart: Speech SDK C++ (macOS) platform setup - Speech service'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para C++ no macOS com o serviço de fala SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: d0f9bc44145f75d7c3b503fffe43042c650025f1
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75466689"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para C++ no macOS 10.13 ou superior.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos de sistema

macOS 10.13 ou superior

## <a name="install-speech-sdk"></a>Instalar o SDK de Voz

1. Escolha um diretório para o qual os ficheiros do SDK de Voz serão extraídos e defina a variável de ambiente `SPEECHSDK_ROOT` para apontar para esse diretório. Esta variável facilita a referência ao diretório em comandos futuros. Por exemplo, se pretender utilizar o diretório `speechsdk` no diretório-raiz, utilize um comando semelhante ao seguinte:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório se este ainda não existir.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Descarregue `.zip` e extraio o arquivo que contém a estrutura SDK da fala:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Valide os conteúdos do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A listagem de diretóriodeve conter o aviso de terceiros e os ficheiros de licença, bem como um `MicrosoftCognitiveServicesSpeech.framework` diretório.

Agora pode passar para [os próximos passos](#next-steps) abaixo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
