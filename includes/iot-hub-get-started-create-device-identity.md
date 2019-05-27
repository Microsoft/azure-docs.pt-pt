---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 40a5416f15b0e2d66d6ce4b4787573560ee4af00
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156391"
---
## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta seção, de usar a CLI do Azure para criar uma identidade de dispositivo para este tutorial. A CLI do Azure está pré-instalado na [Azure Cloud Shell](~/articles/cloud-shell/overview.md), ou pode [instalá-lo localmente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Os IDs dos dispositivos são sensíveis às maiúsculas e minúsculas.

1. Execute o seguinte comando no ambiente de linha de comando em que estiver a utilizar a CLI do Azure para instalar a extensão de IoT:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Se estiver a executar a CLI do Azure localmente, utilize o seguinte comando para iniciar sessão sua conta do Azure (se estiver a utilizar o Cloud Shell, tem sessão iniciada automaticamente e não precisa de executar este comando):

    ```cmd/sh
    az login
    ```

1. Por fim, crie uma nova identidade de dispositivo chamada `myDeviceId` e obter a cadeia de ligação do dispositivo com estes comandos:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anote a cadeia de ligação do dispositivo do resultado. Esta cadeia de ligação do dispositivo é utilizada pela aplicação de dispositivo para ligar ao seu IoT Hub como um dispositivo.

<!-- images and links -->
