---
title: Adicione etiquetas a um laboratório em Azure DevTest Labs [ Laboratórios DevTest] Microsoft Docs
description: Saiba como adicionar uma etiqueta a um laboratório em Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 1d7175d000eebd3f68665e7f097bbe3f68025bc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72302796"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Adicione etiquetas a um laboratório em Azure DevTest Labs

Pode criar etiquetas personalizadas e aplicá-las aos seus recursos de DevTest Labs para categorizar logicamente os seus recursos. Mais tarde, pode ver de forma rápida e fácil todos os recursos da sua subscrição que têm essa etiqueta. As etiquetas são úteis quando precisa de organizar recursos para faturação ou gestão.

Os recursos que são suportados por tags incluem

* VMs computacionais
* NICs
* Endereços IP
* Balanceadores de carga
* Contas de armazenamento
* Managed disks

Pode aplicar etiquetas quando [criar um laboratório](devtest-lab-create-lab.md) e, mais tarde, geri-las através da lâmina de tags em Configuração e configurações.

Cada etiqueta é composta por um par de**valor** de **nome.**/ Por exemplo, pode criar uma etiqueta com o centro de *custos* de nome que tem um valor de *34543*. Uma etiqueta como esta pode ajudá-lo a identificar mais tarde os recursos de laboratório que são faturados para esta área específica da sua organização. Pode escolher nomes e valores que façam sentido para a forma como pretende organizar a sua subscrição.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Passos para gerir etiquetas em um laboratório existente

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista. O seu laboratório pode já estar no Dashboard sob **todos os recursos.**
1. A partir da lista de laboratórios, selecione o laboratório no qual pretende adicionar ou gerir etiquetas.
1. Na área de **visão geral** do laboratório, selecione **Configuração e políticas**.

    ![Botão de configuração e políticas](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. À esquerda sob **GEIR,** selecione **Tags**.
1. Para criar uma nova etiqueta para este laboratório, introduza um**par** de valor de **nome**/e selecione **Guardar**. Também pode selecionar uma etiqueta existente da lista para visualizar ou gerir os recursos associados a essa etiqueta.

    ![Gerir etiquetas](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> As etiquetas criadas ao nível do laboratório fluem através de todos os recursos faturados que o laboratório gira na sua assinatura. Por exemplo, as etiquetas de nível de laboratório fluem para os VMs de computação subjacentes de VMs de laboratório.Você pode usar tags no contexto da gestão de custos. As etiquetas de nível de laboratório aparecem no filtro de etiquetas para a gestão de custos.

## <a name="understanding-limitations-to-tags"></a>Compreender limitações às etiquetas

As seguintes limitações aplicam-se às etiquetas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nomes/valores de etiquetas. Esta limitação aplica-se apenas a etiquetas diretamente aplicadas ao recurso ou grupo de recursos. Um grupo de recursos pode conter muitos recursos que tenham, cada um, 15 pares de nomes/valores de etiqueta.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.

[As etiquetas de utilização para organizar os seus recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) fornecem mais detalhes sobre a utilização de tags em Azure, incluindo como gerir tags usando o PowerShell ou o Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* Pode aplicar restrições e convenções em toda a sua subscrição utilizando políticas personalizadas. Uma política que define pode exigir que todos os recursos tenham um valor para uma determinada etiqueta. Para mais informações, consulte [definir políticas e horários.](devtest-lab-set-lab-policy.md)
* Explore a galeria de [modelos quickstart Do DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
