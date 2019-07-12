---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/21/2019
ms.openlocfilehash: 2ec45b67367198c14fc9d03cdb659a51aed8a504
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841427"
---
1. Entrar para o [portal do Azure](https://portal.azure.com/) com as credenciais para a subscrição do Azure que utilizar. 

   ![Portal do Azure](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. No canto superior esquerdo do portal, selecione **criar um recurso**.

   ![Criar um recurso no portal do Azure](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Utilize a barra de pesquisa, para selecionar **área de trabalho do Machine Learning serviço**.

   ![Procure uma área de trabalho](./media/aml-create-in-portal/allservices-search.png)

1. Na **área de trabalho do ML service** painel, selecione **criar** para começar.

    ![Botão Criar](./media/aml-create-in-portal/portal-create-button.png)

1. Na **área de trabalho do ML service** painel, configurar a sua área de trabalho.

    ![Criar área de trabalho](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Campo|Descrição
   ---|---
   Nome de área de trabalho |Introduza um nome exclusivo que identifica a sua área de trabalho. Neste exemplo, utilizamos **docs ws**. Nomes têm de ser exclusivos entre o grupo de recursos. Utilize um nome que é fácil de lembrar e diferenciar de áreas de trabalho criadas por outras pessoas.  
   Subscription |Selecione a subscrição do Azure que pretende utilizar.
   Resource group | Utilize um grupo de recursos existente na sua subscrição ou introduza um nome para criar um novo grupo de recursos. Um grupo de recursos contém recursos relacionados para uma solução do Azure. Neste exemplo, utilizamos **docs aml**. 
   Location | Selecione a localização mais próxima dos seus utilizadores e os recursos de dados. Esta localização é onde a área de trabalho é criada.

1. Reveja a configuração da área de trabalho e selecione **criar**. Pode demorar alguns minutos a criar a área de trabalho.

1. Quando o processo estiver concluído, é apresentada uma mensagem de êxito da implementação. Ele também está presente na secção notificações. Para ver a nova área de trabalho, selecione **Ir para recurso**.

   ![Estado de criação da área de trabalho](./media/aml-create-in-portal/notifications.png)
