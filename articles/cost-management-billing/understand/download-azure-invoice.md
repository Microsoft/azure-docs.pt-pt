---
title: Ver e transferir a fatura do Microsoft Azure
description: Descreve como ver e transferir a fatura do Microsoft Azure.
keywords: faturação, transferência da fatura, fatura do azure, utilização do azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: 0f413d38565202d379c81570b5cb169c2ed8effe
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987827"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Ver e transferir a fatura do Microsoft Azure

Para a maioria das subscrições, pode transferir a fatura a partir do [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou recebê-la por e-mail. Se for um cliente do Azure com um Contrato Enterprise (cliente EA), não poderá transferir as faturas da sua organização. As faturas são enviadas para quem está configurado para receber faturas para a inscrição.

Apenas determinadas funções não têm permissão para ver faturas. Por exemplo, o Administrador de Conta ou o Administrador do Enterprise. Para obter mais informações sobre como obter acesso às informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](../manage/manage-billing-access.md).

Se tiver um Contrato de Cliente da Microsoft (MCA), deverá ter uma das seguintes funções para obter as faturas:

- Proprietário do perfil de faturação
- Contribuinte
- Leitor
- Gestor de faturas

Se tiver um Contrato de Parceiro da Microsoft (MPA), terá de ser Administrador Global ou Agente de Administração na organização parceira para ver e transferir as faturas do Azure. [Verifique o tipo da conta de faturação](#check-your-billing-account-type) para saber as permissões de que precisa.

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Motivo pelo qual pode não obter uma fatura

Podem existir vários motivos para não ver uma fatura:

- Passaram menos de 30 dias desde o dia em que subscreveu o Azure.

- O Azure cobra-o no final do período da fatura. Portanto, uma fatura pode ainda não ter sido gerada. Aguarde até ao fim do período de faturação.

- Não tem permissão para ver faturas. Se tiver um contrato MCA ou MPA, terá de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas no perfil de faturação. Para as outras subscrições, pode não ver as faturas antigas se não for o Administrador de Conta. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](../manage/manage-billing-access.md).

- Se tiver uma Avaliação Gratuita ou um montante de crédito mensal com a sua subscrição, receberá apenas uma fatura quando exceder o montante de crédito mensal. Se tiver um Contrato de Cliente da Microsoft ou Contrato de Parceiro da Microsoft, receberá sempre uma fatura.

## <a name="download-invoices-in-the-azure-portal"></a>Transferir as faturas no portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Procure *Cost Management + Faturação*.
1. Dependendo do seu acesso, poderá ser preciso selecionar uma conta de faturação ou um perfil de faturação.
1. No menu esquerdo, selecione **Faturas** em **Faturação**.
1. Na grelha da fatura, localize a linha da fatura que quer transferir.
1. Clique no ícone de transferência ou nas reticências (`...`) no final da linha.
1. No menu da transferência, selecione **Fatura**.

Para obter mais informações sobre a fatura, veja [Compreender a fatura do Microsoft Azure](review-individual-bill.md). Para obter ajuda com a gestão dos custos, veja [Evitar custos inesperados com a faturação e a gestão de custos do Azure](../manage/getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Obter as faturas da subscrição por e-mail

Pode ativar e configurar destinatários adicionais para receber a fatura do Azure por e-mail. Esta funcionalidade pode não estar disponível para determinadas subscrições, como as ofertas de suporte, os Contratos Enterprise ou o Azure no Open.

1. Selecione a sua subscrição na [página Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Escolha Optar por receber para cada subscrição que tiver. Clique em **Faturas** e, em seguida, **Enviar a minha fatura por e-mail**.

    ![Captura de ecrã que mostra o fluxo de optar por receber](./media/download-azure-invoice/invoicesdeeplink01.png)

2. Clique em **Optar por receber** e aceite os termos.

    ![Captura de ecrã que mostra o passo 2 do fluxo de optar por receber](./media/download-azure-invoice/invoicearticlestep02.png)

3. Quando aceitar o contrato, pode configurar destinatários adicionais. Quando um destinatário é removido, o endereço de e-mail deixa de ser armazenado. Se mudar de ideia, terá de os adicionar novamente.

    ![Captura de ecrã que mostra o passo 3 do fluxo de optar por receber](./media/download-azure-invoice/invoicearticlestep03.png)

Se não receber um e-mail depois de seguir os passos, confirme que o endereço de e-mail está correto nas [preferências de comunicação do seu perfil](https://account.windowsazure.com/profile).

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Optar por não receber as faturas da subscrição por e-mail

Para optar por não receber as faturas por e-mail, siga os passos anteriores e clique em **Optar por não receber faturas por e-mail**. Esta opção remove todos os endereços de e-mail que foram configurados para receber faturas por e-mail. Pode reconfigurar os destinatários se optar por voltar a receber faturas.

 ![Captura de ecrã que mostra o fluxo de optar por não receber](./media/download-azure-invoice/invoicearticlestep04.png)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obter as faturas do Contrato de Cliente da Microsoft por e-mail

Se você tiver uma conta de cobrança para o contrato do cliente da Microsoft, poderá optar por obter sua fatura em um email. Todos os usuários com uma função de proprietário, colaborador, leitor ou gerente de fatura em um perfil de cobrança receberão sua fatura por email. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa de uma subscrição no portal](./media/download-azure-invoice/search-cmb.png)

1. Selecione **perfis de cobrança** no lado esquerdo. Na lista perfis de cobrança, selecione um perfil de cobrança para obter suas faturas no email.

   [![captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Selecione **Propriedades** no lado esquerdo e, em seguida, selecione **Atualizar preferência de fatura de email**.

   [![captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/mca-select-update-email-preferences.png)](./media/download-azure-invoice/mca-select-update-email-preferences.png#lightbox)

1. Selecione **aceitar** e clique em **Atualizar**.

   [![captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/mca-select-email-opt-in.png)](./media/download-azure-invoice/mca-select-email-opt-in.png#lightbox)

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Optar por não receber as faturas por e-mail do Contrato de Cliente Microsoft

Para cancelar a obtenção de sua fatura por email, siga as etapas anteriores e clique em **recusar**. Todos os usuários com uma função de proprietário, colaborador, leitor ou Gerenciador de faturas são recusados de obter a fatura por email. 

## <a name="give-others-access-to-your-microsoft-customer-agreement-invoices"></a>Fornecer a outros acesso às notas fiscais do contrato do cliente da Microsoft

Você pode conceder a outros acesso para exibir, baixar e pagar faturas atribuindo-lhes a função de gerente de faturas para um perfil de cobrança. Se você tiver optado por obter sua fatura por email, esses usuários também receberão as notas fiscais por email. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa de uma subscrição no portal](./media/download-azure-invoice/search-cmb.png)

1. Selecione **perfis de cobrança** no lado esquerdo. Na lista perfis de cobrança, selecione um perfil de cobrança para o qual você deseja atribuir uma função de Gerenciador de faturas.

   [![captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Selecione **controle de acesso (iam)** no lado esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![captura de tela que mostra a página de controle de acesso](./media/download-azure-invoice/mca-select-access-control.png)](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png#lightbox)

1. Na lista suspensa função, selecione **Gerenciador de faturas**. Introduza o endereço de e-mail do utilizador ao qual pretende conceder acesso. Selecione **Guardar** para atribuir a função.

   [![captura de tela que mostra a adição de um usuário como um Gerenciador de faturas](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)

## <a name="check-your-billing-account-type"></a>Verificar o tipo da conta de faturação
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a fatura e os custos, veja:

- [Ver e transferir a utilização e os custos do Microsoft Azure](download-azure-daily-usage.md)
- [Understand your bill for Microsoft Azure](review-individual-bill.md) (Compreender a sua fatura do Microsoft Azure)
- [Compreender os termos na fatura do Azure](understand-invoice.md)
- [Compreender os termos na utilização diária do Microsoft Azure](understand-usage.md)
- [Ver os preços do Azure da sua organização](../manage/ea-pricing.md)

Se tiver um Contrato de Cliente da Microsoft, veja:

- [Compreender os custos na fatura para o seu perfil de faturação](review-customer-agreement-bill.md)
- [Compreender os termos na fatura para o seu perfil de faturação](mca-understand-your-invoice.md)
- [Compreender o ficheiro de utilização e de custos do Azure para o seu perfil de faturação](mca-understand-your-usage.md)
- [Ver e transferir os documentos fiscais para o seu perfil de faturação](mca-download-tax-document.md)
- [Ver os preços do Azure da sua organização](../manage/ea-pricing.md)
