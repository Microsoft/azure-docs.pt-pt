---
title: Concluir tarefas de contrato Enterprise no contrato de cliente da Microsoft - Azure | Documentos da Microsoft
description: Saiba como executar tarefas de contrato Enterprise na sua nova conta de faturação.
services: ''
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
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341402"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Concluir tarefas de contrato Enterprise em sua conta de cobrança para um contrato de cliente da Microsoft

Se sua organização que assinou um contrato de cliente da Microsoft para renovar a sua inscrição de Enterprise Agreement, é criada uma nova conta de faturação para o contrato. A faturação na sua nova conta é organizada de forma diferente do que o seu contrato Enterprise. Este artigo descreve como pode utilizar a nova conta de faturação para realizar tarefas que realizou no seu contrato Enterprise.

## <a name="how-billing-is-organized-in-the-new-account"></a>Como a faturação é organizada na nova conta

O diagrama a seguir descreve como a faturação é organizada na sua nova conta de faturação.

![Imagem dos ea-mca-post-transição-hierarquia](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Enterprise Agreement   | Contrato de Cliente Microsoft    |
|------------------------|--------------------------------------------------------|
| Inscrição            | Utilize um perfil de faturação para gerenciar a cobrança para sua organização, semelhante à sua inscrição de Enterprise Agreement. Administradores da empresa tornam-se os proprietários do perfil de faturação. Para saber mais sobre os perfis de faturas, veja [compreender os perfis de faturas](billing-mca-overview.md#understand-billing-profiles).
| Departamento            | Utilize uma seção de nota fiscal para organizar os custos, semelhantes a departamentos em sua inscrição de Enterprise Agreement. Departamento torna-se em secções de nota fiscal e os administradores de departamento tornam-se proprietários das seções de nota fiscal respectivos. Para saber mais sobre as secções de nota fiscal, veja [secções de nota fiscal de compreender](billing-mca-overview.md#understand-invoice-sections). |
| Conta               | As contas que foram criadas no seu contrato Enterprise não são suportadas na nova conta de faturação. Subscrições da conta pertencem a seção de nota fiscal respectivos para os seus departamentos. Proprietários da conta podem criar e gerir subscrições para suas seções de nota fiscal. |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>Alterações para os administradores empresariais na nova conta de faturação

As seguintes alterações se aplica aos administradores da empresa num contrato Enterprise que foi renovado para um contrato de cliente da Microsoft.

- É criado um perfil de faturação da sua inscrição. Usará o perfil de faturação para gerenciar a cobrança para sua organização, como a inscrição de Enterprise Agreement. Para saber mais sobre os perfis de faturas [compreender os perfis de faturas](billing-mca-overview.md#understand-billing-profiles).
- Uma seção de nota fiscal é criada para cada departamento na sua inscrição de Enterprise Agreement. Usará as secções de nota fiscal para gerir os seus departamentos. Pode criar duas novas secções da nota fiscal para configurar a departamentos adicionais. Para saber mais sobre as secções de nota fiscal, veja [compreender as secções de nota fiscal](billing-mca-overview.md#understand-invoice-sections).
- Vai utilizar a função de criador de subscrição do Azure no secções de nota fiscal para atribuir a outras pessoas permissão para criar subscrição do Azure, como as contas que foram criados no Registro do Enterprise Agreement.
- Irá utilizar o [portal do Azure](https://portal.azure.com) para gerenciar a cobrança para sua organização, em vez do portal EA do Azure.

Tem as seguintes funções na nova conta de faturação:

**Proprietário de perfil de faturação** -lhe tenha sido atribuída a função de proprietário de perfil de faturação no perfil de faturação que foi criada quando o contrato foi assinado. A função permite-lhe gerir a faturação para a sua organização. Pode ver os custos e notas fiscais, organizar os custos da nota fiscal, gerir métodos de pagamento e controlar o acesso a faturação da sua organização.

**Proprietário da secção de nota fiscal** -lhe tenha sido atribuída a função de proprietário da secção de notas fiscais em todas as seções de nota fiscal que são criados para os departamentos na sua inscrição de Enterprise Agreement. A função permite controlar quem pode criar subscrições do Azure e outros produtos de compra.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Ver os custos e créditos de saldo para a sua organização

Utilize o seu perfil de faturação para controlar os custos e o saldo de créditos do Azure para a sua organização semelhante para a inscrição do seu contrato Enterprise.

Para saber como exibir o saldo do crédito para um perfil de faturação, veja [acompanhar o saldo do crédito do Azure para o seu perfil de faturação](billing-mca-check-azure-credits-balance.md).

Para saber como ver os custos de um perfil de faturação, veja [compreender as cobranças na fatura de seu contrato de cliente de Microsoft](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Custos de vista de um departamento

Uma seção de nota fiscal é criada para cada departamento que tiver no seu contrato Enterprise. Pode ver as cobranças de uma seção de nota fiscal no portal do Azure. Para obter mais informações, consulte [exibir transações de secções de nota fiscal](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Custos de exibição de uma conta

As contas que foram criadas na sua inscrição de Enterprise Agreement não são suportadas na nova conta de faturação. Subscrições da conta pertencem a seção de nota fiscal respectivos para os seus departamentos. Proprietários da conta podem criar e gerir subscrições para suas seções de nota fiscal.

Para ver o custo agregado para as subscrições pertencentes a uma conta, tem de definir um centro de custos para cada subscrição. Em seguida, pode utilizar o ficheiro de csv de utilização e os encargos do Azure para filtrar as subscrições, o Centro de custos.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Csv de utilização e custos de transferência, uma folha de preços e documentos de imposto

Uma nota fiscal mensal é gerada para cada perfil de faturação na sua conta de cobrança. Para cada nota fiscal, pode transferir o ficheiro de csv de utilização e os encargos do Azure, a folha de preços e o documento de imposto sobre (se aplicável). Também pode transferir ficheiros do Azure de csv de utilização e a cobrança de encargos do mês atual.

Para saber como transferir ficheiros de csv de utilização e os encargos do Azure, veja [transferir utilização para o contrato do cliente Microsoft](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Para saber como transferir a folha de preços, veja [baixar os preços para o contrato do cliente Microsoft](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement).

Para saber como transferir documentos de imposto, veja [ver os documentos de imposto para o contrato do cliente Microsoft](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Adicionar um administrador empresarial adicionais

Conceder acesso de utilizadores para o perfil de faturação que lhes ver e gerir a faturação para a sua organização. Pode utilizar o **controlo de acesso (IAM)** página no portal do Azure para dar acesso.  Para saber mais sobre funções de perfis de faturação, veja [funções de perfis e tarefas de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Para saber como fornecer, aceder ao seu perfil de faturação, veja [gerir funções de faturas no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Criar um novo departamento

Crie uma seção de nota fiscal para organizar os custos com base nas suas necessidades, como departamentos em sua inscrição de Enterprise Agreement. Pode criar uma nova seção de nota fiscal no portal do Azure. Para obter mais informações, consulte [criar secções na sua fatura para organizar os seus custos](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Criar uma conta nova

Atribua utilizadores a função de criador de subscrição do Azure em secções de nota fiscal para lhes dar permissão para criar subscrição do Azure, como as contas que são criados no Registro do Enterprise Agreement. Para obter mais informações, consulte [conceder a outros utilizadores permissão para criar subscrições do Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>Alterações para os administradores de departamento na nova conta de faturação

As alterações seguintes aplicam-se aos administradores de departamento num contrato Enterprise que foi renovado para um contrato de cliente da Microsoft.

- Uma seção de nota fiscal é criada para cada departamento na sua inscrição de Enterprise Agreement. Usará as secções de nota fiscal para gerir a sua department(s). Para saber mais sobre as secções de nota fiscal, veja [compreender as secções de nota fiscal](billing-mca-overview.md#understand-invoice-sections).
- Vai utilizar a função de criador de subscrição do Azure na seção de nota fiscal para atribuir a outras pessoas permissão para criar subscrição do Azure, como as contas que são criados no Registro do Enterprise Agreement.
- Irá utilizar o portal do Azure para gerenciar a cobrança para sua organização, em vez do portal EA do Azure.

Tem as seguintes funções na nova conta de faturação:

**Proprietário da secção de nota fiscal** -lhe tenha sido atribuída a função de proprietário da secção de nota fiscal na secção de notas fiscais que é criada para department(s) tinha no contrato Enterprise. A função permite-lhe ver e controlar custos e controle quem pode criar subscrições do Azure e comprar outros produtos para a secção de nota fiscal.

### <a name="view-charges-for-your-department"></a>Custos de exibição para seu departamento

Pode ver os custos para a secção de notas fiscais que é criado para os seus departamentos, no portal do Azure [gestão de custos + faturação página](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

### <a name="add-an-additional-department-administrator"></a>Adicionar um administrador do departamento adicionais

Uma seção de nota fiscal é criada para cada departamento que tiver no seu contrato Enterprise. Pode utilizar o **Control(IAM) acesso** página no portal do Azure para atribuir a outras pessoas acesso para ver e gerir a secção de nota fiscal. Para saber mais sobre as funções da secção de nota fiscal, veja [secção funções e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Para saber como fornecer, aceder à seção de nota fiscal, veja [gerir funções de faturas no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Criar uma nova conta do seu departamento

Atribua utilizadores a função de criador de subscrição do Azure na seção de nota fiscal é criada para o seu departamento. Para obter mais informações, consulte [conceder a outros utilizadores permissão para criar subscrições do Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Custos de vista de contas no seus departamentos

As contas que foram criadas na sua inscrição de Enterprise Agreement não são suportadas na nova conta de faturação. Subscrições da conta pertencem a seção de nota fiscal respectivos para os seus departamentos. Proprietários da conta podem criar e gerir subscrições para suas seções de nota fiscal.

Para ver o custo agregado para as subscrições pertencentes a uma conta do seu departamento, tem de definir um centro de custos para cada subscrição. Em seguida, pode utilizar o ficheiro de utilização e os encargos do Azure para filtrar as subscrições, o Centro de custos.

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>Alterações para proprietários de conta na nova conta de faturação

Os proprietários de conta do Enterprise Agreement obtém permissão para criar subscrições do Azure a nova conta de faturação. Pertencem suas subscrições do Azure existentes para a secção de nota fiscal é criada para o seu departamento. Se a sua conta não pertence a um departamento, as suas subscrições pertencem a uma secção de nota fiscal com o nome predefinido secção de nota fiscal.

Para criar subscrições do Azure adicionais, é-lhe dada a função seguinte sobre a nova conta de faturação.

**Criador de subscrição do Azure** -lhe tenha sido atribuída a função de criador de subscrição do azure na secção de notas fiscais que é criada para o departamento de contrato Enterprise. Se a sua conta não pertence a um departamento, obtém a função de criador de subscrição do Azure numa seção denominada a seção de nota fiscal de Default. A função permite-lhe criar subscrições do Azure para a secção de nota fiscal.

### <a name="create-an-azure-subscription"></a>Criar uma subscrição do Azure

Pode criar subscrições do Azure para a seção de nota fiscal no portal do Azure. Para obter mais informações, consulte [criar uma subscrição do Azure adicional para o contrato de cliente da Microsoft](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Vista de custos para a sua conta

Para ver as cobranças das subscrições pertencentes a uma conta do, vá para o [página subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure. A página de subscrições apresenta custos para todos os sua subscrição.

### <a name="view-charges-for-a-subscription"></a>Custos de exibição de uma subscrição

Pode ver custos para uma subscrição ou sobre o [página subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou do Azure, análise de custo. Para obter mais informações sobre a análise de custos do Azure, consulte [explorar e analisar os custos com a análise de custo](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes

- [Compreender a conta de cobrança para um contrato de cliente da Microsoft](billing-mca-overview.md)
- [Compreender a sua fatura](billing-understand-your-bill.md)
- [Compreender a sua fatura](billing-understand-your-invoice.md)
- [Obter a propriedade das subscrições do Azure com outros usuários de faturação](billing-mca-request-billing-ownership.md)
