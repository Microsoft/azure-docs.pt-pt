---
title: 'Quickstart: Speech SDK for Java (Windows, Linux, macOS) configuração da plataforma - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para a utilização de Java (Windows, Linux, macOS) com o serviço de voz SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: 142d4504ab12e7df5cc1e009038554a5b90dff0c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188422"
---
Este guia mostra como instalar o [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) para Java 8 JRE de 64 bits. Se você só quer que o nome do pacote seja iniciado por conta própria, o Java SDK não está disponível no repositório central de Maven. Quer esteja a utilizar Gradle ou um `pom.xml` ficheiro de dependência, tem de adicionar um repositório personalizado que indique `https://csspeechstorage.blob.core.windows.net/maven/` (ver abaixo o nome do pacote).

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

- O pacote Java Speech SDK está disponível para estes sistemas operativos:
  - Windows: apenas 64 bits
  - Mac: macOS X versão 10.13 ou mais tarde
  - Linux; ver a lista de [distribuições apoiadas do Linux e arquiteturas-alvo.](~/articles/cognitive-services/speech-service/speech-sdk.md)

## <a name="prerequisites"></a>Pré-requisitos

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (requer Java já instalado)
- As plataformas de Linux suportadas exigirão a instalação de certas bibliotecas `libssl` (para suporte de camadas de tomadas seguras e `libasound2` para suporte sonoro). Consulte a sua distribuição abaixo para obter os comandos necessários para instalar as versões corretas destas bibliotecas.

  - Em Ubuntu/Debian, executar os seguintes comandos para instalar os pacotes necessários:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Se o libssl1.0.0 não estiver disponível, instale libssl1.0.x (onde x seja superior a 0) ou libssl1.1.

  - No RHEL/CentOS, executar os seguintes comandos para instalar os pacotes necessários:

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - No RHEL/CentOS 7, siga as instruções sobre [como configurar RHEL/CentOS 7 para A SDK de fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para o Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- No Windows, precisa do [Microsoft Visual C++ Redistributable para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Tenha em atenção que a instalação deste facto pela primeira vez poderá exigir que reinicie o Windows antes de continuar com este guia.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Crie um projeto Eclipse e instale o Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
