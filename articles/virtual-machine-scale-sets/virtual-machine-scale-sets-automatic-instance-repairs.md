---
title: Reparações automáticas de instâncias com conjuntos de escala de máquinas virtuais Azure
description: Saiba como configurar a política de reparações automáticas para casos de VM num conjunto de escala
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274817"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Pré-visualização: Reparações automáticas de instâncias para conjuntos de escala de máquinas virtuais Azure

Permitir reparações automáticas de instâncias para conjuntos de escala de máquinas virtuais Azure ajuda a alcançar uma elevada disponibilidade para aplicações mantendo um conjunto de instâncias saudáveis. Se uma instância no conjunto de escala for considerada insalubre como relatado pela extensão da [aplicação saúde](./virtual-machine-scale-sets-health-extension.md) ou sondas de saúde de [balancer](../load-balancer/load-balancer-custom-probe-overview.md)de carga , então esta função executa automaticamente a reparação de instâncias, apagando a instância pouco saudável e criando uma nova para substituí-la.

> [!NOTE]
> Esta função de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisitos para a utilização de reparações automáticas por exemplo

**Opte pela pré-visualização de reparações automáticas de instâncias**

Utilize a API REST ou a Azure PowerShell para optar pela pré-visualização de reparações automáticas por exemplo. Estes passos irão registar a sua subscrição para a funcionalidade de pré-visualização. Note que esta é apenas uma configuração única necessária para a utilização desta funcionalidade. Se a sua subscrição já estiver registada para pré-visualização automática de reparações por exemplo, então não precisa de voltar a registar-se. 

Com a API REST 

1. Registe-se na funcionalidade utilizando [Funcionalidades - Registar](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. Aguarde alguns minutos para que o *Estado* mude para *o Registo.* Pode utilizar a seguinte API para confirmar isto.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. Uma vez que o *Estado* tenha mudado para *Registro,* então executar o seguinte.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Utilizar o Azure PowerShell

1. Registe-se na funcionalidade utilizando cmdlet [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) seguido do [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Aguarde alguns minutos para que o *Estado de Registo* mude para *Registro*. Pode utilizar o seguinte cmdlet para confirmar isto.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 A resposta deve ser a seguinte.

| Nome de características                           | ProviderName            | Estado de Registos       |
|---------------------------------------|-------------------------|-------------------------|
| ReparaçãoVMScaleSetInstancesPreview      | Microsoft.Compute       | Registado              |

3. Uma vez que o *Estado de Registo* para mudar para *Registro,* em seguida, executar o seguinte cmdlet.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Ativar a monitorização da saúde da aplicação para o conjunto de escala**

O conjunto de escala deve ter um acompanhamento da saúde da aplicação, por exemplo. Isto pode ser feito usando a extensão da saúde da [aplicação](./virtual-machine-scale-sets-health-extension.md) ou as sondas de saúde do [equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Apenas um destes pode ser ativado de cada vez. A extensão da saúde da aplicação ou as sondas do equilibrador de carga ping o ponto final da aplicação configurado em instâncias de máquinas virtuais para determinar o estado de saúde da aplicação. Este estado de saúde é utilizado pelo orquestrador de conjuntos de escala para monitorizar a saúde da instância e efetuar reparações quando necessário.

**Configure ponto final para fornecer estado de saúde**

Antes de permitir a política de reparações automáticas de instâncias, certifique-se de que as instâncias definidas pela escala têm ponto final de aplicação configurado para emitir o estado de saúde da aplicação. Quando uma instância devolve o estado 200 (OK) neste ponto final da aplicação, então a instância é marcada como "Saudável". Em todos os outros casos, a instância está marcada como "Insalubre", incluindo os seguintes cenários:

- Quando não há ponto final de aplicação configurado dentro das instâncias da máquina virtual para fornecer o estado de saúde da aplicação
- Quando o ponto final da aplicação estiver incorretamente configurado
- Quando o ponto final da aplicação não é alcançável

Por exemplo, marcados como "Insalubres", as reparações automáticas são desencadeadas pelo conjunto de escala. Certifique-se de que o ponto final da aplicação está corretamente configurado antes de ativar a política de reparações automáticas, a fim de evitar reparações não intencionais, enquanto o ponto final está a ser configurado.

**Ativar um único grupo de colocação**

Esta pré-visualização está atualmente disponível apenas para conjuntos de escala implantados como grupo de colocação único. O *singlePlacementGroup da* propriedade deve ser definido como *verdadeiro* para a sua balança definida para usar a função de reparação automática de instâncias. Saiba mais sobre grupos de [colocação.](./virtual-machine-scale-sets-placement-groups.md#placement-groups)

**Versão API**

A política de reparações automáticas é suportada para a versão api da computação 2018-10-01 ou superior.

**Restrições aos movimentos de recursos ou subscrições**

Como parte desta pré-visualização, os movimentos de recursos ou subscrições não são atualmente suportados para conjuntos de escala quando a política de reparações automáticas está ativada.

**Restrição para conjuntos de balança de tecido de serviço**

Esta função de pré-visualização não é suportada atualmente para conjuntos de balança de tecido de serviço.

## <a name="how-do-automatic-instance-repairs-work"></a>Como funcionam as reparações automáticas de instâncias?

A função automática de reparação por exemplo baseia-se na monitorização da saúde de instâncias individuais num conjunto de escala. As instâncias vM num conjunto de escala podem ser configuradas para emitir o estado de saúde da aplicação utilizando a extensão da [aplicação Saúde](./virtual-machine-scale-sets-health-extension.md) ou as sondas de saúde do [equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Se uma instância não for saudável, então o conjunto de escala executa uma ação de reparação, apagando a instância pouco saudável e criando uma nova para substituí-la. Esta função pode ser ativada no modelo de conjunto de escala de máquina virtual utilizando o objeto *Reparação automática.*

### <a name="batching"></a>Lotes

As operações automáticas de reparação de instâncias são realizadas em lotes. A qualquer momento, não mais de 5% das ocorrências no conjunto de escalas são reparadas através da política de reparações automáticas. Isto ajuda a evitar a supressão simultânea e a recriação de um grande número de casos se forem considerados insalubres ao mesmo tempo.

### <a name="grace-period"></a>Período de tolerância

Quando uma instância passa por uma operação de mudança de estado por causa de uma ação DE PUT, PATCH ou POST realizada na balança definida (por exemplo, reimagem, reimplantação, atualização, etc.), então qualquer ação de reparação nessa instância só é realizada após a espera do período de carência. O período de graça é a quantidade de tempo para permitir que a instância regresse a um estado saudável. O período de carência começa após a mudança de estado ter terminado. Isto ajuda a evitar quaisquer operações de reparação prematuras ou acidentais. O período de carência é honrado por qualquer instância recém-criada no conjunto de escala (incluindo o criado em resultado da operação de reparação). O período de graça é especificado em minutos no formato ISO 8601 e pode ser definido utilizando a propriedade *automáticaRepairsPolicy.gracePeriod*. O período de graça pode variar entre 30 minutos e 90 minutos, e tem um valor padrão de 30 minutos.

O processo de reparação automática de instâncias funciona da seguinte forma:

1. [Aplicação Extensão de saúde](./virtual-machine-scale-sets-health-extension.md) ou sondas de saúde de [balanceadores de carga](../load-balancer/load-balancer-custom-probe-overview.md) ping o ponto final da aplicação dentro de cada máquina virtual na escala definida para obter o estado de saúde da aplicação para cada instância.
2. Se o ponto final responder com um estado 200 (OK), então a instância é marcada como "Saudável". Em todos os outros casos (incluindo se o ponto final é inalcançável), a instância está marcada como "Insalubre".
3. Quando se constata que uma instância não é saudável, o conjunto de escala desencadeia uma ação de reparação, apagando a instância pouco saudável e criando uma nova para a substituir.
4. As reparações por exemplo são efetuadas em lotes. A qualquer momento, não são reparados mais de 5% do total de casos no conjunto de escala. Se um conjunto de escala supõe menos de 20 instâncias, as reparações são feitas por um caso pouco saudável de cada vez.
5. O processo acima continua até que todos os casos insalubres no conjunto de escala sejam reparados.

## <a name="instance-protection-and-automatic-repairs"></a>Proteção de exemplos e reparações automáticas

Se uma instância num conjunto de escala for protegida aplicando a *[proteção contra a política de proteção das ações](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* definidas à escala, então não são efetuadas reparações automáticas nesse caso.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Ativar a política de reparações automáticas ao criar um novo conjunto de escala

Para permitir a política de reparações automáticas, ao mesmo tempo que cria um novo conjunto de escala, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esta funcionalidade são cumpridos. O ponto final da aplicação deve ser corretamente configurado para as instâncias de conjunto de escala para evitar o desencadeamento de reparações não intencionais enquanto o ponto final está a ser configurado. Para conjuntos de escala recém-criados, quaisquer reparações por exemplo são efetuadas apenas após a espera da duração do período de carência. Para permitir a reparação automática da instância num conjunto de escala, utilize o objeto *Reparose automático* no modelo conjunto de conjuntos de máquinas virtuais.

### <a name="rest-api"></a>API REST

O exemplo que se segue mostra como permitir a reparação automática de instâncias num modelo de conjunto de escala. Utilize a versão API 2018-10-01 ou superior.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

A função de reparação automática de instâncias pode ser ativada ao mesmo tempo que cria uma nova escala, utilizando o cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Este script de amostra sai através da criação de um conjunto de escala e recursos associados usando o ficheiro de configuração: [Criar um conjunto completo](./scripts/powershell-sample-create-complete-scale-set.md)de escala de máquina virtual . Pode configurar a política de reparações automáticas de instâncias adicionando os parâmetros *EnableAutomaticRepair* e *AutomaticRepairGracePeriod* ao objeto de configuração para criar o conjunto de escala. O exemplo seguinte permite que a funcionalidade com um período de carência de 30 minutos.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Ativar a política de reparações automáticas ao atualizar um conjunto de escala existente

Antes de permitir a política de reparações automáticas num conjunto de escala existente, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para a adoção desta funcionalidade são cumpridos. O ponto final da aplicação deve ser corretamente configurado para as instâncias de conjunto de escala para evitar o desencadeamento de reparações não intencionais enquanto o ponto final está a ser configurado. Para permitir a reparação automática da instância num conjunto de escala, utilize o objeto *Reparose automático* no modelo conjunto de conjuntos de máquinas virtuais.

### <a name="rest-api"></a>API REST

O exemplo que se segue permite à política com período de carência de 40 minutos. Utilize a versão API 2018-10-01 ou superior.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para modificar a configuração da função de reparação automática de instâncias num conjunto de escala existente. O exemplo seguinte atualiza o período de graça para 40 minutos.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Resolução de problemas

**Falha na política de reparações automáticas**

Se tiver um erro 'BadRequest' com uma mensagem indicando "Não consegui encontrar o membro 'Reparos automáticos' em objeto de tipo 'propriedades'", verifique a versão API utilizada para o conjunto de escala de máquina virtual. A versão API 2018-10-01 ou superior é necessária para esta funcionalidade.

**Caso não ser reparado mesmo quando a política está ativada**

O caso pode estar em período de graça. Este é o tempo para esperar depois de qualquer alteração de estado na instância antes de efetuar reparações. Isto é para evitar reparações prematuras ou acidentais. A ação de reparação deve ocorrer uma vez concluído o período de carência.

**Ver o estado de saúde da aplicação para casos de conjunto de escala**

Pode utilizar a [API Get Instance View,](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) por exemplo, numa escala de máquina virtual para ver o estado de saúde da aplicação. Com o Azure PowerShell, pode utilizar o cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) com a bandeira *-InstanceView.* O estado de saúde da aplicação é fornecido no âmbito da propriedade *vmHealth*.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar a extensão da saúde da [aplicação](./virtual-machine-scale-sets-health-extension.md) ou as sondas de saúde do [equilibrador](../load-balancer/load-balancer-custom-probe-overview.md) de carga para os seus conjuntos de escala.
