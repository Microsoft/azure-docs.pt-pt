---
title: Dimensionar cotas e limites em seu laboratório no Azure DevTest Labs | Microsoft Docs
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
ms.openlocfilehash: 231a227cbccaa4cbf74f3b13839c0ba42895cf24
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644856"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Dimensionar cotas e limites no DevTest Labs
Enquanto trabalha no DevTest Labs, você pode observar que há alguns limites padrão para alguns recursos do Azure, que podem afetar o serviço do DevTest Labs. Esses limites são chamados de **cotas**.

> [!NOTE]
> O serviço DevTest Labs não impõe nenhuma cota. As cotas que você pode encontrar são restrições padrão da assinatura geral do Azure.

Você pode usar cada recurso do Azure até atingir sua cota. Cada subscrição tem quotas separadas e a utilização é controlada por subscrição.

Por exemplo, cada assinatura tem uma cota padrão de 20 núcleos. Portanto, se você estiver criando VMs em seu laboratório com quatro núcleos cada, você só poderá criar cinco VMs.

[Os limites de serviço e assinatura do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) lista algumas das cotas mais comuns para recursos do Azure. Os recursos mais comumente usados em um laboratório e para os quais você pode encontrar cotas, incluem núcleos de VM, endereços IP públicos, interface de rede, Managed disks, atribuição de função RBAC e circuitos de ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Exibir seu uso e cotas
Estas etapas mostram como exibir as cotas atuais em sua assinatura para recursos específicos do Azure e para ver qual percentual de cada cota você usou.

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **cobrança** na lista.
1. Na folha de cobrança, selecione uma assinatura.
4. Selecione **uso + cotas**.

   ![Botão uso e cotas](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   A folha uso + cotas é exibida, listando diferentes recursos disponíveis nessa assinatura e a porcentagem da cota que está sendo usada por recurso.

   ![Cotas e uso](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Solicitando mais recursos em sua assinatura
Se você atingir um limite de cota, o limite padrão de um recurso em uma assinatura poderá ser aumentado até um máximo, conforme descrito em [assinatura do Azure e limites de serviço](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

Estas etapas mostram como solicitar um aumento de cota por meio do [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **mais serviços**, selecione **cobrança**e, em seguida, selecione **uso + cotas**.
1. Na folha uso + cotas, selecione o botão **solicitar aumento** .

   ![Botão solicitar aumento](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Para concluir e enviar a solicitação, preencha as informações necessárias em todas as três guias do formulário **nova solicitação de suporte** .

   ![Formulário de solicitação de aumento](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Entender os limites e as aumentos do Azure](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) fornece mais informações sobre como contatar o suporte do Azure para solicitar um aumento de cota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Passos seguintes
* Explore a [Galeria de modelos do DevTest Labs Azure Resource Manager guia de início rápido](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
