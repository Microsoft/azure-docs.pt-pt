---
title: Instâncias de conjunto de proteção de instâncias de dimensionamento de máquina virtual do Azure | Documentos da Microsoft
description: Aprenda a proteger as instâncias do conjunto de dimensionamento de máquina virtual do Azure de operações de redução horizontal e o conjunto de dimensionamento.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 61430f5a43a04fa0e5b2f0c79ff03419c73aaf28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416543"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>Proteção de instâncias de dimensionamento de máquina virtual do Azure definir instances (pré-visualização)
Conjuntos de dimensionamento de máquina virtual do Azure permitem elasticidade melhor das cargas de trabalho por meio [dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md), pelo que pode configurar quando a sua infraestrutura aumenta horizontalmente e quando ele reduz horizontalmente. Conjuntos de dimensionamento também permitem-lhe gerir centralmente, configurar e atualizar um grande número de VMs através de diferentes [política de atualização](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) definições. Pode configurar uma atualização no modelo de conjunto de dimensionamento e a nova configuração é aplicada automaticamente a cada instância de conjunto de dimensionamento se tiver definido a política de atualização automática ou a implementar.

Como seu aplicativo processa o tráfego, pode haver situações em que pretende que instâncias específicas para ser tratada de forma diferente do restante da escala a instância do conjunto. Por exemplo, determinadas instâncias no conjunto de dimensionamento podem ser a realizar operações de longa execução, e não quer que estas instâncias para ser dimensionada-no até que as operações concluídas. Poderá também ter especializadas algumas instâncias no conjunto de dimensionamento para efetuar tarefas diferentes ou adicionais que os outros membros do conjunto de dimensionamento. Exigir estas VMs "especiais" não deve ser modificada com as outras instâncias no conjunto de dimensionamento. Proteção de instância fornece os controlos adicionais para permitir que esses e outros cenários para a sua aplicação.

Este artigo descreve como pode aplicar e utilizar as capacidades de proteção de instância diferente com instâncias de conjunto de dimensionamento.

> [!NOTE]
>Proteção de instância está atualmente em pré-visualização pública. Nenhum procedimento participar é necessária para utilizar a funcionalidade de pré-visualização pública descrita abaixo. Pré-visualização de proteção de instância só é suportado com a versão de 2019-03-01 de API e em conjuntos de dimensionamento com discos geridos.

## <a name="types-of-instance-protection"></a>Tipos de proteção de instância
Conjuntos de dimensionamento fornecem dois tipos de funcionalidades de proteção da instância:

-   **Proteger de redução horizontal**
    - Ativada através de **protectFromScaleIn** instância do conjunto de propriedade na escala
    - Protege a instância de dimensionamento-in do dimensionamento automático iniciado
    - Operações de instância iniciada pelo utilizador (incluindo a exclusão de instância) são **não bloqueado**
    - Operações iniciadas no conjunto de dimensionamento (atualizar, criar uma nova imagem, desalocar, etc.) são **não bloqueado**

-   **Proteger contra ações de conjunto de dimensionamento**
    - Ativada através de **protectFromScaleSetActions** instância do conjunto de propriedade na escala
    - Protege a instância de dimensionamento-in do dimensionamento automático iniciado
    - Protege a instância de operações iniciadas no conjunto de dimensionamento (por exemplo, a atualização, recriar imagem, desalocar, etc).
    - Operações de instância iniciada pelo utilizador (incluindo a exclusão de instância) são **não bloqueado**
    - Eliminação de conjunto de dimensionamento completo é **não bloqueado**

## <a name="protect-from-scale-in"></a>Proteger de redução horizontal
Proteção de instância pode ser aplicada para instâncias de conjunto de dimensionamento, depois das instâncias são criadas. Proteção é aplicada e modificada apenas nos [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no [modelo de conjunto de dimensionamento](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Existem várias formas de aplicar a proteção de dimensionamento em instâncias do conjunto de dimensionamento, conforme explicado nos exemplos abaixo.

### <a name="rest-api"></a>API REST

O exemplo seguinte aplica-se a proteção de escala para uma instância no conjunto de dimensionamento.

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
>Proteção de instância só é suportada com a versão de 2019-03-01 de API e superior

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o [AzVmssVM atualização](/powershell/module/az.compute/update-azvmssvm) instância do conjunto de cmdlet para aplicar a proteção de redução horizontal para seu dimensionamento.

O exemplo seguinte aplica-se a proteção de escala para uma instância no conjunto de dimensionamento com o ID de instância 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Uso [az vmss update](/cli/azure/vmss#az-vmss-update) para aplicar a proteção de redução horizontal à sua instância de conjunto de dimensionamento.

O exemplo seguinte aplica-se a proteção de escala para uma instância no conjunto de dimensionamento com o ID de instância 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Proteger contra ações de conjunto de dimensionamento
Proteção de instância pode ser aplicada para instâncias de conjunto de dimensionamento, depois das instâncias são criadas. Proteção é aplicada e modificada apenas nos [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no [modelo de conjunto de dimensionamento](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Também é Protegendo uma instância de ações de conjunto de dimensionamento protege a instância de dimensionamento automático iniciada pelo dimensionamento-in.

Existem várias formas de aplicar a escala instâncias de conjunto de proteção de ações no seu dimensionamento conjunto conforme explicado nos exemplos abaixo.

### <a name="rest-api"></a>API REST

O exemplo seguinte aplica-se a proteção contra ações de conjunto de dimensionamento para uma instância no conjunto de dimensionamento.

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
>Proteção de instâncias apenas é suportada com a versão de 2019-03-01 de API e superior.</br>
Também é Protegendo uma instância de ações de conjunto de dimensionamento protege a instância de dimensionamento automático iniciada pelo dimensionamento-in. Não é possível especificar "protectFromScaleIn": falso quando a definição "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o [AzVmssVM atualização](/powershell/module/az.compute/update-azvmssvm) cmdlet para aplicar a proteção da escala de conjunto de ações à sua instância de conjunto de dimensionamento.

O exemplo seguinte aplica-se a proteção contra ações de conjunto de dimensionamento para uma instância no conjunto de dimensionamento com o ID de instância 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Uso [az vmss update](/cli/azure/vmss#az-vmss-update) para aplicar a proteção de ações de conjunto de dimensionamento à sua instância de conjunto de dimensionamento.

O exemplo seguinte aplica-se a proteção contra ações de conjunto de dimensionamento para uma instância no conjunto de dimensionamento com o ID de instância 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Resolução de problemas
### <a name="no-protectionpolicy-on-scale-set-model"></a>Não existem protectionPolicy no modelo de conjunto de dimensionamento
Proteção de instância só é aplicável em instâncias do conjunto de dimensionamento e não no modelo de conjunto de dimensionamento.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Não existem protectionPolicy no modelo de instância de conjunto de dimensionamento
Por predefinição, a política de proteção não se aplica a uma instância quando é criado.

Pode aplicar a proteção de instâncias para instâncias de conjunto de dimensionamento, depois das instâncias são criadas.

### <a name="not-able-to-apply-instance-protection"></a>Não é possível aplicar a proteção de instância
Proteção de instâncias apenas é suportada com a versão de 2019-03-01 de API e superior. Verifique a versão de API que está a ser utilizada e atualize conforme necessário. Poderá também ter de atualizar o PowerShell ou a CLI para a versão mais recente.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [implementar a sua aplicação](virtual-machine-scale-sets-deploy-app.md) conjuntos de dimensionamento de máquina virtual.
