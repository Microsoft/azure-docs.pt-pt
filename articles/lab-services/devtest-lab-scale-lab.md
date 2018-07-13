---
title: Quotas e limites de dimensionamento no seu laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como dimensionar um laboratório no Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5a179c0c6b777ee6b2afdd0f2e9267d247665d8d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38675970"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Quotas e limites de dimensionamento no DevTest Labs
À medida que trabalha no DevTest Labs, pode observar que existem determinados limites predefinidos a alguns recursos do Azure, o que podem afetar o serviço de DevTest Labs. Estes limites são denominados **quotas**.

> [!NOTE]
> O serviço de DevTest Labs não impõe quaisquer quotas. Quaisquer quotas que poderá encontrar são restrições predefinidas da subscrição do Azure em geral.

Pode usar cada recurso do Azure até atingir a quota. Cada subscrição tem quotas separadas e a utilização é controlada por subscrição.

Por exemplo, cada subscrição tem uma quota predefinida de 20 núcleos. Então, se estiver a criar VMs no seu laboratório com quatro núcleos, pode criar apenas cinco VMs. 

[Subscrição e limites do serviço do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) lista algumas das mais comuns quotas para recursos do Azure. Os recursos mais comumente usados num laboratório e, para que pode encontrar as quotas, inclui os núcleos VM, endereços IP públicos, interface de rede, os discos geridos, atribuição de função RBAC e circuitos do ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Ver a utilização e quotas
Estes passos mostram como para ver as quotas atuais na sua subscrição para recursos específicos do Azure e, para ver o percentual de cada quota que utilizou.

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **faturação** da lista.
1. No painel de faturação, selecione uma subscrição.
4. Selecione **utilização + quotas**.

   ![Botão de utilização e quotas](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   A utilização + quotas é apresentado o painel, listagem diferentes recursos disponíveis nessa subscrição e a percentagem da quota que está a ser utilizado por recurso.

   ![Quotas e utilização](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Pedir mais recursos na sua subscrição
Se atingir um limite de quota, o limite predefinido de um recurso numa subscrição pode ser aumentado até um limite máximo, conforme descrito em [subscrição do Azure e limites do serviço](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Estes passos mostram-lhe como pedir um aumento de quota através da [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **mais serviços**, selecione **faturação**e, em seguida, selecione **utilização + quotas**.
1. Na utilização + quotas painel, selecione o **pedir aumentar** botão.

   ![Botão de aumento de pedido](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Para concluir e submeter o pedido, preencha as informações necessárias em todos os três separadores do **novo pedido de suporte** formulário.

   ![Formulário de pedido de aumento](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Compreender limites do Azure e aumenta](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) fornece mais informações sobre como contactar o suporte do Azure para pedir um aumento de quota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Passos Seguintes
* Explore os [Galeria de modelos de início rápido do DevTest Labs do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
