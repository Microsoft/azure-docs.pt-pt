---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80757011"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Para criar uma política de acesso partilhado que conceda permissões de escrita de **serviços** e **registos** e para obter uma cadeia de ligação para esta política, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Grupos de Recursos.** Selecione o grupo de recursos onde o seu hub está localizado e, em seguida, selecione o seu hub a partir da lista de recursos.

1. No painel do lado esquerdo do seu hub, selecione políticas de **acesso partilhado**.

1. A partir do menu superior acima da lista de políticas, selecione **Adicionar**.

1. Em Adicionar uma política de **acesso partilhado,** introduza um nome descritivo para a sua política, como *serviçoAndRegistryReadWrite*. Em **Permissões,** selecione **registo escrever** e ligar o **serviço,** e, em seguida, selecione **Criar**. (A permissão de **leitura do Registo** é incluída automaticamente quando selecionar o Registo **escrever**.)

    ![Mostre como adicionar uma nova política de acesso partilhado](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Selecione a sua nova política a partir da lista de políticas.

1. Em **teclas de acesso partilhadas,** selecione o ícone de cópia para a **cadeia de ligação -- chave primária** e guarde o valor.

    ![Mostre como recuperar a corda de ligação](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre as políticas e permissões de acesso partilhadas do IoT Hub, consulte [o controlo de acesso e as permissões.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
