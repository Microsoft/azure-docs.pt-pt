---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 12/17/2018
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 0c89c8353d410ddd54e8a59f3838c914702b96ca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124530"
---
1. Inicie sessão no [portal do Azure](http://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**. Procure **Digital Twins** e selecione **Digital Twins (pré-visualização)**. Selecione **criar** para iniciar o processo de implantação.

   ![Seleções para criar uma nova instância de duplos Digital](./media/create-digital-twins-portal/create-digital-twins.png)

1. No painel **Digital Twins**, introduza as seguintes informações:
   * **Nome do recurso**: Crie um nome exclusivo para a sua instância de duplos Digital.
   * **Subscrição**: Escolha a subscrição que pretende utilizar para criar esta instância de duplos Digital. 
   * **Grupo de recursos**: Selecione ou crie uma [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) para a instância de duplos Digital.
   * **Localização**: Selecione a localização mais próxima dos seus dispositivos.

     ![Painel digital de duplos com informações inseridas](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Reveja as suas informações de duplos Digital e, em seguida, selecione **criar**. A instância de duplos Digital poderá demorar alguns minutos a ser criada. Pode monitorizar o progresso no painel **Notificações**.

1. Abra o painel **Descrição geral** da instância do Digital Twins. Tenha em atenção a ligação em **gestão de API**.

   O **gestão de API** URL é formatado como `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Este URL leva-o para a documentação da API REST do Azure Digital Twins que se aplica à sua instância. Leia [Como utilizar o Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) para saber como ler e utilizar esta documentação de API.

    Modificar a **gestão de API** URL nesse formato `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. A aplicação irá utilizar o URL modificado como URL base para aceder à sua instância. Copie este URL modificado para um ficheiro temporário. Precisará na próxima seção.

    ![API de Gestão](./media/create-digital-twins-portal/digital-twins-management-api.png)