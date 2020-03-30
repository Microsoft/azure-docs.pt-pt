---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70048989"
---
<!-- put the ## header in the file that includes this file -->

Nesta secção, cria-se uma identidade de dispositivo no registo de identidade no seu centro ioT. Um dispositivo não pode ligar-se a um hub a menos que tenha uma entrada no registo de identidade. Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. No seu menu de navegação hub IoT, abra **dispositivos IoT**e, em seguida, selecione **New** para adicionar um dispositivo no seu hub IoT.

    ![Criar identidade do dispositivo no portal](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. Em **Create a device**, forneça um nome para o seu novo dispositivo, como o **myDeviceId,** e selecione **Save**. Esta ação cria uma identidade de dispositivo para o seu hub IoT.

   ![Adicione um novo dispositivo](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Depois da criação do dispositivo, abra-o na lista apresentada no painel **Dispositivos IoT**. Copie a cadeia de **ligação primária** para usar mais tarde.

    ![Cadeia de ligação do dispositivo](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos para permitir um acesso seguro ao Hub IoT. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
