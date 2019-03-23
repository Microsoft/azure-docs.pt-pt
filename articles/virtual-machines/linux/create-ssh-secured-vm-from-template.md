---
title: Criar uma VM do Linux no Azure a partir de um modelo | Documentos da Microsoft
description: Como utilizar a CLI do Azure para criar uma VM do Linux a partir de um modelo do Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 334f69390e4506c6db76c1814f8ec8f1e4417ee9
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372340"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Como criar uma máquina virtual Linux com modelos Azure Resource Manager

Saiba como criar uma máquina virtual (VM) do Linux com um modelo Azure Resource Manager e a CLI do Azure a partir do Azure Cloud shell. Para criar uma máquina virtual do Windows, veja [criar uma máquina virtual do Windows a partir de um modelo do Resource Manager](../windows/ps-template.md).

## <a name="templates-overview"></a>Descrição geral de modelos

Modelos Azure Resource Manager são ficheiros JSON que definem a infraestrutura e a configuração da sua solução do Azure. Ao utilizar um modelo, pode implementar repetidamente a solução durante o ciclo de vida da mesma e ter a confiança de que os recursos são implementados num estado consistente. Para saber mais sobre o formato do modelo e como construí-lo, consulte [início rápido: Criar e implementar modelos Azure Resource Manager com o portal do Azure](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). Para ver a sintaxe JSON para tipos de recursos, consulte [Define resources in Azure Resource Manager templates (Definir recursos nos modelos do Azure Resource Manager)](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Normalmente, a criação de uma máquina virtual do Azure inclui duas etapas:

1. Crie um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual.
1. Cria uma máquina virtual.

O exemplo seguinte cria uma VM a partir de um [modelo de início rápido do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Autenticação de SSH apenas é permitida para esta implementação. Quando solicitado, forneça o valor de sua própria chave pública SSH, como o conteúdo do *~/.ssh/id_rsa.pub*. Se precisar de criar um par de chaves SSH, veja [como criar e utilizar um par de chaves SSH para VMs do Linux no Azure](mac-create-ssh-keys.md). Esta é uma cópia do modelo:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Para executar o script da CLI, selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

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
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey='$key' &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

O último comando da CLI do Azure mostra o endereço IP público da VM criada recentemente. Terá do endereço IP público para ligar à máquina virtual. Veja a secção seguinte deste artigo.

No exemplo anterior, que especificou um modelo armazenado no GitHub. Também pode transferir ou criar um modelo e especifique o caminho local com o `--template-file` parâmetro.

Aqui estão alguns recursos adicionais:

- Para saber como desenvolver modelos do Resource Manager, veja [documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- Para ver os esquemas de máquina virtual do Azure, consulte [referência de modelo do Azure](/azure/templates/microsoft.compute/allversions).
- Para ver mais exemplos de modelos de máquina virtual, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Pode, em seguida, SSH à VM como habitualmente. Fornece-lhe próprio endereço IP público no comando anterior:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Passos Seguintes

Neste exemplo, criou uma VM do Linux básica. Para obter mais modelos do Resource Manager que incluem estruturas de aplicações ou crie ambientes mais complexos, procurar as [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Para saber mais sobre a criação de modelos, veja a sintaxe JSON e propriedades para os tipos de recursos que implementou:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
