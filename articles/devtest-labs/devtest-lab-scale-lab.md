---
title: Limites e quotas de escala no seu laboratório em Azure DevTest Labs Microsoft Docs
description: Este artigo descreve como pode escalar o seu laboratório em Azure DevTest Labs. Veja as suas quotas de utilização e limites e solicite um aumento.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: c1074c074b39e107e78a46a6e979e487aea1e0f2
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898165"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Quotas e limites em Laboratórios DevTest
Ao trabalhar na DevTest Labs, poderá notar que existem certos limites padrão para alguns recursos Azure, que podem afetar o serviço DevTest Labs. Estes limites são referidos como **quotas.**

> [!NOTE]
> O serviço DevTest Labs não impõe quotas. Quaisquer quotas que possa encontrar são restrições predefinidas da subscrição global do Azure.

Pode utilizar cada recurso Azure até atingir a sua quota. Cada subscrição tem quotas separadas e a utilização é controlada por subscrição.

Por exemplo, cada subscrição tem uma quota padrão de 20 núcleos. Então, se está a criar VMs no seu laboratório com quatro núcleos cada, então só pode criar cinco VMs.

[A Azure Subscription and Service Limits](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) lista algumas das quotas mais comuns para os recursos Azure. Os recursos mais utilizados em laboratório, e para os quais poderá encontrar quotas, incluem núcleos VM, endereços IP públicos, interface de rede, discos geridos, atribuição de funções RBAC e circuitos ExpressRoute.

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
Se atingir um limite de quota, o limite padrão de um recurso numa subscrição pode ser aumentado até um limite máximo, conforme descrito nos Limites de [Subscrição e Serviço Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

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
