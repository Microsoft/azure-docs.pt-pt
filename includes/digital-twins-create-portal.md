---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.custom: include file
ms.openlocfilehash: 7b2df437833f270a6e102257693426f4cc65b9d2
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949748"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**. Pesquise **gêmeos digital**e selecione **digital gêmeos**. Selecione **criar** para iniciar o processo de implantação.

   [![Selections para criar uma nova instância de gêmeos digital](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

1. No painel **Digital Twins**, introduza as seguintes informações:
   * **Nome do recurso**: Crie um nome exclusivo para sua instância de gêmeos digital.
   * **Subscrição**: Escolha a assinatura que você deseja usar para criar essa instância de gêmeos digital. 
   * **Grupo de recursos**: Selecione ou crie um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) para a instância de gêmeos digital.
   * **Local**: Selecione o local mais próximo para seus dispositivos.

     [painel de gêmeos de @no__t 1Digital com informações inseridas](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Examine as informações de gêmeos digital e, em seguida, selecione **criar**. Sua instância digital gêmeos pode levar alguns minutos para ser criada. Pode monitorizar o progresso no painel **Notificações**.

1. Abra o painel **Descrição geral** da instância do Digital Twins. Observe o link sob **API de gerenciamento**.

   A URL da **API de gerenciamento** é formatada como `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Este URL leva-o para a documentação da API REST do Azure Digital Twins que se aplica à sua instância. Leia [Como utilizar o Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) para saber como ler e utilizar esta documentação de API.

    Modifique a URL da **API de gerenciamento** para esse formato `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. A aplicação irá utilizar o URL modificado como URL base para aceder à sua instância. Copie este URL modificado para um ficheiro temporário. Você precisará dela na próxima seção.

    [API ![Management](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)