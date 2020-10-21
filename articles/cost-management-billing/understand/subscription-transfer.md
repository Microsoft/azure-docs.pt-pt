---
title: Sobre a transferência da propriedade de faturação de uma subscrição do Azure
description: Este artigo explica o que tem de saber antes de transferir a propriedade de faturação de uma subscrição do Azure para outra conta.
keywords: transferir subscrição do azure, subscrição de transferência do azure, mover subscrição do azure para outra conta, alterar proprietário da subscrição do azure, transferir subscrição do azure para outra conta, faturação de transferência do azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: f4dd6d67d60603ed6cad7056cff4bb07dcb1c2e5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149386"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Sobre a transferência da propriedade de faturação de uma subscrição do Azure

Este artigo ajuda a perceber o que deve saber antes de transferir a propriedade de faturação de uma subscrição do Azure para outra conta. 

Pode querer transferir a propriedade de faturação da sua subscrição do Azure se estiver de saída da organização ou se quiser que a sua subscrição seja faturada noutra conta. A transferência da propriedade de faturação para outra conta dá aos administradores da nova conta permissão para tarefas de faturação. Podem alterar o método de pagamento, ver os custos e cancelar a subscrição.

Se quiser manter a propriedade de faturação, mas alterar o tipo da subscrição, veja [Mudar a sua subscrição do Azure para outra oferta](../manage/switch-azure-offer.md). Para controlar quem tem acesso aos recursos na subscrição, veja [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

Se for cliente do Contrato Enterprise (EA), o seu administrador empresarial poderá transferir a propriedade de faturação das suas subscrições entre contas.

Só o administrador de faturação de uma conta pode transferir a propriedade de uma subscrição.

## <a name="determine-account-billing-administrator"></a>Determinar o administrador de faturação da conta

<a name="whoisaa"></a>

O administrador de faturação é a pessoa que tem permissão para gerir a faturação de uma conta. Está autorizado a aceder à faturação no [portal do Azure](https://portal.azure.com) e a realizar várias tarefas de faturação, como criar subscrições, ver e pagar faturas ou atualizar métodos de pagamento.

Para identificar as contas das quais é administrador de faturação, visite a [página Cost Management + Faturação no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview). Em seguida, selecione **Todos os âmbitos de faturação** no painel à esquerda. A página de subscrições mostra todas as subscrições nas quais é administrador de faturação.

Se não tiver a certeza de quem é o administrador de conta de uma subscrição, visite a [página Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Depois, selecione a subscrição que pretende verificar e, em seguida, procure em **Definições**. Selecione **Propriedades** e o administrador de conta da subscrição é apresentado na caixa **Administrador de Conta**.


## <a name="supported-subscription-types"></a>Tipos de subscrições suportadas

A transferência de subscrição no portal do Azure está disponível para os tipos de subscrição listados abaixo. Atualmente, a transferência não é suportada nas subscrições de [Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) nem do [Azure no Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Para uma solução, veja [Mover recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Para transferir outras subscrições, como planos de suporte, [contacte o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay As You Go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plano do Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> Através do EA Portal.

<sup>2</sup> Suportado apenas em contas criadas durante a inscrição no site do Azure.

## <a name="resources-transferred-with-subscriptions"></a>Recursos transferidos com as subscrições

Todos os recursos, como VMs, discos e sites, são transferidos para a nova conta. No entanto, se transferir uma subscrição para uma conta noutro inquilino do Azure AD, as atribuições de [funções de administrador](../manage/add-change-subscription-administrator.md) e de [funções do Azure](../../role-based-access-control/role-assignments-portal.md) na subscrição não são transferidas. Além disso, os [registos de aplicações](../../active-directory/develop/quickstart-register-app.md) e outros serviços específicos do inquilino não são transferidos juntamente com a subscrição.

## <a name="transfer-account-ownership-to-another-countryregion"></a>Transferir a propriedade de conta para outro país/região

Infelizmente, não é possível transferir subscrições entre países ou regiões através do portal do Azure. No entanto, pode abrir um pedido de suporte do Azure para as transferir. Para criar um pedido de suporte, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="transfer-a-subscription-from-one-account-to-another"></a>Transferir uma subscrição de uma conta para outra

Se for administrador de duas contas, pode transferir uma subscrição entre as suas contas. As suas contas são consideradas conceptualmente como contas de dois utilizadores diferentes, por isso, pode transferir subscrições entre as suas contas.
Para ver os passos necessários para transferir a sua subscrição, veja [Transferir a propriedade de faturação de uma subscrição do Azure](../manage/billing-subscription-transfer.md).

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>A transferência de subscrições não deverá criar tempos de inatividade

Se transferir uma subscrição para uma conta no mesmo inquilino do Azure AD, não tem impacto nos recursos em execução na subscrição. No entanto, as informações de contexto guardadas no PowerShell não são atualizadas, pelo que poderá ter de as limpar ou alterar as definições. Se transferir a subscrição para uma conta noutro inquilino e decidir mover a subscrição para o inquilino, todos os utilizadores, grupos e principais de serviço que tinham [atribuições de funções do Azure](../../role-based-access-control/role-assignments-portal.md) para aceder aos recursos na subscrição deixarão de ter acesso. O tempo de inatividade do serviço pode resultar.

## <a name="new-account-usage-and-billing-history"></a>Utilização e histórico de faturação da conta nova

A única informação disponível para os utilizadores relativamente à conta nova é o custo do último mês da subscrição. O resto do histórico de utilização e faturação não é transferido com a subscrição.

## <a name="manually-migrate-data-and-services"></a>Migrar dados e serviços manualmente

Quando transfere uma subscrição, os recursos dessa subscrição permanecem na mesma. Se não puder transferir a propriedade da subscrição, poderá migrar manualmente os recursos da mesma. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="remaining-subscription-credits"></a>Créditos da subscrição restantes 

Se tiver uma subscrição do Visual Studio ou do Microsoft Partner Network, recebe créditos mensais. O crédito não é transferido juntamente com a subscrição na conta nova. O utilizador que aceita o pedido de transferência precisa de ter uma licença do Visual Studio para aceitar o pedido de transferência. A subscrição utiliza o crédito do Visual Studio que está disponível na conta do utilizador. Para obter mais informações, veja [Transferir subscrições do Visual Studio e do Partner Network](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

## <a name="users-keep-access-to-transferred-resources"></a>Os utilizadores mantêm o acesso aos recursos transferidos

Tenha em conta que os utilizadores com acesso a recursos numa subscrição mantêm esse acesso quando a propriedade é transferida. No entanto, as [funções de administrador](../manage/add-change-subscription-administrator.md) e as [atribuições de funções](../../role-based-access-control/role-assignments-portal.md) podem ser removidas. A perda de acesso ocorre quando a sua conta está num inquilino do Azure AD diferente do inquilino da subscrição e o utilizador que enviou o pedido de transferência muda a subscrição para o inquilino da sua conta. 

Pode ver que utilizadores têm atribuições de funções do Azure para aceder a recursos na subscrição através do portal do Azure. Visite a [página de Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Em seguida, selecione a subscrição que pretende verificar e, em seguida, selecione **Controlo de acesso (IAM)** no painel à esquerda. Em seguida, selecione **Atribuições de funções** na parte superior da página. A página de atribuições de funções apresenta todos os utilizadores que têm acesso na subscrição.

Mesmo que as [atribuições de funções do Azure](../../role-based-access-control/role-assignments-portal.md) sejam removidas durante a transferência, os utilizadores na conta do proprietário original poderão continuar a aceder à subscrição através de outros mecanismos de segurança, incluindo:

* Certificados de gestão que concedem ao utilizador direitos de administrador aos recursos da subscrição. Para obter mais informações, veja [Criar e carregar um certificado de gestão do Azure](../../cloud-services/cloud-services-certs-create.md).
* Chaves de acesso dos serviços como o Armazenamento. Para obter mais informações, veja [Acerca das contas de armazenamento do Azure](../../storage/common/storage-account-create.md).
* Credenciais de Acesso Remoto dos serviços como máquinas virtuais do Azure.

Se o destinatário precisar de limitar o acesso aos recursos, deverá ponderar a atualização de todos os segredos associados ao serviço. A maioria dos recursos podem ser atualizados. Inicie sessão no [portal do Azure](https://portal.azure.com) e, no menu Hub, selecione **Todos os recursos**. Depois, selecione o recurso. Em seguida, na página do recurso, selecione **Definições**. Aí, pode ver e atualizar os segredos existentes.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>Quando recebe a propriedade, paga pela utilização

A sua conta é responsável pelo pagamento de uma eventual utilização relatada a partir do momento da transferência em diante. Pode haver utilização que tenha ocorrido antes da transferência, mas que seja relatada depois. A utilização está incluída na fatura da sua conta.

## <a name="use-a-different-payment-method"></a>Utilizar outro método de pagamento

Ao aceitar o pedido de transferência, pode selecionar um método de pagamento existente que esteja associado à sua conta ou adicionar um novo.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Transferir a propriedade da subscrição Contrato Enterprise

O Administrador Empresarial pode atualizar a propriedade de qualquer conta, mesmo depois de o proprietário original da conta deixar de fazer parte da organização. Para obter mais informações sobre a transferência de contas do Contrato Enterprise do Azure, veja [Transferências do Contrato Enterprise do Azure](../manage/ea-transfers.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Transferir a propriedade de faturação de uma subscrição do Azure](../manage/billing-subscription-transfer.md)