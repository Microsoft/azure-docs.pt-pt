---
title: Tarefas do EA num Contrato de Cliente Microsoft – Azure
description: Saiba como concluir as tarefas de Contrato Enterprise na sua nova conta de faturação.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: banders
ms.openlocfilehash: b8f588df89429bd08b1c74fbc528801fb45377ed
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692312"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Concluir as tarefas de Contrato Enterprise na sua conta de faturação de um Contrato de Cliente da Microsoft

Se a sua organização assinou um Contrato de Cliente da Microsoft para renovar a sua inscrição no Contrato Enterprise, será criada uma nova conta de faturação para o contrato. A faturação na sua nova conta é organizada de forma diferente da do seu Contrato Enterprise. Este artigo descreve como utilizar a nova conta de faturação para realizar tarefas que executou no Contrato Enterprise.

## <a name="billing-organization-in-the-new-account"></a>Organização da faturação na nova conta

O seguinte diagrama descreve como a faturação é organizada na nova conta de faturação.

![Imagem de ea-mca-post-transition-hierarchy](./media/mca-enterprise-operations/mca-post-transition-hierarchy.png)

| Contrato Enterprise   | Contrato de Cliente da Microsoft    |
|------------------------|--------------------------------------------------------|
| Inscrição            | Pode utilizar um perfil de faturação para gerir a faturação da organização, que é semelhante à sua inscrição no Contrato Enterprise. Os administradores com Contrato Enterprise tornam-se proprietários do perfil de faturação. Para obter mais informações sobre perfis de faturação, veja [Compreender os perfis de faturação](../understand/mca-overview.md#billing-profiles).
| Departamento            | Utiliza uma secção de faturação para organizar os custos, em tudo semelhante à dos departamentos na inscrição do Contrato Enterprise. O departamento passa a ser secções de faturação e os administradores do departamento passam a ser proprietários das respetivas secções de faturação. Para obter mais informações sobre as secções de faturação, veja [Compreender as secções de faturação](../understand/mca-overview.md#invoice-sections). |
| Conta               | As contas que foram criadas no Contrato Enterprise não são suportadas na nova conta de faturação. As subscrições da conta pertencem à respetiva secção da fatura do departamento. Os proprietários das contas podem criar e gerir subscrições para as secções da fatura. |

## <a name="changes-for-enterprise-administrators"></a>Alterações para administradores com Contrato Enterprise

As seguintes alterações aplicam-se aos administradores com um Contrato Enterprise que foi renovado para um Contrato de Cliente da Microsoft.

- É criado um perfil de faturação na inscrição. Irá utilizar o perfil de faturação para gerir a faturação da organização, tal como faz na inscrição do Contrato Enterprise. Para obter mais informações sobre perfis de faturação, veja [Compreender os perfis de faturação](../understand/mca-overview.md#billing-profiles).
- É criada uma secção de faturação para cada departamento na inscrição do Contrato Enterprise. Irá utilizar as secções de faturação para gerir os departamentos. Pode criar novas secções de faturação para configurar departamentos adicionais. Para obter mais informações sobre as secções da fatura, veja [Compreender as secções de fature](../understand/mca-overview.md#invoice-sections).
- Irá utilizar a função de criador da subscrição do Azure nas secções de faturação para conceder permissão a outras pessoas para criar uma subscrição do Azure, como as contas que foram criadas na inscrição do Contrato Enterprise.
- Irá utilizar o [portal do Azure](https://portal.azure.com) para gerir a faturação da sua organização, em vez do Azure EA Portal.

Na nova conta de faturação são-lhe dadas as seguintes funções:

**Proprietário do perfil de faturação** – é-lhe atribuída a função de proprietário do perfil de faturação no perfil de faturação que foi criado quando o contrato foi assinado. A função permite-lhe gerir a faturação da organização. Pode ver os custos e as faturas, organizar os custos na fatura, gerir os métodos de pagamento e controlar o acesso à faturação da sua organização.

**Proprietário da secção de faturação** – é-lhe atribuída a função de proprietário da secção de faturação em todas as seções de faturas criadas para os departamentos na inscrição do Contrato Enterprise. A função permite-lhe controlar quem pode criar subscrições do Azure e comprar outros produtos.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Ver os custos e o saldo dos créditos da sua organização

Utiliza o seu perfil de faturação para controlar os custos e o saldo de créditos do Azure da sua organização, tal como a sua inscrição no Contrato Enterprise.

Para saber como ver o saldo de crédito de um perfil de faturação, veja [Controlar o saldo de crédito do Azure do perfil de faturação](mca-check-azure-credits-balance.md).

Para saber como ver custos de um perfil de faturação, veja [Compreender os custos na fatura de Contrato de Cliente da Microsoft](../understand/review-customer-agreement-bill.md).

### <a name="view-charges-for-a-department"></a>Ver os custos de um departamento

É criada uma secção de faturação para cada departamento que tinha no Contrato Enterprise. Pode ver os custos de uma secção de faturação no portal do Azure. Para obter mais informações, veja [Ver transações por secções de faturação](../understand/review-customer-agreement-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Ver os custos de uma conta

As contas que foram criadas na inscrição do Contrato Enterprise não são suportadas na nova conta de faturação. As subscrições da conta pertencem à secção de faturas do departamento. Os proprietários das contas podem criar e gerir subscrições para as secções da fatura.

Para ver o custo agregado das subscrições que pertenciam a uma conta, tem de definir um centro de custos para cada subscrição. Em seguida, pode utilizar o ficheiro CSV de utilização e custos do Azure para filtrar as subscrições do centro de custos.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Transferir o ficheiro CSV de utilização e custos, a folha de preços e documentos fiscais

É gerada uma fatura mensal para cada perfil de faturação na conta de faturação. Para cada fatura, pode transferir o ficheiro CSV de utilização e custos do Azure, a folha de preços e o documento fiscal (se aplicável). Também pode transferir o ficheiro CSV de utilização e custos do Azure referente aos custos do mês atual.

Para saber como transferir o ficheiro CSV de utilização e custos do Azure, veja [Transferir a utilização do seu Contrato de Cliente da Microsoft](../understand/download-azure-daily-usage.md).

Para saber como transferir a folha de preços, veja [Transferir a folha de preços do seu Contrato de Cliente da Microsoft](ea-pricing.md).

Para saber como transferir os documentos fiscais, veja [Transferir os documentos fiscais do seu Contrato de Cliente da Microsoft](../understand/mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Adicionar um administrador da empresa adicional

Conceda aos utilizadores acesso ao perfil de faturação para que possam ver e gerir a faturação da sua organização. Pode utilizar a página **Controlo de Acesso (IAM)** no portal do Azure para conceder acesso.  Para mais informações sobre as funções do perfil de faturação, veja [Funções e tarefas do perfil de faturação](understand-mca-roles.md#billing-profile-roles-and-tasks).

Para saber como conceder acesso ao seu perfil de faturação, veja [Gerir funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Criar um novo departamento

Crie uma secção de faturação para organizar os custos com base nas suas necessidades, tal como os departamentos na inscrição do Contrato Enterprise. Pode criar uma nova secção de faturação no portal do Azure. Para mais informações, veja [Criar secções na sua fatura para organizar os custos](mca-section-invoice.md).

### <a name="create-a-new-account"></a>Criar uma nova conta

Atribua aos utilizadores a função de criador de subscrições do Azure nas secções de faturação para lhes conceder permissão para criar uma subscrição do Azure, como as contas criadas na inscrição do Contrato Enterprise. Para obter mais informações sobre a atribuição de funções, veja [Gerir funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

## <a name="changes-for-department-administrators"></a>Alterações para os administradores de departamentos

As seguintes alterações aplicam-se aos administradores de departamentos com um Contrato Enterprise que foi renovado para um Contrato de Cliente da Microsoft.

- É criada uma secção de faturação para cada departamento na inscrição do Contrato Enterprise. Irá utilizar a secção ou secções de faturação para gerir o departamento ou departamentos. Para obter mais informações sobre as secções da fatura, veja [Compreender as secções de fature](../understand/mca-overview.md#invoice-sections).
- Irá utilizar a função de criador da subscrição do Azure na secção de faturação para conceder permissão a outras pessoas para criar uma subscrição do Azure, como as contas criadas na inscrição do Contrato Enterprise.
- Irá utilizar o portal do Azure para gerir a faturação da sua organização, em vez do Azure EA Portal.

Na nova conta de faturação são-lhe dadas as seguintes funções:

**Proprietário da secção de faturação** – é-lhe atribuída a função de proprietário da secção de faturação na secção de faturação criada para o departamento ou departamentos que tinha no Contrato Enterprise. A função permite-lhe ver e monitorizar os custos e controlar quem pode criar subscrições do Azure e comprar outros produtos para a secção de faturação.

### <a name="view-charges-for-your-department"></a>Ver os custos do seu departamento

Pode ver os custos da secção de faturação criada para o seu departamento na página [Gestão de Custos + Faturação](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) do portal do Azure.

### <a name="add-an-additional-department-administrator"></a>Adicionar um administrador de departamento adicional

É criada uma secção de faturação para cada departamento que tinha no Contrato Enterprise. Pode utilizar a página **Controlo de Acesso (IAM)** no portal do Azure para conceder acesso a outras pessoas para ver e gerir a secção de faturação. Para mais informações sobre as funções da secção de faturação, veja [Funções e tarefas da secção de faturação](understand-mca-roles.md#invoice-section-roles-and-tasks).

Para saber como conceder acesso à sua secção de faturação, veja [Gerir funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Criar uma nova conta no seu departamento

Atribua aos utilizadores a função de criador da subscrição do Azure na secção de faturação criada para o seu departamento. Para obter mais informações sobre a atribuição de funções, veja [Gerir funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="view-charges-for-accounts-in-your-departments"></a>Ver os custos para contas nos seus departamentos

As contas que foram criadas na inscrição do Contrato Enterprise não são suportadas na nova conta de faturação. As subscrições da conta pertencem à secção de faturas do departamento. Os proprietários de contas podem criar e gerir subscrições para as respetivas secções de faturação.

Para ver o custo agregado das subscrições que pertenciam a uma conta no seu departamento, tem de definir um centro de custos para cada subscrição. Em seguida, pode utilizar o ficheiro de utilização e custos do Azure para filtrar as subscrições do centro de custos.

## <a name="changes-for-account-owners"></a>Alterações para proprietários de contas

Os proprietários de contas com o Contrato Enterprise têm permissão para criar subscrições do Azure na nova conta de faturação. As suas subscrições do Azure existentes pertencem à secção de faturação criada para o seu departamento. Se a sua conta não pertencer a um departamento, as suas subscrições pertencem a uma secção de faturação denominada Secção de faturação predefinida.

Para criar subscrições do Azure adicionais, é-lhe atribuída a seguinte função na nova conta de faturação.

**Criador de subscrições do Azure** – é-lhe atribuída uma função de criador de subscrições do Azure na secção de fatura criada para o seu departamento com o Contrato Enterprise. Se a sua conta não pertencer a um departamento, obtém uma função de criador de subscrições do Azure numa secção denominada Secção de faturação predefinida. A função permite-lhe criar subscrições do Azure para a secção de faturação.

### <a name="create-an-azure-subscription"></a>Criar uma subscrição do Azure

Pode criar subscrições do Azure para a sua secção de faturação no portal do Azure. Para obter mais informações, veja [Criar uma subscrição do Azure adicional para o Contrato de Cliente da Microsoft](create-subscription.md)

### <a name="view-charges-for-your-account"></a>Ver os custos da sua conta

Para ver os custos das subscrições que pertenciam a uma conta, aceda à [página de subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure. A página de subscrições mostra os custos de todas as suas subscrições.

### <a name="view-charges-for-a-subscription"></a>Ver os custos de uma subscrição

Pode ver os custos de uma subscrição na [página de subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou na análise de custos do Azure. Para obter mais informações sobre a análise de custos do Azure, veja [Explorar e analisar os custos com a Análise de custos](../costs/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- [Compreender a conta de faturação de um Contrato de Cliente da Microsoft](../understand/mca-overview.md)
- [Compreender a sua fatura](../understand/review-individual-bill.md)
- [Compreender a sua fatura](../understand/understand-invoice.md)
- [Obter a propriedade da faturação das subscrições do Azure de outros utilizadores](mca-request-billing-ownership.md)
