---
title: Modificar um conjunto de escala de máquina virtual Azure
description: Saiba como modificar e atualizar uma escala de máquina virtual Azure com as APIs REST, Azure PowerShell e Azure CLI
author: mimckitt
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimckitt
ms.openlocfilehash: af5998a4207521d49ea4fd7956256aa6c880e6e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476829"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificar um conjunto de dimensionamento de máquinas virtuais

Ao longo do ciclo de vida das suas aplicações, poderá ter de modificar ou atualizar o conjunto de escala de máquina virtual. Estas atualizações podem incluir como atualizar a configuração do conjunto de escala, ou alterar a configuração da aplicação. Este artigo descreve como modificar uma escala existente com as APIs REST, Azure PowerShell ou Azure CLI.

## <a name="fundamental-concepts"></a>Conceitos fundamentais

### <a name="the-scale-set-model"></a>O modelo de conjunto de escala
Um conjunto de escala tem um "modelo de conjunto de escala" que captura o estado *desejado* da escala definida como um todo. Para consultar o modelo para um conjunto de escala, você pode usar o 

- REST API com [conjuntos de escalas de computação/virtualmachinescale/obter](/rest/api/compute/virtualmachinescalesets/get) o seguinte:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI com [show az vmss:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [SDKs Azure específicos](https://azure.microsoft.com/downloads/)da língua.

A apresentação exata da saída depende das opções que fornece ao comando. O exemplo seguinte mostra a saída da amostra condensada do Azure CLI:

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


### <a name="the-scale-set-instance-view"></a>A visão de instância de conjunto de escala
Um conjunto de escala também tem uma "visão de instância de conjunto de escala" que captura o estado atual de tempo de *execução* da escala definida como um todo. Para consultar a vista da instância para um conjunto de escala, pode utilizar:

- REST API com [conjuntos de escalas de computação/virtualmachinescales/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) da seguinte forma:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI com [az vmss get-instance-view:](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [SDKs Azure específicos](https://azure.microsoft.com/downloads/) da língua

A apresentação exata da saída depende das opções que fornece ao comando. O exemplo seguinte mostra a saída da amostra condensada do Azure CLI:

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

Estas propriedades fornecem um resumo do estado atual de execução dos VMs no conjunto de escala, como o estado das extensões aplicadas ao conjunto de escala.


### <a name="the-scale-set-vm-model-view"></a>A vista do modelo VM definido em escala
À semelhança de como um conjunto de escala tem uma visão de modelo, cada instância VM no conjunto de escala tem a sua própria visão de modelo. Para consultar a vista do modelo para uma determinada instância VM num conjunto de escala, pode utilizar:

- REST API com [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) as seguintes:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmssVm:](/powershell/module/az.compute/get-azvmssvm)

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI com [show az vmss:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [SDKs Azure](https://azure.microsoft.com/downloads/).

A apresentação exata da saída depende das opções que fornece ao comando. O exemplo seguinte mostra a saída da amostra condensada do Azure CLI:

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

Estas propriedades descrevem a configuração de uma instância VM dentro de um conjunto de escala, não a configuração da escala definida como um todo. Por exemplo, o modelo `overprovision` de conjunto de escala tem como propriedade, enquanto o modelo para uma instância VM dentro de um conjunto de escala não. Esta diferença deve-se ao facto de o excesso de oferta ser um imóvel para a escala definida como um todo, e não os casos individuais de VM no conjunto de escala (para mais informações sobre o excesso de oferta, consulte [considerações de design para conjuntos](virtual-machine-scale-sets-design-overview.md#overprovisioning)de escala).


### <a name="the-scale-set-vm-instance-view"></a>A visão de instância vm definida em escala
À semelhança de como um conjunto de escala tem uma visão de instância, cada instância VM no conjunto de escala tem a sua própria visão de instância. Para consultar a vista da instância para uma determinada instância VM dentro de um conjunto de escala, pode utilizar:

- REST API com [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) da seguinte forma:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmssVm:](/powershell/module/az.compute/get-azvmssvm)

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI com [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [SDKs Azure](https://azure.microsoft.com/downloads/)

A apresentação exata da saída depende das opções que fornece ao comando. O exemplo seguinte mostra a saída da amostra condensada do Azure CLI:

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

Estas propriedades descrevem o estado atual de tempo de execução de uma instância VM dentro de um conjunto de escala, que inclui quaisquer extensões aplicadas ao conjunto de escala.


## <a name="how-to-update-global-scale-set-properties"></a>Como atualizar propriedades de conjunto de escala global
Para atualizar uma propriedade global de conjunto de escala, você deve atualizar a propriedade no modelo de conjunto de escala. Pode fazer esta atualização através de:

- REST API com conjuntos de [escalas de computação/virtualmachinescaleda/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) da seguinte forma:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Você pode implementar um modelo de Gestor de Recursos com as propriedades da API REST para atualizar propriedades de conjunto de escala global.

- Azure PowerShell com [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI com [atualização az vmss:](/cli/azure/vmss)
    - Para modificar uma propriedade:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Para adicionar um objeto a uma propriedade da lista num conjunto de escala: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Para remover um objeto de uma propriedade da lista num conjunto de escala: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Se tiver implantado previamente a `az vmss create` balança definida com `az vmss create` o comando, pode executar novamente o comando para atualizar o conjunto de escala. Certifique-se de que `az vmss create` todas as propriedades do comando são as mesmas de antes, exceto as propriedades que pretende modificar.

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [SDKs Azure](https://azure.microsoft.com/downloads/).

Uma vez atualizado o modelo de conjunto de escala, a nova configuração aplica-se a quaisquer novos VMs criados no conjunto de escala. No entanto, os modelos para os VMexistentes no conjunto de escala sem vencimento devem ainda ser atualizados com o mais recente modelo global de conjuntos de escala. No modelo para cada VM encontra-se uma propriedade booleana chamada `latestModelApplied` que indica se o VM`true` está ou não atualizado com o mais recente modelo global de conjuntos de escala ( significa que o VM está atualizado com o modelo mais recente).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Como trazer os VMs atualizados com o mais recente modelo de conjunto de escala
Os conjuntos de escala têm uma "política de upgrade" que determina como os VMs são atualizados com o modelo mais recente de conjunto de escala. Os três modos para a política de atualização são:

- **Automático** - Neste modo, o conjunto de escala não garante a descção da ordem dos VMs. O conjunto de escala pode derrubar todos os VMs ao mesmo tempo. 
- **Rolling** - Neste modo, o conjunto de escala lança a atualização em lotes com um tempo de pausa opcional entre lotes.
- **Manual** - Neste modo, ao atualizar o modelo de conjunto de escala, nada acontece aos VMs existentes.
 
Para atualizar os VMexistentes, deve fazer uma "atualização manual" de cada VM existente. Pode fazer esta atualização manual com:

- REST API com [compute/virtualmachinescalesets/atualização instâncias](/rest/api/compute/virtualmachinescalesets/updateinstances) da seguinte forma:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell com [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI com [az vmss update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Também pode utilizar os [SDKs Azure específicos](https://azure.microsoft.com/downloads/)da língua.

>[!NOTE]
> Serviço Os clusters de tecido só podem utilizar o modo *Automático,* mas a atualização é manuseada de forma diferente. Para mais informações, consulte as atualizações da [aplicação Service Fabric.](../service-fabric/service-fabric-application-upgrade.md)

Há um tipo de modificação nas propriedades definidas à escala global que não segue a política de upgrade. As alterações ao conjunto de escala Os e Data disk Perfil (como nome de utilizador e palavra-passe) só podem ser alteradas na versão API *2017-12-01* ou posterior. Estas alterações aplicam-se apenas aos VMs criados após a alteração do modelo de conjunto de escala. Para que os VMexistentes estejam atualizados, é necessário fazer uma "reimagem" de cada VM existente. Pode fazer esta reimagem via:

- REST API com [conjuntos de escalas/reimagem compute/virtualmachinescaleda](/rest/api/compute/virtualmachinescalesets/reimage) da seguinte forma:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell com [Set-AzVmssVm:](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI com [reimagem az vmss:](https://docs.microsoft.com/cli/azure/vmss)

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Também pode utilizar os [SDKs Azure específicos](https://azure.microsoft.com/downloads/)da língua.


## <a name="properties-with-restrictions-on-modification"></a>Imóveis com restrições à modificação

### <a name="create-time-properties"></a>Propriedades de tempo de criação
Algumas propriedades só podem ser definidas quando se cria o conjunto de escala. Estas propriedades incluem:

- Zonas de Disponibilidade
- Editor de referência de imagem
- Oferta de referência de imagem
- Tipo de conta de armazenamento de disco os gerido

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Propriedades que só podem ser alteradas com base no valor atual
Algumas propriedades podem ser alteradas, com exceções dependendo do valor atual. Estas propriedades incluem:

- **singlePlacementGroup** - Se o singlePlacementGroup for verdadeiro, pode ser modificado para falso. No entanto, se o singlePlacementGroup for falso, **pode não** ser modificado de forma verdadeira.
- **sub-rede** - A sub-rede de um conjunto de escala pode ser modificada enquanto a sub-rede original e a nova sub-rede estiverem na mesma rede virtual.

### <a name="properties-that-require-deallocation-to-change"></a>Imóveis que exigem desalocação para alterar
Algumas propriedades só podem ser alteradas para determinados valores se os VMs na escala definida forem deallocalizados. Estas propriedades incluem:

- **Nome SKU**- Se o novo VM SKU não for suportado no hardware, o conjunto de escala está atualmente ligado, precisa de desalojar os VMs na escala definida antes de modificar o nome SKU. Para mais informações, consulte [como redimensionar um VM Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Atualizações específicas da VM
Algumas modificações podem ser aplicadas a VMs específicos em vez das propriedades definidas à escala global. Atualmente, a única atualização específica de VM que é suportada é anexar/separar discos de dados de/para VMs no conjunto de escala. Esta funcionalidade está em pré-visualização. Para mais informações, consulte a documentação de [pré-visualização](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Cenários

### <a name="application-updates"></a>Atualizações de aplicações
Se uma aplicação for implementada numa escala definida através de extensões, uma atualização da configuração da extensão faz com que a aplicação atualize de acordo com a política de atualização. Por exemplo, se tiver uma nova versão de um script para ser executado numa extensão de script personalizado, poderá atualizar a propriedade *fileUris* para apontar para o novo script. Em alguns casos, poderá querer forçar uma atualização mesmo que a configuração da extensão se mantenha inalterada (por exemplo, atualizou o script sem alterar o URI do script). Nestes casos, pode modificar o *forceUpdateTag* para forçar uma atualização. A plataforma Azure não interpreta esta propriedade. Se alterar o valor, não há efeito na forma como a extensão funciona. Uma mudança simplesmente força a extensão a recorrer. Para obter mais informações sobre o *forceUpdateTag,* consulte a documentação REST [API para extensões](/rest/api/compute/virtualmachineextensions/createorupdate). Note que o *forceUpdateTag* pode ser usado com todas as extensões, e não apenas com a extensão de script personalizada.

Também é comum que as aplicações sejam implementadas através de uma imagem personalizada. Este cenário está coberto na secção seguinte.

### <a name="os-updates"></a>Atualizações do OS
Se utilizar imagens da plataforma Azure, pode atualizar a imagem modificando a *imagemReference* (mais informações, consulte a documentação REST [API).](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)

>[!NOTE]
> Com imagens da plataforma, é comum especificar "mais recente" para a versão de referência de imagem. Quando crias, escalas e reimagem, os VMs são criados com a versão mais recente disponível. No entanto, **não** significa que a imagem de OS seja automaticamente atualizada ao longo do tempo à medida que novas versões de imagem são lançadas. Uma funcionalidade separada encontra-se atualmente em pré-visualização que fornece atualizações automáticas de SO. Para mais informações, consulte a [documentação de atualizações automáticas do OS](virtual-machine-scale-sets-automatic-upgrade.md).

Se utilizar imagens personalizadas, pode atualizar a imagem atualizando o ID de referência de *imagem* (mais informações, consulte a documentação REST [API).](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="examples"></a>Exemplos

### <a name="update-the-os-image-for-your-scale-set"></a>Atualize a imagem de OS para o seu conjunto de escala
Pode ter um conjunto de escala que executa uma versão antiga do Ubuntu LTS 16.04. Pretende atualizar para uma versão mais recente do Ubuntu LTS 16.04, como a versão *16.04.201801090*. A propriedade da versão de referência de imagem não faz parte de uma lista, pelo que pode modificar diretamente estas propriedades com um dos seguintes comandos:

- Azure PowerShell com [Update-AzVmss](/powershell/module/az.compute/update-azvmss) da seguinte forma:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI com [atualização az vmss:](/cli/azure/vmss)

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Em alternativa, pode querer alterar a imagem que o seu conjunto de escala utiliza. Por exemplo, pode querer atualizar ou alterar uma imagem personalizada utilizada pelo seu conjunto de escala. Pode alterar a imagem que o seu conjunto de escala utiliza atualizando a propriedade id de referência de imagem. A propriedade id de referência de imagem não faz parte de uma lista, pelo que pode modificar diretamente esta propriedade com um dos seguintes comandos:

- Azure PowerShell com [Update-AzVmss](/powershell/module/az.compute/update-azvmss) da seguinte forma:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI com [atualização az vmss:](/cli/azure/vmss)

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Atualize o equilíbrio de carga para o seu conjunto de escala
Digamos que tem um conjunto de escala com um Balancer de Carga Azure, e quer substituir o Balancer de Carga Azure por um Gateway de Aplicação Azure. As propriedades do balanceor de carga e do Gateway de aplicação para um conjunto de escala saem de uma lista, para que possa utilizar os comandos para remover ou adicionar elementos da lista em vez de modificar diretamente as propriedades:

- Azure Powershell:

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

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Estes comandos assumem que há apenas uma configuração IP e um equilíbrio de carga no conjunto de escala. Se houver vários, poderá ter de utilizar um índice de lista que não *seja 0*.


## <a name="next-steps"></a>Passos seguintes
Também pode executar tarefas comuns de gestão em conjuntos de escala com o [Azure CLI](virtual-machine-scale-sets-manage-cli.md) ou [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
