---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/07/2020
ms.custom: include file
ms.openlocfilehash: 9ccdc7a438d1dade534d39dc97a39a3bdae37dc4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75895453"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione a barra lateral da casa e, em seguida, **+ Crie um recurso**. 

   [![Expandir a barra lateral da casa, em seguida, selecione + Criar um recurso](./media/create-digital-twins-portal/azure-portal-create-a-resource.png)](./media/create-digital-twins-portal/azure-portal-create-a-resource.png#lightbox)

1. Procure **gémeos digitais**e selecione **Gémeos Digitais.** 

   [![Seleções para criar um novo exemplo de Gémeos Digitais](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png)](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png#lightbox)

   Alternativamente, selecione **Internet das Coisas**e selecione Digital Twins **(pré-visualização)**.

1. Selecione **Criar** para iniciar o processo de implementação.

   [![Criar e confirmar a implantação do recurso](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png)](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png#lightbox)

1. No painel **Digital Twins**, introduza as seguintes informações:
   * **Nome do Recurso**: crie um nome exclusivo para a instância do Digital Twins.
   * **Subscrição**: escolha a subscrição que quer utilizar para criar esta instância do Digital Twins. 
   * **Grupo de recursos**: selecione ou crie um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) para a instância do Digital Twins.
   * **Localização**: selecione a localização mais próxima dos seus dispositivos.

     [![Painel de Gémeos Digitais com informação inserida](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Reveja as informações dos Seus Gémeos Digitais e, em seguida, selecione **Criar**. A sua instância de Gémeos Digitais pode demorar alguns minutos a ser criada. Pode monitorizar o progresso no painel **Notificações**.

1. Abra o painel **Descrição geral** da instância do Digital Twins. Note o link no âmbito da API de **Gestão**. O URL **da API** de Gestão é formatado como: 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   Este URL leva-o para a documentação da API REST do Azure Digital Twins que se aplica à sua instância. Leia [Como utilizar o Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) para saber como ler e utilizar esta documentação de API. Copiar e modificar o URL **da API** de Gestão para este formato: 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   A aplicação irá utilizar o URL modificado como URL base para aceder à sua instância. Copie este URL modificado para um ficheiro temporário. Vai precisar disto na próxima secção.

   [![Visão geral da API de gestão](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)