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
ms.openlocfilehash: 8156c563573183e51e06650914117f8787922e93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81603669"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Reparações automáticas de exemplo para conjuntos de escala de máquinas virtuais Azure

Permitir reparações automáticas de instâncias para conjuntos de escala de máquinas virtuais Azure ajuda a alcançar uma elevada disponibilidade para aplicações mantendo um conjunto de instâncias saudáveis. Se uma instância no conjunto de escala for considerada insalubre como relatado pela extensão da [aplicação saúde](./virtual-machine-scale-sets-health-extension.md) ou sondas de saúde de [balancer](../load-balancer/load-balancer-custom-probe-overview.md)de carga , então esta função executa automaticamente a reparação de instâncias, apagando a instância pouco saudável e criando uma nova para substituí-la.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisitos para a utilização de reparações automáticas por exemplo

**Ativar a monitorização da saúde da aplicação para o conjunto de escala**

O conjunto de escala deve ter um acompanhamento da saúde da aplicação, por exemplo. Isto pode ser feito usando a extensão da saúde da [aplicação](./virtual-machine-scale-sets-health-extension.md) ou as sondas de saúde do [equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Apenas um destes pode ser ativado de cada vez. A extensão da saúde da aplicação ou as sondas do equilibrador de carga ping o ponto final da aplicação configurado em instâncias de máquinas virtuais para determinar o estado de saúde da aplicação. Este estado de saúde é utilizado pelo orquestrador de conjuntos de escala para monitorizar a saúde da instância e efetuar reparações quando necessário.

**Configure ponto final para fornecer estado de saúde**

Antes de permitir a política de reparações automáticas de instâncias, certifique-se de que as instâncias definidas pela escala têm ponto final de aplicação configurado para emitir o estado de saúde da aplicação. Quando uma instância devolve o estado 200 (OK) neste ponto final da aplicação, então a instância é marcada como "Saudável". Em todos os outros casos, a instância está marcada como "Insalubre", incluindo os seguintes cenários:

- Quando não há ponto final de aplicação configurado dentro das instâncias da máquina virtual para fornecer o estado de saúde da aplicação
- Quando o ponto final da aplicação estiver incorretamente configurado
- Quando o ponto final da aplicação não é alcançável

Por exemplo, marcados como "Insalubres", as reparações automáticas são desencadeadas pelo conjunto de escala. Certifique-se de que o ponto final da aplicação está corretamente configurado antes de ativar a política de reparações automáticas, a fim de evitar reparações não intencionais, enquanto o ponto final está a ser configurado.

**Ativar um único grupo de colocação**

Esta funcionalidade encontra-se atualmente disponível apenas para conjuntos de escala implantados como grupo de colocação único. O *singlePlacementGroup da* propriedade deve ser definido como *verdadeiro* para a sua balança definida para usar a função de reparação automática de instâncias. Saiba mais sobre grupos de [colocação.](./virtual-machine-scale-sets-placement-groups.md#placement-groups)

**Versão API**

A política de reparações automáticas é suportada para a versão api da computação 2018-10-01 ou superior.

**Restrições aos movimentos de recursos ou subscrições**

Os movimentos de recursos ou subscrições não são atualmente suportados para conjuntos de escala quando a função de reparação automática está ativada.

**Restrição para conjuntos de balança de tecido de serviço**

Esta funcionalidade não é suportada atualmente para conjuntos de balança de tecido de serviço.

## <a name="how-do-automatic-instance-repairs-work"></a>Como funcionam as reparações automáticas de instâncias?

A função automática de reparação por exemplo baseia-se na monitorização da saúde de instâncias individuais num conjunto de escala. As instâncias vM num conjunto de escala podem ser configuradas para emitir o estado de saúde da aplicação utilizando a extensão da [aplicação Saúde](./virtual-machine-scale-sets-health-extension.md) ou as sondas de saúde do [equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Se uma instância não for saudável, então o conjunto de escala executa uma ação de reparação, apagando a instância pouco saudável e criando uma nova para substituí-la. O mais recente modelo de conjunto de escala de máquina virtual é usado para criar a nova instância. Esta função pode ser ativada no modelo de conjunto de escala de máquina virtual utilizando o objeto *Reparação automática.*

### <a name="batching"></a>Lotes

As operações automáticas de reparação de instâncias são realizadas em lotes. A qualquer momento, não mais de 5% das ocorrências no conjunto de escalas são reparadas através da política de reparações automáticas. Isto ajuda a evitar a supressão simultânea e a recriação de um grande número de casos se forem considerados insalubres ao mesmo tempo.

### <a name="grace-period"></a>Período de tolerância

Quando uma instância passa por uma operação de mudança de estado por causa de uma ação DE PUT, PATCH ou POST realizada na balança definida (por exemplo, reimagem, reimplantação, atualização, etc.), então qualquer ação de reparação nessa instância só é realizada após a espera do período de carência. O período de graça é a quantidade de tempo para permitir que a instância regresse a um estado saudável. O período de carência começa após a mudança de estado ter terminado. Isto ajuda a evitar quaisquer operações de reparação prematuras ou acidentais. O período de carência é honrado por qualquer instância recém-criada no conjunto de escala (incluindo o criado em resultado da operação de reparação). O período de graça é especificado em minutos no formato ISO 8601 e pode ser definido utilizando a propriedade *automáticaRepairsPolicy.gracePeriod*. O período de graça pode variar entre 30 minutos e 90 minutos, e tem um valor padrão de 30 minutos.

### <a name="suspension-of-repairs"></a>Suspensão de Reparações 

Os conjuntos de escala de máquina virtual fornecem a capacidade de suspender temporariamente as reparações automáticas de instâncias, se necessário. O *serviçoEstado* para reparações automáticas sob a orquestração de *propriedadeServiços* em exemplo vista de conjunto de escala de máquina virtual mostra o estado atual das reparações automáticas. Quando um conjunto de escala é optado em reparações automáticas, o valor do serviço de *parâmetros Estado* está definido para *Executar*. Quando as reparações automáticas são suspensas por um conjunto de escala, o serviço de *parâmetros Estado* está *programado*para suspenso . Se a política *automática DeReparos* for definida num conjunto de escala, mas a função de reparação automática não estiver ativada, então o serviço de *parâmetros State* is set to *Not Running*.

Se os casos recém-criados para substituir os não saudáveis num conjunto de escala continuam a não ser saudáveis mesmo depois de realizarem repetidamente operações de reparação, então, como medida de segurança, a plataforma atualiza o *serviço Estado* para reparações automáticas a *Suspensas*. Pode retomar as reparações automáticas, definindo o valor do *serviçoState* para reparações automáticas em *Execução*. Instruções detalhadas são fornecidas na secção sobre [visualização e atualização do estado de serviço da política de reparações automáticas](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) para o seu conjunto de escala. 

O processo de reparação automática de instâncias funciona da seguinte forma:

1. [Aplicação Extensão de saúde](./virtual-machine-scale-sets-health-extension.md) ou sondas de saúde de [balanceadores de carga](../load-balancer/load-balancer-custom-probe-overview.md) ping o ponto final da aplicação dentro de cada máquina virtual na escala definida para obter o estado de saúde da aplicação para cada instância.
2. Se o ponto final responder com um estado 200 (OK), então a instância é marcada como "Saudável". Em todos os outros casos (incluindo se o ponto final é inalcançável), a instância está marcada como "Insalubre".
3. Quando se constata que uma instância não é saudável, o conjunto de escala desencadeia uma ação de reparação, apagando a instância pouco saudável e criando uma nova para a substituir.
4. As reparações por exemplo são efetuadas em lotes. A qualquer momento, não são reparados mais de 5% do total de casos no conjunto de escala. Se um conjunto de escala supõe menos de 20 instâncias, as reparações são feitas por um caso pouco saudável de cada vez.
5. O processo acima continua até que todos os casos insalubres no conjunto de escala sejam reparados.

## <a name="instance-protection-and-automatic-repairs"></a>Proteção de exemplos e reparações automáticas

Se uma instância num conjunto de escala for protegida aplicando uma das políticas de [proteção,](./virtual-machine-scale-sets-instance-protection.md)não são efetuadas reparações automáticas nesse caso. Isto aplica-se tanto às políticas de proteção: *Proteger contra* a escala e proteger contra ações *de escala.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Encerrar notificação e reparações automáticas

Se a funcionalidade de [notificação de fim](./virtual-machine-scale-sets-terminate-notification.md) estiver ativada num conjunto de escala, então durante o funcionamento de reparação automática, a eliminação de uma instância pouco saudável segue a configuração de notificação de fim. Uma notificação de rescisão é enviada através do serviço de metadados Azure – eventos agendados – e a eliminação por exemplo é adiada durante a duração do tempo de atraso configurado. No entanto, a criação de um novo exemplo para substituir o insalubre não aguarda que o tempo de atraso esteja concluído.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Ativar a política de reparações automáticas ao criar um novo conjunto de escala

Para permitir a política de reparações automáticas, ao mesmo tempo que cria um novo conjunto de escala, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esta funcionalidade são cumpridos. O ponto final da aplicação deve ser corretamente configurado para as instâncias de conjunto de escala para evitar o desencadeamento de reparações não intencionais enquanto o ponto final está a ser configurado. Para conjuntos de escala recém-criados, quaisquer reparações por exemplo são efetuadas apenas após a espera da duração do período de carência. Para permitir a reparação automática da instância num conjunto de escala, utilize o objeto *Reparose automático* no modelo conjunto de conjuntos de máquinas virtuais.

Também pode utilizar este [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) para implantar um conjunto de escala de máquina virtual com sonda de saúde de equilíbrio de carga e reparações automáticas de instâncias ativadas com um período de carência de 30 minutos.

### <a name="azure-portal"></a>Portal do Azure
 
Os seguintes passos permitem a política de reparações automáticas ao criar um novo conjunto de escala.
 
1. Vá a conjuntos de **escala de máquinas virtuais.**
1. Selecione **+ Adicione** para criar um novo conjunto de escala.
1. Vá ao separador **Saúde.** 
1. Localize a secção **de Saúde.**
1. Ativar a opção de saúde da **aplicação Monitor.**
1. Localize a secção de política de **reparação automática.**
1. Ligue **On** a opção **de reparação automática.**
1. No **período Grace (min)**, especifique o período de graça em minutos, os valores permitidos são entre 30 e 90 minutos. 
1. Quando terminar de criar o novo conjunto de escala, selecione **Review + crie** o botão.

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

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

O exemplo seguinte permite a política de reparações automáticas, ao mesmo tempo que cria um novo conjunto de escala utilizando a *[z vmss criar](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)*. Primeiro crie um grupo de recursos, em seguida, crie um novo conjunto de escala com período de carência de política de reparações automáticas definido para 30 minutos.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-period 30
```

O exemplo acima utiliza um equilibrador de carga existente e uma sonda de saúde para monitorizar o estado de saúde das aplicações de instâncias. Se preferir utilizar uma extensão de saúde de aplicação para monitorização, pode criar um conjunto de escala, configurar a extensão de saúde da aplicação e, em seguida, ativar a política de reparação automática de instâncias utilizando a *atualização az vmss,* como explicado na secção seguinte.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Ativar a política de reparações automáticas ao atualizar um conjunto de escala existente

Antes de permitir a política de reparações automáticas num conjunto de escala existente, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para a adoção desta funcionalidade são cumpridos. O ponto final da aplicação deve ser corretamente configurado para as instâncias de conjunto de escala para evitar o desencadeamento de reparações não intencionais enquanto o ponto final está a ser configurado. Para permitir a reparação automática da instância num conjunto de escala, utilize o objeto *Reparose automático* no modelo conjunto de conjuntos de máquinas virtuais.

Depois de atualizar o modelo de um conjunto de escala existente, certifique-se de que o modelo mais recente é aplicado a todas as instâncias da escala. Consulte as instruções sobre como aproximar os [VMs com o modelo mais recente](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)de conjunto de escala .

### <a name="azure-portal"></a>Portal do Azure

Pode modificar a política de reparações automáticas de uma escala existente definida através do portal Azure. 
 
1. Vá a um conjunto de máquinas virtuais existente.
1. Em **Definições** no menu à esquerda, selecione **Saúde e reparação**.
1. Ativar a opção de saúde da **aplicação Monitor.**
1. Localize a secção de política de **reparação automática.**
1. Ligue **On** a opção **de reparação automática.**
1. No **período Grace (min)**, especifique o período de graça em minutos, os valores permitidos são entre 30 e 90 minutos. 
1. Quando tiver terminado, selecione **Guardar**. 

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

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Segue-se um exemplo para a atualização da política de reparação automática por exemplo de um conjunto de escala existente, utilizando a *[atualização az vmss](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Visualização e atualização do estado de serviço da política de reparação automática de instâncias

### <a name="rest-api"></a>API REST 

Use [Get Instance View](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) com a versão API 2019-12-01 ou superior para a escala de máquina virtual definida para ver o *serviçoEstado* para reparações automáticas sob a orquestração de *propriedadeServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Utilizar *setOrchestrationServiceState* API com a versão API 2019-12-01 ou superior numa escala de máquina virtual definida para definir o estado das reparações automáticas. Uma vez que o conjunto de escala é optado pela função de reparação automática, pode utilizar esta API para suspender ou retomar as reparações automáticas para o seu conjunto de escala. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>CLI do Azure 

Utilize cmdlet [de visualização get-instance](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) para visualizar o estado de *serviço* para reparações automáticas por exemplo. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Utilize cmdlet de [serviço de set-orchestration-service-service](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) para atualizar o *serviço Estado* para reparações automáticas por exemplo. Uma vez que o conjunto de calcário é optado pela função de reparação automática, pode utilizar este cmdlet para suspender ou retomar as reparações automáticas para o seu conjunto de escala. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Utilize [o Cmdlet Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) com parâmetro *InstanceView* para visualizar o *ServiceState* para reparações automáticas por exemplo.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Utilize set-AzVmssOrchestrationServiceState cmdlet para atualizar o *serviçoState* para reparações automáticas por exemplo. Uma vez que o conjunto de calcário é optado pela função de reparação automática, pode utilizar este cmdlet para suspender ou retomar as reparações automáticas para o seu conjunto de escala.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Resolução de problemas

**Falha na política de reparações automáticas**

Se tiver um erro 'BadRequest' com uma mensagem indicando "Não consegui encontrar o membro 'Reparos automáticos' em objeto de tipo 'propriedades'", verifique a versão API utilizada para o conjunto de escala de máquina virtual. A versão API 2018-10-01 ou superior é necessária para esta funcionalidade.

**Caso não ser reparado mesmo quando a política está ativada**

O caso pode estar em período de graça. Este é o tempo para esperar depois de qualquer alteração de estado na instância antes de efetuar reparações. Isto é para evitar reparações prematuras ou acidentais. A ação de reparação deve ocorrer uma vez concluído o período de carência.

**Ver o estado de saúde da aplicação para casos de conjunto de escala**

Pode utilizar a [API Get Instance View,](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) por exemplo, numa escala de máquina virtual para ver o estado de saúde da aplicação. Com o Azure PowerShell, pode utilizar o cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) com a bandeira *-InstanceView.* O estado de saúde da aplicação é fornecido no âmbito da propriedade *vmHealth*.

No portal Azure, pode ver também o estado de saúde. Vá a um conjunto de escala existente, selecione **Instâncias** do menu à esquerda, e olhe para a coluna do Estado de **Saúde** para o estado de saúde de cada instância definida por escala. 

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar a extensão da saúde da [aplicação](./virtual-machine-scale-sets-health-extension.md) ou as sondas de saúde do [equilibrador](../load-balancer/load-balancer-custom-probe-overview.md) de carga para os seus conjuntos de escala.
