---
title: Introdução à conta de faturação do Contrato de Cliente da Microsoft – Azure
description: Compreender a conta de faturação do Contrato de Cliente da Microsoft
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: 835686d639679cca7e9a83b5297b365953835e47
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880733"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Introdução à conta de faturação do Contrato de Cliente da Microsoft

Ao inscrever-se para utilizar o Azure, é criada uma conta de faturação. Utiliza a conta de faturação para gerir faturas, pagamentos e controlar custos. Pode ter acesso a múltiplas contas de faturação. Por exemplo, talvez se tenha inscrito no Azure com o objetivo de desenvolver projetos pessoais. Também poderá ter acesso ao Azure através do Contrato Enterprise ou do Contrato de Cliente da Microsoft da sua organização. Para cada um desses cenários, teria uma conta de faturação separada.

Este artigo aplica-se a uma conta de faturação para um Contrato de Cliente da Microsoft. [Verifique se tem acesso ao Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>A sua conta de faturação

A sua conta de faturação para o Contrato de Cliente da Microsoft contém um ou mais perfis de faturação, que lhe permitem gerir as suas faturas e os seus métodos de pagamento. Cada perfil de faturação contém uma ou mais secções de faturação que lhe permitem organizar os custos na fatura do perfil de faturação.

O diagrama seguinte mostra a relação entre uma conta de faturação, os perfis de faturação e as secções de faturação.

![Um diagrama a mostrar a hierarquia de faturação do Contrato de Cliente da Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

As funções na conta de faturação têm o nível mais elevado de permissões. Por predefinição, apenas o utilizador que aderiu ao Azure tem acesso à conta de faturação. Essas funções devem ser atribuídas a utilizadores que precisam de ver as faturas e controlar os custos de toda a organização, como gestores financeiros ou de TI. Para obter mais informações, veja [Funções e tarefas da conta de faturação](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Perfis de faturação

Utilize um perfil de faturação para gerir a sua fatura e os seus métodos de pagamento. Uma fatura mensal é gerada no início do mês para cada perfil de faturação na sua conta. A fatura contém os custos para todas as subscrições do Azure e outras compras do mês anterior.

Um perfil de faturação é criado automaticamente para a conta de faturação. Contém uma secção de faturação por predefinição. Pode criar secções adicionais para controlar e organizar os custos com facilidade com base nas suas necessidades, seja por projeto, departamento ou ambiente de desenvolvimento. Verá que estas secções na fatura do perfil de faturação refletem a utilização de cada subscrição e as compras que atribuiu às mesmas.

As funções nos perfis de faturação têm permissões para ver e gerir faturas e métodos de pagamento. Atribuir essas funções a utilizadores que pagam faturas, como membros da equipa de contabilidade da sua organização. Para obter mais informações, veja [Funções e tarefas do perfil de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Cada perfil de faturação obtém uma fatura mensal

Uma fatura mensal é gerada no início do mês para cada perfil de faturação. A fatura contém todos os custos do mês anterior.

Pode ver a fatura, transferir documentos e alterar definições para receber faturas posteriores por e-mail no portal do Azure. Para obter mais informações, veja [Transferir as faturas para um Contrato de Cliente da Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Métodos de pagamento por fatura

Cada perfil de faturação tem os seus próprios métodos de pagamento que são utilizados para pagar as respetivas faturas. São suportados os seguintes métodos de pagamento:

| Tipo             | Definição  |
|------------------|-------------|
|Créditos do Azure    |  Os créditos são automaticamente aplicados aos custos elegíveis na sua fatura, o que reduz o valor que tem de pagar. Para obter mais informações, veja [Controlar o saldo de crédito do Azure do perfil de faturação](billing-mca-check-azure-credits-balance.md). |
|Transferência bancária/cheque | Se a sua conta for aprovada para pagamento por cheque/transferência bancária. Pode pagar o montante em dívida da sua fatura através de cheque/transferência bancária. As instruções de pagamento constam na fatura. |
|Cartão de crédito | Os clientes que subscreverem o Azure através do site do Azure podem pagar com cartão de crédito. |

### <a name="apply-policies-to-control-purchases"></a>Aplicar políticas de controlo de compras

Aplicar políticas de controlo de compras do Azure Marketplace e das Reservas através de um perfil de faturação. Pode definir políticas para desativar a compra de produtos de Reservas do Azure e do Marketplace. Quando as políticas são aplicadas, as subscrições que são faturadas no perfil de faturação não podem ser utilizadas para fazer essas compras.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Os planos do Azure determinam o preço e o contrato de nível de serviço das subscrições

Os planos do Azure determinam o preço e os contratos de nível de serviço das subscrições do Azure. São ativados automaticamente quando o utilizador cria um perfil de faturação. Todas as secções da faturação associadas ao perfil de faturação podem utilizar esses planos. Os utilizadores com acesso à secção de faturação utilizam os planos para criar subscrições do Azure. Os seguintes planos do Azure são suportados nas contas de faturação do Contrato de Cliente da Microsoft:

| Planear             | Definição  |
|------------------|-------------|
|Plano do Microsoft Azure   | Permita que os utilizadores criem subscrições que possam executar as cargas de trabalho.  |
|Plano do Microsoft Azure para Dev/Test | Permita que os subscritores do Visual Studio criem subscrições que são restritas para cargas de trabalho de desenvolvimento ou teste. Essas subscrições têm vantagens como tarifas mais baixas e acesso a imagens de máquinas virtuais exclusivas no portal do Azure. |

## <a name="invoice-sections"></a>Secções de faturação

Crie secções de faturação para organizar os custos na fatura. Por exemplo, pode precisar de uma única fatura para a sua organização, mas quer organizar os custos por departamento, equipa ou projeto. Para este cenário, existe um único perfil de faturação onde se cria uma secção de faturação para cada departamento, equipa ou projeto.

Quando uma secção de faturação é criada, pode dar permissão a outras pessoas para criarem subscrições do Azure que são faturadas na secção. Todos os custos de utilização e compras relativos às subscrições são então faturados na secção.

As funções na secção de faturação têm permissões para controlar quem cria as subscrições do Azure. Atribua essas funções a utilizadores que configuram o ambiente do Azure para as equipas da nossa organização, como líderes de engenharia e arquitetos técnicos. Para obter mais informações, veja [Funções e tarefas da secção de faturação](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre a conta de faturação:

- [Compreender as funções administrativas do Contrato de Cliente da Microsoft no Azure](billing-understand-mca-roles.md)
- [Criar uma subscrição do Azure adicional para o Contrato de Cliente da Microsoft](billing-mca-create-subscription.md)
- [Criar secções na sua fatura para organizar os custos](billing-mca-section-invoice.md)
