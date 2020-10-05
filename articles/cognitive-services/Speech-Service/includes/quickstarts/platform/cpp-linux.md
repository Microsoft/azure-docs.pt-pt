---
title: 'Quickstart: Configuração da plataforma SDK C++ (Linux) de discurso - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para C++ no Linux com o serviço de voz SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 099c835ae2047538c882414660f699341af60b8b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "83673259"
---
Este guia mostra como instalar o [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) para o Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos de sistema

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, você precisará:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* As plataformas de Linux suportadas exigirão a instalação de certas bibliotecas `libssl` (para suporte de camadas de tomadas seguras e `libasound2` para suporte sonoro). Consulte a sua distribuição abaixo para obter os comandos necessários para instalar as versões corretas destas bibliotecas.

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

   * No RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - No RHEL/CentOS 7, siga as instruções sobre [como configurar RHEL/CentOS 7 para A SDK de fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para o Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
