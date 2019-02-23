---
title: Ver e utilizar o modelo do Azure Resource Manager de uma máquina virtual | Documentos da Microsoft
description: Saiba como utilizar o modelo Azure Resource Manager de uma máquina virtual para criar outras VMs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 663864837e2847125d0925b9b82d8962e54290b3
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726487"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Criar máquinas virtuais com um modelo Azure Resource Manager 

Quando está a criar uma máquina virtual (VM) no DevTest Labs através da [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), pode ver o modelo Azure Resource Manager antes de guardar a VM. O modelo, em seguida, pode ser usado como base para criar o laboratório de mais VMs com as mesmas definições.

Este artigo descreve várias VMS versus modelos do Resource Manager de VM única e mostra-lhe como ver e guardar um modelo ao criar uma VM.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Várias VMS versus modelos do Resource Manager de VM única
Existem duas formas de criar VMs nos laboratórios DevTest através de um modelo do Resource Manager: aprovisionar o recurso de Microsoft.DevTestLab/labs/virtualmachines ou aprovisionar o recurso de Microsoft.Commpute/virtualmachines. Cada um é utilizado em cenários diferentes e deve ter permissões diferentes.

- Modelos do Resource Manager que utilizam um tipo de recurso Microsoft.DevTestLab/labs/virtualmachines (como declarado na propriedade "recurso" no modelo) podem aprovisionar VMs do laboratório individuais. Cada VM, em seguida, mostrado como um único item na lista de máquinas virtuais do DevTest Labs:

   ![Lista de VMs como únicos itens da lista de máquinas virtuais do DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Este tipo de modelo do Resource Manager pode ser aprovisionado através do comando do Azure PowerShell **New-AzResourceGroupDeployment** ou através do comando da CLI do Azure **criar a implementação do grupo az**. Requer permissões de administrador, para que os utilizadores que tiver sido atribuídos uma função de utilizador de DevTest Labs não é possível efetuar a implementação. 

- Modelos do Resource Manager que utilizam um tipo de recurso Microsoft.Compute/virtualmachines podem aprovisionar várias VMs como um único ambiente na lista de máquinas virtuais do DevTest Labs:

   ![Lista de VMs como únicos itens da lista de máquinas virtuais do DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   As VMs no mesmo ambiente podem ser geridas em conjunto e partilham o mesmo ciclo de vida. Os utilizadores que tiver sido atribuídos uma função de utilizador de DevTest Labs, podem criar ambientes usando esses modelos, desde que o administrador tenha configurado o laboratório, dessa forma.

O restante deste artigo aborda os modelos do Resource Manager que utilizam Microsoft.DevTestLab/labs/virtualmachines. Estes são utilizados por administradores de laboratório para automatizar a criação de VM de laboratório (por exemplo, VMs reclamáveis) ou de geração de imagem dourada (por exemplo, a fábrica de imagem).

[Melhores práticas para a criação de modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferece muitas orientações e sugestões para ajudar a criar modelos Azure Resource Manager que são confiáveis e fáceis de usar.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Ver e guardar o modelo do Resource Manager de uma máquina virtual
1. Siga os passos indicados em [criar a sua primeira VM num laboratório](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) para começar a criar uma máquina virtual.
1. Introduza as informações necessárias para a máquina virtual e adicione quaisquer artefactos de que pretende para esta VM.
1. Na parte inferior da janela configurar as definições, escolha **modelo de ARM de vista**.

   ![Botão de modelo ARM do Vista](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Copie e guarde o modelo do Resource Manager para utilizar mais tarde para criar outra máquina virtual.

   ![Modelo do Resource Manager para guardar para utilização posterior](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Depois de guardar o modelo do Resource Manager, tem de atualizar a secção de parâmetros do modelo antes de poder utilizá-lo. Pode criar um parameter.json que personaliza a apenas os parâmetros fora o modelo do Resource Manager. 

![Personalizar os parâmetros através de um ficheiro JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Está agora pronto para utilizar para o modelo do Resource Manager [criar uma VM](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Passos Seguintes
* Saiba como [criar ambientes multi-VM com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Implementar um modelo do Resource Manager para criar uma VM](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm)
* Explore mais modelos do Resource Manager de início rápido para automatização de DevTest Labs dos [repositório público do GitHub de laboratórios DevTest](https://github.com/Azure/azure-quickstart-templates).
