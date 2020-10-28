---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: b1a863498603006e37ab98b838ffd426b962d788
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755998"
---
Nesta secção, utilize o CLI Azure para criar uma identidade de dispositivo para este artigo. Os IDs dos dispositivos são sensíveis às maiúsculas e minúsculas.

1. Abra o [Azure Cloud Shell](https://shell.azure.com/).

1. Em Azure Cloud Shell, executar o seguinte comando para instalar a extensão IoT do Microsoft Azure para Azure CLI:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Crie uma nova identidade do dispositivo chamada `myDeviceId` e recupere a cadeia de ligação do dispositivo com estes comandos:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Tome nota da cadeia de ligação do dispositivo a partir do resultado. Esta cadeia de ligação do dispositivo é utilizada pela aplicação do dispositivo para se ligar ao seu IoT Hub como um dispositivo.

<!-- images and links -->
