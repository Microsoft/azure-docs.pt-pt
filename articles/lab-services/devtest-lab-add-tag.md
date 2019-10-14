---
title: Adicionar marcas a um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar uma marca a um laboratório no Azure DevTest Labs
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
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302796"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Adicionar marcas a um laboratório no Azure DevTest Labs

Você pode criar marcas personalizadas e aplicá-las aos seus recursos do DevTest Labs para categorizar logicamente seus recursos. Posteriormente, você pode ver com rapidez e facilidade todos os recursos em sua assinatura que têm essa marca. As marcas são úteis quando você precisa organizar os recursos de cobrança ou gerenciamento.

Os recursos com suporte nas marcas incluem

* VMs de computação
* NICs
* Endereços IP
* Balanceadores de carga
* Contas de armazenamento
* Managed Disks

Você pode aplicar marcas ao [criar um laboratório](devtest-lab-create-lab.md) e, posteriormente, gerenciá-los por meio da folha marcas em configuração e configurações.

Cada marca é composta de um par **nome**@no__t**valor** -1. Por exemplo, você pode criar uma marca com o nome *costcenter* que tem um valor de *34543*. Uma marca como esta pode ajudá-lo a identificar mais tarde os recursos de laboratório que são faturáveis para essa área específica da sua organização. Você precisa escolher nomes e valores que façam sentido para o modo como você deseja organizar sua assinatura.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Etapas para gerenciar marcas em um laboratório existente

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista. Seu laboratório já pode ter sido mostrado no painel em **todos os recursos**.
1. Na lista de laboratórios, selecione o laboratório no qual você deseja adicionar ou gerenciar marcas.
1. Na área **visão geral** do laboratório, selecione **configuração e políticas**.

    ![Botão configuração e políticas](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. À esquerda em **gerenciar**, selecione **marcas**.
1. Para criar uma nova marca para este laboratório, insira um **nome**@no__t par**valor** -1 e selecione **salvar**. Você também pode selecionar uma marca existente na lista para exibir ou gerenciar os recursos associados a essa marca.

    ![Gerenciar marcas](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Marcas criadas no nível de laboratório fluem por todos os recursos faturáveis que o laboratório gira em sua assinatura. Por exemplo, as marcas de nível de laboratório fluem para as VMs de computação subjacentes de VMs de laboratório. você pode usar marcas no contexto do gerenciamento de custos. As marcas de nível de laboratório aparecem no filtro de marca para o gerenciamento de custos.

## <a name="understanding-limitations-to-tags"></a>Noções básicas sobre limitações de marcas

As seguintes limitações aplicam-se às etiquetas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nomes/valores de etiquetas. Esta limitação aplica-se apenas a etiquetas diretamente aplicadas ao recurso ou grupo de recursos. Um grupo de recursos pode conter muitos recursos que tenham, cada um, 15 pares de nomes/valores de etiqueta.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.

[Usar marcas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) fornece mais detalhes sobre o uso de marcas no Azure, incluindo como gerenciar marcas usando o PowerShell ou CLI do Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* Você pode aplicar restrições e convenções em sua assinatura usando políticas personalizadas. Uma política que você define pode exigir que todos os recursos tenham um valor para uma determinada marca. Para obter mais informações, consulte [definir políticas e agendamentos](devtest-lab-set-lab-policy.md).
* Explore a [Galeria de modelos do DevTest Labs Azure Resource Manager guia de início rápido](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
