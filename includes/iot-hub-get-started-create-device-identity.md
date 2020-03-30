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
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78892791"
---
Nesta secção, utiliza o Azure CLI para criar uma identidade de dispositivo para este artigo. Os IDs dos dispositivos são sensíveis às maiúsculas e minúsculas.

1. Abra o [Azure Cloud Shell](https://shell.azure.com/).

1. No Azure Cloud Shell, execute o seguinte comando para instalar a extensão Microsoft Azure IoT para o Azure CLI:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Criar uma nova `myDeviceId` identidade de dispositivo chamada e recuperar a cadeia de ligação do dispositivo com estes comandos:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Tome nota da cadeia de ligação do dispositivo a partir do resultado. Esta cadeia de ligação do dispositivo é utilizada pela aplicação do dispositivo para se ligar ao seu IoT Hub como dispositivo.

<!-- images and links -->
