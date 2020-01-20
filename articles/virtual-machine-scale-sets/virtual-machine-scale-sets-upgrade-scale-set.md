---
title: Modificar um conjunto de dimensionamento de máquinas virtuais do Azure
description: Saiba como modificar e atualizar um conjunto de dimensionamento de máquinas virtuais do Azure com as APIs REST, Azure PowerShell e CLI do Azure
author: mayanknayar
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: manayar
ms.openlocfilehash: 49327ff0c3aeab25de02fc67c049f24597215d45
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274459"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificar um conjunto de dimensionamento de máquinas virtuais

Durante todo o ciclo de vida de seus aplicativos, talvez seja necessário modificar ou atualizar o conjunto de dimensionamento de máquinas virtuais. Essas atualizações podem incluir como atualizar a configuração do conjunto de dimensionamento ou alterar a configuração do aplicativo. Este artigo descreve como modificar um conjunto de dimensionamento existente com as APIs REST, Azure PowerShell ou CLI do Azure.

## <a name="fundamental-concepts"></a>Conceitos fundamentais

### <a name="the-scale-set-model"></a>O modelo do conjunto de dimensionamento
Um conjunto de dimensionamento tem um "modelo de conjunto de dimensionamento" que captura o estado *desejado* do conjunto de dimensionamento como um todo. Para consultar o modelo para um conjunto de dimensionamento, você pode usar o 

- API REST com [Compute/virtualmachinescalesets/Get](/rest/api/compute/virtualmachinescalesets/get) da seguinte maneira:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- CLI do Azure com [AZ vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Você também pode usar o [Resources.Azure.com](https://resources.azure.com) ou os [SDKs do Azure](https://azure.microsoft.com/downloads/)específicos do idioma.

A apresentação exata da saída depende das opções fornecidas para o comando. O exemplo a seguir mostra a saída de exemplo condensada do CLI do Azure:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Essas propriedades se aplicam ao conjunto de dimensionamento como um todo.


### <a name="the-scale-set-instance-view"></a>A exibição da instância do conjunto de dimensionamento
Um conjunto de dimensionamento também tem uma "exibição de instância do conjunto de dimensionamento" que captura o estado de *tempo de execução* atual do conjunto de dimensionamento como um todo. Para consultar a exibição de instância de um conjunto de dimensionamento, você pode usar:

- API REST com [Compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) da seguinte maneira:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- CLI do Azure com [AZ vmss Get-Instance-View](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Você também pode usar o [Resources.Azure.com](https://resources.azure.com) ou os [SDKs do Azure](https://azure.microsoft.com/downloads/) específicos do idioma

A apresentação exata da saída depende das opções fornecidas para o comando. O exemplo a seguir mostra a saída de exemplo condensada do CLI do Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Essas propriedades fornecem um resumo do estado de tempo de execução atual das VMs no conjunto de dimensionamento, como o status das extensões aplicadas ao conjunto de dimensionamento.


### <a name="the-scale-set-vm-model-view"></a>A exibição do modelo da VM do conjunto de dimensionamento
Assim como um conjunto de dimensionamento tem uma exibição de modelo, cada instância de VM no conjunto de dimensionamento tem sua própria exibição de modelo. Para consultar o modo de exibição de modelo de uma determinada instância de VM em um conjunto de dimensionamento, você pode usar:

- API REST com [Compute/virtualmachinescalesetvms/Get](/rest/api/compute/virtualmachinescalesetvms/get) da seguinte maneira:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- CLI do Azure com [AZ vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Você também pode usar o [Resources.Azure.com](https://resources.azure.com) ou os [SDKs do Azure](https://azure.microsoft.com/downloads/).

A apresentação exata da saída depende das opções fornecidas para o comando. O exemplo a seguir mostra a saída de exemplo condensada do CLI do Azure:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Essas propriedades descrevem a configuração de uma instância de VM em um conjunto de dimensionamento, não a configuração do conjunto de dimensionamento como um todo. Por exemplo, o modelo de conjunto de dimensionamento tem `overprovision` como uma propriedade, enquanto o modelo para uma instância de VM em um conjunto de dimensionamento não. Essa diferença ocorre porque o excesso de provisionamento é uma propriedade para o conjunto de dimensionamento como um todo, não para instâncias de VM individuais no conjunto de dimensionamento (para obter mais informações sobre o provisionamento em excesso, consulte [considerações de design para conjuntos de dimensionamento](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>A exibição da instância VM do conjunto de dimensionamento
Assim como um conjunto de dimensionamento tem uma exibição de instância, cada instância de VM no conjunto de dimensionamento tem sua própria exibição de instância. Para consultar a exibição de instância de uma instância de VM específica em um conjunto de dimensionamento, você pode usar:

- API REST com [Compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) da seguinte maneira:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- CLI do Azure com [AZ vmss Get-Instance-View](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Você também pode usar o [Resources.Azure.com](https://resources.azure.com) ou os [SDKs do Azure](https://azure.microsoft.com/downloads/)

A apresentação exata da saída depende das opções fornecidas para o comando. O exemplo a seguir mostra a saída de exemplo condensada do CLI do Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Essas propriedades descrevem o estado de tempo de execução atual de uma instância de VM em um conjunto de dimensionamento, que inclui todas as extensões aplicadas ao conjunto de dimensionamento.


## <a name="how-to-update-global-scale-set-properties"></a>Como atualizar propriedades globais do conjunto de dimensionamento
Para atualizar uma propriedade global de conjunto de dimensionamento, você deve atualizar a propriedade no modelo de conjunto de dimensionamento. Você pode fazer essa atualização por meio de:

- API REST com [Compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) da seguinte maneira:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Você pode implantar um modelo do Resource Manager com as propriedades da API REST para atualizar as propriedades globais do conjunto de dimensionamento.

- Azure PowerShell com [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- CLI do Azure com [AZ vmss Update](/cli/azure/vmss):
    - Para modificar uma propriedade:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Para adicionar um objeto a uma propriedade de lista em um conjunto de dimensionamento: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Para remover um objeto de uma propriedade de lista em um conjunto de dimensionamento: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Se você tiver implantado anteriormente o conjunto de dimensionamento com o comando `az vmss create`, poderá executar o comando `az vmss create` novamente para atualizar o conjunto de dimensionamento. Certifique-se de que todas as propriedades no comando `az vmss create` sejam as mesmas de antes, exceto pelas propriedades que você deseja modificar.

- Você também pode usar o [Resources.Azure.com](https://resources.azure.com) ou os [SDKs do Azure](https://azure.microsoft.com/downloads/).

Depois que o modelo do conjunto de dimensionamento for atualizado, a nova configuração se aplicará a qualquer nova VM criada no conjunto de dimensionamento. No entanto, os modelos para as VMs existentes no conjunto de dimensionamento ainda devem ser atualizados com o modelo de conjunto de dimensionamento geral mais recente. No modelo para cada VM, há uma propriedade booliana chamada `latestModelApplied` que indica se a VM está atualizada com o modelo de conjunto de dimensionamento mais recente (`true` significa que a VM está atualizada com o modelo mais recente).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Como colocar as VMs atualizadas com o modelo de conjunto de dimensionamento mais recente
Os conjuntos de dimensionamento têm uma "política de atualização" que determinam como as VMs são atualizadas com o modelo de conjunto de dimensionamento mais recente. Os três modos para a política de atualização são:

- **Automático** – nesse modo, o conjunto de dimensionamento não garante a ordem das VMs que estão sendo trazidas. O conjunto de dimensionamento pode retirar todas as VMs ao mesmo tempo. 
- **Sem interrupção** -nesse modo, o conjunto de dimensionamento distribui a atualização em lotes com um tempo de pausa opcional entre os lotes.
- **Manual** -neste modo, quando você atualiza o modelo do conjunto de dimensionamento, nada acontece com as VMs existentes.
 
Para atualizar as VMs existentes, você deve fazer uma "atualização manual" de cada VM existente. Você pode fazer essa atualização manual com:

- API REST com [Compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) da seguinte maneira:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell com [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- CLI do Azure com [AZ vmss Update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Você também pode usar os [SDKs do Azure](https://azure.microsoft.com/downloads/)específicos do idioma.

>[!NOTE]
> Service Fabric clusters só podem usar o modo *automático* , mas a atualização é tratada de forma diferente. Para obter mais informações, consulte [Service Fabric atualizações de aplicativos](../service-fabric/service-fabric-application-upgrade.md).

Há um tipo de modificação para propriedades globais do conjunto de dimensionamento que não segue a política de atualização. As alterações no perfil de sistema operacional do conjunto de dimensionamento (como nome de usuário e senha de administrador) só podem ser alteradas na versão de API *2017-12-01* ou posterior. Essas alterações se aplicam somente às VMs criadas após a alteração no modelo do conjunto de dimensionamento. Para colocar as VMs existentes atualizadas, você deve fazer uma "reimagem" de cada VM existente. Você pode fazer essa reimagem por meio de:

- API REST com [computação/virtualmachinescalesets/Reimage](/rest/api/compute/virtualmachinescalesets/reimage) da seguinte maneira:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell com [set-AzVmssVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- CLI do Azure com [AZ vmss](https://docs.microsoft.com/cli/azure/vmss)refazer a imagem:

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Você também pode usar os [SDKs do Azure](https://azure.microsoft.com/downloads/)específicos do idioma.


## <a name="properties-with-restrictions-on-modification"></a>Propriedades com restrições de modificação

### <a name="create-time-properties"></a>Propriedades de tempo de criação
Algumas propriedades só podem ser definidas quando você cria o conjunto de dimensionamento. Essas propriedades incluem:

- Zonas de Disponibilidade
- Editor de referência de imagem
- Oferta de referência de imagem
- Tipo de conta de armazenamento de disco do sistema operacional gerenciado

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Propriedades que só podem ser alteradas com base no valor atual
Algumas propriedades podem ser alteradas, com exceções dependendo do valor atual. Essas propriedades incluem:

- **singlePlacementGroup** -se singlePlacementGroup for true, ele poderá ser modificado para false. No entanto, se singlePlacementGroup for false, ele **não poderá** ser modificado para true.
- **sub** -rede-a sub-rede de um conjunto de dimensionamento pode ser modificada desde que a sub-rede original e a nova sub-rede estejam na mesma rede virtual.

### <a name="properties-that-require-deallocation-to-change"></a>Propriedades que exigem a desalocação para alterar
Algumas propriedades só podem ser alteradas para determinados valores se as VMs no conjunto de dimensionamento forem desalocadas. Essas propriedades incluem:

- **Nome do SKU**– se o novo SKU da VM não tiver suporte no hardware em que o conjunto de dimensionamento está atualmente ativado, você precisará desalocar as VMs no conjunto de dimensionamento antes de modificar o nome do SKU. Para obter mais informações, consulte [como redimensionar uma VM do Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Atualizações específicas da VM
Determinadas modificações podem ser aplicadas a VMs específicas em vez das propriedades globais do conjunto de dimensionamento. Atualmente, a única atualização específica da VM com suporte é anexar/desanexar discos de dados de/para VMs no conjunto de dimensionamento. Esta funcionalidade está em pré-visualização. Para obter mais informações, consulte a [documentação de visualização](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Cenários

### <a name="application-updates"></a>Atualizações de aplicativos
Se um aplicativo for implantado em um conjunto de dimensionamento por meio de extensões, uma atualização para a configuração de extensão fará com que o aplicativo seja atualizado de acordo com a política de atualização. Por exemplo, se você tiver uma nova versão de um script para ser executada em uma extensão de script personalizado, você poderá atualizar a propriedade *fileuris* para apontar para o novo script. Em alguns casos, talvez você queira forçar uma atualização, mesmo que a configuração de extensão não seja alterada (por exemplo, você atualizou o script sem uma alteração no URI do script). Nesses casos, você pode modificar o *forceUpdateTag* para forçar uma atualização. A plataforma Azure não interpreta essa propriedade. Se você alterar o valor, não haverá nenhum efeito sobre como a extensão é executada. Uma alteração simplesmente força a execução da extensão. Para obter mais informações sobre o *forceUpdateTag*, consulte a [documentação da API REST para extensões](/rest/api/compute/virtualmachineextensions/createorupdate). Observe que o *forceUpdateTag* pode ser usado com todas as extensões, não apenas a extensão de script personalizado.

Também é comum que os aplicativos sejam implantados por meio de uma imagem personalizada. Esse cenário é abordado na seção a seguir.

### <a name="os-updates"></a>Atualizações do so
Se você usar imagens da plataforma Azure, poderá atualizar a imagem modificando o *imageReference* (mais informações, consulte a [documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Com imagens de plataforma, é comum especificar "mais recente" para a versão de referência da imagem. Quando você cria, dimensiona horizontalmente e recria a imagem, as VMs são criadas com a versão mais recente disponível. No entanto, isso **não** significa que a imagem do sistema operacional seja atualizada automaticamente ao longo do tempo à medida que novas versões de imagem são lançadas. Um recurso separado está atualmente em visualização que fornece atualizações automáticas do sistema operacional. Para obter mais informações, consulte a [documentação de atualizações automáticas do sistema operacional](virtual-machine-scale-sets-automatic-upgrade.md).

Se você usar imagens personalizadas, poderá atualizar a imagem atualizando a ID do *imageReference* (mais informações, consulte a [documentação da API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exemplos

### <a name="update-the-os-image-for-your-scale-set"></a>Atualizar a imagem do sistema operacional para seu conjunto de dimensionamento
Você pode ter um conjunto de dimensionamento que executa uma versão antiga do Ubuntu LTS 16, 4. Você deseja atualizar para uma versão mais recente do Ubuntu LTS 16, 4, como a versão *16.04.201801090*. A propriedade da versão de referência da imagem não faz parte de uma lista, portanto, você pode modificar essas propriedades diretamente com um dos seguintes comandos:

- Azure PowerShell com [Update-AzVmss](/powershell/module/az.compute/update-azvmss) da seguinte maneira:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- CLI do Azure com [AZ vmss Update](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Como alternativa, talvez você queira alterar a imagem usada pelo seu conjunto de dimensionamento. Por exemplo, talvez você queira atualizar ou alterar uma imagem personalizada usada pelo seu conjunto de dimensionamento. Você pode alterar a imagem que seu conjunto de dimensionamento usa atualizando a propriedade de ID de referência da imagem. A propriedade da ID de referência da imagem não faz parte de uma lista, portanto, você pode modificar essa propriedade diretamente com um dos seguintes comandos:

- Azure PowerShell com [Update-AzVmss](/powershell/module/az.compute/update-azvmss) da seguinte maneira:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- CLI do Azure com [AZ vmss Update](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Atualizar o balanceador de carga para seu conjunto de dimensionamento
Digamos que você tenha um conjunto de dimensionamento com um Azure Load Balancer e queira substituir o Azure Load Balancer por um gateway de Aplicativo Azure. As propriedades do balanceador de carga e do gateway de aplicativo para um conjunto de dimensionamento fazem parte de uma lista, de modo que você pode usar os comandos para remover ou adicionar elementos de lista em vez de modificar as propriedades diretamente:

- Azure PowerShell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- CLI do Azure:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Esses comandos pressupõem que há apenas uma configuração de IP e um balanceador de carga no conjunto de dimensionamento. Se houver vários, talvez seja necessário usar um índice de lista diferente de *0*.


## <a name="next-steps"></a>Passos seguintes
Você também pode executar tarefas comuns de gerenciamento em conjuntos de dimensionamento com o [CLI do Azure](virtual-machine-scale-sets-manage-cli.md) ou [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
