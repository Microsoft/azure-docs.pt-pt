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
ms.openlocfilehash: 72f23b10047928f32886d9054f4dd1abdc569bd8
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66396996"
---
1. Entrar para o [portal do Azure](https://portal.azure.com/) com as credenciais para a subscrição do Azure que utilizar. 

   ![Portal do Azure](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. No canto superior esquerdo do portal, selecione **criar um recurso**.

   ![Criar um recurso no portal do Azure](./media/aml-create-in-portal/portal-create-a-resource-05-2019.png)

1. Na barra de pesquisa, introduza **Machine Learning**. Selecione o **área de trabalho do Machine Learning serviço** resultado de pesquisa.

   ![Procure uma área de trabalho](./media/aml-create-in-portal/allservices-search-05-2019.png)

1. Na **área de trabalho do ML service** painel, selecione **criar** para começar.

    ![Botão Criar](./media/aml-create-in-portal/portal-create-button-05-2019.png)

1. Na **área de trabalho do ML service** painel, configurar a sua área de trabalho.

   Campo|Descrição
   ---|---
   Nome de área de trabalho |Introduza um nome exclusivo que identifica a sua área de trabalho. Neste exemplo, utilizamos **docs ws**. Nomes têm de ser exclusivos entre o grupo de recursos. Utilize um nome que é fácil de lembrar e diferenciar de áreas de trabalho criadas por outras pessoas.  
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Utilize um grupo de recursos existente na sua subscrição ou introduza um nome para criar um novo grupo de recursos. Um grupo de recursos contém recursos relacionados para uma solução do Azure. Neste exemplo, utilizamos **docs aml**. 
   Location | Selecione a localização mais próxima dos seus utilizadores e os recursos de dados. Esta localização é onde a área de trabalho é criada.

   ![Criar área de trabalho](./media/aml-create-in-portal/workspace-create-main-tab-05-2019.png)

1. Para iniciar o processo de criação, selecione **rever + criar**.

    ![Criar](./media/aml-create-in-portal/workspace-create-main-review-button-05-2019.png)

1. Reveja a configuração de área de trabalho. Se estiver correto, selecione **criar**. Pode demorar alguns minutos a criar a área de trabalho.

    ![Criar](./media/aml-create-in-portal/workspace-create-review-tab-05-2019.png)

1. Para verificar o estado da implementação, selecione o ícone de notificações **bell**, na barra de ferramentas.

1. Quando o processo estiver concluído, é apresentada uma mensagem de êxito da implementação. Ele também está presente na secção notificações. Para ver a nova área de trabalho, selecione **Ir para recurso**.

   ![Estado de criação da área de trabalho](./media/aml-create-in-portal/notifications-05-2019.png)
