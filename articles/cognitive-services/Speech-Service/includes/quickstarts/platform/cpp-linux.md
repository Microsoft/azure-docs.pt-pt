---
title: 'Quickstart: Speech SDK C++ (Linux) configuração da plataforma - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para C++ no Linux com o serviço de fala SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 4f211e4b90dcc8bffa2fbba6fa4783caf846f50c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78383844"
---
Este guia mostra como instalar o [SDK de Discurso](~/articles/cognitive-services/speech-service/speech-sdk.md) para Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos de sistema

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, vai precisar:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* As plataformas Linux suportadas exigirão`libssl` certas bibliotecas instaladas `libasound2` (para suporte de camada de tomadas seguras e para suporte sonoro). Consulte a sua distribuição abaixo para os comandos necessários para instalar as versões corretas destas bibliotecas.

   * Em Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Em Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * No RHEL/CentOS 8:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="install-speech-sdk"></a>Instalar o SDK de Voz

O SDK de Voz para Linux pode ser utilizado para criar aplicações de 64 e 32 bits. As bibliotecas e ficheiros de cabeçalho sandados podem ser descarregados como um ficheiro de alcatrão a partir de https://aka.ms/csspeech/linuxbinary.

Transfira e instale o SDK da seguinte forma:

1. Escolha um diretório para o qual os ficheiros do SDK de Voz serão extraídos e defina a variável de ambiente `SPEECHSDK_ROOT` para apontar para esse diretório. Esta variável facilita a referência ao diretório em comandos futuros. Por exemplo, se pretender utilizar o diretório `speechsdk` no diretório-raiz, utilize um comando semelhante ao seguinte:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório se este ainda não existir.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Transfira e extraia o arquivo `.tar.gz` que contém os binários do SDK de Voz:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Valide os conteúdos do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A lista de diretórios deve conter o aviso de terceiros e os ficheiros de licença, bem como um diretório `include` com ficheiros de cabeçalho (`.h`) e um diretório `lib` com as bibliotecas.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

Agora pode passar para [os próximos passos](#next-steps) abaixo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
