---
title: Criar e gerir máquinas virtuais no DevTest Labs com a CLI do Azure | Documentos da Microsoft
description: Saiba como utilizar o Azure DevTest Labs para criar e gerir máquinas virtuais com a CLI do Azure
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622325"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Criar e gerir máquinas virtuais com o DevTest Labs com a CLI do Azure
Este início rápido irá guiá-lo por meio de criar, iniciar, ligar, atualização e limpeza de um computador de desenvolvimento no seu laboratório. 

Antes de começar:

* Se não tiver sido criado um laboratório, pode encontrar instruções [aqui](devtest-lab-create-lab.md).

* [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Para iniciar, execute o início de sessão az para criar uma ligação com o Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Criar e certifique-se a máquina virtual 
Antes de executar comandos relacionados do DevTest Labs, definir o contexto apropriado do Azure utilizando o `az account set` comando:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

O comando para criar uma máquina virtual é: `az lab vm create`. O grupo de recursos para o laboratório, o nome de laboratório e o nome da máquina virtual são todas as necessárias. Alterar o resto dos argumentos dependendo do tipo de máquina virtual.

O comando seguinte cria uma imagem baseada em Windows, do local de mercado do Azure. O nome da imagem é o mesmo, como verá quando criar uma máquina virtual com o portal do Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

O comando seguinte cria uma máquina virtual com base numa imagem personalizada, disponível no laboratório:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

O **tipo de imagem** argumento mudou de **galeria** para **personalizado**. O nome da imagem corresponde ao que vê se fosse criar a máquina virtual no portal do Azure.

O comando seguinte cria uma VM a partir de uma imagem do marketplace com ssh autenticação:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Também pode criar máquinas virtuais com base em fórmulas definindo a **tipo de imagem** parâmetro para **fórmula**. Se tiver de escolher uma rede virtual específica para a máquina virtual, utilize o **vnet-name** e **sub-rede** parâmetros. Para obter mais informações, consulte [az lab vm criar](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Certifique-se de que a VM está disponível.
Utilize o `az lab vm show` comando para verificar se a VM está disponível antes de iniciar e ligá-la. 

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

## <a name="start-and-connect-to-the-virtual-machine"></a>Iniciar e estabelecer ligação à máquina virtual
O comando de exemplo seguinte inicia uma VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Ligar a uma VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [ambiente de trabalho remoto](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Atualizar a máquina virtual
O comando de exemplo seguinte aplica-se os artefactos para uma VM:

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

Artefactos de lista disponível no laboratório.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Parar e eliminar a máquina virtual    
O seguinte comando de exemplo deixa de uma VM.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Elimine uma VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes
Consulte o seguinte conteúdo: [Documentação da CLI do Azure para o Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 