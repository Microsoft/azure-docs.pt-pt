---
title: Exemplo Proteção para casos de conjunto de máquinas virtuais Azure
description: Aprenda a proteger as instâncias de conjunto de máquinas virtuais Azure de operações de escala e de escala.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392920"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Exemplo Proteção para casos de conjunto de máquinas virtuais Azure

Os conjuntos de escala de máquinas virtuais Azure permitem uma melhor elasticidade para as suas cargas de trabalho através da [escala Automática,](virtual-machine-scale-sets-autoscale-overview.md)para que possa configurar quando a sua infraestrutura se esescala e quando se escala. Os conjuntos de escala também permitem gerir, configurar e atualizar centralmente um grande número de VMs através de diferentes definições de política de [upgrade.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) Pode configurar uma atualização no modelo de conjunto de escala e a nova configuração é aplicada automaticamente a todas as instâncias de conjunto de escala se tiver definido a política de atualização para Automático ou Rolante.

À medida que a sua aplicação processa o tráfego, pode haver situações em que pretende que casos específicos sejam tratados de forma diferente do resto da instância definida pela escala. Por exemplo, certos casos no conjunto de escala podem estar a realizar operações de longo prazo, e você não quer que estes casos sejam escalados até que as operações estejam concluídas. Também pode ter especializado alguns casos na escala definida para executar tarefas adicionais ou diferentes do que os outros membros do conjunto de escala. É necessário que estes VM 'especiais' não sejam modificados com as outras instâncias do conjunto de escala. A proteção por exemplo fornece os controlos adicionais para permitir estes e outros cenários para a sua aplicação.

Este artigo descreve como pode aplicar e usar as diferentes capacidades de proteção de instâncias com instâncias definidas por escala.

## <a name="types-of-instance-protection"></a>Tipos de proteção por exemplo
Os conjuntos de escala fornecem dois tipos de capacidades de proteção por exemplo:

-   **Proteger contra a escala**
    - Ativado através da **propriedade protectFromScaleIn** na instância de conjunto de escala
    - Protege a instância da escala automática iniciada escala-in
    - As operações de instância iniciadas pelo utilizador (incluindo a eliminação por exemplo) não estão **bloqueadas**
    - As operações iniciadas no conjunto de escala (upgrade, reimagem, desafetação, etc.) não estão **bloqueadas**

-   **Proteger contra ações de conjunto de escala**
    - Ativado através da **propriedade protectFromScaleSetActions** na instância de conjunto de escala
    - Protege a instância da escala automática iniciada escala-in
    - Protege a instância das operações iniciadas no conjunto de escala (como atualização, reimagem, desafetação, etc.)
    - As operações de instância iniciadas pelo utilizador (incluindo a eliminação por exemplo) não estão **bloqueadas**
    - A eliminação do conjunto de escala completa não está **bloqueada**

## <a name="protect-from-scale-in"></a>Proteger contra a escala
A proteção por exemplo pode ser aplicada em instâncias de conjunto de escala após a criação das instâncias. A proteção é aplicada e modificada apenas no modelo de [instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no [modelo de conjunto](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)de escala .

Existem várias formas de aplicar proteção à escala na sua escala definir instâncias como detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Pode aplicar a proteção de escala através do portal Azure para uma instância no conjunto de escala. Não se pode ajustar mais do que um caso de cada vez. Repita os passos para cada instância que pretende proteger.
 
1. Vá a um conjunto de máquinas virtuais existente.
1. Selecione **Instâncias** do menu à esquerda, em **Definições**.
1. Selecione o nome da instância que pretende proteger.
1. Selecione o separador Política de **Proteção.**
1. Na lâmina da Política de **Proteção,** selecione o Protect da opção **scale-in.**
1. Selecione **Guardar**. 

### <a name="rest-api"></a>API REST

O exemplo que se segue aplica proteção à escala a uma instância no conjunto de escala.

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
>A proteção por exemplo só é suportada com a versão API 2019-03-01 e acima

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) para aplicar proteção de escala na sua instância definida pela escala.

O exemplo seguinte aplica proteção à escala a uma instância na escala definida com a instância ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Utilize a [atualização az vmss](/cli/azure/vmss#az-vmss-update) para aplicar proteção de escala na sua instância de conjunto de escala.

O exemplo seguinte aplica proteção à escala a uma instância na escala definida com a instância ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Proteger contra ações de conjunto de escala
A proteção por exemplo pode ser aplicada em instâncias de conjunto de escala após a criação das instâncias. A proteção é aplicada e modificada apenas no modelo de [instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no [modelo de conjunto](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)de escala .

Proteger uma instância de ações de conjuntos de escala também protege a instância da escala automática iniciada escala.

Existem várias formas de aplicar a proteção de ações definidas por escala na sua escala definir instâncias como detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Pode aplicar a proteção contra as ações definidas pela escala através do portal Azure para uma instância no conjunto de escala. Não se pode ajustar mais do que um caso de cada vez. Repita os passos para cada instância que pretende proteger.
 
1. Vá a um conjunto de máquinas virtuais existente.
1. Selecione **Instâncias** do menu à esquerda, em **Definições**.
1. Selecione o nome da instância que pretende proteger.
1. Selecione o separador Política de **Proteção.**
1. Na lâmina da Política de **Proteção,** selecione a opção Proteger a partir da opção de ações de **conjunto de escala.**
1. Selecione **Guardar**. 

### <a name="rest-api"></a>API REST

O exemplo que se segue aplica a proteção das ações definidas à escala a uma instância no conjunto de escala.

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
>A proteção por exemplo só é suportada com a versão API 2019-03-01 e acima.</br>
Proteger uma instância de ações de conjuntos de escala também protege a instância da escala automática iniciada escala. Não é possível especificar "protectFromScaleIn": falso ao definir "protectFromScaleSetActions": verdadeiro

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) para aplicar a proteção das ações de conjunto de escala para a sua instância de conjunto de escala.

O exemplo seguinte aplica a proteção das ações definidas por escala a uma instância na escala definida com a instância ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Utilize a [atualização az vmss](/cli/azure/vmss#az-vmss-update) para aplicar a proteção contra as ações de conjunto de escala para a sua instância de conjunto de escala.

O exemplo seguinte aplica a proteção das ações definidas por escala a uma instância na escala definida com a instância ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Resolução de problemas
### <a name="no-protectionpolicy-on-scale-set-model"></a>Sem protecçãoPolítica no modelo conjunto de escala
A proteção por exemplo só é aplicável em instâncias definidas à escala e não no modelo de conjunto de escala.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Sem protecçãoPolítica no modelo de instância definida em escala
Por predefinição, a política de proteção não é aplicada a uma instância em que é criada.

Pode aplicar proteção de exemplo seleções após a criação das ocorrências.

### <a name="not-able-to-apply-instance-protection"></a>Incapaz de aplicar proteção de instâncias
A proteção por exemplo só é suportada com a versão API 2019-03-01 e acima. Verifique a versão API que está a ser utilizada e atualize conforme necessário. Também poderá ser necessário atualizar o seu PowerShell ou CLI para a versão mais recente.

## <a name="next-steps"></a>Passos seguintes
Aprenda a implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquinas virtuais.
