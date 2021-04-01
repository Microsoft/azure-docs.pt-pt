---
title: Ver e usar o modelo de gestor de recursos Azure de uma máquina virtual
description: Saiba como usar o modelo Azure Resource Manager a partir de uma máquina virtual para criar outros VMs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fb7cb38dfb2c1664017f0e342fefecdd464df45f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696011"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Criar máquinas virtuais usando um modelo de Gestor de Recursos Azure 

Quando estiver a criar uma máquina virtual (VM) em Laboratórios DevTest através do [portal Azure,](https://go.microsoft.com/fwlink/p/?LinkID=525040)pode ver o modelo Azure Resource Manager antes de guardar o VM. O modelo pode então ser usado como base para criar mais VMs de laboratório com as mesmas definições.

Este artigo descreve modelos multi-VM vs. single-VM Resource Manager e mostra-lhe como ver e guardar um modelo ao criar um VM.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Multi-VM vs. modelos de gestor de recursos single-VM
Existem duas formas de criar VMs em DevTest Labs usando um modelo de Gestor de Recursos: provisão do recurso Microsoft.DevTestLab/labs/virtualmachines ou provisão do recurso Microsoft.Compute/virtualmachines. Cada um é usado em diferentes cenários e requer diferentes permissões.

- Os modelos de gestor de recursos que usam um tipo de recurso Microsoft.DevTestLab/labs/virtualmachines (conforme declarado na propriedade "recursos" no modelo) podem providenciar VMs de laboratório individuais. Cada VM aparece então como um único item na lista de máquinas virtuais da DevTest Labs:

   ![Screenshot que mostra a lista de máquinas virtuais como itens únicos na lista de máquinas virtuais DavTest Labs.](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Este tipo de modelo de Gestor de Recursos pode ser fornecido através do comando Azure PowerShell **New-AzResourceGroupDeployment** ou através do **grupo de implementação de** comando Azure CLI. Requer permissões de administrador, para que os utilizadores que são designados com uma função de utilizador da DevTest Labs não possam executar a implementação. 

- Os modelos de Gestor de Recursos que utilizam um tipo de recurso Microsoft.Compute/virtualmachines podem fornecer vários VMs como um único ambiente na lista de máquinas virtuais DevTest Labs:

   ![Lista de VMs como itens individuais na lista de máquinas virtuais da DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Os VMs no mesmo ambiente podem ser geridos em conjunto e partilhar o mesmo ciclo de vida. Os utilizadores que são designados com uma função de utilizador da DevTest Labs podem criar ambientes usando esses modelos desde que o administrador tenha configurado o laboratório dessa forma.

O restante deste artigo discute os modelos do Gestor de Recursos que utilizam o Microsoft.DevTestLab/labs/virtualmachines. Estes são usados por administradores de laboratório para automatizar a criação de VM de laboratório (por exemplo, VMs reivindicaíveis) ou geração de imagem dourada (por exemplo, fábrica de imagem).

[As melhores práticas para criar modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/template-best-practices.md) oferecem muitas diretrizes e sugestões para ajudá-lo a criar modelos de Gestor de Recursos Azure que são fiáveis e fáceis de usar.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Ver e guardar o modelo de Gestor de Recursos de uma máquina virtual
1. Siga os passos na [Create your first VM in a lab](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) para começar a criar uma máquina virtual.
1. Introduza as informações necessárias para a sua máquina virtual e adicione todos os artefactos que desejar para este VM.
1. Mude para o **separador Definições Avançadas.** 
1. Na parte inferior da janela configurar definições, escolha **Ver MODELO ARM**.
1. Copie e guarde o modelo de Gestor de Recursos para usar mais tarde para criar outra máquina virtual.

   ![Modelo de gestor de recursos para guardar para posterior utilização](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Depois de ter guardado o modelo de Gestor de Recursos, tem de atualizar a secção de parâmetros do modelo antes de o poder utilizar. Pode criar uma parameter.jsque personalize apenas os parâmetros, fora do modelo real de Gestor de Recursos. 

![Personalize parâmetros usando um ficheiro JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

O modelo de Gestor de Recursos está agora pronto a ser utilizado para [criar um VM](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Definir data de validade
Em cenários como treino, demonstrações e testes, pode querer criar máquinas virtuais e eliminá-las automaticamente após uma duração fixa para que não incorram em custos desnecessários. Pode criar um VM de laboratório com uma data de validade especificando a propriedade **expiraçãoda** para o VM. Confira o mesmo modelo de Gestor de Recursos no [nosso repositório GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration)



### <a name="next-steps"></a>Passos seguintes
* Saiba como [criar ambientes multi-VM com modelos de Gestor de Recursos.](devtest-lab-create-environment-from-arm.md)
* [Implemente um modelo de Gestor de Recursos para criar um VM](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Explore modelos mais rápidos de Gestor de Recursos para automação de DevTest Labs a partir do [repo de DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
