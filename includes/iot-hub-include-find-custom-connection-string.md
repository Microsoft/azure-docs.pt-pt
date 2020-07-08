---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "70050383"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Para criar uma política de acesso partilhado que conceda permissões **de serviço de ligação** e **leitura de registo** e obtenha uma cadeia de ligação para esta política, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Grupos de Recursos**. Selecione o grupo de recursos onde o seu hub está localizado e, em seguida, selecione o seu hub a partir da lista de recursos.

1. No painel de esquerda do seu hub, selecione Políticas de **acesso partilhado**.

1. A partir do menu superior acima da lista de políticas, **selecione Adicionar**.

1. No Under **Add a shared access policy**, insira um nome descritivo para a sua política, como o *serviçoAndRegistryRead*. Em **Permissões**, selecione **a leitura do registo** e o serviço **conecte-** e, em seguida, selecione **Criar**.

    ![Mostrar como adicionar uma nova política de acesso partilhado](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selecione a sua nova política na lista de políticas.

1. Nas **teclas de acesso partilhadas,** selecione o ícone de cópia para a **cadeia De ligação -- tecla primária** e guarde o valor.

    ![Mostrar como recuperar a cadeia de ligação](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre as políticas e permissões de acesso partilhados do IoT Hub, consulte [o controlo de acesso e permissões.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
