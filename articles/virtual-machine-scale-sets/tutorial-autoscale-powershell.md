---
title: Tutorial – Dimensionar automaticamente um conjunto de dimensionamento com o Azure PowerShell | Microsoft Docs
description: Saiba como dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais com o Azure PowerShell à medida que a CPU exige aumentos e diminuições
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5a87563e54e0d932acefc5bd5808ac49b3cbf30a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005232"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: Dimensionar automaticamente um conjunto de dimensionamento com o Azure PowerShell

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Quando criar um conjunto de dimensionamento, pode definir o número de instâncias de VM que quer executar. À medida que a sua aplicação exige alterações, pode aumentar ou reduzir automaticamente o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a par da exigência do cliente ou responder às alterações de desempenho durante todo o ciclo de vida da aplicação. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e utilizar regras de dimensionamento automático
> * Teste de esforço das instâncias e acionar as regras de dimensionamento automático
> * Voltar ao dimensionamento automático à medida que a exigência diminui

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Existe um problema conhecido que afeta a versão do módulo 6.8.1 do Azure PowerShell ou posterior, incluindo a versão atual do Azure Cloud Shell. Este tutorial pode ser executado apenas com a versão 6.0.0 à 6.8.0 do Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzureRmAccount` para criar uma ligação com o Azure.


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Para facilitar a criação de regras de dimensionamento automático, defina algumas variáveis para o conjunto de dimensionamento. O exemplo seguinte define as variáveis para o conjunto de dimensionamento denominado *myScaleSet* no grupo de recursos *myResourceGroup* na região *E.U.A. Leste*. O ID de subscrição é obtido com [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Se tiver várias subscrições associadas à sua conta, apenas é devolvida a primeira subscrição. Ajuste os nomes e o ID de subscrição da seguinte forma:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Para distribuir o tráfego para instâncias de VM individuais, é também criado um balanceador de carga. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, bem como permitir o tráfego de ambiente de trabalho remoto na porta TCP 3389 e a comunicação remota do PowerShell na porta TCP 5985. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.

## <a name="create-a-rule-to-autoscale-out"></a>Criar uma regra para dimensionamento automático para aumentar horizontalmente
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Controla as métricas a monitorizar, como a CPU ou o disco, quando a carga da aplicação tem de cumprir um determinado limiar e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

Vamos criar uma regra com [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que aumenta o número de instâncias de VM num conjunto de dimensionamento quando a carga média da CPU é superior a 70% durante um período de 5 minutos. Quando a regra for acionada, o número de instâncias de VM é aumentado em três.

Os parâmetros seguintes são utilizados para esta regra:

| Parâmetro               | Explicação                                                                                                         | Value          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | A métrica de desempenho para monitorizar e aplicar ações ao conjunto de dimensionamento.                                                   | Percentagem da CPU |
| *-TimeGrain*            | Frequência com que as métricas são recolhidas para análise.                                                                   | 1 minuto       |
| *-MetricStatistic*      | Define a forma como as métricas recolhidas devem ser agregadas para análise.                                                | Média        |
| *-TimeWindow*           | A quantidade de tempo monitorizado antes de os valores de métrica e limiar serem comparados.                                   | 5 minutos      |
| *-Operator*             | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                     | Maior Que   |
| *-Threshold*            | O valor que faz com que a regra de dimensionamento automático acione uma ação.                                                      | 70%            |
| *-ScaleActionDirection* | Define se o conjunto de dimensionamento deve aumentar ou reduzir verticalmente quando a regra se aplicar.                                             | Aumentar       |
| *–ScaleActionScaleType* | Indica que o número de instâncias de VM deve ser alterado por um valor específico.                                    | Contagem de Alterações   |
| *-ScaleActionValue*     | A percentagem de instâncias de VM deve ser alterada quando a regra for acionada.                                            | 3              |
| *-ScaleActionCooldown*  | A quantidade de tempo de espera antes de a regra ser aplicada novamente, para que as ações de dimensionamento automático tenham tempo de entrar em vigor. | 5 minutos      |

O exemplo seguinte cria um objeto com o nome *myRuleScaleOut* que contém esta regra para aumentar verticalmente. *- MetricResourceId* utiliza as variáveis definidas anteriormente para o ID de subscrição, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Criar uma regra para dimensionamento automático para reduzir horizontalmente
À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.

Crie outra regra com [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que diminui o número de instâncias de VM num conjunto de dimensionamento quando a carga média da CPU passa a ser inferior a 30% durante um período de 5 minutos. Quando a regra é acionada, o número de instâncias de VM é diminuído em um. O exemplo seguinte cria um objeto com o nome *myRuleScaleDown* que contém esta regra para aumentar verticalmente. *- MetricResourceId* utiliza as variáveis definidas anteriormente para o ID de subscrição, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Definir um perfil de dimensionamento automático
Para associar as regras de dimensionamento automático a um conjunto de dimensionamento, crie um perfil. O perfil de dimensionamento automático define a capacidade predefinida, máxima e mínima do conjunto de dimensionamento e associa as suas regras de dimensionamento automático. Crie um perfil de dimensionamento automático com [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). O exemplo seguinte define a capacidade predefinida e mínima de *2* instâncias de VM e um máximo de *10*. Em seguida, são anexadas as regras para aumentar e reduzir horizontalmente criadas nos passos anteriores:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicar regras de dimensionamento automático a um conjunto de dimensionamento
O último passo consiste em aplicar o perfil de dimensionamento automático ao conjunto de dimensionamento. O conjunto de dimensionamento passa a ter a capacidade de aumentar e reduzir horizontalmente de forma automática com base na exigência da aplicação. Aplique o perfil de dimensionamento automático com [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) da seguinte forma:

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Gerar a carga da CPU no conjunto de dimensionamento
Para testar as regras de dimensionamento automático, gere a carga de CPU nas instâncias de VM no conjunto de dimensionamento. Esta carga de CPU simulada faz com que as regras de dimensionamento automático aumentem horizontalmente e aumentem o número de instâncias de VM. Uma vez que a carga de CPU simulada é diminuída, as regras de dimensionamento automático reduzem horizontalmente e reduzem o número de instâncias de VM.

Para listar as portas NAT para ligar a instâncias da VM num conjunto de dimensionamento, obtenha primeiro o objeto do balanceador de carga com [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Em seguida, veja as regras NAT de entrada com [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

O seguinte resultado de exemplo mostra o nome de instância, o endereço IP público do balanceador de carga e o número de porta para o qual as regras NAT encaminham o tráfego:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

O *Nome* da regra está alinhado com o nome da instância de VM, conforme mostrado num comando [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) anterior. Por exemplo, para ligar à instância de VM *0*, utilize *myRDPRule.0* e ligue à porta *50001*. Para ligar à instância de VM *1*, utilize o valor de *myRDPRule.1* e ligue à porta *50002*.

Veja o endereço IP público do balanceador de carga com [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Exemplo de saída:

```powershell
IpAddress
---------
52.168.121.216
```

Crie uma ligação remota à primeira instância de VM. Especifique o seu endereço IP público e o número da porta da instância de VM necessária, conforme mostrado nos comandos anteriores. Quando lhe for pedido, introduza as credenciais utilizadas quando criou o conjunto de dimensionamento (por predefinição nos comandos de exemplo, eles são *azureuser* e *P\@ssw0rd!*). Se utiliza o Azure Cloud Shell, efetue este passo a partir de uma linha de comandos do PowerShell ou do Cliente de Ambiente de Trabalho Remoto. O exemplo seguinte liga à instância de VM *0*:

```powershell
mstsc /v 52.168.121.216:50001
```

Depois de iniciar sessão, abra o Internet Explorer a partir da barra de tarefas.

- Selecione **OK** para aceitar o pedido para *Utilizar as definições de segurança, privacidade e compatibilidade recomendadas*
- Escreva *http://download.sysinternals.com/files/CPUSTRES.zip* na barra de endereço.
- Como a Configuração de Segurança Avançada do Internet Explorer está ativada, selecione **Adicionar** o domínio *http://download.sysinternals.com* à lista de sites fidedignos.
- Quando lhe for pedido para transferir o ficheiro, selecione **Abrir** e, em seguida, selecione **Executar** a ferramenta *CPUSTRES.EXE*.

Para gerar carga de CPU, selecione as duas caixas para os threads **Ativo**. No menu pendente **Atividade** para ambos os threads, selecione *Máximo*. Pode abrir o Gestor de Tarefas para confirmar que a carga da CPU na VM está a 100%.

![O utilitário CPU Stress gera carga na instância de VM](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Deixe a sessão de ligação ao ambiente de trabalho remoto aberta e abra outra sessão. Ligue à segunda instância de VM com o número de porta listado no cmdlet [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) anterior:

```powershell
mstsc /v 52.168.121.216:50002
```

Assim que iniciar sessão na segunda instância de VM, repita os passos anteriores para transferir e executar *CPUSTRES.EXE*. Novamente, inicie os dois threads **Ativo** e defina a atividade para *Máximo*.

Para permitir que a ferramenta **CPU Stress** continue a ser executada, deixe ambas as sessões de ligação ao ambiente de trabalho remoto abertas.


## <a name="monitor-the-active-autoscale-rules"></a>Monitorizar as regras de dimensionamento automático ativas
Para monitorizar o número de instâncias de VM no conjunto de dimensionamento, utilize **while**. Demora 5 minutos até o dimensionamento automático iniciar o processo de aumentar horizontalmente para a carga de CPU gerada por **CPUStress* em cada uma das instâncias de VM:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Assim que tiver sido cumprido o limiar de CPU, as regras de dimensionamento automático aumentam o número de instâncias de VM no conjunto de dimensionamento. O resultado seguinte mostra três VMs criadas à medida que o conjunto de dimensionamento aumenta horizontalmente:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

Na sessão de ligação ao ambiente de trabalho remoto de cada uma das instâncias de VM, feche a ferramenta **CPU Stress**. A carga de CPU média no conjunto de dimensionamento volta ao normal. Após mais 5 minutos, as regras de dimensionamento automático reduzem horizontalmente o número de instâncias de VM. As ações para reduzir horizontalmente removem primeiro as instâncias de VM com os IDs mais elevados. Quando um conjunto de dimensionamento utiliza Conjuntos de Disponibilidade ou Zonas de Disponibilidade, as ações de redução horizontal são distribuídas uniformemente nessas instâncias de VMs. O resultado de exemplo seguinte mostra uma instância de VM eliminada à medida que o conjunto de dimensionamento reduz horizontalmente de forma automática:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Saia de *while* com `Ctrl-c`. O conjunto de dimensionamento continua a reduzir horizontalmente a cada 5 minutos e remove uma instância de VM até ser atingida uma contagem mínima de duas instâncias.


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o seu conjunto de dimensionamento e recursos adicionais, elimine o grupo de recursos e todos os respetivos recursos com [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). O parâmetro `-Force` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo. O parâmetro `-AsJob` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a aumentar e reduzir automaticamente um conjunto de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Utilizar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e utilizar regras de dimensionamento automático
> * Teste de esforço das instâncias e acionar as regras de dimensionamento automático
> * Voltar ao dimensionamento automático à medida que a exigência diminui

Para obter mais exemplos de conjuntos de dimensionamento de máquinas virtuais em ação, veja os seguintes scripts de exemplo do Azure PowerShell:

> [!div class="nextstepaction"]
> [Exemplos de scripts de conjuntos de dimensionamento do Azure PowerShell](powershell-samples.md)
