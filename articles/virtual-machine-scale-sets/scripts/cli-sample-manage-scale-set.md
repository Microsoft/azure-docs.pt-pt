---
title: Amostra de CLI Azure para gestão de conjunto de escala de máquina virtual
description: Esta amostra mostra como adicionar discos a um conjunto de escala de máquina virtual. Pode atualizar discos e adicionar as suas máquinas virtuais à autenticação AD AZure.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 493f479a09fc7b21bb857ebd98c35824c548b5d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539789"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Criar e gerir conjunto de escala de máquina virtual

Utilize estes comandos de amostra para protótipo de uma balança de máquina virtual definida utilizando o Azure CLI.

Estes comandos de amostra demonstram as seguintes operações:

* Criar um conjunto de dimensionamento de máquinas virtuais.
* Adicione e atualize discos novos ou existentes a um conjunto de escala ou a uma instância do conjunto.
* Adicione a massa definida à autenticação do Azure Ative Directory (Azure AD).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Comandos de amostra

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Depois de adicionar um novo disco de dados, o formato e o disco. Para máquinas virtuais do Windows, consulte [anexar um disco de dados gerido a um VM do Windows utilizando o portal Azure](../../virtual-machines/windows/attach-managed-disk-portal.md). Para máquinas virtuais Linux, consulte [Adicionar um disco a um Linux VM](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Para utilizar o disco expandido, expanda a partição subjacente. Para obter mais informações, consulte [expandir uma partição de disco e um sistema de ficheiros.](/azure/virtual-machines/linux/expand-disks#expand-a-disk-partition-and-filesystem)

Este exemplo redimensionou um disco de dados. Pode utilizar este mesmo procedimento para atualizar um disco de so. Para obter mais informações para uma máquina virtual do Windows, consulte [Como expandir a unidade de SISTEMA de uma máquina virtual.](../../virtual-machines/windows/expand-os-disk.md) Para obter mais informações sobre máquinas virtuais Linux, consulte [expandir discos rígidos virtuais num Linux VM com o Azure CLI](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de utilizar estes comandos, executar o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Referências Azure CLI utilizadas neste artigo

* [az dissuam](/cli/azure/disk#az_disk_delete)
* [lista de discos az](/cli/azure/disk#az_disk_list)
* [atualização de disco az](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [az escala de máquina definida deallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [dissião do disco az vmss](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [identidade az vmss atribuir](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)
* [az vmss reiniciar](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)
