---
title: Criar um plano no Azure Stack | Documentos da Microsoft
description: Como um administrador da nuvem, crie um plano que permita aos subscritores aprovisionar máquinas virtuais.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: a2ac138228b7b54f486acb0f42975748136a8da7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759474"
---
# <a name="create-a-plan-in-azure-stack"></a>Criar um plano no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

[Planos](azure-stack-key-features.md) consistem em agrupamentos de um ou mais serviços e as respetivas quotas. Como um fornecedor, pode criar planos para oferecer aos seus utilizadores. Por sua vez, os seus utilizadores subscrevem suas ofertas para utilizar os planos, serviços e quotas incluem. Este exemplo mostra como criar um plano que inclui computação, rede e os fornecedores de recursos de armazenamento. Este plano dá os assinantes a capacidade de aprovisionar máquinas virtuais.

## <a name="create-a-plan-1902-and-later"></a>Criar um plano (1902 e posterior)

1. Inicie sessão para o [portal de administrador do Azure Stack](https://adminportal.local.azurestack.external).

2. Para criar um plano e uma oferta que os utilizadores podem subscrever, selecione **+ criar um recurso**, em seguida, **ofertas + planos**, em seguida, **plano**.
  
   ![Selecionar um plano](media/azure-stack-create-plan/select-plan.png)

3. Uma interface do usuário com guias aparece que permite-lhe especificar o nome do plano, adicionar serviços e definir quotas para cada um dos serviços selecionados. Mais importante, pode rever os detalhes da oferta que cria, antes de decidir para criá-lo.

   Sob o **Noções básicas** separador da **novo plano** janela, introduza um **nome a apresentar** e uma **nome do recurso**. O nome a apresentar é o nome amigável do plano que podem ver operadores. Observe que, no portal do administrador, detalhes do plano são visíveis apenas para operadores.

   ![Especifique os detalhes](media/azure-stack-create-plan/plan-name.png)

4. Criar uma nova **grupo de recursos**, ou selecione um existente, como um contêiner para o plano.

   ![Especifique o grupo de recursos](media/azure-stack-create-plan/resource-group.png)

5. Selecione o **serviços** separador e, em seguida, selecione a caixa de verificação **Microsoft. Compute**, **Network**, e **Microsoft. Storage** .
  
   ![Selecionar serviços](media/azure-stack-create-plan/services.png)

6. Selecione o **Quotas** separador. Junto a **Microsoft. Storage**, escolha a quota de predefinição na lista suspensa ou selecione **criar nova** para criar uma quota personalizada.
  
   ![Quotas](media/azure-stack-create-plan/quotas.png)

7. Se estiver a criar uma quota de novo, introduza um **nome** da quota e, em seguida, especifique os valores de quota. Selecione **OK** para criar a quota.

   ![Nova quota](media/azure-stack-create-plan/new-quota.png)

8. Repita os passos 6 e 7 para criar e atribuir as quotas para **Network** e **Microsoft. Compute**. Quando todos os três serviços tem quotas atribuídas, eles ver como o exemplo seguinte.

   ![Atribuições de quota concluída](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Selecione **rever + criar** para rever o plano. Reveja todos os valores e as quotas para garantir que estão corretos. Tenha em atenção as setas de expansão para a esquerda de cada par de quota do serviço. Uma nova funcionalidade permite-lhe expandir as quotas nos planos de selecionada, uma de cada vez, para ver os detalhes de cada quota num plano e volte atrás para fazer as alterações necessárias.

   ![Criar o plano](media/azure-stack-create-plan/create.png)

10. Quando estiver pronto, selecione **criar** para criar o plano.

11. Para ver o novo plano, selecione **planos**, em seguida, procure o plano e selecione o seu nome. Se a sua lista de recursos é longa, utilize **pesquisa** para localizar o seu plano por nome.

## <a name="create-a-plan-1901-and-earlier"></a>Criar um plano (1901 e anterior)

1. Inicie sessão para o [portal de administrador do Azure Stack](https://adminportal.local.azurestack.external).

2. Para criar um plano e uma oferta que os utilizadores podem subscrever, selecione **+ criar um recurso**, em seguida, **ofertas + planos**, em seguida, **plano**.
  
   ![Selecionar um plano](media/azure-stack-create-plan/select-plan1901.png)

3. Sob **novo plano**, introduza um **nome a apresentar** e um **nome do recurso**. O nome a apresentar é o nome amigável do plano que os utilizadores podem ver. Apenas o administrador pode ver o nome do recurso, o administrador utiliza para trabalhar com o plano como um recurso do Azure Resource Manager.

   ![Especifique os detalhes](media/azure-stack-create-plan/plan-name1901.png)

4. Criar uma nova **grupo de recursos**, ou selecione um existente, como um contêiner para o plano.

   ![Especifique o grupo de recursos](media/azure-stack-create-plan/resource-group1901.png)

5. Selecione **serviços** e, em seguida, selecione a caixa de verificação **Microsoft. Compute**, **Network**, e **Microsoft. Storage**. Em seguida, escolha **selecione** para guardar a configuração. Caixas de seleção aparecem quando o mouse passa sobre cada opção.
  
   ![Selecionar serviços](media/azure-stack-create-plan/services1901.png)

6. Selecione **Quotas**, **Microsoft. Storage (local)** e, em seguida, escolha o quota predefinida ou selecione **criar nova quota** para criar uma quota personalizada.
  
   ![Quotas](media/azure-stack-create-plan/quotas1901.png)

7. Se estiver a criar uma quota de novo, introduza um **Name** para a quota > especifique os valores de quota > selecione **OK**. O **Criar quota** fecha a caixa de diálogo.

   ![Nova quota](media/azure-stack-create-plan/new-quota1901.png)

   Em seguida, selecione a quota de novo que criou. Selecionar a quota, atribui-o e fecha a caixa de diálogo de seleção.
  
   ![Atribuir a quota](media/azure-stack-create-plan/assign-quota1901.png)

8. Repita os passos 6 e 7 para criar e atribuir as quotas para **Network (local)** e **Microsoft. Compute (local)**. Quando todos os três serviços tem quotas atribuídas, eles ver como o exemplo seguinte.

   ![Atribuições de quota concluída](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. Sob **Quotas**, escolha **OK**e, em **novo plano**, selecione **criar** para criar o plano.

    ![Criar o plano](media/azure-stack-create-plan/create1901.png)

10. Para ver o seu plano de novo, selecione **todos os recursos**, em seguida, procure o plano e selecione o seu nome. Se a sua lista de recursos é longa, utilize **pesquisa** para localizar o seu plano por nome.

    ![Rever o plano](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma oferta](azure-stack-create-offer.md)
