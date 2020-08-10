---
title: Ver e transferir a utilização e os custos do Azure
description: Aprenda a transferir ou a ver a utilização e os custos diários do Azure, bem como a ver recursos disponíveis adicionais.
keywords: billing usage, usage charges, usage download, view usage, azure invoice, azure usage
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: d09f792e784c89e704691975d44cb3cbfa753cf4
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460512"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Ver e transferir a utilização e os custos do Azure

Pode transferir uma discriminação diária da utilização e dos custos do Azure no portal do Azure. Apenas determinadas funções têm permissão para receber informações de utilização do Azure, como o Administrador de Conta ou o Administrador da Empresa. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](../manage/manage-billing-access.md).

Se tiver um Contrato de Cliente da Microsoft (MCA), terá de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas de um perfil de faturação para ver a utilização e os custos do Azure.  Se tiver um Contrato de Parceiro da Microsoft (MPA), apenas as funções de Administrador Global e de Agente de Administração na organização parceira poderão ver e transferir a utilização e os custos do Azure. [Verifique o tipo da conta de faturação no portal do Azure](#check-your-billing-account-type).

Com base no tipo de subscrição que utiliza, as opções para transferir a sua utilização e os custos variam.

## <a name="download-usage-from-the-azure-portal-csv"></a>Transferir a utilização do portal do Azure (.csv)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *Cost Management + Faturação*.  
    ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Dependendo do seu acesso, poderá ser preciso selecionar uma Conta de faturação ou um Perfil de faturação.
1. No menu esquerdo, selecione **Faturas** em **Faturação**.
1. Na grelha da fatura, localize a linha do período de faturação que corresponde à utilização que quer transferir.
1. Selecione o **ícone de transferência** ou as reticências (`...`) à direita.  
  ![Captura de ecrã que mostra a localização de transferência de Utilização + custos](./media/download-azure-daily-usage/download-usage-others.png)  
1. O painel Transferência é apresentado no lado direito. Selecione **Transferir** na secção **Detalhes de Utilização**.  

## <a name="download-usage-for-ea-customers"></a>Transferir a utilização para clientes do EA

Para ver e transferir os dados de utilização como um cliente do EA, tem de ser um Administrador da Empresa, o Proprietário da Conta ou o Administrador do Departamento com a política de visualização de custos ativada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *Cost Management + Faturação*.  
    ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Selecione **Utilização + custos**.
1. Para o mês que pretende transferir, selecione **Transferir**.  
    ![Captura de ecrã que mostra a localização de transferência de Utilização + custos](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-pending-charges"></a>Transferir utilização para custos pendentes

Se tiver um Contrato de Cliente da Microsoft, poderá transferir a utilização do mês até à data atual para o período de faturação atual. Estes custos de utilização que ainda não foram faturados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Procure *Cost Management + Faturação*.
3. Selecione um perfil de faturação. Dependendo do seu acesso, pode ser preciso selecionar primeiro uma conta de faturação.
4. Na área **Descrição Geral**, localize as ligações para transferência por baixo dos custos recentes.
5. Selecione **Transferir utilização e preços**.  
    :::image type="content" source="./media/download-azure-daily-usage/open-usage01.png" alt-text="Captura de ecrã que mostra a transferência a partir da Descrição geral" lightbox="./media/download-azure-daily-usage/open-usage01.png" :::

## <a name="check-your-billing-account-type"></a>Verificar o tipo da conta de faturação
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a fatura e os custos de utilização, veja:

- [Compreender os termos na sua utilização detalhada do Microsoft Azure](understand-usage.md)
- [Understand your bill for Microsoft Azure](review-individual-bill.md) (Compreender a sua fatura do Microsoft Azure)
- [Ver e transferir a sua fatura do Microsoft Azure](download-azure-invoice.md)
- [Ver e transferir os preços do Azure da sua organização](../manage/ea-pricing.md)

Se tiver um Contrato de Cliente da Microsoft, veja:

- [Compreender os termos da utilização detalhada do Azure do Contrato de Cliente da Microsoft](mca-understand-your-usage.md)
- [Compreender os custos na fatura do Contrato de Cliente da Microsoft](review-customer-agreement-bill.md)
- [Ver e transferir a sua fatura do Microsoft Azure](download-azure-invoice.md)
- [Ver e transferir os documentos fiscais do Contrato de Cliente da Microsoft](mca-download-tax-document.md)
- [Ver e transferir os preços do Azure da sua organização](../manage/ea-pricing.md)
