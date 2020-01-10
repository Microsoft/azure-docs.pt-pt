---
title: Ver as contas de faturação no portal do Azure | Microsoft Docs
description: Saiba como ver as suas contas de faturação no portal do Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 952cf89a4e1f6c5ed82a817b81d32b61ab673fe4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449184"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Âmbitos e contas de faturação no portal do Azure

Ao inscrever-se para utilizar o Azure, é criada uma conta de faturação. Utiliza a conta de faturação para gerir faturas, pagamentos e controlar custos. Pode ter acesso a múltiplas contas de faturação. Por exemplo, talvez se tenha inscrito no Azure com o objetivo de desenvolver projetos pessoais. Também poderá ter acesso através do Contrato Enterprise ou do Contrato de Cliente da Microsoft da sua organização. Para cada um desses cenários, teria uma conta de faturação separada.

O portal do Azure suporta os seguintes tipos de conta de faturação:

- **Programa Microsoft Online Services**: uma conta de faturação para um Programa Microsoft Online Services é criada ao inscrever-se no Azure através do site do Azure. Por exemplo, quando se inscreve numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrato Enterprise**: uma conta de faturação para um Contrato Enterprise é criada quando a sua organização assina um [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) para utilizar o Azure.

- **Contrato de Cliente da Microsoft**: uma conta de faturação para um Contrato de Cliente Microsoft é criada quando a sua organização trabalha com um representante da Microsoft para assinar um Contrato de Cliente da Microsoft. Alguns clientes em determinadas regiões, que se inscrevem através do site do Azure numa [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), também podem ter uma conta de faturação para um Contrato de Cliente Microsoft. Para obter mais informações, veja [Introdução à conta de faturação para o Contrato de Cliente da Microsoft](billing-mca-overview.md).

- **Contrato de Parceiro da Microsoft**: é criada uma conta de faturação de um Contrato de Parceiro da Microsoft para os parceiros Fornecedores de Soluções Cloud (CSP) para gestão dos clientes na nova experiência comercial. Os parceiros precisam de ter, pelo menos, um cliente com um [plano do Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) para gerir a conta de faturação no portal do Azure. Para obter mais informações, veja [Introdução à conta de faturação do Contrato de Parceiro da Microsoft](mpa-overview.md).

Para determinar o tipo da conta de faturação, veja [Verificar o tipo da conta de faturação](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Âmbitos de contas de faturação
Um âmbito é um nó numa conta de faturação que utiliza para ver e gerir a faturação. É onde gere os dados de faturação, os pagamentos, as faturas e onde realiza a gestão global da conta. 

### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

 ![Captura de ecrã que mostra a hierarquia do MOSP](./media/billing-view-all-accounts/mosp-hierarchy.png)

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação     | Representa um contrato que um cliente aceita para utilizar o Azure. Contém uma ou mais subscrições.  |
|Subscrição     |  Representa um agrupamento de recursos do Azure. A fatura é gerada neste âmbito. Outras informações de faturação, como métodos de pagamento e endereço de utilização, estão associadas a este âmbito.|

### <a name="enterprise-agreement"></a>Contrato Enterprise

![Captura de ecrã que mostra a hierarquia do EA](./media/billing-view-all-accounts/ea-hierarchy.png)

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação    | Representa uma inscrição do Contrato Enterprise. Contém um ou mais departamentos e contas. A fatura é gerada neste âmbito. |
|Departamento     |  Agrupamento de contas opcional para segmentar custos em agrupamentos lógicos e definir o orçamento.     |
|Conta     |  Representa um único proprietário da conta. Os proprietários de conta têm permissões para criar e gerir as subscrições do Azure que são faturadas na inscrição. |

### <a name="microsoft-customer-agreement"></a>Contrato de Cliente da Microsoft

![Captura de ecrã que mostra a hierarquia do MCA](./media/billing-view-all-accounts/mca-hierarchy.png)

|Âmbito  |Tarefas  |
|---------|---------|
|Conta de faturação     |   Representa um contrato que um cliente aceita para utilizar os produtos e serviços Microsoft. Contém um ou mais perfis de faturação. |
|Perfil de faturação     |   Representa uma fatura e as informações de faturação relacionadas, como métodos de pagamento e endereço de faturação. Contém uma ou mais secções de fatura. |
|Secção de fatura     |   Representa um agrupamento de custos numa fatura. As subscrições do Azure e outras compras, como produtos de origem da Aplicação e do Azure Marketplace, estão associadas a este âmbito.    |

### <a name="microsoft-partner-agreement"></a>Contrato de Parceiro da Microsoft

![Captura de ecrã que mostra a hierarquia do MPA](./media/billing-view-all-accounts/mpa-hierarchy.png)

|Âmbito  |Tarefas  |
|---------|---------|
|Conta de faturação     |   Representa um contrato de parceiro para gerir os produtos e serviços Microsoft dos clientes na nova experiência comercial. Contém um ou mais perfis de faturação e clientes.   |
|Perfil de faturação     |   Representa uma fatura numa moeda.     |
|Cliente    |   Representa um cliente de um parceiro Fornecedor de Soluções Cloud (CSP).  As subscrições do Azure e outras compras, como produtos de origem da Aplicação e do Azure Marketplace, estão associadas a este âmbito.  |
|Revendedor    |   O revendedor que fornece serviços a um cliente. Este é um campo opcional de uma subscrição e aplica-se apenas a Fornecedores indiretos no modelo de duas camadas do CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Alterar o âmbito de faturação no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Na página da descrição geral, selecione **Mudar âmbito**.

   ![Captura de ecrã que mostra os âmbitos de faturação](./media/billing-view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Não verá a opção Mudar âmbito se só tiver acesso a um âmbito.

4. Selecione um âmbito para ver os detalhes.

   ![Captura de ecrã que mostra os âmbitos de faturação](./media/billing-view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Verificar o tipo de conta
[!INCLUDE [billing-check-mca](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- Saiba como começar a [analisar os seus custos](../cost-management/quick-acm-cost-analysis.md).