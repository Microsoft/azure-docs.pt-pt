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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80757011"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Para criar uma política de acesso partilhado que conceda permissões **de serviço de ligação** e **registo** e para obter uma cadeia de ligação para esta política, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Grupos de Recursos**. Selecione o grupo de recursos onde o seu hub está localizado e, em seguida, selecione o seu hub a partir da lista de recursos.

1. No painel de esquerda do seu hub, selecione Políticas de **acesso partilhado**.

1. A partir do menu superior acima da lista de políticas, **selecione Adicionar**.

1. No Under **Add a shared access policy**, insira um nome descritivo para a sua política, como o *serviçoAndRegistryReadWrite*. Sob **Permissões**, selecione **registro** e **ligação de serviço**, e, em seguida, selecione **Criar**. (A permissão **de leitura do registo** é incluída automaticamente quando seleciona a escrita do **Registo**.)

    ![Mostrar como adicionar uma nova política de acesso partilhado](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Selecione a sua nova política na lista de políticas.

1. Nas **teclas de acesso partilhadas,** selecione o ícone de cópia para a **cadeia De ligação -- tecla primária** e guarde o valor.

    ![Mostrar como recuperar a cadeia de ligação](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre as políticas e permissões de acesso partilhados do IoT Hub, consulte [o controlo de acesso e permissões.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
