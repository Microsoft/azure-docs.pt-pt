---
title: Reparações automáticas de instância com conjuntos de escala de máquina virtual Azure
description: Saiba como configurar a política de reparações automáticas para instâncias VM num conjunto de escala
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ff67ac4be32142848a12185199d63db5a14e6c34
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501860"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Reparações de instâncias automáticas dos conjuntos de dimensionamento de máquinas virtuais do Azure

Permitir reparações automáticas de instâncias para conjuntos de escala de máquina virtual Azure ajuda a alcançar uma elevada disponibilidade para aplicações mantendo um conjunto de instâncias saudáveis. Se se verificar que uma instância no conjunto de escalas não é saudável, tal como reportada pelas sondas de [saúde da Aplicação Health](./virtual-machine-scale-sets-health-extension.md) ou [do balancer de carga,](../load-balancer/load-balancer-custom-probe-overview.md)esta funcionalidade executa automaticamente a reparação de casos, eliminando o caso pouco saudável e criando uma nova para a substituir.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisitos para a utilização de reparações automáticas de instâncias

**Permitir a monitorização da saúde da aplicação para conjunto de escalas**

O conjunto de escalas deve ter um acompanhamento de saúde da aplicação, por exemplo, ativado. Isto pode ser feito utilizando a [extensão de aplicação de saúde](./virtual-machine-scale-sets-health-extension.md) ou [as sondas de saúde do balanceador](../load-balancer/load-balancer-custom-probe-overview.md)de carga. Só um destes pode ser ativado de cada vez. A extensão de saúde da aplicação ou o equilibrador de carga sonda o ponto final da aplicação configurado em casos de máquina virtual para determinar o estado de saúde da aplicação. Este estado de saúde é utilizado pelo orquestrador de conjunto de escalas para monitorizar a saúde da instância e efetuar reparações quando necessário.

**Configure o ponto final para fornecer o estado de saúde**

Antes de permitir a política de reparações automáticas de instâncias, certifique-se de que as instâncias definidas em escala têm o ponto final de aplicação configurado para emitir o estado de saúde da aplicação. Quando um caso devolve o estado 200 (OK) neste ponto final da aplicação, então a instância é marcada como "Saudável". Em todos os outros casos, o caso é marcado como "Insalubre", incluindo os seguintes cenários:

- Quando não há nenhum ponto final de aplicação configurado dentro das instâncias da máquina virtual para fornecer o estado de saúde da aplicação
- Quando o ponto final da aplicação estiver configurado incorretamente
- Quando o ponto final da aplicação não é alcançável

Por exemplos marcados como "Insalubres", as reparações automáticas são desencadeadas pelo conjunto de escalas. Certifique-se de que o ponto final da aplicação está corretamente configurado antes de permitir a política de reparações automáticas, a fim de evitar reparações de instâncias não intencionais, enquanto o ponto final está a ficar configurado.

**Número máximo de ocorrências no conjunto de escala**

Atualmente esta funcionalidade está disponível apenas para conjuntos de escala que tenham um máximo de 200 instâncias. O conjunto de escala pode ser implantado como um único grupo de colocação ou um grupo multi-colocação, no entanto a contagem de instâncias não pode ser superior a 200 se as reparações automáticas de instâncias forem ativadas para o conjunto de escalas.

**Versão API**

A política de reparações automáticas é suportada para a versão API computacional 2018-10-01 ou superior.

**Restrições aos movimentos de recursos ou subscrição**

Atualmente, os movimentos de recursos ou de subscrição não são suportados para conjuntos de escala quando a funcionalidade de reparação automática está ativada.

**Restrição para conjuntos de balança de tecido de serviço**

Atualmente, esta função não é suportada para conjuntos de balanças de tecido de serviço.

## <a name="how-do-automatic-instance-repairs-work"></a>Como funcionam as reparações automáticas de casos?

A função de reparação automática de instâncias baseia-se na monitorização sanitária de instâncias individuais num conjunto de escala. As instâncias VM num conjunto de escala podem ser configuradas para emitir o estado de saúde da aplicação utilizando a [extensão de Saúde](./virtual-machine-scale-sets-health-extension.md) da Aplicação ou [as sondas de saúde do balanceador](../load-balancer/load-balancer-custom-probe-overview.md)de carga . Se um caso não for saudável, então o conjunto de escala executa uma ação de reparação eliminando o caso pouco saudável e criando um novo para substituí-lo. O mais recente modelo de conjunto de escala de máquina virtual é usado para criar o novo exemplo. Esta função pode ser ativada no modelo de conjunto de escala de máquina virtual utilizando o objeto *automáticoRepairsPolicy.*

### <a name="batching"></a>Lotes

As operações automáticas de reparação de casos são efetuadas em lotes. Em qualquer momento, não mais de 5% das instâncias do conjunto de escalas são reparadas através da política de reparações automáticas. Isto ajuda a evitar a eliminação simultânea e a recriação de um grande número de casos se for considerado insalubre ao mesmo tempo.

### <a name="grace-period"></a>Período de tolerância

Quando uma instância passa por uma operação de mudança de estado por causa de uma ação PUT, PATCH ou POST realizada no conjunto de escala (por exemplo, reimagem, reafectação, atualização, etc.), qualquer ação de reparação nesse caso só é realizada após a espera do período de carência. O período de graça é o tempo que permite que a instância regresse a um estado saudável. O período de graça começa depois da mudança de estado ter terminado. Isto ajuda a evitar quaisquer operações de reparação prematuras ou acidentais. O período de graça é honrado por qualquer instância recém-criada no conjunto de escala (incluindo o criado como resultado da operação de reparação). O período de graça é especificado em minutos no formato ISO 8601 e pode ser definido usando a propriedade *automáticaRepairsPolicy.gracePeriod*. O período de graça pode variar entre 30 minutos e 90 minutos, e tem um valor predefinido de 30 minutos.

### <a name="suspension-of-repairs"></a>Suspensão das Reparações 

Os conjuntos de escala de máquina virtual fornecem a capacidade de suspender temporariamente as reparações automáticas de instâncias, se necessário. O *serviço Estado* para reparações automáticas sob a orquestração da propriedade Os serviços de *orquestração,* por exemplo, vistas do conjunto de escalas de máquinas virtuais mostram o estado atual das reparações automáticas. Quando um conjunto de escala é optado por reparações automáticas, o valor do serviço de *parâmetrosState* é definido para *Executar*. Quando as reparações automáticas forem suspensas para um conjunto de escala, o *serviço de parâmetros Estado* está definido para *Suspenso*. Se *a Política automática deRepairs* é definida num conjunto de escala, mas a função de reparação automática não está ativada, então o serviço de *parâmetrosState* está definido para *Não Funcionar*.

Se os casos recém-criados para substituir os insalubres num conjunto de escala continuarem a não ser saudáveis mesmo depois de terem realizado repetidamente operações de reparação, então, como medida de segurança, a plataforma atualiza o *serviço Estado* para reparações automáticas a *Suspender.* Pode retomar as reparações automáticas, definindo o valor do *serviço Estado* para reparações automáticas em *Funcionamento*. São fornecidas instruções detalhadas na secção de [visualização e atualização do estado de serviço da política de reparações automáticas](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) para o seu conjunto de escalas. 

O processo de reparação de instâncias automáticas funciona da seguinte forma:

1. [Aplicação Extensão de saúde](./virtual-machine-scale-sets-health-extension.md) ou [sondas de saúde de balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md) ping o ponto final da aplicação dentro de cada máquina virtual na escala definida para obter o estado de saúde da aplicação para cada instância.
2. Se o ponto final responder com um estado 200 (OK), então o caso é marcado como "Saudável". Em todos os outros casos (incluindo se o ponto final for inalcançável), o caso está marcado como "Insalubre".
3. Quando se constata que um caso não é saudável, o conjunto de escalas desencadeia uma ação de reparação eliminando o caso pouco saudável e criando um novo para substituí-lo.
4. As reparações de casos são efetuadas em lotes. Em qualquer momento, não são reparados mais de 5% do total de instâncias na balança definida. Se um conjunto de escala tiver menos de 20 instâncias, as reparações são feitas por um caso pouco saudável de cada vez.
5. O processo acima continua até que todos os casos insalubres no conjunto de escala sejam reparados.

## <a name="instance-protection-and-automatic-repairs"></a>Proteção de casos e reparações automáticas

Se uma instância num conjunto de escala estiver protegida aplicando uma das políticas de [proteção,](./virtual-machine-scale-sets-instance-protection.md)as reparações automáticas não são efetuadas nesse caso. Isto aplica-se a ambas as políticas de proteção: *Proteger contra a escala* e proteger contra ações de *escala.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Notificação de fim de ano e reparações automáticas

Se a [função de notificação de terminação](./virtual-machine-scale-sets-terminate-notification.md) estiver ativada num conjunto de escala, então durante a operação de reparação automática, a supressão de uma instância pouco saudável segue a configuração da notificação de terminação. Uma notificação de encerramento é enviada através do serviço de metadados Azure – eventos programados – e a eliminação por instância é adiada para a duração do tempo de atraso configurado. No entanto, a criação de um novo caso para substituir o insalubre não espera que o prazo de demora esteja concluído.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Permitir a política de reparações automáticas ao criar um conjunto de escala nova

Para permitir a política de reparações automáticas e criar um conjunto de escala nova, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esta funcionalidade são cumpridos. O ponto final da aplicação deve ser configurado corretamente para instâncias definidas em escala para evitar o desencadear de reparações não intencionais enquanto o ponto final está a ficar configurado. Para conjuntos de escala recém-criados, quaisquer reparações de instâncias são efetuadas apenas após a espera da duração do período de carência. Para ativar a reparação automática de instâncias num conjunto de escala, utilize o objeto *automáticoRepairsPolicy* no modelo de conjunto de escala de máquina virtual.

Também pode utilizar este [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) para implantar uma balança de máquina virtual definida com sonda de saúde do balanceador de carga e reparações automáticas de instâncias ativadas com um período de carência de 30 minutos.

### <a name="azure-portal"></a>Portal do Azure
 
Os seguintes passos que permitem a política de reparações automáticas ao criar um conjunto de escala nova.
 
1. Aceda aos **conjuntos de escala de máquina virtual**.
1. **Selecione + Adicione** para criar um conjunto de escala nova.
1. Vá ao separador **Saúde.** 
1. Localize a secção **Saúde.**
1. Ativar a opção de saúde da **aplicação Monitor.**
1. Localize a secção **de política de reparação automática.**
1. Ligue  a opção **de reparação automática.**
1. No **período de graça (min)**, especifique o período de carência em minutos, os valores permitidos são entre 30 e 90 minutos. 
1. Quando terminar de criar o novo conjunto de escala, selecione **Rever + criar** o botão.

### <a name="rest-api"></a>API REST

O exemplo a seguir mostra como permitir a reparação automática de casos num modelo de conjunto de escala. Utilize a versão API 2018-10-01 ou superior.

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

A função de reparação de instâncias automáticas pode ser ativada ao criar uma nova escala definida utilizando o cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Este script de amostra percorre a criação de um conjunto de escala e recursos associados utilizando o ficheiro de configuração: [Criar um conjunto completo de escala de máquina virtual](./scripts/powershell-sample-create-complete-scale-set.md). Pode configurar a política de reparações automáticas de instâncias adicionando os parâmetros *EnableAutomaticRepair* e *AutomaticRepairGracePeriod* ao objeto de configuração para criar o conjunto de escala. O exemplo a seguir permite a funcionalidade com um período de carência de 30 minutos.

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

O exemplo a seguir permite a política de reparações automáticas, ao mesmo tempo que cria um conjunto de nova escala utilizando *[a az vmss create](/cli/azure/vmss#az-vmss-create)*. Primeiro crie um grupo de recursos, em seguida, crie um conjunto de nova escala com período de graça de reparação automática definido para 30 minutos.

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
  --automatic-repairs-grace-period 30
```

O exemplo acima referido utiliza um equilibrador de carga e uma sonda sanitária existentes para monitorizar o estado de saúde da aplicação de casos. Se preferir utilizar uma extensão de saúde da aplicação para monitorização, pode criar um conjunto de escala, configurar a extensão de saúde da aplicação e, em seguida, ativar a política de reparações de instâncias automáticas utilizando a *atualização az vmss*, como explicado na secção seguinte.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Habilitar a política de reparações automáticas ao atualizar um conjunto de escala existente

Antes de permitir a política de reparações automáticas num conjunto de escala existente, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esta funcionalidade são cumpridos. O ponto final da aplicação deve ser configurado corretamente para instâncias definidas em escala para evitar o desencadear de reparações não intencionais enquanto o ponto final está a ficar configurado. Para ativar a reparação automática de instâncias num conjunto de escala, utilize o objeto *automáticoRepairsPolicy* no modelo de conjunto de escala de máquina virtual.

Depois de atualizar o modelo de um conjunto de escala existente, certifique-se de que o modelo mais recente é aplicado a todas as instâncias da escala. Consulte as instruções sobre [como aproximar os VMs com o modelo de conjunto de escala mais recente](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Portal do Azure

Pode modificar a política de reparações automáticas de uma escala existente definida através do portal Azure. 
 
1. Aceda a um conjunto de escala de máquina virtual existente.
1. Em **Definições** no menu à esquerda, selecione **Saúde e reparação**.
1. Ativar a opção de saúde da **aplicação Monitor.**
1. Localize a secção **de política de reparação automática.**
1. Ligue  a opção **de reparação automática.**
1. No **período de graça (min)**, especifique o período de carência em minutos, os valores permitidos são entre 30 e 90 minutos. 
1. Quando tiver terminado, selecione **Guardar**. 

### <a name="rest-api"></a>API REST

O exemplo a seguir permite a política com um período de carência de 40 minutos. Utilize a versão API 2018-10-01 ou superior.

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

Utilize o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para modificar a configuração da função de reparação automática de instâncias num conjunto de escala existente. O exemplo a seguir atualiza o período de carência para 40 minutos.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Segue-se um exemplo para atualizar a política de reparações automáticas de instâncias automáticas de um conjunto de escala existente, utilizando *[a atualização az vmss](/cli/azure/vmss#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Visualização e atualização do estado de serviço da política de reparações automáticas de instâncias

### <a name="rest-api"></a>API REST 

Use [Get Instance View](/rest/api/compute/virtualmachinescalesets/getinstanceview) com a versão API 2019-12-01 ou superior para a escala de máquina virtual definida para ver o serviço *Estado* para reparações automáticas sob os *serviços de orquestração* da propriedade . 

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

Utilize *o setOrchestrationServiceState* API com a versão API 2019-12-01 ou superior numa escala de máquina virtual definida para definir o estado das reparações automáticas. Uma vez que o conjunto de balanças é optado pela função de reparação automática, pode utilizar esta API para suspender ou retomar as reparações automáticas para o seu conjunto de escala. 

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

Utilize o cmdlet [get-instance-view](/cli/azure/vmss#az-vmss-get-instance-view) para visualizar o *Estado de serviço* para reparações automáticas de instâncias. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Utilize [o cmdlet estado de serviço de orquestração de conjunto](/cli/azure/vmss#az-vmss-set-orchestration-service-state) para atualizar o serviço *Estado* para reparações automáticas de instâncias. Uma vez que o conjunto de balança é optado pela função de reparação automática, então pode utilizar este cmdlet para suspender ou retomar as reparações automáticas para o seu conjunto de balanças. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Utilize o cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) com parâmetro *InstanceView* para visualizar o *Estado de Serviço* para reparações automáticas de instâncias.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Utilize Set-AzVmssOrchestrationServiceState cmdlet para atualizar o *estado de serviço* para reparações automáticas de instâncias. Uma vez que o conjunto de balanças é optado pela função de reparação automática, pode utilizar este cmdlet para suspender ou retomar as reparações automáticas para o seu conjunto de escalas.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Resolução de problemas

**Falha na política de reparações automáticas**

Se obter um erro de 'BadRequest' com uma mensagem indicando "Não foi possível encontrar o membro 'AutomaticRepairsPolicy' no objeto do tipo 'propriedades'", verifique a versão API utilizada para o conjunto de escalas de máquina virtual. Para esta funcionalidade é necessária a versão API 2018-10-01 ou superior.

**Caso não ser reparado mesmo quando a política está ativada**

O caso pode estar em período de graça. Esta é a quantidade de tempo para esperar depois de qualquer alteração de estado na instância antes de efetuar reparações. Isto é para evitar reparações prematuras ou acidentais. A ação de reparação deve ocorrer uma vez que o período de carência esteja concluído, por exemplo.

**Visualização do estado de saúde da aplicação para instâncias definidas em escala**

Pode utilizar a [API Get Instance View,](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) por exemplo, numa escala de máquina virtual definida para visualizar o estado de saúde da aplicação. Com a Azure PowerShell, pode utilizar o cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) com a bandeira *-InstanceView.* O estado de saúde da aplicação é fornecido ao abrigo da propriedade *vmHealth.*

No portal Azure, também é possível ver o estado de saúde. Vá a um conjunto de escala existente, selecione **Instâncias** do menu à esquerda e olhe para a coluna **do estado de Saúde** para o estado de saúde de cada instância definida em escala. 

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar a [extensão de saúde da aplicação](./virtual-machine-scale-sets-health-extension.md) ou [as sondas de saúde do balanceador](../load-balancer/load-balancer-custom-probe-overview.md) de carga para os seus conjuntos de escala.
