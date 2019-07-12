---
title: Criar uma VM do Linux com zona definida com a CLI do Azure | Documentos da Microsoft
description: Criar uma VM do Linux numa zona de disponibilidade com a CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 87263d11828ff5122122ef36850fade87949bcac
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671618"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Criar uma máquina virtual do Linux numa zona de disponibilidade com a CLI do Azure

Este artigo explica a utilizar a CLI do Azure para criar uma VM do Linux numa zona de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona separada fisicamente numa região do Azure. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

Para utilizar uma zona de disponibilidade, crie a máquina virtual numa [região suportada do Azure](../../availability-zones/az-overview.md#services-support-by-region).

Certifique-se de que instalou a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) e tem sessão iniciada numa conta do Azure com [início de sessão az](/cli/azure/reference-index).


## <a name="check-vm-sku-availability"></a>Verificar a disponibilidade de SKU de VM
A disponibilidade de tamanhos de VM ou SKUs poderá variar consoante a região e zona. Para ajudar a planear a utilização das Zonas de Disponibilidade, pode listar os SKUs de VM disponíveis por região e zona do Azure. Esta capacidade garante que escolhe um tamanho de VM adequado e obtém a resiliência pretendida nas zonas. Para obter mais informações sobre os diferentes tipos e tamanhos de VM, veja [Descrição geral de Tamanhos de VM](sizes.md).

Pode ver os SKUs de VM disponíveis com o [az vm lista-skus](/cli/azure/vm) comando. O exemplo seguinte lista os SKUs de VM disponíveis na região *eualeste2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

O resultado será semelhante ao seguinte exemplo condensado, que mostra as Zonas de Disponibilidade em que cada tamanho de VM está disponível:

```azurecli
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group).  

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual. Neste exemplo, um grupo de recursos chamado *myResourceGroupVM* é criado na *eualeste2* região. E.U.A. Leste 2 é uma das regiões do Azure que suporta zonas de disponibilidade.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

O grupo de recursos é especificado ao criar ou modificar uma VM, o que pode ser vista ao longo deste artigo.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm). 

Ao criar uma máquina virtual, várias opções estão disponíveis, como a imagem do sistema operativo, as credenciais administrativas e o dimensionamento do disco. Neste exemplo, é criada uma máquina virtual com o nome *myVM* a executar o Servidor Ubuntu. A VM é criada numa zona de disponibilidade *1*. Por predefinição, a VM é criada no *Standard_DS1_v2* tamanho.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

A criação da VM pode demorar alguns minutos. Quando a VM tiver sido criada, a CLI do Azure produz informações sobre a VM. Anote o `zones` valor, que indica a zona de disponibilidade na qual a VM está em execução. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirmar a zona do disco gerido e o endereço IP

Quando a VM é implementada numa zona de disponibilidade, um disco gerido para a VM é criado na mesma zona de disponibilidade. Por predefinição, um endereço IP público também é criado nessa zona. Os exemplos seguintes obtém informações sobre esses recursos.

Para verificar se o disco gerido da VM está na zona de disponibilidade, utilize o [show de vm de az](/cli/azure/vm) comando para devolver o ID do disco. Neste exemplo, o ID do disco é armazenado numa variável que é utilizada num passo posterior. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Agora, pode obter informações sobre o disco gerido:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

O resultado mostra que o disco gerido está na mesma zona de disponibilidade que a VM:

```azurecli
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Utilize o [az vm lista-ip-addresses](/cli/azure/vm) comando para devolver o nome do recurso de endereço IP público na *myVM*. Neste exemplo, o nome é armazenado numa variável que é utilizada num passo posterior.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Agora, pode obter informações sobre o endereço IP:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

O resultado mostra que o endereço IP está na mesma zona de disponibilidade da VM:

```azurecli
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a criar uma VM numa zona de disponibilidade. Saiba mais sobre [regiões e disponibilidade](regions-and-availability.md) para VMs do Azure.




