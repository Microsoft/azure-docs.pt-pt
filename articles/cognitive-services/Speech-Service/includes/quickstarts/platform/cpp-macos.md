---
title: 'Início rápido: instalação C++ da plataforma do SDK de fala (MacOS)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para C++ o MacOS com o SDK dos serviços de fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 2c6a186eef631372f08b876083dacede31cf1077
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502549"
---
Este guia mostra como instalar o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para C++ no MacOS 10,13 e superior.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos de sistema

macOS 10,13 e superior

## <a name="install-speech-sdk"></a>Instalar o SDK de Voz

1. Escolha um diretório para o qual os ficheiros do SDK de Voz serão extraídos e defina a variável de ambiente `SPEECHSDK_ROOT` para apontar para esse diretório. Esta variável facilita a referência ao diretório em comandos futuros. Por exemplo, se pretender utilizar o diretório `speechsdk` no diretório-raiz, utilize um comando semelhante ao seguinte:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório se este ainda não existir.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Baixe e extraia o arquivo de `.zip` que contém a estrutura do SDK de fala:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Valide os conteúdos do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A listagem de diretório deve conter os arquivos de aviso e licença de terceiros, bem como um diretório `MicrosoftCognitiveServicesSpeech.framework`.

Agora você pode passar para [as próximas etapas](#next-steps) abaixo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
