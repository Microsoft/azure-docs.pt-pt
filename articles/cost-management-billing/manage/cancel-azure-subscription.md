---
title: Cancelar a sua subscrição do Azure
description: Descreve como cancelar a sua subscrição do Azure, como a subscrição de Avaliação Gratuita
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: banders
ms.openlocfilehash: 662e1bf721c93ee1d59946d2fd603551f3f88ad3
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503799"
---
# <a name="cancel-your-azure-subscription"></a>Cancelar a sua subscrição do Azure

Se já não precisar da subscrição do Azure, pode cancelar a mesma no portal do Azure.

Embora não seja necessário, a Microsoft *recomenda* que faça o seguinte antes de cancelar a sua subscrição:

* Crie uma cópia de segurança dos dados. Por exemplo, se estiver a armazenar dados no armazenamento do Azure ou no SQL, transfira uma cópia. Se tiver uma máquina virtual, guarde uma imagem da mesma localmente.
* Encerre os seus serviços. Aceda à [página de recursos no portal de gestão](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) e **Pare** quaisquer máquinas virtuais, aplicações ou outros serviços em execução.
* Considere migrar os seus dados. Veja como [Mover recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Elimine todos os recursos e grupos de recursos.
* Se tiver funções personalizadas que façam referência a esta subscrição em `AssignableScopes`, deverá atualizar estas funções personalizadas para remover a subscrição. Se tentar atualizar uma função personalizada depois de cancelar uma subscrição, poderá obter um erro. Para obter mais informações, veja [Como resolver problemas com funções personalizadas](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) e [Funções personalizadas do Azure](../../role-based-access-control/custom-roles.md).

Se cancelar um Plano de suporte do Azure, ser-lhe-á faturado o resto do período da subscrição. Para obter mais informações, veja [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

## <a name="who-can-cancel-a-subscription"></a>Quem pode cancelar uma subscrição?

A tabela a seguir descreve a permissão necessária para cancelar uma subscrição.

|Tipo de subscrição     |Quem pode cancelar  |
|---------|---------|
|As subscrições criadas quando se inscreve no Azure através do site do Azure. Por exemplo, quando se inscreve numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrador de conta e proprietários da subscrição  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) e [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Proprietário de conta e proprietários da subscrição       |
|[Plano do Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) e [Plano do Azure para DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Proprietários da subscrição      |


## <a name="cancel-subscription-in-the-azure-portal"></a>Cancelar a subscrição no portal do Azure

1. Selecione a sua subscrição a partir da [Página de subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que quer cancelar.
1. Selecione **Descrição Geral** e, em seguida, selecione **Cancelar subscrição**.
    ![Captura de ecrã a mostrar o botão Cancelar](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Siga as instruções e termine o cancelamento.

> [!NOTE]
> Os parceiros podem suspender ou cancelar uma subscrição caso um cliente peça ou em situações de não pagamento ou fraude. Para obter mais informações, veja [Suspender ou cancelar uma subscrição](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription).

## <a name="cancel-a-support-plan"></a>Cancelar um plano de suporte

Se tiver comprado um plano de suporte através do site do Azure ou do portal do Azure ou se tiver um plano ao abrigo do Contrato de Cliente Microsoft, pode cancelá-lo. Se tiver comprado o plano de suporte junto de um representante ou parceiro da Microsoft, contacte-o para assistência. 

1. No portal do Azure, navegue para **Cost Management + Faturação**.
1. Em **Faturação**, selecione **Custos recorrentes**.
1. No lado direito do item de linha do plano de suporte, selecione as reticências ( **...** ) e **Desativar a renovação automática**.

## <a name="what-happens-after-subscription-cancellation"></a>O que acontece após o cancelamento de uma subscrição?

Depois de cancelar, a faturação é interrompida imediatamente. No entanto, pode levar até 10 minutos para que o cancelamento seja mostrado no portal. Se cancelar a meio de um período de faturação, enviaremos a fatura final na data da fatura normal depois de o período terminar.

Depois de cancelar, os seus serviços serão desativados. Isto significa que as máquinas virtuais são desalocadas, os endereços IP temporários são libertados e o armazenamento é só de leitura.

Após o cancelamento da subscrição, a Microsoft espera entre 30 e 90 dias antes de eliminar definitivamente os seus dados, caso precise de aceder aos mesmos ou mude de ideias. Não cobramos por manter os dados. Para saber mais, veja [Centro de Confiança da Microsoft – Como gerimos os dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="delete-free-trial-subscription"></a>Eliminar a subscrição de avaliação gratuita

Se tiver uma subscrição de avaliação gratuita, não tem de aguardar 30 dias para que a subscrição seja eliminada automaticamente. Pode eliminar a subscrição *três dias* depois do cancelamento. A opção **Eliminar subscrição** só está disponível três dias depois de cancelar a subscrição.

1. Aguarde três dias após a data de cancelamento da subscrição.
1. Selecione a sua subscrição na página [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.
1. Selecione a subscrição que quer eliminar.
1. Selecione **Descrição geral** e, em seguida, selecione **Eliminar subscrição**.

## <a name="reactivate-a-subscription"></a>Reativar uma subscrição

Se cancelar a sua subscrição com tarifas Pay As You Go por engano, pode [reativá-la no Centro de Contas](subscription-disabled.md).

Se a sua subscrição não for uma subscrição com tarifas Pay As You Go, contacte o suporte no prazo de 90 dias após o cancelamento para reativar a sua subscrição.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Por que motivo não vejo a opção Cancelar Subscrição no portal do Azure? 

Talvez não tenha as permissões necessárias para cancelar uma subscrição. Veja [Quem pode cancelar uma subscrição?](#who-can-cancel-a-subscription) para obter uma descrição de quem pode cancelar vários tipos de subscrições.

## <a name="how-do-i-delete-my-azure-account"></a>Como devo proceder para eliminar a minha Conta do Azure?

*Preciso de remover a minha conta, incluindo todas as minhas informações pessoais. Já cancelei as minhas subscrições ativas (Avaliação Gratuita). Não tenho nenhuma subscrição ativa e gostaria de eliminar totalmente a minha conta*.

* Se tiver uma conta de Azure Active Directory através da sua organização, o administrador do Azure AD poderá eliminar a conta. Depois disso, os seus serviços serão desativados. Isto significa que as máquinas virtuais são desalocadas, os endereços IP temporários são libertados e o armazenamento é só de leitura. Em resumo, depois de cancelar, a faturação é interrompida imediatamente.

* Se não tiver uma conta do Azure AD através da sua organização, pode cancelar e eliminar as suas subscrições do Azure e, em seguida, remover o seu cartão de crédito da conta. Embora a ação não elimine a conta, torna-a inoperável. Pode avançar um pouco e também eliminar a conta Microsoft associada se não estiver a ser utilizada para nenhuma outra finalidade.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Como devo proceder para cancelar uma conta do Visual Studio Professional?

Veja o artigo [Renovação e Cancelamento](/visualstudio/subscriptions/faq/admin/renewal-cancellation). Se tiver uma subscrição do Azure para o Visual Studio, a mesma também terá de ser cancelada e eliminada.

## <a name="next-steps"></a>Passos seguintes

- Se for necessário, pode reativar uma subscrição pay as you go no [Centro de contas](subscription-disabled.md).