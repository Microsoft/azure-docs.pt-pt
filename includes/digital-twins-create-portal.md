---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 87599c6e0c4bd4313eac154b4d07110cbe4389a0
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622955"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**. Pesquise **gêmeos digital**e selecione **digital gêmeos**. Selecione **criar** para iniciar o processo de implantação.

   ![Seleções para criar uma nova instância de gêmeos digital](./media/create-digital-twins-portal/create-digital-twins.png)

1. No painel **Digital Twins**, introduza as seguintes informações:
   * **Nome do recurso**: Crie um nome exclusivo para sua instância de gêmeos digital.
   * **Assinatura**: Escolha a assinatura que você deseja usar para criar essa instância de gêmeos digital. 
   * **Grupo de recursos**: Selecione ou crie um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) para a instância de gêmeos digital.
   * **Local**: Selecione o local mais próximo para seus dispositivos.

     ![Painel digital gêmeos com informações inseridas](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Examine as informações de gêmeos digital e, em seguida, selecione **criar**. Sua instância digital gêmeos pode levar alguns minutos para ser criada. Pode monitorizar o progresso no painel **Notificações**.

1. Abra o painel **Descrição geral** da instância do Digital Twins. Observe o link sob **API de gerenciamento**.

   A URL da **API de gerenciamento** é `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`formatada como. Este URL leva-o para a documentação da API REST do Azure Digital Twins que se aplica à sua instância. Leia [Como utilizar o Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) para saber como ler e utilizar esta documentação de API.

    Modifique a URL da **API de gerenciamento** para `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`esse formato. A aplicação irá utilizar o URL modificado como URL base para aceder à sua instância. Copie este URL modificado para um ficheiro temporário. Você precisará dela na próxima seção.

    ![API de Gestão](./media/create-digital-twins-portal/digital-twins-management-api.png)