---
title: Proteção de instância para instâncias do conjunto de dimensionamento de máquinas virtuais do Azure
description: Saiba como proteger as instâncias do conjunto de dimensionamento de máquinas virtuais do Azure de operações de expansão e de conjunto de escala.
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 071ea79f4d288e86cc5b9347f8607b4ff7190bc1
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275796"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>Proteção de instância para instâncias do conjunto de dimensionamento de máquinas virtuais do Azure (versão prévia)
Os conjuntos de dimensionamento de máquinas virtuais do Azure permitem uma melhor elasticidade para suas cargas de trabalho por meio do [dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md), para que você possa configurar quando sua infraestrutura é dimensionada e quando ela é dimensionada. Os conjuntos de dimensionamento também permitem que você gerencie, configure e atualize centralmente um grande número de VMs por meio de diferentes configurações de [política de atualização](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) . Você pode configurar uma atualização no modelo do conjunto de dimensionamento e a nova configuração será aplicada automaticamente a cada instância do conjunto de dimensionamento se você tiver definido a política de atualização como automática ou sem interrupção.

À medida que seu aplicativo processa o tráfego, pode haver situações em que você deseja que instâncias específicas sejam tratadas de forma diferente do restante da instância do conjunto de dimensionamento. Por exemplo, determinadas instâncias no conjunto de dimensionamento podem estar executando operações de longa execução e você não quer que essas instâncias sejam dimensionadas até que as operações sejam concluídas. Você também pode ter especializado algumas instâncias no conjunto de dimensionamento para executar tarefas adicionais ou diferentes dos outros membros do conjunto de dimensionamento. Você precisa que essas VMs ' especiais ' não sejam modificadas com as outras instâncias no conjunto de dimensionamento. A proteção de instância fornece os controles adicionais para habilitar esses e outros cenários para seu aplicativo.

Este artigo descreve como você pode aplicar e usar os diferentes recursos de proteção de instância com instâncias de conjunto de dimensionamento.

> [!NOTE]
>A proteção de instância está atualmente em visualização pública. Nenhum procedimento de aceitação é necessário para usar a funcionalidade de visualização pública descrita abaixo. A visualização da proteção de instância só tem suporte com a versão de API 2019-03-01 e em conjuntos de dimensionamento usando discos gerenciados.

## <a name="types-of-instance-protection"></a>Tipos de proteção de instância
Os conjuntos de dimensionamento fornecem dois tipos de recursos de proteção de instância:

-   **Proteger do Scale-in**
    - Habilitado por meio da propriedade **protectFromScaleIn** na instância do conjunto de dimensionamento
    - Protege a instância do dimensionamento automático iniciado
    - As operações de instância iniciadas pelo usuário (incluindo exclusão de instância) **não são bloqueadas**
    - As operações iniciadas no conjunto de dimensionamento (atualizar, refazer imagem, desalocar, etc.) **não são bloqueadas**

-   **Proteger contra ações de conjunto de dimensionamento**
    - Habilitado por meio da propriedade **protectFromScaleSetActions** na instância do conjunto de dimensionamento
    - Protege a instância do dimensionamento automático iniciado
    - Protege a instância de operações iniciadas no conjunto de dimensionamento (como atualizar, refazer imagem, desalocar, etc.)
    - As operações de instância iniciadas pelo usuário (incluindo exclusão de instância) **não são bloqueadas**
    - A exclusão do conjunto de dimensionamento completo **não está bloqueada**

## <a name="protect-from-scale-in"></a>Proteger do Scale-in
A proteção da instância pode ser aplicada às instâncias do conjunto de dimensionamento depois que as instâncias são criadas. A proteção é aplicada e modificada somente no [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no modelo de conjunto de [dimensionamento](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Há várias maneiras de aplicar a proteção de escala em suas instâncias do conjunto de dimensionamento, conforme detalhado nos exemplos abaixo.

### <a name="rest-api"></a>API REST

O exemplo a seguir aplica a proteção de scale-in a uma instância no conjunto de dimensionamento.

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
>A proteção de instância só tem suporte com a versão de API 2019-03-01 e posterior

### <a name="azure-powershell"></a>Azure PowerShell

Use o cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) para aplicar proteção de escala em sua instância do conjunto de dimensionamento.

O exemplo a seguir aplica a proteção de scale-in a uma instância no conjunto de dimensionamento com a ID de instância 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Use [AZ vmss Update](/cli/azure/vmss#az-vmss-update) para aplicar a proteção de escala em sua instância do conjunto de dimensionamento.

O exemplo a seguir aplica a proteção de scale-in a uma instância no conjunto de dimensionamento com a ID de instância 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Proteger contra ações de conjunto de dimensionamento
A proteção da instância pode ser aplicada às instâncias do conjunto de dimensionamento depois que as instâncias são criadas. A proteção é aplicada e modificada somente no [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no modelo de conjunto de [dimensionamento](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

A proteção de uma instância de ações do conjunto de dimensionamento também protege a instância do dimensionamento automático iniciado.

Há várias maneiras de aplicar a proteção de ações do conjunto de dimensionamento em suas instâncias do conjunto de dimensionamento, conforme detalhado nos exemplos abaixo.

### <a name="rest-api"></a>API REST

O exemplo a seguir aplica a proteção das ações do conjunto de dimensionamento a uma instância no conjunto de dimensionamento.

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
>A proteção de instância só tem suporte com a versão de API 2019-03-01 e superior.</br>
A proteção de uma instância de ações do conjunto de dimensionamento também protege a instância do dimensionamento automático iniciado. Você não pode especificar "protectFromScaleIn": false ao definir "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Use o cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) para aplicar proteção contra ações do conjunto de dimensionamento à sua instância do conjunto de dimensionamento.

O exemplo a seguir aplica a proteção das ações do conjunto de dimensionamento a uma instância no conjunto de dimensionamento com a ID de instância 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Use [AZ vmss Update](/cli/azure/vmss#az-vmss-update) para aplicar proteção contra ações do conjunto de dimensionamento à sua instância do conjunto de dimensionamento.

O exemplo a seguir aplica a proteção das ações do conjunto de dimensionamento a uma instância no conjunto de dimensionamento com a ID de instância 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Resolução de problemas
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nenhum protectionPolicy no modelo de conjunto de dimensionamento
A proteção de instância só é aplicável em instâncias de conjunto de dimensionamento e não no modelo de conjunto de dimensionamento.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nenhum protectionPolicy no modelo de instância do conjunto de dimensionamento
Por padrão, a política de proteção não é aplicada a uma instância do quando ela é criada.

Você pode aplicar a proteção de instância a instâncias de conjunto de dimensionamento depois que as instâncias são criadas.

### <a name="not-able-to-apply-instance-protection"></a>Não é possível aplicar a proteção de instância
A proteção de instância só tem suporte com a versão de API 2019-03-01 e superior. Verifique a versão da API que está sendo usada e atualize conforme necessário. Talvez você também precise atualizar o PowerShell ou a CLI para a versão mais recente.

## <a name="next-steps"></a>Passos seguintes
Saiba como [implantar seu aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais.
