---
title: Exemplo Proteção para séries de máquinas virtuais Azure conjunto instâncias
description: Saiba como proteger a escala de máquina virtual Azure definir instâncias de operações de escala e escala.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 8b331eaf52a0a97232d481dccfff932221cd5faa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933462"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Exemplo Proteção para séries de máquinas virtuais Azure conjunto instâncias

Os conjuntos de balanças de máquinas virtuais Azure permitem uma melhor elasticidade para as suas cargas de trabalho através da [Autoscale,](virtual-machine-scale-sets-autoscale-overview.md)para que possa configurar quando a sua infraestrutura se escasseia e quando se escala. Os conjuntos de escala também permitem gerir, configurar e atualizar um grande número de VMs através de diferentes definições de política de [atualização.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) Pode configurar uma atualização no modelo de conjunto de escala e a nova configuração é aplicada automaticamente em todas as instâncias definidas em escala se tiver definido a política de atualização para Automático ou Rolante.

À medida que a sua aplicação processa o tráfego, pode haver situações em que pretende que casos específicos sejam tratados de forma diferente do resto da instância definida em escala. Por exemplo, certos casos no conjunto de escala podem estar a realizar operações de longo prazo, e não queremos que estas instâncias sejam dimensionadas até que as operações estejam concluídas. Também pode ter se especializado algumas instâncias na escala definida para executar tarefas adicionais ou diferentes do que os outros membros do conjunto de escala. Exige que estes VM 'especiais' não sejam modificados com as outras instâncias do conjunto de escalas. A proteção de instâncias fornece os controlos adicionais para ativar estes e outros cenários para a sua aplicação.

Este artigo descreve como pode aplicar e usar as diferentes capacidades de proteção de instâncias com instâncias definidas em escala.

## <a name="types-of-instance-protection"></a>Tipos de proteção de instâncias
Os conjuntos de escala fornecem dois tipos de capacidades de proteção de instâncias:

-   **Proteger da escala**
    - Ativado através da **propriedade protectFromScaleIn** na instância definida em escala
    - Protege a instância da escala iniciada por Autoscale
    - As operações de instância iniciadas pelo utilizador (incluindo a eliminação de instâncias) não estão **bloqueadas**
    - As operações iniciadas no conjunto de escala (upgrade, reimagem, deallocate, etc.) não estão **bloqueadas**

-   **Proteja as ações definidas em escala**
    - Ativado através da **propriedade protectFromScaleSetActions** na instância definida em escala
    - Protege a instância da escala iniciada por Autoscale
    - Protege a ocorrência das operações iniciadas no conjunto de escala (tais como upgrade, reimagem, deallocate, etc.)
    - As operações de instância iniciadas pelo utilizador (incluindo a eliminação de instâncias) não estão **bloqueadas**
    - A eliminação do conjunto de escala completa não está **bloqueada**

## <a name="protect-from-scale-in"></a>Proteger da escala
A proteção de instâncias pode ser aplicada a instâncias definidas em escala após a criação dos casos. A proteção é aplicada e modificada apenas no [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no modelo de conjunto de [escala](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Existem várias formas de aplicar proteção de escala na sua escala, conforme descrito nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Pode aplicar proteção de escala através do portal Azure a uma instância no conjunto de escalas. Não se pode ajustar mais do que um caso de cada vez. Repita os passos para cada instância que pretende proteger.
 
1. Aceda a um conjunto de escala de máquina virtual existente.
1. Selecione **Instâncias** do menu à esquerda, em **Definições**.
1. Selecione o nome do caso que pretende proteger.
1. Selecione o separador **Política de Proteção.**
1. Na lâmina **'Política de Protecção',** selecione a opção **Protect from scale-in.**
1. Selecione **Guardar**. 

### <a name="rest-api"></a>API REST

O exemplo a seguir aplica proteção de escala a um caso no conjunto de escalas.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>A proteção de instâncias só é suportada com a versão API 2019-03-01 e acima

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) para aplicar proteção de escala na sua instância definida em escala.

O exemplo a seguir aplica proteção de escala a um caso na escala definida com identificação de exemplo 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Utilize [a atualização az vmss](/cli/azure/vmss#az-vmss-update) para aplicar proteção de escala na sua instância definida em escala.

O exemplo a seguir aplica proteção de escala a um caso na escala definida com identificação de exemplo 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Proteja as ações definidas em escala
A proteção de instâncias pode ser aplicada a instâncias definidas em escala após a criação dos casos. A proteção é aplicada e modificada apenas no [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no modelo de conjunto de [escala](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Proteger uma instância de ações definidas em escala também protege a instância da escala iniciada por Autoscale.

Existem várias formas de aplicar a proteção de ações definidas em escala na sua escala, conforme descrito nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Pode aplicar proteção contra ações definidas em escala através do portal Azure a uma instância no conjunto de escalas. Não se pode ajustar mais do que um caso de cada vez. Repita os passos para cada instância que pretende proteger.
 
1. Aceda a um conjunto de escala de máquina virtual existente.
1. Selecione **Instâncias** do menu à esquerda, em **Definições**.
1. Selecione o nome do caso que pretende proteger.
1. Selecione o separador **Política de Proteção.**
1. Na lâmina **'Política de Protecção',** selecione a opção **Protect from scale set actions.**
1. Selecione **Guardar**. 

### <a name="rest-api"></a>API REST

O exemplo a seguir aplica a proteção das ações definidas em escala a um caso no conjunto de escalas.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>A proteção de instâncias só é suportada com a versão API 2019-03-01 ou acima.</br>
Proteger uma instância de ações definidas em escala também protege a instância da escala iniciada por Autoscale. Não é possível especificar "protectFromScaleIn": falso ao configurar "protectFromScaleSetActions": verdadeiro

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) para aplicar proteção das ações definidas em escala à sua instância definida em escala.

O exemplo a seguir aplica a proteção das ações definidas em escala a um caso no conjunto de escala com identificação de exemplo 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Utilize [a atualização az vmss](/cli/azure/vmss#az-vmss-update) para aplicar a proteção das ações definidas em escala à sua instância definida em escala.

O exemplo a seguir aplica a proteção das ações definidas em escala a um caso no conjunto de escala com identificação de exemplo 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Resolução de problemas
### <a name="no-protectionpolicy-on-scale-set-model"></a>Sem protecçãoPolícica no modelo de conjunto de escala
A proteção por instância só é aplicável em instâncias definidas à escala e não no modelo definido em escala.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Sem protecçãoPolída no modelo de instância definida em escala
Por predefinição, a política de proteção não é aplicada a uma instância quando é criada.

Pode aplicar proteção de instâncias a instâncias definidas após a criação dos casos.

### <a name="not-able-to-apply-instance-protection"></a>Não é capaz de aplicar proteção de instâncias
A proteção de instâncias só é suportada com a versão API 2019-03-01 ou acima. Verifique se a versão API está a ser utilizada e atualização conforme necessário. Pode também necessitar de atualizar o powerShell ou o CLI para a versão mais recente.

## <a name="next-steps"></a>Passos seguintes
Saiba como implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquina virtual.
