---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: cf765145cafa2eb06d77ea2e153e45c296281b71
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552355"
---
Este guia mostra como instalar o [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) para o Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos de sistema

Linux; ver a lista de [distribuições apoiadas do Linux e arquiteturas-alvo.](~/articles/cognitive-services/speech-service/speech-sdk.md)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, você precisará:

* No Windows, precisa do [Microsoft Visual C++ Redistributable para Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) para a sua plataforma. A instalação desta situação pela primeira vez pode exigir um reinício.
* ccg
* [Ir binário (1.13 ou mais tarde)](https://golang.org/dl/)

* As plataformas de Linux suportadas exigirão a instalação de certas bibliotecas `libssl` (para suporte de camadas de tomadas seguras e `libasound2` para suporte sonoro). Consulte a sua distribuição abaixo para obter os comandos necessários para instalar as versões corretas destas bibliotecas.

   * Em Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Se o libssl1.0.0 não estiver disponível, instale libssl1.0.x (onde x seja superior a 0) ou libssl1.1.

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


## <a name="configure-go-environment"></a>Configurar ambiente Go

Execute os seguintes passos para configurar o seu ambiente Go para encontrar o SDK do discurso. Em ambos os passos, `<architecture>` substitua-se pela arquitetura do processador do seu CPU. Isto `x86` será, `x64` `arm32` , ou `arm64` . .

1. Uma vez que as ligações `cgo` dependem, é necessário definir as variáveis ambientais para que Go possa encontrar o SDK:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Para executar aplicações incluindo o SDK, precisamos dizer ao SO onde encontrar as libs:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list-go.md)]
