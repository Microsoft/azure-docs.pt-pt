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
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558503"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Para obter a cadeia de conexão do Hub IoT para a política de **serviço** , siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), selecione **grupos de recursos**. Selecione o grupo de recursos onde o Hub está localizado e, em seguida, selecione o Hub na lista de recursos.

1. No painel do lado esquerdo do Hub IoT, selecione políticas de **acesso compartilhado**.

1. Na lista de políticas, selecione a política de **serviço** .

1. Em **chaves de acesso compartilhado**, selecione o ícone de cópia para a **cadeia de conexão--chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre políticas de acesso compartilhado do Hub IoT e permissões, consulte [controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
