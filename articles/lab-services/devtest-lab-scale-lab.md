---
title: Quotas e limites de escala no seu laboratório em Azure DevTest Labs [ Microsoft Docs
description: Este artigo descreve como pode escalar o seu laboratório em Laboratórios Azure DevTest. Veja as suas quotas e limites de utilização e solicite um aumento.
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
ms.openlocfilehash: f3299c24bc751263ccd284a4fddb492fba5dd1e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76761190"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Quotas e limites de escala nos Laboratórios DevTest
Como trabalha em DevTest Labs, pode notar que existem certos limites de padrão para alguns recursos Azure, que podem afetar o serviço DevTest Labs. Estes limites são referidos como **quotas.**

> [!NOTE]
> O serviço DevTest Labs não impõe quotas. Quaisquer quotas que possa encontrar são constrangimentos predefinidos da subscrição total do Azure.

Pode utilizar cada recurso Azure até atingir a sua quota. Cada subscrição tem quotas separadas e a utilização é controlada por subscrição.

Por exemplo, cada subscrição tem uma quota padrão de 20 núcleos. Então, se estás a criar VMs no teu laboratório com quatro núcleos cada, então só podes criar cinco VMs.

Os limites de [subscrição e serviço do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) listam algumas das quotas mais comuns para os recursos Azure. Os recursos mais utilizados num laboratório, e para os quais pode encontrar quotas, incluem núcleos VM, endereços IP públicos, interface de rede, discos geridos, atribuição de funções RBAC e circuitos ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Veja o seu uso e quotas
Estes passos mostram-lhe como ver as quotas atuais na sua subscrição para recursos específicos do Azure, e para ver qual a percentagem de cada quota que utilizou.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços**e, em seguida, selecione **Faturação** da lista.
1. Na lâmina de faturação, selecione uma subscrição.
4. **Selecione Utilização + quotas**.

   ![Botão de utilização e quotas](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Aparece a lâmina de utilização + quotas, enumerando diferentes recursos disponíveis nessa subscrição e na percentagem da quota que está a ser utilizada por recurso.

   ![Quotas e utilização](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Solicitando mais recursos na sua subscrição
Se atingir um limite de quota, o limite de predefinição de um recurso numa subscrição pode ser aumentado até um limite máximo, conforme descrito nos limites de [subscrição e serviço do Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)

Estes passos mostram-lhe como solicitar um aumento de quota através do [portal Azure.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Selecione **Mais Serviços,** selecione **Faturação**e, em seguida, selecione **Usage + quotas**.
1. Na lâmina utilização + quotas, selecione o botão **'Pedido Aumentar'.**

   ![Solicitar aumento do botão](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Para completar e submeter o pedido, preencha as informações necessárias nos três separadores do novo formulário de pedido de **suporte.**

   ![Formulário de aumento de pedido](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[A Compreensão dos Limites e Aumentos do Azure](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) fornece mais informações sobre o contacto com o suporte do Azure para solicitar um aumento de quota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Passos seguintes
* Explore a galeria de [modelos QuickStart Do Gestor de Recursos DevTest Labs Azure](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
