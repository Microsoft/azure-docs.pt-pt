---
title: 'Quickstart: Speech SDK for Java (Windows, Linux, macOS) configuração da plataforma - Serviço de discurso'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para utilizar java (Windows, Linux, macOS) com o serviço de Fala SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 94946b4b6d0f0fc9c94cac7aca1e971b3f6262f9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673225"
---
Este guia mostra como instalar o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) de Discurso para Java 8 JRE de 64 bits. Se você só quer que o nome do pacote seja iniciado por conta própria, o Java SDK não está disponível no repositório central de Maven. Quer esteja a usar gradle ou um `pom.xml` ficheiro de dependência, precisa adicionar um repositório personalizado que aponta (ver abaixo para o nome do `https://csspeechstorage.blob.core.windows.net/maven/` pacote).

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

- O pacote Java Speech SDK está disponível para estes sistemas operativos:
  - Janelas: apenas 64 bits
  - Mac: macOS X versão 10.13 ou posterior
  - Linux: 64-bit apenas em Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8

## <a name="prerequisites"></a>Pré-requisitos

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (requer Java já instalado)
- As plataformas Linux suportadas exigirão certas bibliotecas instaladas (para suporte de `libssl` camada de tomadas seguras e para suporte `libasound2` sonoro). Consulte a sua distribuição abaixo para os comandos necessários para instalar as versões corretas destas bibliotecas.

  - Em Ubuntu, execute os seguintes comandos para instalar as embalagens necessárias:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - No Debian 9, execute os seguintes comandos para instalar as embalagens necessárias:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - No RHEL/CentOS, execute os seguintes comandos para instalar as embalagens necessárias:

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> - No RHEL/CentOS 7, siga as instruções sobre [como configurar o RHEL/CentOS 7 para o SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)da Fala .
> - No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- No Windows, é necessário o [Microsoft Visual C++ Redistribuável para o Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Note que a instalação desta esta sede pela primeira vez poderá exigir que reinicie o Windows antes de continuar com este guia.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Crie um projeto Eclipse e instale o Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [windows](../quickstart-list.md)]
