---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050383"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Para criar uma política de acesso compartilhado que conceda permissões de **leitura do registro** e **conexão de serviço** e obtenha uma cadeia de conexão para essa política, siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), selecione **grupos de recursos**. Selecione o grupo de recursos onde o Hub está localizado e, em seguida, selecione o Hub na lista de recursos.

1. No painel do lado esquerdo do Hub, selecione políticas de **acesso compartilhado**.

1. No menu superior acima da lista de políticas, selecione **Adicionar**.

1. Em **Adicionar uma política de acesso compartilhado**, insira um nome descritivo para sua política, como *serviceAndRegistryRead*. Em **permissões**, selecione **registro ler** e **serviço conectar**e, em seguida, selecione **criar**.

    ![Mostrar como adicionar uma nova política de acesso compartilhado](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selecione sua nova política na lista de políticas.

1. Em **chaves de acesso compartilhado**, selecione o ícone de cópia para a **cadeia de conexão--chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre políticas de acesso compartilhado do Hub IoT e permissões, consulte [controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
