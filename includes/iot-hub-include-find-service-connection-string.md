---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69558503"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Para obter a cadeia de ligação IoT Hub para a política de **serviço,** siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Grupos de Recursos.** Selecione o grupo de recursos onde o seu hub está localizado e, em seguida, selecione o seu hub a partir da lista de recursos.

1. No painel do lado esquerdo do seu hub IoT, selecione políticas de **acesso partilhado**.

1. A partir da lista de políticas, selecione a política de **serviços.**

1. Em **teclas de acesso partilhadas,** selecione o ícone de cópia para a **cadeia de ligação -- chave primária** e guarde o valor.

    ![Mostre como recuperar a corda de ligação](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre as políticas e permissões de acesso partilhadas do IoT Hub, consulte [o controlo de acesso e as permissões.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
