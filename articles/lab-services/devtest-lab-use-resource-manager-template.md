---
title: Ver e usar o modelo de gestor de recursos azure da máquina virtual
description: Aprenda a usar o modelo de Gestor de Recursos Azure a partir de uma máquina virtual para criar outros VMs
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 7064fdeec04f4dc5ae2c73c1a3896cf2d10dd01d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169122"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Crie máquinas virtuais usando um modelo de Gestor de Recursos Azure 

Quando estiver a criar uma máquina virtual (VM) em DevTest Labs através do [portal Azure,](https://go.microsoft.com/fwlink/p/?LinkID=525040)pode ver o modelo do Gestor de Recursos Azure antes de guardar o VM. O modelo pode então ser usado como base para criar mais VMs de laboratório com as mesmas configurações.

Este artigo descreve modelos multi-VM vs. single-VM Resource Manager e mostra-lhe como ver e guardar um modelo ao criar um VM.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Modelos de Gestor de Recursos Multi-VM vs. single-VM
Existem duas formas de criar VMs em DevTest Labs usando um modelo de Gestor de Recursos: fornecer o recurso Microsoft.DevTestLab/labs/virtualmachines ou fornecer o recurso Microsoft.Compute/virtualmachines. Cada um é usado em diferentes cenários e requer permissões diferentes.

- Os modelos do Gestor de Recursos que utilizam um tipo de recurso Microsoft.DevTestLab/labs/virtualmachines (conforme declarado na propriedade "recurso" no modelo) podem fornecer VMs individuais de laboratório. Cada VM aparece então como um único item na lista de máquinas virtuais DevTest Labs:

   ![Lista de VMs como itens únicos na lista de máquinas virtuais DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Este tipo de modelo de Gestor de Recursos pode ser provisionado através do comando Azure PowerShell **New-AzResourceGroupDeployment** ou através da criação de **implementação**do grupo Decomando Azure CLI . Requer permissões de administrador, para que os utilizadores que são atribuídos com uma função de utilizador do DevTest Labs não possam executar a implementação. 

- Os modelos do Gestor de Recursos que utilizam um tipo de recurso Microsoft.Compute/virtualmachines podem fornecer vários VMs como um único ambiente na lista de máquinas virtuais DevTest Labs:

   ![Lista de VMs como itens únicos na lista de máquinas virtuais DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   As VMs no mesmo ambiente podem ser geridas em conjunto e partilhar o mesmo ciclo de vida. Os utilizadores que são atribuídos com uma função de utilizador do DevTest Labs podem criar ambientes usando esses modelos desde que o administrador tenha configurado o laboratório dessa forma.

O restante deste artigo discute os modelos do Gestor de Recursos que utilizam o Microsoft.DevTestLab/labs/virtualmachines. Estes são usados por administrações de laboratório para automatizar criação de VM de laboratório (por exemplo, VMs reivindicadas) ou geração de imagem dourada (por exemplo, fábrica de imagem).

[As melhores práticas para criar modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) de Gestor de Recursos Azure oferecem muitas diretrizes e sugestões para ajudá-lo a criar modelos de Gestor de Recursos Azure que são fiáveis e fáceis de usar.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Ver e guardar o modelo de Gestor de Recursos da máquina virtual
1. Siga os passos na [Create your first VM em laboratório](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) para começar a criar uma máquina virtual.
1. Introduza as informações necessárias para a sua máquina virtual e adicione todos os artefactos que desejar para este VM.
1. Swtich para o separador **Definições Avançadas.** 
1. Na parte inferior da janela de configurações configure, escolha modelo **'Ver ARM**' .
1. Copie e guarde o modelo de Gestor de Recursos para usar mais tarde para criar outra máquina virtual.

   ![Modelo de Gestor de Recursos para guardar para uso posterior](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Depois de ter guardado o modelo de Gestor de Recursos, tem de atualizar a secção de parâmetros do modelo antes de o poder utilizar. Você pode criar um parâmetro.json que personaliza apenas os parâmetros, fora do modelo real de Gestor de Recursos. 

![Personalize os parâmetros usando um ficheiro JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

O modelo de Gestor de Recursos está agora pronto a ser utilizado para [criar um VM](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Definir data de validade
Em cenários como treino, demonstrações e ensaios, pode querer criar máquinas virtuais e eliminá-las automaticamente após uma duração fixa para que não incorra em custos desnecessários. Pode criar um VM de laboratório com uma data de validade especificando a propriedade **expiraçãoData** para o VM. Confira o mesmo modelo de Gestor de Recursos no [nosso repositório GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Passos seguintes
* Saiba como [criar ambientes multi-VM com modelos](devtest-lab-create-environment-from-arm.md)de Gestor de Recursos .
* [Implementar um modelo de Gestor de Recursos para criar um VM](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Explore modelos mais rápidos de Gestor de Recursos para automatização de DevTest Labs do [público DevTest Labs GitHub repo](https://github.com/Azure/azure-quickstart-templates).
