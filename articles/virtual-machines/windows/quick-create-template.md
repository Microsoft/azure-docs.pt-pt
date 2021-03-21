---
title: 'Quickstart: Use um modelo de Gestor de Recursos para criar um VM do Windows'
description: Neste arranque rápido, aprende-se a usar um modelo de Gestor de Recursos para criar uma máquina virtual do Windows
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: b2856c51ccc31b57ea567c5d0940cd260797f770
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560757"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-an-arm-template"></a>Quickstart: Criar uma máquina virtual do Windows utilizando um modelo ARM

Este quickstart mostra-lhe como usar um modelo de Gestor de Recursos Azure (modelo ARM) para implementar uma máquina virtual Do Windows (VM) em Azure.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json":::


Vários recursos são definidos no modelo:

- [**Microsoft.Network/virtualNetworks/subnets:**](/azure/templates/Microsoft.Network/virtualNetworks/subnets)criar uma sub-rede.
- [**Microsoft.Storage/storageAstas:**](/azure/templates/Microsoft.Storage/storageAccounts)criar uma conta de armazenamento.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): criar um endereço IP público.
- [**Microsoft.Network/networkSecurityGroups:**](/azure/templates/Microsoft.Network/networkSecurityGroups)criar um grupo de segurança de rede.
- [**Microsoft.Network/virtualNetworks:**](/azure/templates/Microsoft.Network/virtualNetworks)criar uma rede virtual.
- [**Microsoft.Network/networkInterfaces:**](/azure/templates/Microsoft.Network/networkInterfaces)criar um NIC.
- [**Microsoft.Compute/virtualMachines:**](/azure/templates/Microsoft.Compute/virtualMachines)criar uma máquina virtual.



## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um cofre chave e um segredo.

    [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. Selecione ou introduza os seguintes valores. Utilize os valores predefinidos, quando disponível.

    - **Subscrição**: selecione uma subscrição do Azure.
    - **Grupo de recursos**: selecione um grupo de recursos existente a partir do drop-down, ou selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, clique em **OK**.
    - **Localização**: selecione uma localização.  Por exemplo, **E.U.A. Central**.
    - **Nome de utilizador Admin**: fornecer um nome de utilizador, como *azureuser*.
    - **Senha de administração**: forneça uma palavra-passe para a conta de administração. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).
    - **Prefixo da etiqueta DNS**: introduza um identificador único para utilizar como parte da etiqueta DNS.
    - **Versão Windows OS**: selecione qual a versão do Windows que pretende executar no VM.
    - **Tamanho VM**: selecione o [tamanho](../sizes.md) a utilizar para o VM.
    - **Localização:** o padrão é a mesma localização que o grupo de recursos, se já existir.
1. Selecione **Rever + criar**. Após a validação concluída, selecione **Criar** para criar e implementar o VM.


O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para verificar o VM e outros recursos que foram criados. Depois de concluída a implementação, selecione **Ir ao grupo de recursos** para ver o VM e outros recursos.


## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina o VM e todos os recursos do grupo de recursos. 

1. Selecione o **grupo De Recursos**.
1. Na página para o grupo de recursos, **selecione Delete**.
1. Quando solicitado, digite o nome do grupo de recursos e, em seguida, **selecione Delete**.


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, lançou uma máquina virtual simples utilizando um modelo ARM. Para saber mais sobre as máquinas virtuais do Azure, continue para o tutorial das VMs do Linux.


> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](./tutorial-manage-vm.md)