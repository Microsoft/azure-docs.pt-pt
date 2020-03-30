---
title: Crie um Linux VM em Azure a partir de um modelo
description: Como usar o Azure CLI para criar um VM Linux a partir de um modelo de Gestor de Recursos
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 581eadc60835b758f67ae616d4413800f1d6d718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969517"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Como criar uma máquina virtual Linux com modelos de Gestor de Recursos Azure

Aprenda a criar uma máquina virtual Linux (VM) utilizando um modelo de Gestor de Recursos Azure e o Azure CLI da concha Azure Cloud. Para criar uma máquina virtual do Windows, consulte [Criar uma máquina virtual do Windows a partir de um modelo de Gestor de Recursos](../windows/ps-template.md).

## <a name="templates-overview"></a>Descrição geral de modelos

Os modelos do Gestor de Recursos Azure são ficheiros JSON que definem a infraestrutura e configuração da sua solução Azure. Ao utilizar um modelo, pode implementar repetidamente a solução durante o ciclo de vida da mesma e ter a confiança de que os recursos são implementados num estado consistente. Para saber mais sobre o formato do modelo e como o constrói, consulte [Quickstart: Crie e implemente modelos](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)do Gestor de Recursos Azure utilizando o portal Azure . Para ver a sintaxe JSON para tipos de recursos, consulte [Define resources in Azure Resource Manager templates (Definir recursos nos modelos do Azure Resource Manager)](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

A criação de uma máquina virtual Azure geralmente inclui dois passos:

1. Crie um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual.
1. Cria uma máquina virtual.

O exemplo seguinte cria um VM a partir de um [modelo Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Apenas é permitida a autenticação SSH para esta implementação. Quando solicitado, forneça o valor da sua própria chave pública SSH, como o conteúdo de *~/.ssh/id_rsa.pub*. Se precisar de criar um par de chaves SSH, consulte como criar e utilizar um par de [chaves SSH para VMs Linux em Azure](mac-create-ssh-keys.md). Aqui está uma cópia do modelo:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Para executar o script CLI, selecione **Experimente-o** para abrir a concha Azure Cloud. Para colar o script, clique na concha e, em seguida, selecione **Pasta:**

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

O último comando Azure CLI mostra o endereço IP público do VM recém-criado. Precisa do endereço IP público para ligar à máquina virtual. Veja a próxima secção deste artigo.

No exemplo anterior, especificou um modelo armazenado no GitHub. Também pode descarregar ou criar um modelo e `--template-file` especificar o caminho local com o parâmetro.

Seguem-se alguns recursos adicionais:

- Para aprender a desenvolver modelos de Gestor de Recursos, consulte a [documentação do Gestor de Recursos do Azure.](/azure/azure-resource-manager/)
- Para ver os esquemas de máquinas virtuais Azure, consulte a referência do [modelo Azure](/azure/templates/microsoft.compute/allversions).
- Para ver mais amostras de modelos de máquinas virtuais, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Em seguida, pode sSH para o seu VM normalmente. Forneça-lhe o seu próprio endereço IP público a partir do comando anterior:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Passos seguintes

Neste exemplo, criou um VM Linux básico. Para mais modelos de Gestor de Recursos que incluam estruturas de aplicação ou criam ambientes mais complexos, navegue nos [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Para saber mais sobre a criação de modelos, consulte a sintaxe jSON e propriedades para os tipos de recursos que implementou:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresss](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
