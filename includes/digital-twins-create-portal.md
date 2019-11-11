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
ms.openlocfilehash: 92b9a4754769566feb3658e07081e9fdae78fcfc
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903884"
---
1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

1. Na home page do, selecione **+ criar um recurso**. Pesquise **gêmeos digital**e selecione **digital gêmeos**. Selecione **Criar** para iniciar o processo de implementação.

   [![seleções para criar uma nova instância de gêmeos digital](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

1. No painel **Digital Twins**, introduza as seguintes informações:
   * **Nome do Recurso**: crie um nome exclusivo para a instância do Digital Twins.
   * **Subscrição**: escolha a subscrição que quer utilizar para criar esta instância do Digital Twins. 
   * **Grupo de recursos**: selecione ou crie um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) para a instância do Digital Twins.
   * **Localização**: selecione a localização mais próxima dos seus dispositivos.

     [![painel digital gêmeos com informações inseridas](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Examine as informações de gêmeos digital e, em seguida, selecione **criar**. Sua instância digital gêmeos pode levar alguns minutos para ser criada. Pode monitorizar o progresso no painel **Notificações**.

1. Abra o painel **Descrição geral** da instância do Digital Twins. Observe o link sob **API de gerenciamento**.

   A URL da **API de gerenciamento** é formatada como `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Este URL leva-o para a documentação da API REST do Azure Digital Twins que se aplica à sua instância. Leia [Como utilizar o Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) para saber como ler e utilizar esta documentação de API.

    Copie e modifique a URL da **API de gerenciamento** para este formato: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. A aplicação irá utilizar o URL modificado como URL base para aceder à sua instância. Copie este URL modificado para um ficheiro temporário. Você precisará dela na próxima seção.

    [API de gerenciamento de ![](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)