---
title: Modo de exibição e o Azure transferir utilização e custos | Documentos da Microsoft
description: Descreve como transferir ou visualizar a sua utilização diária do Azure e os encargos.
keywords: utilização de faturação, os custos de utilização, utilização transferir, ver a utilização, a utilização de fatura do azure, azure
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 20404cea2aca984ef35472fa94d37c04eb8080e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872659"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Ver e transferir a sua utilização do Azure e os encargos

Se estiver num cliente EA ou têm um [contrato de cliente do Microsoft](#check-your-access-to-a-microsoft-customer-agreement), pode transferir utilização do Azure e custos no [portal do Azure](https://portal.azure.com/). Para outras subscrições, vá para o [Centro de contas do Azure](https://account.azure.com/Subscriptions) para transferir utilização.

Apenas determinadas funções do utilizador tem permissão para obter informações de utilização do Azure, como o administrador de conta ou administrador de empresa. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

Se tiver um [contrato de cliente da Microsoft](#Check-your-access-to-a-Microsoft-Customer-Agreement), tem de ser um perfil de faturação proprietário, Contribuidor, leitor, ou manager para ver a sua utilização do Azure e a cobrança de nota fiscal. Para saber mais sobre as funções de faturas para contratos de cliente da Microsoft, consulte [funções de perfis e tarefas de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Transferir utilização a partir do Centro de contas (. csv)

1. Inicie sessão no [Centro de contas do Azure](https://account.windowsazure.com/subscriptions) como o administrador de conta.

2. Selecione a subscrição que pretende que as informações da fatura e utilização.

3. Selecione **histórico de FATURAÇÃO**.

    ![Captura de ecrã que mostra a opção de histórico de faturação](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Pode ver as suas instruções para os últimos seis períodos de faturas e o período de faturação por faturar atual.

    ![Captura de ecrã que mostra períodos de faturação, as opções a transferir fatura e utilização diária e total das cobranças para cada período de faturação](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selecione **Ver Extrato Atual** para ver uma estimativa dos custos no momento em que a estimativa foi gerada. Esta informação é atualizada diariamente apenas e não pode incluir toda a sua utilização. A fatura mensal poderá divergir desta estimativa.

    ![Captura de ecrã que mostra a opção de ver extrato atual](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Captura de ecrã que mostra a estimativa de custos atuais](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selecione **Transferir Utilização** para transferir os dados de utilização diária como um ficheiro CSV. Se vir duas versões disponíveis, transfira a versão 2.

    ![Captura de ecrã que mostra a opção de transferir utilização](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Apenas o administrador de conta pode aceder ao centro de contas do Azure. Outros administradores de faturas como, por exemplo, um proprietário, podem obter a utilização de informações de utilização a [APIs de faturação](billing-usage-rate-card-overview.md).

Para obter mais informações sobre a sua utilização diária, veja [Compreender a fatura do Microsoft Azure](billing-understand-your-bill.md). Para ajudar a gerir os custos, veja [evitar custos inesperados com a faturação do Azure e a gestão de custos](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Transferir utilização para clientes com EA

Para ver e transferir dados de utilização como um cliente EA, tem de ser um administrador empresarial, o proprietário da conta, ou administrador do departamento com o modo de exibição cobra política ativada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *custo Management + faturação*.

    ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selecione **utilização e custos**.
1. Para o mês em que deseja baixar, selecione **transferir**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Transferir utilização para o seu contrato de cliente da Microsoft

Se tiver um contrato de cliente da Microsoft, pode transferir a sua utilização do Azure e a cobrança para o seu perfil de faturação. Tem de ser um perfil de faturação proprietário, a Contribuidor, leitor, ou criar uma fatura manager transferir a utilização do Azure e os encargos CSV.

### <a name="download-usage-for-billed-charges"></a>Transferir utilização para os custos de faturação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Pesquisar nos **custo Management + faturação**.
3. Selecione um perfil de faturação. Consoante o acesso, poderá ter de selecionar primeiro uma conta de cobrança.
4. Selecione **Faturas**.
5. Na grelha de nota fiscal, localize a linha da nota fiscal correspondente à utilização de que pretende transferir.
6. Clique nas reticências (`...`) no final da linha.

    ![Captura de ecrã que mostra o botão de reticências no final da linha](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. No menu de contexto do download, selecione **utilização do Azure e os encargos**.

     ![Captura de ecrã que mostra a utilização do Azure e os encargos selecionados](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Transferir utilização para pendentes custos

Também pode transferir a utilização de mês até à data para o período de faturação atual. Estes custos de utilização que não foi paga ainda.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Pesquisar nos **custo Management + faturação**.
3. Selecione um perfil de faturação. Consoante o acesso, poderá ter de selecionar primeiro uma conta de cobrança.
4. Na **descrição geral** painel, encontra os links de download abaixo os encargos do mês até à data.
5. Selecione **utilização do Azure e os encargos**.

    ![Captura de ecrã que mostra o download da descrição geral](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as suas cobranças de fatura e utilização, consulte:

- [Compreender os termos na utilização de detalhadas do Microsoft Azure](billing-understand-your-usage.md)
- [Compreender a sua fatura do Microsoft Azure](billing-understand-your-bill.md)
- [Ver e transferir a fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Veja e transfira os preços do Azure da sua organização](billing-ea-pricing.md)

Se tiver um contrato de cliente da Microsoft, consulte:

- [Compreender os termos no seu contrato de cliente do Microsoft Azure detalhadas da utilização](billing-mca-understand-your-usage.md)
- [Compreender os encargos na sua fatura de contrato de cliente da Microsoft](billing-mca-understand-your-bill.md)
- [Ver e transferir a fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Ver e transferir documentos de imposto para o seu contrato de cliente da Microsoft](billing-mca-download-tax-document.md)
- [Veja e transfira os preços do Azure da sua organização](billing-ea-pricing.md)
