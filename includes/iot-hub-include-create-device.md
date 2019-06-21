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
ms.openlocfilehash: d70544866b9e321d98acd3978da145276e036025
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184358"
---
<!-- put the ## header in the file that includes this file -->

Nesta secção, vai criar uma identidade de dispositivo no registo de identidade no seu hub IoT. Não é possível ligar um dispositivo ao hub IoT, exceto se tiver uma entrada no registo de identidade. Para obter mais informações, consulte a secção "Registo de identidade" o [Guia do programador do IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) 

1. No menu de navegação de hub do IoT, abra **dispositivos IoT**, em seguida, selecione **Add** para registar um novo dispositivo no IoT hub.

    ![Criar a identidade de dispositivo no portal](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Forneça um nome para o seu dispositivo novo, como **myDeviceId**e selecione **guardar**. Esta ação cria uma nova identidade de dispositivo do hub IoT.

   ![Adicionar um novo dispositivo](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. Depois do dispositivo é criado, abra-na lista os **dispositivos IoT** painel. Copiar o **cadeia de ligação---chave primária** para utilizar mais tarde.

    ![Cadeia de ligação do dispositivo](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos para permitir um acesso seguro ao Hub IoT. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação. Para obter mais informações, consulte [Guia do programador do IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
