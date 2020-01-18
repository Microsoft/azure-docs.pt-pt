---
title: Criar e gerenciar máquinas virtuais no DevTest Labs com CLI do Azure
description: Saiba como usar Azure DevTest Labs para criar e gerenciar máquinas virtuais com CLI do Azure
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167057"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Criar e gerenciar máquinas virtuais com o DevTest Labs usando o CLI do Azure
Este guia de início rápido orientará você na criação, no início, na conexão, na atualização e na limpeza de um computador de desenvolvimento em seu laboratório. 

Antes de começar:

* Se um laboratório não tiver sido criado, as instruções poderão ser encontradas [aqui](devtest-lab-create-lab.md).

* [Instale o CLI do Azure](/cli/azure/install-azure-cli). Para começar, execute AZ login para criar uma conexão com o Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Criar e verificar a máquina virtual 
Antes de executar comandos relacionados ao DevTest Labs, defina o contexto do Azure apropriado usando o comando `az account set`:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

O comando para criar uma máquina virtual é: `az lab vm create`. O grupo de recursos para o laboratório, o nome do laboratório e o nome da máquina virtual são todos necessários. O restante dos argumentos muda dependendo do tipo de máquina virtual.

O comando a seguir cria uma imagem baseada no Windows do Azure Market Place. O nome da imagem é o mesmo que você veria ao criar uma máquina virtual usando o portal do Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O comando a seguir cria uma máquina virtual com base em uma imagem personalizada disponível no laboratório:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O argumento de **tipo de imagem** foi alterado da **Galeria** para **personalizada**. O nome da imagem corresponde ao que você vê se você criasse a máquina virtual no portal do Azure.

O comando a seguir cria uma VM de uma imagem do Marketplace com autenticação SSH:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Você também pode criar máquinas virtuais com base em fórmulas definindo o parâmetro de **tipo de imagem** como **fórmula**. Se você precisar escolher uma rede virtual específica para sua máquina virtual, use os parâmetros **vnet-Name** e **subnet** . Para obter mais informações, consulte [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Verifique se a VM está disponível.
Use o comando `az lab vm show` para verificar se a VM está disponível antes de iniciar e conectar-se a ela. 

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

## <a name="start-and-connect-to-the-virtual-machine"></a>Iniciar e conectar-se à máquina virtual
O comando de exemplo a seguir inicia uma VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Conectar-se a uma VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [área de trabalho remota](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Atualizar a máquina virtual
O seguinte comando de exemplo aplica artefatos a uma VM:

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

### <a name="list-artifacts-available-in-the-lab"></a>Listar artefatos disponíveis no laboratório

Para listar os artefatos disponíveis em uma VM em um laboratório, execute os comandos a seguir.

**Cloud Shell-PowerShell**: Observe o uso da marca de seleção (\`) antes da $Expand $ in (ou seja, ' $Expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud shell-bash**: Observe o uso do caractere de barra (\\) na frente de $ no comando. 

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

## <a name="stop-and-delete-the-virtual-machine"></a>Parar e excluir a máquina virtual    
O comando de exemplo a seguir interrompe uma VM.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Excluir uma VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte conteúdo: [CLI do Azure documentação para Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
