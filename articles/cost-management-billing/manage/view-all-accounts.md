---
title: Ver as suas contas de faturação no portal do Azure
description: Saiba como ver as contas de faturação no portal do Azure. Veja as informações de âmbito relativas ao Contrato Enterprise, ao Contrato de Cliente da Microsoft e ao Contrato de Parceiro da Microsoft.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: banders
ms.openlocfilehash: a9e3297ad055157d552424ecc48db6801f43b220
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929960"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Âmbitos e contas de faturação no portal do Azure

Ao inscrever-se para utilizar o Azure, é criada uma conta de faturação. Utiliza a conta de faturação para gerir faturas, pagamentos e controlar custos. Pode ter acesso a múltiplas contas de faturação. Por exemplo, talvez se tenha inscrito no Azure com o objetivo de desenvolver projetos pessoais. Também poderá ter acesso através do Contrato Enterprise ou do Contrato de Cliente da Microsoft da sua organização. Para cada um desses cenários, teria uma conta de faturação separada.

O portal do Azure suporta os seguintes tipos de conta de faturação:

- **Programa Microsoft Online Services**: uma conta de faturação para um Programa Microsoft Online Services é criada ao inscrever-se no Azure através do site do Azure. Por exemplo, quando se inscreve numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrato Enterprise**: uma conta de faturação para um Contrato Enterprise é criada quando a sua organização assina um [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) para utilizar o Azure. Pode ter um máximo de 2000 subscrições num Contrato Enterprise.

- **Contrato de Cliente da Microsoft**: uma conta de faturação para um Contrato de Cliente Microsoft é criada quando a sua organização trabalha com um representante da Microsoft para assinar um Contrato de Cliente da Microsoft. Alguns clientes em determinadas regiões, que se inscrevem através do site do Azure numa [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), também podem ter uma conta de faturação para um Contrato de Cliente Microsoft. Pode ter um máximo de 20 subscrições num Contrato de Cliente Microsoft para um indivíduo. Um Contrato de Cliente Microsoft para uma empresa não tem um limite para o número de subscrições. Para obter mais informações, veja [Introdução à conta de faturação para o Contrato de Cliente da Microsoft](../understand/mca-overview.md).

- **Contrato de Parceiro da Microsoft**: é criada uma conta de faturação de um Contrato de Parceiro da Microsoft para os parceiros Fornecedores de Soluções Cloud (CSP) para gestão dos clientes na nova experiência comercial. Os parceiros precisam de ter, pelo menos, um cliente com um [plano do Azure](/partner-center/purchase-azure-plan) para gerir a conta de faturação no portal do Azure. Para obter mais informações, veja [Introdução à conta de faturação do Contrato de Parceiro da Microsoft](../understand/mpa-overview.md).

Para determinar o tipo da conta de faturação, veja [Verificar o tipo da conta de faturação](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Âmbitos de contas de faturação
Um âmbito é um nó numa conta de faturação que utiliza para ver e gerir a faturação. É onde gere os dados de faturação, os pagamentos, as faturas e onde realiza a gestão global da conta.

Se não tiver acesso para ver ou gerir contas de faturação, o mais provável é que não tenha permissão para aceder. Pode pedir ao seu administrador da conta de faturação para lhe conceder acesso. Para obter mais informações, veja os seguintes artigos:

- [Acesso ao Programa Microsoft Online Services](manage-billing-access.md)
- [Acesso ao Contrato de Cliente Microsoft](understand-mca-roles.md)
- [Acesso ao Contrato Enterprise](understand-ea-roles.md)

### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

![Captura de ecrã que mostra a hierarquia do MOSP](./media/view-all-accounts/mosp-hierarchy.png)

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação     | Representa um contrato que um cliente aceita para utilizar o Azure. Contém uma ou mais subscrições.  |
|Subscrição     |  Representa um agrupamento de recursos do Azure. A fatura é gerada neste âmbito. Outras informações de faturação, como métodos de pagamento e endereço de utilização, estão associadas a este âmbito.|

### <a name="enterprise-agreement"></a>Contrato Enterprise

![Captura de ecrã que mostra a hierarquia do EA](./media/view-all-accounts/ea-hierarchy.png)

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação    | Representa uma inscrição do Contrato Enterprise. Contém um ou mais departamentos e contas. A fatura é gerada neste âmbito. |
|Departamento     |  Agrupamento de contas opcional para segmentar custos em agrupamentos lógicos e definir o orçamento.     |
|Conta     |  Representa um único proprietário da conta. Os proprietários de conta têm permissões para criar e gerir as subscrições do Azure que são faturadas na inscrição. |

### <a name="microsoft-customer-agreement"></a>Contrato de Cliente da Microsoft

![Captura de ecrã que mostra a hierarquia do MCA](./media/view-all-accounts/mca-hierarchy.png)

|Âmbito  |Tarefas  |
|---------|---------|
|Conta de faturação     |   Representa um contrato que um cliente aceita para utilizar os produtos e serviços Microsoft. Contém um ou mais perfis de faturação. |
|Perfil de faturação     |   Representa uma fatura e as informações de faturação relacionadas, como métodos de pagamento e endereço de faturação. Contém uma ou mais secções de fatura. |
|Secção de fatura     |   Representa um agrupamento de custos numa fatura. As subscrições do Azure e outras compras, como produtos de origem da Aplicação e do Azure Marketplace, estão associadas a este âmbito.    |

### <a name="microsoft-partner-agreement"></a>Contrato de Parceiro da Microsoft

![Captura de ecrã que mostra a hierarquia do MPA](./media/view-all-accounts/mpa-hierarchy.png)

|Âmbito  |Tarefas  |
|---------|---------|
|Conta de faturação     |   Representa um contrato de parceiro para gerir os produtos e serviços Microsoft dos clientes na nova experiência comercial. Contém um ou mais perfis de faturação e clientes.   |
|Perfil de faturação     |   Representa uma fatura numa moeda.     |
|Cliente    |   Representa um cliente de um parceiro Fornecedor de Soluções Cloud (CSP).  As subscrições do Azure e outras compras, como produtos de origem da Aplicação e do Azure Marketplace, estão associadas a este âmbito.  |
|Revendedor    |   O revendedor que fornece serviços a um cliente. É um campo opcional de uma subscrição e aplica-se apenas a Fornecedores indiretos no modelo de duas camadas do CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Alterar o âmbito de faturação no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa da opção Cost Management + Faturação no portal do Azure.](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Na página da descrição geral, selecione **Mudar âmbito**.

   ![Captura de ecrã que mostra os âmbitos de faturação.](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Não verá a opção Mudar âmbito se só tiver acesso a um âmbito.

4. Selecione um âmbito para ver os detalhes.

   ![Captura de ecrã que mostra os âmbitos de faturação, que pode selecionar para ver detalhes.](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Verificar o tipo de conta
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- Saiba como começar a [analisar os seus custos](../costs/quick-acm-cost-analysis.md).