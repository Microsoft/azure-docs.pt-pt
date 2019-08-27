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
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70048989"
---
<!-- put the ## header in the file that includes this file -->

Nesta seção, você criará uma identidade de dispositivo no registro de identidade em seu hub IoT. Um dispositivo não pode se conectar a um Hub, a menos que tenha uma entrada no registro de identidade. Para obter mais informações, consulte o [Guia do desenvolvedor do Hub IOT](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. No menu de navegação do Hub IoT, abra **dispositivos IOT**e, em seguida, selecione **novo** para adicionar um dispositivo em seu hub IOT.

    ![Criar a identidade do dispositivo no portal](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. Em **criar um dispositivo**, forneça um nome para o novo dispositivo, como mydeviceid e selecione **salvar**. Esta ação cria uma identidade de dispositivo para o Hub IoT.

   ![Adicionar um novo dispositivo](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Depois que o dispositivo for criado, abra o dispositivo na lista no painel **dispositivos IOT** . Copie a **cadeia de conexão primária** para usar mais tarde.

    ![Cadeia de conexão do dispositivo](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos para permitir um acesso seguro ao Hub IoT. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação. Para obter mais informações, consulte [Guia do desenvolvedor do Hub IOT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
