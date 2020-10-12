---
title: Limites e quotas de escala no seu laboratório em Azure DevTest Labs Microsoft Docs
description: Este artigo descreve como pode escalar o seu laboratório em Azure DevTest Labs. Veja as suas quotas de utilização e limites e solicite um aumento.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2166eaab073e2abc24ca24494ae13eb876db1fcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533959"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Quotas e limites em Laboratórios DevTest
Ao trabalhar na DevTest Labs, poderá notar que existem certos limites padrão para alguns recursos Azure, que podem afetar o serviço DevTest Labs. Estes limites são referidos como **quotas.**

> [!NOTE]
> O serviço DevTest Labs não impõe quotas. Quaisquer quotas que possa encontrar são restrições predefinidas da subscrição global do Azure.

Pode utilizar cada recurso Azure até atingir a sua quota. Cada subscrição tem quotas separadas e a utilização é controlada por subscrição.

Por exemplo, cada subscrição tem uma quota padrão de 20 núcleos. Então, se está a criar VMs no seu laboratório com quatro núcleos cada, então só pode criar cinco VMs.

[A Azure Subscription and Service Limits](../azure-resource-manager/management/azure-subscription-service-limits.md) lista algumas das quotas mais comuns para os recursos Azure. Os recursos mais utilizados em laboratório, e para os quais poderá encontrar quotas, incluem núcleos VM, endereços IP públicos, interface de rede, discos geridos, atribuição de funções Azure e circuitos ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Veja o seu uso e quotas
Estes passos mostram-lhe como visualizar as quotas atuais na sua subscrição para recursos específicos da Azure, e ver qual a percentagem de cada quota que utilizou.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços**e, em seguida, selecione **Billing** da lista.
1. Na lâmina de faturação, selecione uma subscrição.
4. Selecione **Utilização + quotas**.

   ![Botão de utilização e quotas](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   Aparece a lâmina de utilização + quotas, enumerando diferentes recursos disponíveis nessa subscrição e a percentagem da quota que está a ser utilizada por recurso.

   ![Quotas e utilização](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Solicitando mais recursos na sua subscrição
Se atingir um limite de quota, o limite padrão de um recurso numa subscrição pode ser aumentado até um limite máximo, conforme descrito nos Limites de [Subscrição e Serviço Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

Estes passos mostram como solicitar um aumento de quota através do [portal Azure.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Selecione **Mais Serviços,** selecione **Billing**e, em seguida, selecione **Usage + quotas**.
1. Na lâmina Utilização + quotas, selecione o botão **'Aumentar pedido'.**

   ![Pedido de aumento botão](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. Para completar e submeter o pedido, preencha as informações necessárias nos três separadores do novo formulário de pedido de **apoio.**

   ![Formulário de aumento de pedido](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

[Compreender a Azure Limits and Increases](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) fornece mais informações sobre o contacto com o suporte da Azure para solicitar um aumento de quota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Passos seguintes
* Explore a [galeria de modelos QuickStart do DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
