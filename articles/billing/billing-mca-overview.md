---
title: Introdução à sua conta de cobrança de contrato de cliente da Microsoft - Azure
description: Compreender o contrato do cliente Microsoft conta de faturação
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490738"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Introdução à sua conta de cobrança do contrato de cliente da Microsoft

Uma conta de cobrança é criada quando se inscreve utilizar o Azure. Utilizar a sua conta de cobrança para gerir as notas fiscais, pagamentos e controlar os custos. Pode ter acesso a várias contas de faturas. Por exemplo, poderá ter inscrito para o Azure para os seus projetos pessoais. Também poderia ter acesso ao Azure através do Enterprise Agreement ou contrato de cliente da Microsoft da sua organização. Para cada um destes cenários, teria uma conta de cobrança separada.

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Sua conta de cobrança

Sua conta de cobrança para o contrato de cliente da Microsoft contém um ou mais perfis de faturas que permitem-lhe gerir os seus métodos de pagamento e notas fiscais. Cada perfil de faturação contém um ou mais secções de nota fiscal que permitem-lhe organizar os custos de nota fiscal do perfil de faturação.

O diagrama seguinte mostra a relação entre uma conta de cobrança, perfis de faturas e seções de nota fiscal.

![Diagrama que mostra o contrato de cliente da Microsoft hierarquia de faturação](./media/billing-mca-overview/mca-billing-hierarchy.png)

Funções na conta de faturação têm o maior nível de permissões. Por predefinição, apenas o utilizador que inscreveu no Azure obtém acesso à conta de cobrança. Estas funções devem ser atribuídas a utilizadores que precisam de ver as notas fiscais e controlar os custos para toda a organização, como o financeiro ou gerentes de TI. Para obter mais informações, consulte [funções de conta e tarefas de faturação](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Perfis de faturação

Utilize um perfil de faturação para gerir os seus métodos de pagamentos e notas fiscais. Uma nota fiscal mensal é gerada no início do mês para cada perfil de faturação na sua conta. A nota fiscal contém os respetivos custos para todas as subscrições do Azure e outras compras do mês anterior.

Um perfil de faturação é criado automaticamente para a sua conta de cobrança. Ele contém uma secção de nota fiscal por predefinição. Pode criar secções adicionais para controlar e os custos com base nas suas necessidades de organizar facilmente seja por ambiente de desenvolvimento, departamento ou projeto. Verá que estas secções na fatura do perfil de faturação que reflete a utilização de cada subscrição e compras que atribuiu a ele.

Funções nos perfis de faturas tem permissões para ver e gerir as notas fiscais e métodos de pagamento. Atribua essas funções a utilizadores que pagam notas fiscais, como os membros da equipa de contabilidade em sua organização. Para obter mais informações, consulte [funções de perfis e tarefas de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Cada perfil de faturação obtém uma fatura mensal

Uma nota fiscal mensal é gerada no início do mês para cada perfil de faturação. A nota fiscal contém todos os encargos do mês anterior.

Pode ver a nota fiscal, transferir os documentos e alterar a definição para obter futuras faturas por e-mail, no portal do Azure. Para obter mais informações, consulte [transferência de faturas para um contrato de cliente da Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Métodos de pagamento da nota fiscal

Cada perfil de Faturação tem seus próprios métodos de pagamento que são utilizados para pagar seus notas fiscais. Os seguintes métodos de pagamento são suportados:

| Type             | Definição  |
|------------------|-------------|
|Créditos do Azure    |  Os créditos são aplicados automaticamente às taxas de elegíveis na sua fatura, reduzindo a quantidade que terá de pagar. Para obter mais informações, consulte [acompanhar o saldo do crédito do Azure para o seu perfil de faturação](billing-mca-check-azure-credits-balance.md). |
|Transferência de verificação/rede | Se a sua conta é aprovada para pagamento através de transferência de verificação/durante a transmissão. Pode pagar o montante devido para a sua fatura através de transferência de verificação/durante a transmissão. As instruções de pagamento recebem da nota fiscal |
|Cartão de crédito | Os clientes que se inscrever no Azure através do site do Azure podem pagar por meio de um cartão de crédito. |

### <a name="apply-policies-to-control-purchases"></a>Aplicar políticas para controlar as compras

Aplica políticas de controlo do Azure Marketplace e compras de reserva com um perfil de faturação. Pode definir políticas para desabilitar a compra de reservas do Azure e produtos do Marketplace. Quando as políticas são aplicadas, as subscrições que são cobradas ao perfil de faturação não podem ser utilizadas para fazer estas compras.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Preço e de serviço contrato de nível para subscrições de determinar os planos do Azure

Os planos do Azure determinam os preços e contratos de nível para as subscrições do Azure do serviço. Eles são ativados automaticamente quando cria um perfil de faturação. Todas as seções de nota fiscal que estão associadas ao perfil de faturação podem utilizar estes planos. Os utilizadores com acesso para a secção de nota fiscal utilizar os planos de criar subscrições do Azure. Os planos do Azure seguintes são suportados contas de faturação para o contrato de cliente da Microsoft:

| Planear             | Definição  |
|------------------|-------------|
|Microsoft Azure Plan   | Permitir aos utilizadores criar subscrições que podem ser executadas quaisquer cargas de trabalho. Para obter mais informações, consulte [plano do Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Plano do Microsoft Azure para programação/teste | Permitir que os subscritores do Visual Studio criar as subscrições que estão limitadas para desenvolvimento ou teste de cargas de trabalho. Estas subscrições obtém benefícios, como as menores taxas e acesso a imagens de máquina de virtual exclusivo no portal do Azure. Para obter mais informações, consulte [plano do Microsoft Azure para Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>Secções de nota fiscal

Crie secções de nota fiscal para organizar os custos na sua fatura. Por exemplo, poderá ter uma única fatura para a sua organização, mas pretender para organizar os custos por departamento, a equipe ou projeto. Para este cenário, tiver um perfil de faturação único onde pode criar uma seção de nota fiscal para cada departamento, a equipe ou o projeto.

Quando uma seção de nota fiscal é criada, pode dar outras pessoas a permissão para criar subscrições do Azure que são faturadas à secção. Todos os custos de utilização e compras para as subscrições, em seguida, são faturadas a seção.

Funções na seção de nota fiscal de ter permissões para controlar quem cria as subscrições do Azure. Atribua essas funções a utilizadores que configurar o ambiente do Azure para as equipas na nossa organização, como oportunidades potenciais de engenharia e arquitetos técnicos. Para obter mais informações, consulte [secção funções e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para saber mais sobre a sua conta de cobrança:

- [Compreender as funções administrativas do contrato de cliente da Microsoft no Azure](billing-understand-mca-roles.md)
- [Criar uma subscrição do Azure adicional para o contrato de cliente da Microsoft](billing-mca-create-subscription.md)
- [Criar secções na sua fatura para organizar os seus custos](billing-mca-section-invoice.md)
