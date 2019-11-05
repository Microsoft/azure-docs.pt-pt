---
title: 'Guia de início rápido: SDK de fala para Java (Windows, Linux, macOS) instalação de plataforma-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para usar Java (Windows, Linux, macOS) com o SDK dos serviços de fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 2814327bdc434dbdae5644bd40b09d0506b21df9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502437"
---
Este guia mostra como instalar o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para o JRE do Java 8 de 64 bits.

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

- O pacote do SDK de fala do Java está disponível para estes sistemas operacionais:
  - Windows: 64 bits somente
  - Mac: macOS X versão 10,13 ou posterior
  - Linux: 64 bits somente no Ubuntu 16, 4, Ubuntu 18, 4 ou Debian 9

## <a name="prerequisites"></a>Pré-requisitos

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Java IDE Eclipse](https://www.eclipse.org/downloads/) (requer que o Java já esteja instalado)
- As plataformas Linux com suporte exigirão determinadas bibliotecas instaladas (`libssl` para suporte do Secure Sockets Layer e `libasound2` para suporte a som). Consulte a distribuição abaixo para os comandos necessários para instalar as versões corretas dessas bibliotecas.

  - No Ubuntu, execute os seguintes comandos para instalar os pacotes necessários:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - No Debian 9, execute os seguintes comandos para instalar os pacotes necessários:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- No Windows, você precisa do [Microsoft Visual C++ redistribuível para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para sua plataforma. Observe que a instalação desta pela primeira vez pode exigir que você reinicie o Windows antes de continuar com este guia.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Criar um projeto do Eclipse e instalar o SDK de fala

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
