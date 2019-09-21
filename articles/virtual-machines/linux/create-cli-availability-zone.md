---
title: Criar uma VM do Linux zoneada com o CLI do Azure | Microsoft Docs
description: Criar uma VM do Linux em uma zona de disponibilidade com o CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: e732693a63b3c866dc767e98bbe298474286f178
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173885"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Criar uma máquina virtual do Linux em uma zona de disponibilidade com o CLI do Azure

Este artigo percorre o uso do CLI do Azure para criar uma VM do Linux em uma zona de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona separada fisicamente numa região do Azure. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

Para utilizar uma zona de disponibilidade, crie a máquina virtual numa [região suportada do Azure](../../availability-zones/az-overview.md#services-support-by-region).

Verifique se você instalou o [CLI do Azure](/cli/azure/install-az-cli2) mais recente e fez logon em uma conta do Azure com [AZ login](/cli/azure/reference-index).


## <a name="check-vm-sku-availability"></a>Verificar a disponibilidade de SKU de VM
A disponibilidade de tamanhos de VM ou SKUs poderá variar consoante a região e zona. Para ajudar a planear a utilização das Zonas de Disponibilidade, pode listar os SKUs de VM disponíveis por região e zona do Azure. Esta capacidade garante que escolhe um tamanho de VM adequado e obtém a resiliência pretendida nas zonas. Para obter mais informações sobre os diferentes tipos e tamanhos de VM, veja [Descrição geral de Tamanhos de VM](sizes.md).

Você pode exibir os SKUs de VM disponíveis com o comando [AZ VM List-SKUs](/cli/azure/vm) . O exemplo seguinte lista os SKUs de VM disponíveis na região *eualeste2*:

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

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual. Neste exemplo, um grupo de recursos chamado *myResourceGroupVM* é criado na região *eastus2* . Leste dos EUA 2 é uma das regiões do Azure que oferece suporte a zonas de disponibilidade.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

O grupo de recursos é especificado ao criar ou modificar uma VM, que pode ser visto neste artigo.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm). 

Ao criar uma máquina virtual, várias opções estão disponíveis, como a imagem do sistema operativo, as credenciais administrativas e o dimensionamento do disco. Neste exemplo, é criada uma máquina virtual com o nome *myVM* a executar o Servidor Ubuntu. A VM é criada na zona de disponibilidade *1*. Por padrão, a VM é criada no tamanho *Standard_DS1_v2* .

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

A criação da VM pode demorar alguns minutos. Quando a VM tiver sido criada, a CLI do Azure produz informações sobre a VM. Anote o `zones` valor, que indica a zona de disponibilidade em que a VM está em execução. 

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

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirmar zona para disco gerenciado e endereço IP

Quando a VM é implantada em uma zona de disponibilidade, um disco gerenciado para a VM é criado na mesma zona de disponibilidade. Por padrão, um endereço IP público também é criado nessa zona. Os exemplos a seguir obtêm informações sobre esses recursos.

Para verificar se o disco gerenciado da VM está na zona de disponibilidade, use o comando [AZ VM show](/cli/azure/vm) para retornar a ID do disco. Neste exemplo, a ID do disco é armazenada em uma variável que é usada em uma etapa posterior. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Agora você pode obter informações sobre o disco gerenciado:

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

Use o comando [AZ VM List-IP-addresses](/cli/azure/vm) para retornar o nome do recurso de endereço IP público em *myVM*. Neste exemplo, o nome é armazenado em uma variável que é usada em uma etapa posterior.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Agora você pode obter informações sobre o endereço IP:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

A saída mostra que o endereço IP está na mesma zona de disponibilidade que a VM:

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

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar uma VM numa zona de disponibilidade. Saiba mais sobre a [disponibilidade](availability.md) de VMs do Azure.




