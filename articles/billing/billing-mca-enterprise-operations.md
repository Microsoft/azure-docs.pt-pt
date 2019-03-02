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
ms.openlocfilehash: 5980b8b701695d002d58b6b5239e6fa04c02fb2f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249102"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Concluir tarefas de contrato Enterprise em sua conta de cobrança para um contrato de cliente da Microsoft

Se sua organização que assinou um contrato de cliente da Microsoft para renovar a sua inscrição de Enterprise Agreement, em seguida, tem de configurar a conta de cobrança novo criada para o contrato. Sua nova conta de cobrança fornece a que avançado de capacidades de gestão de faturação e de custo através de uma nova experiência de gerenciamento simplificado e unificada. Para obter mais informações, consulte [configurar a sua conta de cobrança para um contrato de cliente da Microsoft](billing-mca-setup-account.md).

Depois da configuração estiver concluída, utilize este artigo para compreender como executar tarefas executadas no seu registro de contrato Enterprise.

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-your-access-to-a-microsoft-customer-agreement).

## <a name="mapping-of-enterprise-agreements-billing-to-the-new-billing-account"></a>Mapeamento de faturação do contrato Enterprise para a nova conta de faturação

A faturação na sua nova conta é organizada de forma diferente do que a sua inscrição de Enterprise Agreement. A tabela seguinte descreve o mapeamento de faturação da sua inscrição do contrato Enterprise para a sua nova conta de cobrança. Para saber mais sobre a sua nova conta de faturação, veja [introdução à sua conta de cobrança para o contrato de cliente da Microsoft](billing-mca-overview.md).

| Enterprise Agreement   | Contrato de Cliente Microsoft    |
|------------------------|--------------------------------------------------------|
| Inscrição            | Utilize um perfil de faturação para gerenciar a cobrança para sua organização, semelhante à sua inscrição de Enterprise Agreement. Administradores da empresa tornam-se os proprietários do perfil de faturação. Para saber mais sobre os perfis de faturas, veja [compreender os perfis de faturas](billing-mca-overview.md#understand-billing-profiles).
| Departamento            | Utilize uma seção de nota fiscal para organizar os custos, semelhantes a departamentos em sua inscrição de Enterprise Agreement. Departamento torna-se em secções de nota fiscal e os administradores de departamento tornam-se proprietários das seções de nota fiscal respectivos. Para saber mais sobre as secções de nota fiscal, veja [secções de nota fiscal de compreender](billing-mca-overview.md#understand-invoice-sections). |
| Conta               | As contas que foram criadas no seu contrato Enterprise não são suportadas na nova conta de faturação. Subscrições da conta pertencem a seção de nota fiscal respectivos para os seus departamentos. Proprietários da conta podem criar e gerir subscrições para suas seções de nota fiscal. |

## <a name="tasks-performed-by-an-enterprise-administrator"></a>Tarefas realizadas por um administrador empresarial

Se for administrador da empresa num contrato Enterprise que foi renovado para um contrato de cliente da Microsoft, são atribuídas as seguintes funções na conta de faturação do novo criado para o contrato:

**Proprietário de perfil de faturação** -lhe tenha sido atribuída a função de proprietário de perfil de faturação no perfil de faturação que foi criada quando o contrato foi assinado. A função permite-lhe gerir a faturação para a sua organização. Pode ver os custos e notas fiscais, organizar os custos da nota fiscal, gerir métodos de pagamento e controlar o acesso a faturação da sua organização.

**Proprietário da secção de nota fiscal** -lhe tenha sido atribuída a função de proprietário da secção de notas fiscais em todas as seções de nota fiscal que são criados para os departamentos na sua inscrição de Enterprise Agreement. A função permite controlar quem pode criar subscrições do Azure e outros produtos de compra.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Ver os custos e créditos de saldo para a sua organização

Utilize o seu perfil de faturação para controlar os custos e o saldo de créditos do Azure para a sua organização semelhante para a inscrição do seu contrato Enterprise.

Para saber como exibir o saldo do crédito para um perfil de faturação, veja [saldo de crédito do Azure de controle para o seu perfil de faturação](billing-mca-check-azure-credits-balance.md).

Para saber como ver os custos de um perfil de faturação, veja [compreender as cobranças na fatura de seu contrato de cliente de Microsoft](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Custos de vista de um departamento

Uma seção de nota fiscal é criada para cada departamento que tiver no seu contrato Enterprise. Pode ver as cobranças de uma seção de nota fiscal no portal do Azure. Para obter mais informações, consulte [exibir transações de secções de nota fiscal](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Custos de exibição de uma conta

As contas que foram criadas na sua inscrição de Enterprise Agreement não são suportadas na nova conta de faturação. Subscrições da conta pertencem a seção de nota fiscal respectivos para os seus departamentos. Proprietários da conta podem criar e gerir subscrições para suas seções de nota fiscal.

Para ver o custo agregado para as subscrições pertencentes a uma conta, tem de definir um centro de custos para cada subscrição. Em seguida, pode utilizar o ficheiro de csv de utilização e os encargos do Azure para filtrar as subscrições, o Centro de custos.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Csv de utilização e custos de transferência, uma folha de preços e documentos de imposto

Uma nota fiscal mensal é gerada para cada perfil de faturação na sua conta de cobrança. Para cada nota fiscal, pode transferir o ficheiro de csv de utilização e os encargos do Azure, a folha de preços e o documento de imposto sobre (se aplicável). Também pode transferir ficheiros do Azure de csv de utilização e a cobrança de encargos do mês atual.

Para saber como transferir ficheiros de csv de utilização e os encargos do Azure, veja [transferir ou visualizar a nota fiscal e diário de dados de utilização de faturação do Azure](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

Para saber como transferir documentos de folha e imposto sobre preços, consulte [documentos de imposto de Download para a sua fatura](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

### <a name="add-an-additional-enterprise-administrator"></a>Adicionar um administrador empresarial adicionais

Utilize o **Control(IAM) acesso** página no portal do Azure para atribuir a outras pessoas acesso para ver e gerir o perfil de faturação. Para saber mais sobre funções de perfis de faturação, veja [funções de perfis e tarefas de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Para saber como fornecer, aceder ao seu perfil de faturação, veja [gerir funções de faturas no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Criar um novo departamento

Na sua nova conta de faturação, utilize uma seção de nota fiscal para organizar os custos com base nas suas necessidades, semelhantes a departamentos em sua inscrição de Enterprise Agreement. Pode criar uma nova seção de nota fiscal no portal do Azure para configurar a Azure para um novo departamento. Para obter mais informações, consulte [criar secções na sua fatura para organizar os seus custos](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Criar uma conta nova

O contexto de faturação da conta no seu contrato Enterprise não é suportado na nova conta de faturação. No entanto, pode fornecer a função de criador de subscrição do Azure para os utilizadores na sua organização para lhe permitir criar subscrições do Azure. Para obter mais informações, consulte [conceder a outros utilizadores permissão para criar subscrições do Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="tasks-performed-by-a-department-administrator"></a>Tarefas realizadas por um administrador do departamento

Se for um administrador do departamento num contrato Enterprise que renovada para um contrato de cliente da Microsoft, são atribuídas as funções seguintes sobre a nova conta de faturação:

**Proprietário da secção de nota fiscal** -lhe tenha sido atribuída a função de proprietário da secção de nota fiscal na secção de notas fiscais que é criada para os departamentos que tinha no contrato Enterprise. A função permite-lhe ver e controlar custos e controle quem pode criar subscrições do Azure e comprar outros produtos para a secção de nota fiscal.

Se for um administrador do departamento em vários departamentos no Enterprise Agreement, é atribuída a função de proprietário de secção de notas fiscais em todas as seções de nota fiscal criado para os departamentos.

### <a name="view-charges-for-your-department"></a>Custos de exibição para seu departamento

Uma seção de nota fiscal é criada para cada departamento que tiver no seu contrato Enterprise. A secção de nota fiscal tem o mesmo nome que o departamento do contrato Enterprise. Pode ver as cobranças da secção de nota fiscal no portal do Azure. <!-- Todo Add a link -->

### <a name="add-an-additional-department-administrator"></a>Adicionar um administrador do departamento adicionais

Uma seção de nota fiscal é criada para cada departamento que tiver no seu contrato Enterprise. Pode utilizar o **Control(IAM) acesso** página no portal do Azure para atribuir a outras pessoas acesso para ver e gerir a secção de nota fiscal. Para saber mais sobre as funções da secção de nota fiscal, veja [funções de perfis e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Para saber como fornecer, aceder à seção de nota fiscal, veja [gerir funções de faturas no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Criar uma nova conta do seu departamento

O contexto de faturação da conta no seu contrato Enterprise não é suportado na nova conta de faturação. No entanto, pode fornecer a função de criador de subscrição do Azure para os utilizadores na sua organização para lhe permitir criar subscrições do Azure. Para obter mais informações, consulte [conceder a outros utilizadores permissão para criar subscrições do Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Custos de vista de contas no seus departamentos

O contexto de faturação da conta no seu contrato Enterprise não é suportado na nova conta de faturação. Subscrições da conta pertencerem a seção de nota fiscal criado para seu departamento. Proprietários da conta podem criar e gerir subscrições para as secções de nota fiscal.

Para ver o custo agregado para as subscrições pertencentes a uma conta do seu departamento, tem de definir um centro de custos para cada subscrição. Em seguida, pode utilizar o ficheiro de utilização e os encargos do Azure para filtrar as subscrições, o Centro de custos. <!-- Todo - can they go to cost analysis -->

## <a name="tasks-performed-by-an-account-owner"></a>Tarefas realizadas por um proprietário da conta

Se for um proprietário da conta num contrato Enterprise que foi renovado para um contrato de cliente da Microsoft, é-lhe atribuída a função seguinte sobre a nova conta de faturação criada para o contrato:

**Criador de subscrição do Azure** -lhe tenha sido atribuída a função de criador de subscrição do azure na secção de notas fiscais que é criada para o departamento de contrato Enterprise. Se a sua conta não pertence a um departamento, as suas subscrições irão pertencer a seção de nota fiscal com o nome predefinido secção de nota fiscal e obterá o criador de subscrição do Azure na seção. A função permite-lhe criar subscrições do Azure para a secção de nota fiscal.

### <a name="view-charges-for-your-account"></a>Vista de custos para a sua conta

O contexto de faturação da conta no seu contrato Enterprise não é suportado na nova conta de faturação. Subscrições da conta pertencem a seção de nota fiscal criada para o departamento da sua conta.

Para ver as cobranças das subscrições pertencentes a uma conta do, vá para o **página subscrições** no portal do Azure. A página de subscrições apresenta custos para todos os sua subscrição. <!-- Todo - can they go to cost analysis -->

### <a name="view-charges-for-a-subscription"></a>Custos de exibição de uma subscrição

Pode utilizar a análise de custos do Azure para ver os custos para uma subscrição. Para obter mais informações, consulte [explorar e analisar os custos com a análise de custo](../cost-management/quick-acm-cost-analysis.md).

### <a name="create-an-azure-subscription"></a>Criar uma subscrição do Azure

Pode criar subscrições do Azure para a seção de nota fiscal no portal do Azure. Para obter mais informações, consulte [criar uma subscrição do Azure adicional para o contrato de cliente da Microsoft](billing-mca-create-subscription.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes

- [Compreender a conta de cobrança para um contrato de cliente da Microsoft](billing-mca-overview.md)
- [Compreender a sua fatura](billing-understand-your-bill.md)
- [Compreender a sua fatura](billing-understand-your-invoice.md)
- [Obter a propriedade das subscrições do Azure com outros usuários de faturação](billing-mca-request-billing-ownership.md)
