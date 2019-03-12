---
title: Criar uma subscrição do Azure adicional para a sua conta de faturação | Documentos da Microsoft
description: Saiba como adicionar uma nova subscrição do Azure no portal do Azure.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 686543a322d1c87760060a1f4db1b26abca75cb8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539416"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Criar uma subscrição do Azure adicional para o contrato de cliente da Microsoft

Crie subscrições adicionais para sua conta de cobrança para configurar ambientes separados para desenvolvimento e teste, segurança ou para isolar os dados por motivos de conformidade.

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement). Se quiser criar subscrições para outras contas de faturas, veja [criar uma subscrição adicional no portal do Azure](billing-create-subscription.md).

Para criar uma subscrição, tem de ser um **proprietário da secção de nota fiscal**, **contribuinte da secção de nota fiscal**, ou **criador de subscrição do Azure**. Para obter mais informações, consulte [funções de faturas de subscrição e tarefas](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Para fornecer a outros utilizadores permissão para criar subscrições do Azure para a sua conta de faturação, consulte [conceder a outros utilizadores permissão para criar subscrições do Azure](#give-others-permission-to-create-azure-subscriptions).

## <a name="create-a-subscription-in-the-azure-portal"></a>Criar uma subscrição no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **subscrições**.

   ![Captura de ecrã que mostra a pesquisa no portal de subscrições](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Selecione **adicionar**

4. Se tiver acesso a várias contas de faturas, selecione a conta de faturação para o contrato do cliente Microsoft.

   ![Captura de ecrã que mostra criar a página de subscrição](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Selecione um perfil de faturação. Os encargos para a sua subscrição, o qual refletirá na fatura do perfil de faturação e irão ser pagas através de seus métodos de pagamento. Se tiver acesso a apenas um perfil de faturação, a seleção estarão esbatida.

6. Selecione uma secção de nota fiscal. Os encargos para a sua subscrição serão refletir sobre esta secção da nota fiscal do perfil de faturação. Se tiver acesso a apenas uma secção de nota fiscal, a seleção estarão esbatida.

7. Selecione um plano para a subscrição. Selecione **plano do Microsoft Azure para Dev/Test**, se planeja usar esta subscrição para o desenvolvimento ou testes cargas de trabalho mais usar **plano do Microsoft Azure**. Se tiver acesso a apenas um plano, a seleção estarão esbatida.

8. Introduza um nome para a subscrição. O nome ajuda-o a identificar facilmente a subscrição no portal do Azure.

9. Selecione **Criar**.

## <a name="give-others-permission-to-create-azure-subscriptions"></a>Conceder a outros utilizadores permissão para criar subscrições do Azure

Adicione utilizadores de como os criadores de subscrição do Azure, numa seção de nota fiscal, para lhes dar permissão para criar subscrições do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa no portal de subscrições](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Vá para a secção de nota fiscal. Consoante o acesso, terá de selecionar uma conta de cobrança ou o perfil de faturação. A partir da conta de faturação ou um perfil, selecione **secções de nota fiscal** e, em seguida, uma seção de nota fiscal.

4. Selecione **gestão de acessos (IAM)** no lado superior esquerdo.

5. Na parte superior da página, selecione **adicionar**.

6. Selecione **criador de subscrição do Azure** para a função.

   ![Captura de ecrã que mostra a conceder a função de criador de subscrição do Azure para um utilizador](./media/billing-mca-create-subscription/billing-mca-add-azure-subscription-creator.png)

7. Introduza o endereço de e-mail do utilizador a quem pretende conceder acesso.

8. Selecione **Guardar**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

- [Conceder a outros utilizadores permissão para criar recursos do Azure com funções incorporadas](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Criar uma máquina virtual do windows](../virtual-machines/windows/quick-create-portal.md)
- [Criar uma máquina virtual do linux](../virtual-machines/linux/quick-create-portal.md)
- [Criar grupos de gestão para a organização de recursos e gestão](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
