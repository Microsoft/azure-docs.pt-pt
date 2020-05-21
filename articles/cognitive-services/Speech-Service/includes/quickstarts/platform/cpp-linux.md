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
ms.openlocfilehash: 099c835ae2047538c882414660f699341af60b8b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673259"
---
Este guia mostra como instalar o [SDK de Discurso](~/articles/cognitive-services/speech-service/speech-sdk.md) para Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos de sistema

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, vai precisar:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* As plataformas Linux suportadas exigirão certas bibliotecas instaladas (para suporte de `libssl` camada de tomadas seguras e para suporte `libasound2` sonoro). Consulte a sua distribuição abaixo para os comandos necessários para instalar as versões corretas destas bibliotecas.

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
> - No RHEL/CentOS 7, siga as instruções sobre [como configurar o RHEL/CentOS 7 para o SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)da Fala .
> - No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [windows](../quickstart-list.md)]
