---
title: Cancelar a sua subscrição do Azure | Microsoft Docs
description: Descreve como cancelar a sua subscrição do Azure, como a subscrição de Avaliação Gratuita
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e4ce97b69250d8af4c94e8eed8df9c9497e0bb46
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "75993079"
---
# <a name="cancel-your-azure-subscription"></a>Cancelar a sua subscrição do Azure

Se já não precisar da subscrição do Azure, pode cancelá-la no portal do Azure.

Antes de cancelar a sua subscrição:
* Crie uma cópia de segurança dos dados. Por exemplo, se estiver a armazenar dados no armazenamento do Azure ou no SQL, transfira uma cópia. Se tiver uma máquina virtual, guarde uma imagem da mesma localmente.
* Encerre os seus serviços. Aceda à [página de recursos no portal de gestão](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) e **Pare** quaisquer máquinas virtuais, aplicações ou outros serviços em execução.
* Considere migrar os seus dados. Veja como [Mover recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Elimine todos os recursos e grupos de recursos. A eliminação dos mesmos é necessária para que possa cancelar uma subscrição. Cada grupo de recursos tem de ser eliminado individualmente. Durante a eliminação do grupo de recursos, tem de confirmar a eliminação ao digitar o nome do grupo de recursos.
* Se tiver funções personalizadas que façam referência a esta subscrição em `AssignableScopes`, deverá atualizar estas funções personalizadas para remover a subscrição. Se tentar atualizar uma função personalizada depois de cancelar uma subscrição, poderá obter um erro. Para obter mais informações, veja [Como resolver problemas com funções personalizadas](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) e [Funções personalizadas para recursos do Azure](../../role-based-access-control/custom-roles.md).

Se cancelar um Plano de suporte do Azure, ser-lhe-á faturado o resto do período da subscrição. Para obter mais informações, veja [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Cancelar a subscrição no portal do Azure

1. Selecione a sua subscrição a partir da [Página de subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selecione a subscrição que quer cancelar.
3. Selecione **Descrição Geral** e, em seguida, selecione **Cancelar subscrição**.
    ![Captura de ecrã a mostrar o botão Cancelar](./media/cancel-azure-subscription/cancel_ibiza.png)
3. Siga as instruções e termine o cancelamento.


## <a name="who-can-cancel-a-subscription"></a>Quem pode cancelar uma subscrição?

A tabela a seguir descreve a permissão necessária para cancelar uma subscrição.

|Tipo de subscrição     |Quem pode cancelar  |
|---------|---------|
|As subscrições criadas quando se inscreve no Azure através do site do Azure. Por exemplo, quando se inscreve numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrador da conta, proprietários e contribuidores para a subscrição  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) e [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Proprietário da conta, proprietários e contribuidores para a subscrição       |
|[Plano do Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) e [Plano do Azure para DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Proprietários e contribuidores para a subscrição      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>O que acontece depois de cancelar a minha subscrição?

Depois de cancelar, a faturação é interrompida imediatamente. No entanto, pode levar até 10 minutos para que o cancelamento seja mostrado no portal. Se cancelar a meio de um período de faturação, enviaremos a fatura final na data da fatura normal depois de o período terminar.

Depois de cancelar, os seus serviços serão desativados. Isto significa que as máquinas virtuais são desalocadas, os endereços IP temporários são libertados e o armazenamento é só de leitura.

Aguardamos 90 dias antes de eliminar definitivamente os seus dados, caso precise de aceder aos mesmos ou mude de ideias. Não cobramos por manter os dados. Para saber mais, veja [Centro de Confiança da Microsoft – Como gerimos os dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Reativar subscrição

Se cancelar a sua subscrição com tarifas Pay As You Go por engano, pode [reativá-la no Centro de Contas](subscription-disabled.md).

Se a sua subscrição não for uma subscrição com tarifas Pay As You Go, entre em contacto com o suporte no prazo de 90 dias após o cancelamento para reativar a sua subscrição.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
