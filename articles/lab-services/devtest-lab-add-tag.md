---
title: Adicionar etiquetas a um laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como adicionar uma etiqueta a um laboratório no Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e4d9aeb527461cc7292235fef1de0abdfa4242bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311373"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Adicionar etiquetas a um laboratório no Azure DevTest Labs

Pode criar etiquetas personalizadas e aplicá-las aos seus recursos do DevTest Labs para categorizar logicamente os recursos. Mais tarde, poderá rápida e facilmente ver todos os recursos na sua subscrição que possua tal marca. As etiquetas são úteis quando precisa organizar os recursos de gestão ou de faturação.

Os recursos que são suportados por etiquetas incluem

* VMs de computação
* NICs
* Endereços IP
* Balanceadores de carga
* Contas de armazenamento
* Managed disks

Pode aplicar etiquetas quando [criar um laboratório](devtest-lab-create-lab.md) e geri-los mais tarde através do painel do etiquetas em configuração e definições.

Cada etiqueta é constituída por um **name**/**valor** par. Por exemplo, pode criar uma etiqueta com o nome *centrodecustos* que tem um valor de *34543*. Uma etiqueta, como este pode ajudá-lo mais tarde identificar recursos de laboratório que são a cobrar para esta área específica da sua organização. Tem de escolher os nomes e valores que façam sentido para a forma como pretende organizar a sua subscrição.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Passos para gerir marcas num laboratório existente

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista. Já pode ser mostrado o laboratório no Dashboard em **todos os recursos**.
1. Na lista de laboratórios, selecione o laboratório em que pretende adicionar ou gerir etiquetas.
1. O laboratório **descrição geral** área, selecione **Konfigurace a zásady**.

    ![Botão de configuração e políticas](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. À esquerda sob **MANAGE**, selecione **etiquetas**.
1. Para criar uma nova etiqueta para este laboratório, introduza um **Name**/**valor** emparelhar e selecione **guardar**. Também pode selecionar uma etiqueta existente na lista para ver ou gerir os recursos associados essa marca.

    ![Gerir etiquetas](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Limitações de compreensão de etiquetas

As seguintes limitações aplicam-se às etiquetas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nomes/valores de etiquetas. Esta limitação aplica-se apenas a etiquetas diretamente aplicadas ao recurso ou grupo de recursos. Um grupo de recursos pode conter muitos recursos que tenham, cada um, 15 pares de nomes/valores de etiqueta.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.

[Utilizar etiquetas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) fornece mais detalhes sobre como utilizar as etiquetas no Azure, incluindo como gerir etiquetas com o PowerShell ou da CLI do Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
* Pode aplicar restrições e convenções na sua subscrição ao utilizar políticas personalizadas. Uma política que definir pode exigir que todos os recursos tenham um valor para uma etiqueta específica. Para obter mais informações, consulte [definir políticas e agendas](devtest-lab-set-lab-policy.md).
* Explore os [Galeria de modelos de início rápido do DevTest Labs do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
