---
title: 'Quickstart: Use um modelo de Gestor de Recursos para criar um Ubuntu Linux VM'
description: Neste arranque rápido, você aprende a usar um modelo de Gestor de Recursos para criar uma máquina virtual Linux
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 97a9d3632cb0e78b899844b1aaa84a030c6da23e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549622"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-an-arm-template"></a>Quickstart: Criar uma máquina virtual Ubuntu Linux usando um modelo ARM

Este quickstart mostra-lhe como usar um modelo de Gestor de Recursos Azure (modelo ARM) para implantar uma máquina virtual Ubuntu Linux (VM) em Azure.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-linux/azuredeploy.json":::


Vários recursos são definidos no modelo:

- [**Microsoft.Network/virtualNetworks/subnets:**](/azure/templates/Microsoft.Network/virtualNetworks/subnets)criar uma sub-rede.
- [**Microsoft.Storage/storageAstas:**](/azure/templates/Microsoft.Storage/storageAccounts)criar uma conta de armazenamento.
- [**Microsoft.Network/networkInterfaces:**](/azure/templates/Microsoft.Network/networkInterfaces)criar um NIC.
- [**Microsoft.Network/networkSecurityGroups:**](/azure/templates/Microsoft.Network/networkSecurityGroups)criar um grupo de segurança de rede.
- [**Microsoft.Network/virtualNetworks:**](/azure/templates/Microsoft.Network/virtualNetworks)criar uma rede virtual.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): criar um endereço IP público.
- [**Microsoft.Compute/virtualMachines:**](/azure/templates/Microsoft.Compute/virtualMachines)criar uma máquina virtual.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um cofre chave e um segredo.

    [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. Selecione ou introduza os seguintes valores. Utilize os valores predefinidos, quando disponível.

    - **Subscrição**: selecione uma subscrição do Azure.
    - **Grupo de recursos**: selecione um grupo de recursos existente a partir do drop-down, ou selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, clique em **OK**.
    - **Localização**: selecione uma localização.  Por exemplo, **E.U.A. Central**.
    - **Nome de utilizador Admin**: fornecer um nome de utilizador, como *azureuser*.
    - **Tipo de autenticação**: Pode escolher entre utilizar uma chave SSH ou uma palavra-passe.
    - **Palavra-passe ou chave** de administração dependendo do que escolher para o tipo de autenticação:
        - Se escolher a **palavra-passe,** a palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).
        - Se escolher **sshPublicKey,** cole o conteúdo da sua chave pública.
    - **Prefixo da etiqueta DNS**: introduza um identificador único para utilizar como parte da etiqueta DNS.
    - **Versão Ubuntu OS**: selecione qual a versão de Ubuntu que pretende executar no VM.
    - **Localização:** o padrão é a mesma localização que o grupo de recursos, se já existir.
    - **Tamanho VM**: selecione o [tamanho](../sizes.md) a utilizar para o VM.
    - **Nome da rede virtual**: nome a utilizar para o vNet.
    - **Nome da sub-rede**: nome para a sub-rede que o VM deve utilizar.
    - **Nome do grupo de segurança da** rede : nome para o NSG.
1. Selecione **Rever + criar**. Após a validação concluída, selecione **Criar** para criar e implementar o VM.


O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar o Azure CLI, Azure PowerShell e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../../azure-resource-manager/templates/deploy-cli.md).

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
> [Tutoriais das máquinas virtuais do Linux do Azure](./tutorial-manage-vm.md)