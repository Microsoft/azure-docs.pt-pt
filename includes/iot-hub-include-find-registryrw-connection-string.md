---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68883780"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Para obter a cadeia de ligação IoT Hub para a política **registryReadWrite,** siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Grupos de Recursos**. Selecione o grupo de recursos onde o seu hub está localizado e, em seguida, selecione o seu hub a partir da lista de recursos.

2. No painel de esquerda do seu hub, selecione Políticas de **acesso partilhado**.

3. Na lista de políticas, selecione a política **registroReadWrite.**

4. Nas **teclas de acesso partilhadas,** selecione o ícone de cópia para a **cadeia De ligação -- tecla primária** e guarde o valor.

    ![Mostrar como recuperar a cadeia de ligação](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Para obter mais informações sobre as políticas e permissões de acesso partilhados do IoT Hub, consulte [o controlo de acesso e permissões.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
