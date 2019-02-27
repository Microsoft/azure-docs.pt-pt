---
title: Modificar um conjunto de dimensionamento de máquina virtual do Azure | Documentos da Microsoft
description: Saiba como modificar e atualizar um conjunto com as REST APIs, Azure PowerShell e CLI do Azure de dimensionamento de máquina virtual do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: manayar
ms.openlocfilehash: 71899a9d6782c4700c287458c85ec83bd1516a4b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873632"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificar um conjunto de dimensionamento de máquinas virtuais

Em todo o ciclo de vida das suas aplicações, terá de modificar ou atualizar o conjunto de dimensionamento de máquina virtual. Estas atualizações podem incluir como atualizar a configuração do conjunto de dimensionamento, ou alterar a configuração da aplicação. Este artigo descreve como modificar um existente conjunto de dimensionamento com as REST APIs, o Azure PowerShell ou a CLI do Azure.

## <a name="fundamental-concepts"></a>Conceitos fundamentais

### <a name="the-scale-set-model"></a>O modelo de conjunto de dimensionamento
Um conjunto de dimensionamento tem um "conjunto de modelo de dimensionamento" que captura a *pretendido* estado da escala definido como um todo. Para consultar o modelo para um conjunto de dimensionamento, pode utilizar o 

- API de REST com [computação/virtualmachinescalesets/obter](/rest/api/compute/virtualmachinescalesets/get) da seguinte forma:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- O Azure PowerShell com [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- CLI do Azure com [show do az vmss](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou o idioma específico [SDKs do Azure](https://azure.microsoft.com/downloads/).

A apresentação exata da saída depende as opções de que fornecer ao comando. O exemplo seguinte mostra a saída de exemplo condensado partir da CLI do Azure:

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

Estas propriedades aplicam-se à escala definida como um todo.


### <a name="the-scale-set-instance-view"></a>O conjunto de dimensionamento vista de instância
Um conjunto também de dimensionamento tem uma "instância conjunto de dimensionamento ver" que captura o atual *tempo de execução* estado da escala definido como um todo. Para consultar a vista de instância de um conjunto de dimensionamento, pode utilizar:

- API de REST com [computação/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) da seguinte forma:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- O Azure PowerShell com [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- CLI do Azure com [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou o idioma específico [SDKs do Azure](https://azure.microsoft.com/downloads/)

A apresentação exata da saída depende as opções de que fornecer ao comando. O exemplo seguinte mostra a saída de exemplo condensado partir da CLI do Azure:

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

Estas propriedades fornecem um resumo do estado atual do tempo de execução das VMs no conjunto de dimensionamento, tais como o estado das extensões aplicadas ao conjunto de dimensionamento.


### <a name="the-scale-set-vm-model-view"></a>O conjunto de dimensionamento vista de modelo VM
Semelhante à forma como um conjunto de dimensionamento tem uma vista de modelo, cada instância de VM no conjunto de dimensionamento tem sua própria exibição de modelo. Para consultar a vista de modelo para uma determinada instância VM num conjunto de dimensionamento, pode utilizar:

- API de REST com [computação/virtualmachinescalesetvms/obter](/rest/api/compute/virtualmachinescalesetvms/get) da seguinte forma:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- O Azure PowerShell com [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- CLI do Azure com [show do az vmss](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou o [SDKs do Azure](https://azure.microsoft.com/downloads/).

A apresentação exata da saída depende as opções de que fornecer ao comando. O exemplo seguinte mostra a saída de exemplo condensado partir da CLI do Azure:

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

Estas propriedades descrevem a configuração de uma instância VM dentro de um conjunto de dimensionamento, não a configuração da escala definida como um todo. Por exemplo, o modelo de conjunto de dimensionamento tem `overprovision` como uma propriedade, enquanto o modelo para uma instância VM dentro de um conjunto de dimensionamento não existir. Essa diferença é como aprovisionar em excesso é uma propriedade para o conjunto como um todo, não individuais instâncias VM no conjunto de dimensionamento de dimensionamento (para obter mais informações sobre como aprovisionar em excesso, consulte [considerações de Design para conjuntos de dimensionamento](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>O conjunto de dimensionamento vista de instância VM
Semelhante à forma como um conjunto de dimensionamento tem uma vista de instância, cada instância de VM no conjunto de dimensionamento tem sua própria vista de instância. Para consultar a vista de instância para uma determinada instância VM dentro de um conjunto de dimensionamento, pode utilizar:

- API de REST com [computação/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) da seguinte forma:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- O Azure PowerShell com [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- CLI do Azure com [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou o [SDKs do Azure](https://azure.microsoft.com/downloads/)

A apresentação exata da saída depende as opções de que fornecer ao comando. O exemplo seguinte mostra a saída de exemplo condensado partir da CLI do Azure:

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

Estas propriedades descrevem o estado atual do tempo de execução de uma instância VM dentro de um conjunto de dimensionamento, que inclui quaisquer extensões aplicadas ao conjunto de dimensionamento.


## <a name="how-to-update-global-scale-set-properties"></a>Como atualizar a escala global, definir propriedades
Para atualizar uma propriedade de conjunto de escala global, tem de atualizar a propriedade no modelo de conjunto de dimensionamento. Pode fazer esta atualização por meio de:

- API de REST com [computação/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) da seguinte forma:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Pode implementar um modelo do Resource Manager com as propriedades da API REST para atualizar as propriedades do conjunto de escala global.

- O Azure PowerShell com [AzVmss atualização](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- CLI do Azure com [az vmss update](/cli/azure/vmss):
    - Para modificar uma propriedade de:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Para adicionar um objeto para uma propriedade de lista num conjunto de dimensionamento: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Para remover um objeto de uma propriedade de lista num conjunto de dimensionamento: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Se tiver implementado anteriormente o conjunto de dimensionamento com o `az vmss create` comando, pode executar o `az vmss create` comando novamente para atualizar o conjunto de dimensionamento. Certifique-se de que todas as propriedades no `az vmss create` comando são os mesmos como antes, exceto para as propriedades que deseja modificar.

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou o [SDKs do Azure](https://azure.microsoft.com/downloads/).

Assim que o modelo de conjunto de dimensionamento for atualizado, a nova configuração se aplica a VMs novas criadas no conjunto de dimensionamento. No entanto, os modelos para as VMs existentes no conjunto de dimensionamento devem ainda ser colocados atualizado com o mais recente dimensionamento conjunto modelo geral. No modelo para cada VM é uma propriedade boolean chamada `latestModelApplied` que indica se a VM está atualizada com o mais recente dimensionamento conjunto modelo geral (`true` significa que a VM está atualizada com o modelo mais recente).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Como colocar atualizadas com o modelo mais recente do conjunto de dimensionamento de VMs
Conjuntos de dimensionamento de tem uma "política de atualização" que determinam como as VMs são encaminhadas atualizado com o modelo de conjunto de dimensionamento mais recente. Os três modos para a política de atualização são:

- **Automática** -neste modo, o conjunto de dimensionamento não garante a ordem das VMs a ser movido para baixo. O conjunto de dimensionamento pode demorar para baixo de todas as VMs ao mesmo tempo. 
- **Sem interrupção** -neste modo, o conjunto de dimensionamento for implementada a atualização em lotes com um tempo de pausa opcionais entre lotes.
- **Manual** - neste modo, quando atualizar o modelo de conjunto de dimensionamento, nada acontece às VMs existentes.
 
Para atualizar as VMs existentes, tem de fazer uma "atualização manual" de cada VM existente. Pode fazer esta atualização manual com:

- API de REST com [computação/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) da seguinte forma:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- O Azure PowerShell com [AzVmssInstance atualização](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- CLI do Azure com [az vmss update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Também pode utilizar o idioma específico [SDKs do Azure](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Só podem utilizar clusters do Service Fabric *automática* modo, mas a atualização é tratada diferentemente. Para obter mais informações, consulte [as atualizações de aplicações do Service Fabric](../service-fabric/service-fabric-application-upgrade.md).

Existe um tipo de modificação de propriedades do conjunto de dimensionamento global que não segue a política de atualização. Alterações no conjunto de dimensionamento do perfil do SO (como o nome de utilizador administrador e a palavra-passe) só pode ser alterado na versão de API *2017-12-01* ou posterior. Estas alterações só se aplicam a VMs criadas após a alteração na escala de definir o modelo. Para transformar VMs existentes atualizadas, tem de fazer uma "recriação de imagem" de cada VM existente. Pode fazer esta recriação de imagem por meio de:

- API de REST com [computação/virtualmachinescalesets/recriação de imagem](/rest/api/compute/virtualmachinescalesets/reimage) da seguinte forma:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- O Azure PowerShell com [Set-AzVmssVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- CLI do Azure com [recriação de imagem do az vmss](https://docs.microsoft.com/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Também pode utilizar o idioma específico [SDKs do Azure](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Propriedades com restrições de modificação

### <a name="create-time-properties"></a>Propriedades do tempo de criar
Algumas propriedades só podem ser definidas quando criar o conjunto de dimensionamento. Essas propriedades incluem:

- Zonas de Disponibilidade
- Publicador de referência de imagem
- Oferta de imagem de referência
- Tipo de conta de armazenamento de disco de SO de geridos

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Propriedades que só podem ser alteradas com base no valor atual
Algumas propriedades podem ser alteradas com exceções, dependendo do valor atual. Essas propriedades incluem:

- **singlePlacementGroup** - se singlePlacementGroup for VERDADEIRO, ele pode ser modificado como false. No entanto, se singlePlacementGroup é false, ele **podem não** ser modificado como true.
- **sub-rede** - a sub-rede de um conjunto de dimensionamento pode ser modificada, desde que a sub-rede original e a nova sub-rede estão na mesma rede virtual.

### <a name="properties-that-require-deallocation-to-change"></a>Propriedades que necessitam de Desalocação para alterar
Algumas propriedades só podem ser alteradas para determinados valores se as VMs no conjunto de dimensionamento são desalocadas. Essas propriedades incluem:

- **Nome SKU**- se o novo SKU de VM não é suportado no hardware o conjunto de dimensionamento está atualmente em, precisa ao desalocar as VMs no conjunto antes de modificar o nome do SKU de dimensionamento. Para obter mais informações, consulte [como redimensionar uma VM do Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Atualizações específicas de VM
Determinados modificações podem ser aplicadas a VMs específicas em vez das propriedades do conjunto de escala global. Atualmente, o único é de atualização de VM específicos que é suportada anexar/desanexar discos de dados de/para as VMs no conjunto de dimensionamento. Esta funcionalidade está em pré-visualização. Para obter mais informações, consulte a [documentação de pré-visualização](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Cenários

### <a name="application-updates"></a>Atualizações de aplicativos
Se uma aplicação for implementada para um conjunto através de extensões de dimensionamento, uma atualização para a configuração da extensão faz com que o aplicativo atualizar em conformidade com a política de atualização. Por exemplo, se tiver uma nova versão de um script para ser executado numa extensão de Script personalizado, foi possível atualizar o *fileUris* propriedade para apontar para o novo script. Em alguns casos, poderá querer forçar uma atualização, apesar da configuração da extensão não é alterada (por exemplo, atualizar o script sem fazer alterações para o URI do script). Nestes casos, pode modificar os *forceUpdateTag* para forçar uma atualização. A plataforma do Azure não interpreta esta propriedade. Se alterar o valor, não há nenhum efeito sobre como a extensão é executado. Uma alteração simplesmente força a extensão para voltar a executar. Para obter mais informações sobre o *forceUpdateTag*, consulte a [documentação da REST API para extensões](/rest/api/compute/virtualmachineextensions/createorupdate). Tenha em atenção que o *forceUpdateTag* podem ser utilizadas com todas as extensões, não apenas a extensão de script personalizado.

Também é comum para aplicativos a serem implantados por meio de uma imagem personalizada. Este cenário é descrito na secção seguinte.

### <a name="os-updates"></a>Atualizações do SO
Se utilizar imagens da plataforma do Azure, pode atualizar a imagem ao modificar os *imageReference* (mais informações, consulte a [documentação da REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Com as imagens de plataforma, é comum especificar "mais recente" para a versão de referência de imagem. Quando criar, aumentar horizontalmente e recriação de imagem, as VMs são criadas com a versão mais recente disponível. No entanto, ele **não** significa que a imagem do SO é atualizada automaticamente ao longo do tempo à medida que novas versões de imagem são lançadas. Um recurso separado está atualmente em pré-visualização, que fornece atualizações automáticas de SO. Para obter mais informações, consulte a [documentação de atualizações automáticas de SO](virtual-machine-scale-sets-automatic-upgrade.md).

Se utilizar imagens personalizadas, pode atualizar a imagem ao atualizar o *imageReference* ID (mais informações, consulte a [documentação da REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exemplos

### <a name="update-the-os-image-for-your-scale-set"></a>Atualizar a imagem do SO para o conjunto de dimensionamento
Pode ter um conjunto de dimensionamento que executa uma versão antiga do Ubuntu LTS 16.04. Pretende atualizar para uma versão mais recente do Ubuntu 16.04 LTS., como a versão *16.04.201801090*. A propriedade de versão de referência de imagem não faz parte de uma lista, para que pode modificar diretamente essas propriedades com um dos seguintes comandos:

- O Azure PowerShell com [AzVmss atualização](/powershell/module/az.compute/update-azvmss) da seguinte forma:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- CLI do Azure com [az vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Em alternativa, pode querer alterar a imagem que utiliza o seu conjunto de dimensionamento. Por exemplo, pode querer atualizar ou alterar uma imagem personalizada utilizada pelo seu conjunto de dimensionamento. Pode alterar a imagem do seu conjunto de dimensionamento utiliza ao atualizar a propriedade de ID de referência de imagem. A propriedade de ID de referência de imagem não faz parte de uma lista, para que pode modificar diretamente esta propriedade com um dos seguintes comandos:

- O Azure PowerShell com [AzVmss atualização](/powershell/module/az.compute/update-azvmss) da seguinte forma:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- CLI do Azure com [az vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Atualize o Balanceador de carga para o conjunto de dimensionamento
Digamos que tem um conjunto de dimensionamento com um balanceador de carga do Azure e pretende substituir o Balanceador de carga do Azure com um Gateway de aplicação do Azure. O Balanceador de carga e propriedades de Gateway de aplicação para um conjunto de dimensionamento fazem parte de uma lista, pelo que pode utilizar os comandos para remover ou adicionar elementos de lista em vez de modificar as propriedades diretamente:

- O Azure Powershell:

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
> Estes comandos pressupõem que existe apenas um balanceador de carga e de configuração de IP no conjunto de dimensionamento. Se existirem vários, poderá ter de utilizar um índice de lista que *0*.


## <a name="next-steps"></a>Passos Seguintes
Também pode efetuar tarefas de gestão comuns em conjuntos de dimensionamento com o [CLI do Azure](virtual-machine-scale-sets-manage-cli.md) ou [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
