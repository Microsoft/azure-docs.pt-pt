---
title: Modificar um conjunto de escala de máquina virtual Azure
description: Saiba como modificar e atualizar uma balança de máquina virtual Azure definida com as APIs REST, Azure PowerShell e Azure CLI
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 45c52e141ed2d93f89c9d7c1426a9d7cbbbd47f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023933"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificar um conjunto de dimensionamento de máquinas virtuais

Ao longo do ciclo de vida das suas aplicações, poderá ter de modificar ou atualizar o seu conjunto de escalas de máquinas virtuais. Estas atualizações podem incluir como atualizar a configuração do conjunto de escala ou alterar a configuração da aplicação. Este artigo descreve como modificar um conjunto de escala existente com as APIs REST, Azure PowerShell ou Azure CLI.

## <a name="fundamental-concepts"></a>Conceitos fundamentais

### <a name="the-scale-set-model"></a>O modelo de conjunto de escala
Um conjunto de escala tem um "modelo de conjunto de escala" que captura o estado *desejado* da escala definida como um todo. Para consultar o modelo para um conjunto de escala, pode usar o 

- REST API com [conjuntos de escalas compute/virtualmachines/obter o](/rest/api/compute/virtualmachinescalesets/get) seguinte:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI com [az vmss show:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [SDKs Azure específicos](https://azure.microsoft.com/downloads/)da língua.

A apresentação exata da saída depende das opções que fornece ao comando. O exemplo a seguir mostra a saída da amostra condensada do Azure CLI:

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


### <a name="the-scale-set-instance-view"></a>A vista de instância definida em escala
Um conjunto de escala também tem uma "visão de instância definida em escala" que captura o estado atual do tempo de *execução* da escala definida como um todo. Para consultar a vista de exemplo para um conjunto de escala, pode utilizar:

- REST API com [conjuntos de escalas compute/virtualmachinescales/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) da seguinte forma:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI com [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [SDKs Azure específicos](https://azure.microsoft.com/downloads/) da língua

A apresentação exata da saída depende das opções que fornece ao comando. O exemplo a seguir mostra a saída da amostra condensada do Azure CLI:

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


### <a name="the-scale-set-vm-model-view"></a>A vista do modelo VM definida em escala
Semelhante à forma como um conjunto de escala tem uma visão de modelo, cada instância VM no conjunto de escala tem a sua própria visão de modelo. Para consultar a vista do modelo para uma determinada instância VM num conjunto de escala, pode utilizar:

- REST API com [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) as seguintes:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmssVm:](/powershell/module/az.compute/get-azvmssvm)

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI com [az vmss show:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [Azure SDKs](https://azure.microsoft.com/downloads/).

A apresentação exata da saída depende das opções que fornece ao comando. O exemplo a seguir mostra a saída da amostra condensada do Azure CLI:

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

Estas propriedades descrevem a configuração de uma instância VM dentro de um conjunto de escala, e não a configuração do conjunto de escala como um todo. Por exemplo, o modelo de conjunto de escala tem `overprovision` como propriedade, enquanto o modelo para uma instância VM dentro de um conjunto de escala não. Esta diferença deve-se ao facto de a superprovisão ser uma propriedade para a escala definida como um todo, e não instâncias VM individuais no conjunto de escala (para mais informações sobre a sobreprovisionamento, ver [considerações de Design para conjuntos de escala).](virtual-machine-scale-sets-design-overview.md#overprovisioning)


### <a name="the-scale-set-vm-instance-view"></a>A escala definida VM ver instância
Semelhante à forma como um conjunto de escala tem uma visão de exemplo, cada instância VM no conjunto de escala tem a sua própria visão de exemplo. Para consultar a visualização de um exemplo de exemplo em VM dentro de um conjunto de escala, pode utilizar:

- REST API com [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) da seguinte forma:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmssVm:](/powershell/module/az.compute/get-azvmssvm)

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- CLI Azure com [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Você também pode usar [resources.azure.com](https://resources.azure.com) ou os [Azure SDKs](https://azure.microsoft.com/downloads/)

A apresentação exata da saída depende das opções que fornece ao comando. O exemplo a seguir mostra a saída da amostra condensada do Azure CLI:

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

Estas propriedades descrevem o estado atual de execução de uma instância VM dentro de um conjunto de escala, que inclui quaisquer extensões aplicadas ao conjunto de escala.


## <a name="how-to-update-global-scale-set-properties"></a>Como atualizar as propriedades definidas à escala global
Para atualizar uma propriedade definida à escala global, você deve atualizar a propriedade no modelo de conjunto de escala. Pode fazer esta atualização através de:

- REST API com [conjuntos compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) da seguinte forma:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Pode implementar um modelo de Gestor de Recursos com as propriedades da API REST para atualizar as propriedades definidas à escala global.

- Azure PowerShell com [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI com [atualização az vmss:](/cli/azure/vmss)
    - Para modificar uma propriedade:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Para adicionar um objeto a uma propriedade de lista em um conjunto de escala: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Para remover um objeto de uma propriedade de lista em um conjunto de escala: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Se tiver implantado previamente a escala definida com o `az vmss create` comando, pode voltar a executar o `az vmss create` comando para atualizar o conjunto de escala. Certifique-se de que todas as propriedades no `az vmss create` comando são as mesmas de antes, exceto as propriedades que deseja modificar.

- Também pode utilizar [resources.azure.com](https://resources.azure.com) ou os [Azure SDKs](https://azure.microsoft.com/downloads/).

Uma vez atualizado o modelo de conjunto de escala, a nova configuração aplica-se a quaisquer novos VMs criados no conjunto de escala. No entanto, os modelos para os VM existentes no conjunto de escalas devem ainda ser atualizados com o mais recente modelo global de conjunto de escala. No modelo para cada VM encontra-se uma propriedade booleana `latestModelApplied` chamada que indica se o VM está ou não atualizado com o modelo de escala global mais recente `true` (significa que o VM está atualizado com o modelo mais recente).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Como aproximar os VMs com o modelo mais recente da escala
Os conjuntos de escala têm uma "política de upgrade" que determina como os VMs são atualizados com o modelo de conjunto de escala mais recente. Os três modos para a política de atualização são:

- **Automático** - Neste modo, o conjunto de escala não garante a descida da ordem dos VMs. O conjunto de escala pode derrubar todos os VMs ao mesmo tempo. 
- **Rolar** - Neste modo, o conjunto de escala sai da atualização em lotes com um tempo de pausa opcional entre lotes.
- **Manual** - Neste modo, quando atualiza o modelo de conjunto de escala, nada acontece aos VM existentes.
 
Para atualizar os VM existentes, tem de fazer uma "atualização manual" de cada VM existente. Pode fazer esta atualização manual com:

- REST API com [conjuntos de escalas/atualizações compute/virtualmachines como](/rest/api/compute/virtualmachinescalesets/updateinstances) se segue:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell com [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI com [az vmss atualização-instâncias](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Também pode utilizar os [SDKs Azure específicos](https://azure.microsoft.com/downloads/)da linguagem.

>[!NOTE]
> Os clusters de tecido de serviço só podem utilizar o modo *Automático,* mas a atualização é tratada de forma diferente. Para mais informações, consulte [as atualizações da aplicação Service Fabric](../service-fabric/service-fabric-application-upgrade.md).

Há um tipo de modificação nas propriedades definidas à escala global que não seguem a política de upgrade. As alterações ao conjunto de sistemas de sistemas OPERATIVOs e perfil de disco de dados (como o nome de utilizador e a palavra-passe) só podem ser alteradas na versão API *2017-12-01* ou posterior. Estas alterações aplicam-se apenas aos VM criados após a alteração do modelo de conjunto de escala. Para que os VMs existentes estejam atualizados, é necessário fazer uma "reimagem" de cada VM existente. Pode fazer esta reimagem via:

- REST API com [conjuntos de escalas compute/virtualmachinescales/reimagem](/rest/api/compute/virtualmachinescalesets/reimage) da seguinte forma:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell com [Set-AzVmssVm:](/powershell/module/az.compute/set-azvmssvm)

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI com [az vmss reimagem:](/cli/azure/vmss)

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Também pode utilizar os [SDKs Azure específicos](https://azure.microsoft.com/downloads/)da linguagem.


## <a name="properties-with-restrictions-on-modification"></a>Propriedades com restrições à modificação

### <a name="create-time-properties"></a>Propriedades de tempo de criação
Algumas propriedades só podem ser definidas quando se cria o conjunto de escala. Estas propriedades incluem:

- Zonas de Disponibilidade
- Editor de referência de imagem
- Oferta de referência de imagem
- Tipo de conta de armazenamento de disco de SO gerido

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Propriedades que só podem ser alteradas com base no valor atual
Algumas propriedades podem ser alteradas, com exceções dependendo do valor atual. Estas propriedades incluem:

- **singlePlacementGroup** - Se o SinglePlacementGroup for verdadeiro, pode ser modificado para falso. No entanto, se o Grupo de Substituição Único for falso, **pode não** ser modificado para ser verdadeiro.
- **sub-rede** - A sub-rede de um conjunto de escala pode ser modificada enquanto a sub-rede original e a nova sub-rede estiverem na mesma rede virtual.
- **imageReferenceSku** - Referência de imagem SKU pode ser atualizado para [distros Linux endossados, imagens](../virtual-machines/linux/endorsed-distros.md)do servidor do Windows/cliente e imagens sem [informações do plano](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information). 

### <a name="properties-that-require-deallocation-to-change"></a>Imóveis que exigem a mudança de negociação
Algumas propriedades só podem ser alteradas para determinados valores se os VMs na escala definida forem transabilitados. Estas propriedades incluem:

- **Nome SKU**- Se o novo VM SKU não for suportado no hardware em que o conjunto de escala está atualmente ligado, tem de transferir os VMs na escala definida antes de modificar o nome SKU. Para mais informações, [consulte como redimensionar um VM Azure](../virtual-machines/windows/resize-vm.md). 

## <a name="vm-specific-updates"></a>Atualizações específicas do VM
Determinadas modificações podem ser aplicadas a VMs específicos em vez das propriedades definidas à escala global. Atualmente, a única atualização específica em VM que é suportada é anexar/desprender discos de dados de/para VMs no conjunto de escala. Esta funcionalidade está em pré-visualização. Para mais informações, consulte a [documentação de pré-visualização.](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)


## <a name="scenarios"></a>Cenários

### <a name="application-updates"></a>Atualizações de aplicações
Se uma aplicação for implementada numa escala definida através de extensões, uma atualização da configuração de extensão faz com que a aplicação seja atualizada de acordo com a política de atualização. Por exemplo, se tiver uma nova versão de um script para executar numa Extensão de Script Personalizada, poderá atualizar a propriedade *do ficheiroUris* para apontar para o novo script. Em alguns casos, poderá pretender forçar uma atualização mesmo que a configuração da extensão se mantenha inalterada (por exemplo, atualizou o script sem alterar o URI do script). Nestes casos, pode modificar o *ForceUpdateTag* para forçar uma atualização. A plataforma Azure não interpreta esta propriedade. Se alterar o valor, não há qualquer efeito sobre o funcionação da extensão. Uma mudança simplesmente força a extensão para a repetição. Para obter mais informações sobre o *forceUpdateTag,* consulte a [documentação da API REST para extensões](/rest/api/compute/virtualmachineextensions/createorupdate). Note que o *ForceUpdateTag* pode ser usado com todas as extensões, e não apenas com a extensão de script personalizada.

Também é comum que as aplicações sejam implementadas através de uma imagem personalizada. Este cenário é abordado na secção seguinte.

### <a name="os-updates"></a>Atualizações do OS
Se utilizar imagens da plataforma Azure, pode atualizar a imagem modificando a *imagemReferência* (mais informações, consulte a [documentação da API REST).](/rest/api/compute/virtualmachinescalesets/createorupdate)

>[!NOTE]
> Com imagens de plataforma, é comum especificar "o mais recente" para a versão de referência de imagem. Quando cria, escala e reimagem, os VMs são criados com a versão mais recente disponível. No entanto, **não** significa que a imagem de SO seja atualizada automaticamente ao longo do tempo à medida que novas versões de imagem são lançadas. Uma funcionalidade separada encontra-se atualmente em pré-visualização que fornece atualizações automáticas de SO. Para mais informações, consulte a [documentação automática de atualizações do SISTEMA](virtual-machine-scale-sets-automatic-upgrade.md).

Se utilizar imagens personalizadas, pode atualizar a imagem atualizando o *ID* de referência de imagem (mais informações, consulte a [documentação DA API REST).](/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="examples"></a>Exemplos

### <a name="update-the-os-image-for-your-scale-set"></a>Atualize a imagem de SO para o seu conjunto de escala
Pode ter um conjunto de escala que executa uma versão antiga de Ubuntu LTS 16.04. Pretende atualizar para uma versão mais recente do Ubuntu LTS 16.04, como a versão *16.04.201801090*. A propriedade da versão de referência de imagem não faz parte de uma lista, pelo que pode modificar diretamente estas propriedades com um dos seguintes comandos:

- Azure PowerShell com [Update-AzVmss](/powershell/module/az.compute/update-azvmss) da seguinte forma:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI com [atualização az vmss:](/cli/azure/vmss)

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Em alternativa, pode querer alterar a imagem que o conjunto de escala utiliza. Por exemplo, pode querer atualizar ou alterar uma imagem personalizada utilizada pelo seu conjunto de escala. Pode alterar a imagem que o seu conjunto de escala utiliza atualizando a propriedade de ID de referência de imagem. A propriedade de ID de referência de imagem não faz parte de uma lista, pelo que pode modificar diretamente esta propriedade com um dos seguintes comandos:

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


### <a name="update-the-load-balancer-for-your-scale-set"></a>Atualize o balançador de carga para o seu conjunto de escala
Digamos que tem uma balança definida com um Balançador de Carga Azure, e pretende substituir o Balançador de Carga Azure por um Gateway de aplicação Azure. As propriedades do equilibrador de carga e do Gateway de aplicação para um conjunto de escala fazem parte de uma lista, para que possa utilizar os comandos para remover ou adicionar elementos de lista em vez de modificar as propriedades diretamente:

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
> Estes comandos assumem que existe apenas uma configuração IP e um balançador de carga no conjunto de escala. Se houver vários, poderá ter de utilizar um índice de lista que não *seja 0*.


## <a name="next-steps"></a>Passos seguintes
Também pode executar tarefas de gestão comuns em conjuntos de escala com o [Azure CLI](virtual-machine-scale-sets-manage-cli.md) ou [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).