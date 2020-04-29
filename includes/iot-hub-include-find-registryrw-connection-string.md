---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68883780"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Para obter a cadeia de ligação IoT Hub para a política **de registoReadWrite,** siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Grupos de Recursos.** Selecione o grupo de recursos onde o seu hub está localizado e, em seguida, selecione o seu hub a partir da lista de recursos.

2. No painel do lado esquerdo do seu hub, selecione políticas de **acesso partilhado**.

3. A partir da lista de políticas, selecione a política **de registoReadWrite.**

4. Em **teclas de acesso partilhadas,** selecione o ícone de cópia para a **cadeia de ligação -- chave primária** e guarde o valor.

    ![Mostre como recuperar a corda de ligação](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Para obter mais informações sobre as políticas e permissões de acesso partilhadas do IoT Hub, consulte [o controlo de acesso e as permissões.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
