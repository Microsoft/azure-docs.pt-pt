---
title: Ver e transferir a utilização e os custos do Azure
description: Descreve como transferir ou ver os custos e a utilização diária do Azure.
keywords: utilização de faturação, custos de utilização, transferência da utilização, ver utilização, fatura do azure, utilização do azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7d2d7be562eaaa7dd21e63735f5697ffe5a62f8a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491434"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Ver e transferir a utilização e os custos do Azure

Se for cliente do EA ou tiver um [Contrato de Cliente da Microsoft](#check-your-access-to-a-microsoft-customer-agreement), pode transferir a utilização e os custos do Azure no [portal do Azure](https://portal.azure.com/). Para outras subscrições, aceda ao [Centro de Contas do Azure](https://account.azure.com/Subscriptions) para transferir a utilização.

Apenas determinadas funções têm permissão para receber informações de utilização do Azure, como o Administrador de Conta ou o Administrador da Empresa. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

Se tiver um [Contrato de Cliente da Microsoft](#check-your-access-to-a-microsoft-customer-agreement), terá de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas de um perfil de faturação para ver a utilização e os custos do Azure. Para saber mais sobre as funções de faturação para Contratos de Cliente da Microsoft, veja [Funções e tarefas de perfil de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Transferir a utilização a partir do Centro de Contas (.csv)

1. Inicie sessão no [Centro de Contas do Azure](https://account.windowsazure.com/subscriptions) como Administrador de Conta.

2. Selecione a subscrição para a qual quer as informações da fatura e de utilização.

3. Selecione **HISTÓRICO DE FATURAÇÃO**.

    ![Captura de ecrã a mostrar a opção Histórico de Faturação](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Pode ver as declarações correspondentes aos últimos seis períodos de faturação e o período de faturação por faturar atual.

    ![Captura de ecrã a mostrar os períodos de faturação, as opções de transferência da fatura e utilização diária e os custos totais de cada período de faturação](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selecione **Ver Extrato Atual** para ver uma estimativa dos custos no momento em que esta foi gerada. Esta informação só é atualizada diariamente e poderá não incluir a totalidade da utilização. A fatura mensal poderá divergir desta estimativa.

    ![Captura de ecrã a mostrar a opção Ver Extrato Atual](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Captura de ecrã a mostrar a estimativa dos custos atuais](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selecione **Transferir Utilização** para transferir os dados de utilização diária como um ficheiro CSV. Se vir duas versões disponíveis, transfira a versão 2.

    ![Captura de ecrã a mostrar a opção Transferir Utilização](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Apenas o Administrador de Conta pode aceder ao Centro de Contas do Azure. Outros administradores de faturação, como um Proprietário, podem obter informações de utilização com as [APIs de Faturação](billing-usage-rate-card-overview.md).

Para obter mais informações sobre a sua utilização diária, veja [Compreender a fatura do Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda para gerir os seus custos, veja [Evitar custos inesperados com a faturação e a gestão de custos do Azure](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Transferir a utilização para clientes do EA

Para ver e transferir os dados de utilização como um cliente do EA, tem de ser um Administrador da Empresa, o Proprietário da Conta ou o Administrador do Departamento com a política de visualização de custos ativada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *Gestão de Custos + Faturação*.

    ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selecione **Utilização + custos**.
1. Para o mês que pretende transferir, selecione **Transferir**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Transferir utilização para o Contrato de Cliente da Microsoft

Se tiver um Contrato de Cliente da Microsoft, pode transferir a sua utilização e custos do Azure para o seu perfil de faturação. Tem de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas do perfil de faturação para transferir o CSV de utilização e custos do Azure.

### <a name="download-usage-for-billed-charges"></a>Transferir utilização para custos faturados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Procure *Gestão de Custos + Faturação*.
3. Selecione um perfil de faturação. Dependendo do seu acesso, pode ser preciso selecionar primeiro uma conta de faturação.
4. Selecione **Faturas**.
5. Na grelha da fatura, localize a linha da fatura que corresponde à utilização que pretende transferir.
6. Clique nas reticências (`...`) no final da linha.

    ![Captura de ecrã a mostrar as reticências no final da linha](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. No menu de contexto da transferência, selecione **Utilização e custos do Azure**.

     ![Captura de ecrã a mostrar a opção Utilização e custos do Azure selecionada](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Transferir utilização para custos pendentes

Pode também transferir a utilização do mês até à data atual para o período de faturação atual. Estes custos de utilização que ainda não foram faturados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Procure *Gestão de Custos + Faturação*.
3. Selecione um perfil de faturação. Dependendo do seu acesso, pode ser preciso selecionar primeiro uma conta de faturação.
4. Na área **Descrição Geral**, localize as ligações para transferência por baixo dos custos do mês até à data atual.
5. Selecione **Utilização e custos do Azure**.

    ![Captura de ecrã a mostrar a transferência a partir da Descrição Geral](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a fatura e os custos de utilização, veja:

- [Compreender os termos na sua utilização detalhada do Microsoft Azure](billing-understand-your-usage.md)
- [Understand your bill for Microsoft Azure](billing-understand-your-bill.md) (Compreender a sua fatura do Microsoft Azure)
- [Ver e transferir a sua fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Ver e transferir os preços do Azure da sua organização](billing-ea-pricing.md)

Se tiver um Contrato de Cliente da Microsoft, veja:

- [Compreender os termos da utilização detalhada do Azure do Contrato de Cliente da Microsoft](billing-mca-understand-your-usage.md)
- [Compreender os custos na fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-bill.md)
- [Ver e transferir a sua fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Ver e transferir os documentos fiscais do Contrato de Cliente da Microsoft](billing-mca-download-tax-document.md)
- [Ver e transferir os preços do Azure da sua organização](billing-ea-pricing.md)
