---
title: Criar e gerir máquinas virtuais em Laboratórios DevTest com Azure CLI
description: Saiba como usar a Azure DevTest Labs para criar e gerir máquinas virtuais com o Azure CLI
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 22ee6bf607fe1b66cece0e7ddb25a2da2830258b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201469"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Criar e gerir máquinas virtuais com DevTest Labs usando o Azure CLI
Este quickstart irá guiá-lo através da criação, partida, ligação, atualização e limpeza de uma máquina de desenvolvimento no seu laboratório. 

Antes de começar:

* Se um laboratório não tiver sido criado, as instruções podem ser encontradas [aqui.](devtest-lab-create-lab.md)

* [Instale o Azure CLI](/cli/azure/install-azure-cli). Para começar, faça login az para criar uma ligação com a Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Criar e verificar a máquina virtual 
Antes de executar comandos relacionados com a DevTest Labs, desabrava o contexto Azure apropriado utilizando o `az account set` comando:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

O comando para criar uma máquina virtual é: `az lab vm create` . O grupo de recursos para o laboratório, nome de laboratório e nome de máquina virtual são todos necessários. O resto dos argumentos mudam dependendo do tipo de máquina virtual.

O seguinte comando cria uma imagem baseada no Windows a partir do Azure Market Place. O nome da imagem é o mesmo que se veria ao criar uma máquina virtual utilizando o portal Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O seguinte comando cria uma máquina virtual baseada numa imagem personalizada disponível em laboratório:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O argumento **do tipo imagem** mudou de **galeria** para **costume.** O nome da imagem corresponde ao que vê se foi criar a máquina virtual no portal Azure.

O seguinte comando cria um VM a partir de uma imagem de mercado com autenticação ssh:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Também pode criar máquinas virtuais com base em fórmulas, definindo o parâmetro **do tipo de imagem** para a **fórmula**. Se precisar de escolher uma rede virtual específica para a sua máquina virtual, utilize os parâmetros **de nome vnet** e **sub-rede.** Para mais informações, consulte [a criação do laboratório AZ Vm](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Verifique se o VM está disponível.
Utilize o `az lab vm show` comando para verificar se o VM está disponível antes de iniciar e ligar-lhe. 

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
O seguinte exemplo de comando inicia um VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Ligue-se a um VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Remote Desktop](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Atualize a máquina virtual
O seguinte comando de amostra aplica artefactos a um VM:

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

**Cloud Shell - PowerShell**: note a utilização do backtick \` ( ) antes do $ em $expand (isto é, '$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash**: note o uso do \\ caractere slash () na frente de $ no comando. 

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

## <a name="stop-and-delete-the-virtual-machine"></a>Parar e apagar a máquina virtual    
O seguinte comando de amostra para um VM.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Eliminar uma VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte conteúdo: [Documentação Azure CLI para Azure DevTest Labs](/cli/azure/lab). 
