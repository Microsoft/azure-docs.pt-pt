---
title: Exibir e usar o modelo de Azure Resource Manager de uma máquina virtual | Microsoft Docs
description: Saiba como usar o modelo de Azure Resource Manager de uma máquina virtual para criar outras VMs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: c14abf3acce0084507a03f3d34fdd59566d88c28
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854280"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Criar máquinas virtuais usando um modelo de Azure Resource Manager 

Ao criar uma VM (máquina virtual) no DevTest Labs por meio do [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), você pode exibir o modelo de Azure Resource Manager antes de salvar a VM. O modelo pode então ser usado como base para criar mais VMs de laboratório com as mesmas configurações.

Este artigo descreve os modelos de várias VMs vs. do Resource Manager de VM única e mostra como exibir e salvar um modelo ao criar uma VM.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Modelos do Gerenciador de recursos de várias VMs versus uma VM única
Há duas maneiras de criar VMs no DevTest Labs usando um modelo do Resource Manager: provisione o recurso Microsoft. DevTestLab/Labs/VirtualMachines ou provisione o recurso Microsoft. Compute/VirtualMachines. Cada um é usado em cenários diferentes e requer permissões diferentes.

- Os modelos do Resource Manager que usam um tipo de recurso Microsoft. DevTestLab/Labs/VirtualMachines (como declarado na propriedade "Resource" no modelo) podem provisionar VMs de laboratório individuais. Em seguida, cada VM aparece como um único item na lista de máquinas virtuais do DevTest Labs:

   ![Lista de VMs como itens únicos na lista de máquinas virtuais do DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Esse tipo de modelo do Resource Manager pode ser provisionado por meio do comando Azure PowerShell **New-AzResourceGroupDeployment** ou por meio do comando CLI do Azure **AZ Group Deployment Create**. Ele requer permissões de administrador, para que os usuários atribuídos com uma função de usuário do DevTest Labs não possam realizar a implantação. 

- Os modelos do Resource Manager que usam um tipo de recurso Microsoft. Compute/VirtualMachines podem provisionar várias VMs como um único ambiente na lista de máquinas virtuais do DevTest Labs:

   ![Lista de VMs como itens únicos na lista de máquinas virtuais do DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   As VMs no mesmo ambiente podem ser gerenciadas em conjunto e compartilhar o mesmo ciclo de vida. Os usuários atribuídos com uma função de usuário do DevTest Labs podem criar ambientes usando esses modelos, contanto que o administrador tenha configurado o laboratório dessa maneira.

O restante deste artigo discute os modelos do Resource Manager que usam Microsoft. DevTestLab/Labs/VirtualMachines. Eles são usados por administradores de laboratório para automatizar a criação da VM do laboratório (por exemplo, VMs declaráveis) ou geração de imagem dourada (por exemplo, a fábrica de imagens).

[As práticas recomendadas para a criação de modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferecem muitas diretrizes e sugestões para ajudá-lo a criar modelos de Azure Resource Manager que são confiáveis e fáceis de usar.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Exibir e salvar o modelo do Resource Manager de uma máquina virtual
1. Siga as etapas em [criar sua primeira VM em um laboratório](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) para começar a criar uma máquina virtual.
1. Insira as informações necessárias para sua máquina virtual e adicione os artefatos desejados para essa VM.
1. Swtich na guia **Configurações avançadas** . 
1. Na parte inferior da janela definir configurações, escolha **Exibir modelo do ARM**.
1. Copie e salve o modelo do Resource Manager para usar posteriormente para criar outra máquina virtual.

   ![Modelo do Resource Manager para salvar para uso posterior](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Depois de salvar o modelo do Resource Manager, você deve atualizar a seção de parâmetros do modelo antes de poder usá-lo. Você pode criar um Parameter. JSON que personaliza apenas os parâmetros, fora do modelo real do Resource Manager. 

![Personalizar parâmetros usando um arquivo JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

O modelo do Resource Manager agora está pronto para ser usado para [criar uma VM](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Definir data de validade
Em cenários como treinamento, demonstrações e avaliações, talvez você queira criar máquinas virtuais e excluí-las automaticamente após uma duração fixa para que não incorra em custos desnecessários. Você pode criar uma VM de laboratório com uma data de expiração especificando a propriedade **expirationDate** para a VM. Confira o mesmo modelo do Resource Manager em [nosso repositório GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Passos seguintes
* Saiba como [criar ambientes multi-VM com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Implantar um modelo do Resource Manager para criar uma VM](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Explore mais modelos do Resource Manager de início rápido para automatização de DevTest Labs dos [repositório público do GitHub de laboratórios DevTest](https://github.com/Azure/azure-quickstart-templates).
