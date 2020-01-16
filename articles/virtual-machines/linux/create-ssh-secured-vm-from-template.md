---
title: Criar uma VM do Linux no Azure por meio de um modelo
description: Como usar o CLI do Azure para criar uma VM do Linux com base em um modelo do Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
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
ms.openlocfilehash: cf440542e8b16231514447ddcddca081b4b89368
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979059"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Como criar uma máquina virtual Linux com modelos de Azure Resource Manager

Saiba como criar uma VM (máquina virtual) do Linux usando um modelo de Azure Resource Manager e o CLI do Azure do Azure cloud Shell. Para criar uma máquina virtual do Windows, consulte [criar uma máquina virtual do Windows a partir de um modelo do Resource Manager](../windows/ps-template.md).

## <a name="templates-overview"></a>Visão geral de modelos

Os modelos de Azure Resource Manager são arquivos JSON que definem a infraestrutura e a configuração de sua solução do Azure. Ao utilizar um modelo, pode implementar repetidamente a solução durante o ciclo de vida da mesma e ter a confiança de que os recursos são implementados num estado consistente. Para saber mais sobre o formato do modelo e como construí-lo, consulte [início rápido: criar e implantar modelos de Azure Resource Manager usando o portal do Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Para ver a sintaxe JSON para tipos de recursos, consulte [Define resources in Azure Resource Manager templates (Definir recursos nos modelos do Azure Resource Manager)](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

A criação de uma máquina virtual do Azure geralmente inclui duas etapas:

1. Crie um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual.
1. Cria uma máquina virtual.

O exemplo a seguir cria uma VM a partir de um [modelo de início rápido do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Somente a autenticação SSH é permitida para essa implantação. Quando solicitado, forneça o valor da sua própria chave pública SSH, como o conteúdo de *~/.ssh/id_rsa. pub*. Se você precisar criar um par de chaves SSH, consulte [como criar e usar um par de chaves SSH para VMs do Linux no Azure](mac-create-ssh-keys.md). Aqui está uma cópia do modelo:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Para executar o script da CLI, selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

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

O último comando CLI do Azure mostra o endereço IP público da VM recém-criada. Você precisa do endereço IP público para se conectar à máquina virtual. Consulte a próxima seção deste artigo.

No exemplo anterior, você especificou um modelo armazenado no GitHub. Você também pode baixar ou criar um modelo e especificar o caminho local com o parâmetro `--template-file`.

Seguem-se alguns recursos adicionais:

- Para saber como desenvolver modelos do Resource Manager, consulte a [documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- Para ver os esquemas de máquina virtual do Azure, consulte [referência de modelo do Azure](/azure/templates/microsoft.compute/allversions).
- Para ver mais exemplos de modelo de máquina virtual, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Em seguida, você pode SSH para sua VM como de costume. Forneça seu próprio endereço IP público do comando anterior:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Passos seguintes

Neste exemplo, você criou uma VM do Linux básica. Para obter mais modelos do Resource Manager que incluem estruturas de aplicativo ou criar ambientes mais complexos, procure os [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Para saber mais sobre como criar modelos, exiba a sintaxe e as propriedades JSON para os tipos de recursos implantados:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
