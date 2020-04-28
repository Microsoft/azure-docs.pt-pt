---
title: Crie e gerencie máquinas virtuais em Laboratórios DevTest com Azure CLI
description: Aprenda a usar o Azure DevTest Labs para criar e gerir máquinas virtuais com o Azure CLI
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76167057"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Crie e gereça máquinas virtuais com Laboratórios DevTest utilizando o Azure CLI
Este quickstart irá guiá-lo através da criação, arranque, ligação, atualização e limpeza de uma máquina de desenvolvimento no seu laboratório. 

Antes de começar:

* Se um laboratório não tiver sido criado, podem ser encontradas instruções [aqui.](devtest-lab-create-lab.md)

* [Instale o Azure CLI](/cli/azure/install-azure-cli). Para começar, faça login az para criar uma ligação com Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Criar e verificar a máquina virtual 
Antes de executar comandos relacionados com a DevTest Labs, detete o contexto Azure apropriado utilizando o `az account set` comando:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

O comando para criar uma `az lab vm create`máquina virtual é: . O grupo de recursos para o laboratório, o nome do laboratório e o nome da máquina virtual são todos necessários. Os restantes argumentos mudam dependendo do tipo de máquina virtual.

O comando seguinte cria uma imagem baseada no Windows a partir do Azure Market Place. O nome da imagem é o mesmo que se veria ao criar uma máquina virtual utilizando o portal Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O seguinte comando cria uma máquina virtual baseada numa imagem personalizada disponível no laboratório:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O argumento **do tipo imagem** mudou de **galeria** para **costume.** O nome da imagem corresponde ao que vê se vai criar a máquina virtual no portal Azure.

O seguinte comando cria um VM a partir de uma imagem de mercado com autenticação SSH:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Também pode criar máquinas virtuais baseadas em fórmulas, definindo o parâmetro **do tipo de imagem** para a **fórmula**. Se precisar de escolher uma rede virtual específica para a sua máquina virtual, utilize os parâmetros **de nome vnet** e **subnet.** Para mais informações, consulte [a z lab vm criar](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Verifique se o VM está disponível.
Utilize `az lab vm show` o comando para verificar se o VM está disponível antes de iniciar e ligar-se ao mesmo. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Iniciar e ligar à máquina virtual
O comando de exemplo seguinte inicia um VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Ligue-se a um VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Remote Desktop](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Atualizar a máquina virtual
O seguinte comando da amostra aplica artefactos a um VM:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Lista de artefactos disponíveis no laboratório

Para listar artefactos disponíveis num VM num laboratório, execute os seguintes comandos.

**Cloud Shell - PowerShell:** note a\`utilização do backtick () antes do $ em $expand (isto é, '$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash:** note\\o uso do personagem slash ( ) na frente de $ no comando. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Resultado do exemplo: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Pare e elimine a máquina virtual    
O comando da amostra seguinte para um VM.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Apague um VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes conteúdos: [Documentação Azure CLI para O Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
