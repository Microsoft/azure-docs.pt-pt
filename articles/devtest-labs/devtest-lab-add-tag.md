---
title: Adicione etiquetas a um laboratório em Azure DevTest Labs Microsoft Docs
description: Aprenda a criar tags personalizadas em Azure DevTest Labs e use tags para categorizar recursos. Pode ver todos os recursos da sua subscrição que têm uma etiqueta.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a4d2b328626f0d0f096082fa88bb170894ccf3e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270738"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Adicionar tags a um laboratório em Azure DevTest Labs

Pode criar etiquetas personalizadas e aplicá-las aos recursos da DevTest Labs para categorizar logicamente os seus recursos. Mais tarde, pode ver de forma rápida e fácil todos os recursos da sua subscrição que tenham essa etiqueta. As etiquetas são úteis quando precisa de organizar recursos para faturação ou gestão.

Os recursos que são suportados por tags incluem

* Compute VMs
* NICs
* Endereços IP
* Balanceadores de carga
* Contas de armazenamento
* Managed disks

Pode aplicar tags quando [criar um laboratório](devtest-lab-create-lab.md) e, mais tarde, geri-las através da lâmina Tags por configuração e configurações.

Cada etiqueta é composta por um par de valor **de nome.** / **value** Por exemplo, pode criar uma etiqueta com o *nome costcenter* que tem um valor de *34543*. Uma etiqueta como esta pode ajudá-lo a identificar mais tarde recursos de laboratório que são faturais para esta área específica da sua organização. Pode escolher nomes e valores que façam sentido para a forma como pretende organizar a sua subscrição.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Passos para gerir etiquetas num laboratório existente

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista. O seu laboratório pode já estar no Painel de Instrumentos sob **Todos os Recursos.**
1. A partir da lista de laboratórios, selecione o laboratório no qual pretende adicionar ou gerir tags.
1. Na área de **visão geral** do laboratório, selecione **Configuração e políticas**.

    ![Botão de configuração e políticas](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. À esquerda em **MANAGE**, selecione **Tags**.
1. Para criar uma nova etiqueta para **Name**este laboratório, insira um par / **de valor de** nome e selecione **Save**. Também pode selecionar uma etiqueta existente na lista para visualizar ou gerir os recursos associados a essa etiqueta.

    ![Gerir tags](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> As etiquetas criadas ao nível do laboratório fluem através de todos os recursos faturantes que o laboratório gira na sua assinatura. Por exemplo, as etiquetas de nível de laboratório fluem para os VMs computados subjacentes de VMs de laboratório.Você pode usar tags no contexto da gestão de custos. As etiquetas de nível de laboratório aparecem no filtro de etiquetas para a gestão de custos.

## <a name="understanding-limitations-to-tags"></a>Compreender limitações às etiquetas

As seguintes limitações aplicam-se às etiquetas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nomes/valores de etiquetas. Esta limitação aplica-se apenas a etiquetas diretamente aplicadas ao recurso ou grupo de recursos. Um grupo de recursos pode conter muitos recursos que tenham, cada um, 15 pares de nomes/valores de etiqueta.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.

[Use tags para organizar os seus recursos Azure](../azure-resource-manager/management/tag-resources.md) fornece mais detalhes sobre a utilização de tags em Azure, incluindo como gerir tags usando PowerShell ou Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* Pode aplicar restrições e convenções em toda a sua subscrição utilizando políticas personalizadas. Uma política que você define pode exigir que todos os recursos têm um valor para uma determinada etiqueta. Para obter mais informações, consulte [Definir políticas e horários.](devtest-lab-set-lab-policy.md)
* Explore a [galeria de modelos Quickstart DavTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
