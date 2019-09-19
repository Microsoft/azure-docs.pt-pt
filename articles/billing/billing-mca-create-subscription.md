---
title: Criar uma subscrição do Azure adicional para a conta de faturação
description: Saiba como adicionar uma nova subscrição do Azure no portal do Azure.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490913"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Criar uma subscrição do Azure adicional para o Contrato de Cliente da Microsoft

Crie subscrições adicionais para a conta de faturação para configurar ambientes separados para desenvolvimento e teste, segurança ou para isolar dados por motivos de conformidade.

Este artigo aplica-se à conta de faturação de um Contrato de Cliente da Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access). Se quiser criar subscrições para outros tipos de contas de faturação, veja [Criar uma subscrição adicional no portal do Azure](billing-create-subscription.md).

Para criar uma subscrição, deve ser um **proprietário da secção de fatura**, **contribuidor da secção de fatura** ou **criador da subscrição do Azure**. Para obter mais informações, veja [Tarefas e funções da faturação da subscrição](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Para conceder a outros utilizadores permissões para criar subscrições do Azure para a conta de faturação, veja [Conceder permissões a outros utilizadores para criar subscrições do Azure](#give-others-permission).

## <a name="create-a-subscription"></a>Criar uma subscrição

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Escreva **Subscrições** na caixa de pesquisa.

   ![Captura de ecrã que mostra a pesquisa de subscrições no portal](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Selecione **Adicionar**

4. Se tiver acesso a várias contas de faturação, selecione a conta de faturação do seu contrato de cliente da Microsoft.

   ![Captura de ecrã que mostra a página Criar subscrição](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Selecione um perfil de faturação. Os custos da subscrição serão faturados no perfil de faturação selecionado. Se tiver acesso a apenas um perfil de faturação, a seleção estará desativada.

6. Selecione a secção de fatura. Os custos da subscrição serão faturados nesta secção da fatura do perfil de faturação. Se tiver acesso a apenas uma secção de fatura, a seleção estará desativada.

7. Selecione um plano para a subscrição. Selecione o **Plano do Microsoft Azure para DevTest**. Se planear utilizar esta subscrição para desenvolvimento ou teste de cargas de trabalho, utilize o **Plano do Microsoft Azure**. Se tiver acesso a apenas um plano, a seleção vai estará desativada.

8. Introduza um nome para a subscrição. O nome ajuda-o a identificar facilmente a subscrição no portal do Azure.

9. Selecione **Criar**.

## <a name="give-others-permission"></a>Conceder permissão a outros utilizadores

Adicione utilizadores como criadores da subscrição do Azure numa secção de fatura para lhes conceder permissão para criar subscrições do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa de subscrições no portal](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Aceda à secção de fatura. Dependendo do seu acesso, pode ser preciso selecionar uma conta de faturação ou um perfil de faturação. Na conta ou no perfil de faturação, selecione **Secções de fatura** e, em seguida, uma seção de fatura na lista. Todas as subscrições criadas pelos utilizadores serão faturadas nesta secção de fatura.
   
   ![Captura de ecrã que mostra a seleção das secções de faturas](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Selecione **Gestão de Acessos (IAM)** no canto superior esquerdo.

5. Na parte superior da página, selecione **Adicionar**.

6. Selecione a função **Criador da subscrição do Azure**.

7. Introduza o endereço de e-mail do utilizador ao qual pretende conceder acesso.

8. Selecione **Guardar**.

## <a name="check-access"></a>Verificar acesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Conceder a outros utilizadores permissão para criar recursos do Azure com funções incorporadas](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Criar uma máquina virtual Windows](../virtual-machines/windows/quick-create-portal.md)
- [Criar uma máquina virtual Linux](../virtual-machines/linux/quick-create-portal.md)
- [Criar grupos de gestão para a organização e gestão de recursos](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
