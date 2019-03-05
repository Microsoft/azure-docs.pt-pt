---
title: Introdução à sua conta de cobrança para um contrato de cliente da Microsoft - Azure | Documentos da Microsoft
description: Compreender a conta de cobrança para um contrato de cliente da Microsoft
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337418"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Introdução à sua conta de cobrança para um contrato de cliente da Microsoft

É criada uma conta de faturação para cada contrato que iniciar sessão com a Microsoft a utilizar o Azure. Utilize a conta de cobrança para gerenciar a cobrança e controlar os custos. Pode ter acesso a várias contas de faturas. Por exemplo, poderá ter inscrito para o Azure para os seus projetos pessoais. Também poderia ter acesso ao Azure através do Enterprise Agreement ou contrato de cliente da Microsoft da sua organização. Para cada um destes cenários, teria uma conta de cobrança separada.

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Compreender a conta de cobrança

Sua conta de cobrança para o contrato de cliente da Microsoft contém um ou mais perfis de faturas que permitem-lhe gerir os seus métodos de pagamento e notas fiscais. Cada perfil de faturação contém um ou mais secções de nota fiscal que permitem-lhe organizar os custos de nota fiscal do perfil de faturação.

O diagrama seguinte mostra a relação entre uma conta de cobrança, os perfis de faturas e seções de nota fiscal.

![Diagrama que mostra a hierarquia de faturação para o contrato de cliente da Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

Funções na conta de faturação têm o maior nível de permissões. Por predefinição, apenas os administradores globais no Azure Active Directory da sua organização, obter acesso à conta de faturação. Estas funções devem ser atribuídas a utilizadores que precisam de ver as notas fiscais e controlar os custos para toda a organização, como o financeiro ou gerentes de TI. Para obter mais informações, consulte [funções de conta e tarefas de faturação](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Compreender os perfis de faturas

Utilize um perfil de faturação para gerir os seus métodos de pagamentos e notas fiscais. Uma nota fiscal mensal é gerada para subscrições do Azure e outros produtos comprados com o perfil de faturação. Utilize os métodos de pagamentos para pagar a nota fiscal.

Um perfil de faturação é criado automaticamente para a sua conta de cobrança. Pode criar novos perfis de faturas para configurar a notas fiscais adicionais. Por exemplo, poderá pretender faturas diferentes para cada departamento ou projeto na sua organização.

Também pode criar secções de nota fiscal para organizar os custos de nota fiscal de um perfil de faturação. Custos de subscrições do Azure e os produtos adquiridos para uma seção de nota fiscal aparecem na secção. Nota fiscal de faturação do perfil inclui os custos para todas as secções de nota fiscal.

Funções nos perfis de faturas tem permissões para ver e gerir as notas fiscais e métodos de pagamento. Atribua essas funções a utilizadores que pagam notas fiscais, como os membros da equipa de contabilidade em sua organização. Para obter mais informações, consulte [funções de perfis e tarefas de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Fatura mensal gerada para cada perfil de faturação

Uma nota fiscal mensal é gerada na data da nota fiscal para cada perfil de faturação. A nota fiscal contém todos os encargos do mês anterior.

Pode ver a nota fiscal, transferir os documentos e alterar a definição para obter futuras faturas por e-mail, no portal do Azure. Para obter mais informações, consulte [transferência de faturas para um contrato de cliente da Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoices-paid-through-payment-methods"></a>Notas fiscais pagos através de métodos de pagamento

Cada perfil de Faturação tem seus próprios métodos de pagamento que são utilizados para pagar seus notas fiscais. Os seguintes métodos de pagamento são suportados:

| Type             | Definição  |
|------------------|-------------|
|Créditos do Azure    |  Os créditos são aplicados automaticamente para a quantidade total de faturação na sua fatura para calcular a quantidade que terá de pagar. Para obter mais informações, consulte [acompanhar o saldo do crédito do Azure para o seu perfil de faturação](billing-mca-check-azure-credits-balance.md). |
|Cheque ou transferência bancária | Pode pagar a quantia devida obter a fatura por meio de verificação ou durante a transmissão de transferência. As instruções de pagamento recebem da nota fiscal |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Compras de controlo do Azure Marketplace e reserva ao aplicar políticas

Aplica políticas para controlar as compras efetuadas com um perfil de faturação. Pode definir políticas para desabilitar a compra de reservas do Azure e produtos do Marketplace. Quando as políticas são aplicadas, subscrições criadas para as secções de nota fiscal no perfil de faturação não podem ser utilizadas para comprar reservas do Azure e produtos do Marketplace.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Permitir aos utilizadores criar subscrições do Azure, permitindo que os planos do Azure

Os planos do Azure são ativados automaticamente quando cria um perfil de faturação. Todas as seções de nota fiscal no perfil de faturação obtém acesso a estes planos. Os utilizadores com acesso para a secção de nota fiscal utilizar os planos de criar subscrições do Azure. Eles não é possível criar subscrições do Azure, a menos que um plano do Azure está ativado para o perfil de faturação. Os planos do Azure seguintes são suportados contas de faturação para o contrato de cliente da Microsoft:

| Planear             | Definição  |
|------------------|-------------|
|Microsoft Azure Plan   | Permitir aos utilizadores criar subscrições que podem ser executadas quaisquer cargas de trabalho. Para obter mais informações, consulte [plano do Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Plano do Microsoft Azure para programação/teste | Permitir que os subscritores do Visual Studio criar as subscrições que estão limitadas para desenvolvimento ou teste de cargas de trabalho. Estas subscrições obtém benefícios, como as menores taxas e acesso a imagens de máquina de virtual exclusivo no portal do Azure. Para obter mais informações, consulte [plano do Microsoft Azure para Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="understand-invoice-sections"></a>Compreender as secções de nota fiscal

Crie secções de nota fiscal para organizar os custos na fatura de um perfil de faturação. Por exemplo, poderá ter uma única fatura para a sua organização, mas pretender para organizar os custos por departamento, a equipe ou projeto. Para este cenário, tiver um perfil de faturação único onde pode criar uma seção de nota fiscal para cada departamento, a equipe ou o projeto.

Quando uma seção de nota fiscal é criada, pode dar outras pessoas a permissão para criar subscrições do Azure para a seção. Todos os custos de utilização e compras para as subscrições, em seguida, são refletidas na secção adequada da nota fiscal.

Funções na seção de nota fiscal de ter permissões para controlar quem cria as subscrições do Azure. Atribua essas funções a utilizadores que configurar o ambiente do Azure para as equipas na nossa organização, como oportunidades potenciais de engenharia e arquitetos técnicos. Para obter mais informações, consulte [secção funções e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para saber mais sobre a sua conta de cobrança:

- [Compreender as funções administrativas do contrato de cliente da Microsoft no Azure](billing-understand-mca-roles.md)
- [Criar uma subscrição do Azure adicional para o contrato de cliente da Microsoft](billing-mca-create-subscription.md)
- [Criar secções na sua fatura para organizar os seus custos](billing-mca-section-invoice.md)