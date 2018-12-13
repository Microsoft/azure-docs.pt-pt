---
title: Aumentar verticalmente os conjuntos de dimensionamento de máquina virtual do Azure | Documentos da Microsoft
description: Como aumentar verticalmente uma Máquina Virtual em resposta a alertas com a automatização do Azure de monitorização
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: manayar
ms.openlocfilehash: b961dadbe8743f1485ef0d13a44a1bb0f3ce32b5
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321380"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento automático vertical com dimensionamento de máquinas virtuais
Este artigo descreve como aumentar verticalmente do Azure [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/) com ou sem reprovisionamento. Para o dimensionamento de VMs que não estão em conjuntos de dimensionamento na vertical, consulte [aumentar verticalmente a máquina virtual do Azure com a automatização do Azure](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dimensionamento vertical, também conhecido como *aumentar verticalmente* e *reduzir verticalmente*, significa aumentar ou diminuir o tamanhos de máquina virtual (VM) em resposta a uma carga de trabalho. Compare esse comportamento com [dimensionamento horizontal](virtual-machine-scale-sets-autoscale-overview.md), também referido como *aumentar horizontalmente* e *reduzir horizontalmente*, onde o número de VMs é alterado dependendo da carga de trabalho.

Reprovisionamento significa remover uma VM existente e substituí-la com uma nova. Quando aumentar ou diminuir o tamanho das VMs num dimensionamento de máquinas virtuais definido, em alguns casos, que deseja redimensionar VMs existentes e manter os seus dados, enquanto em outros casos que necessita para implementar novas VMs do tamanho do novo. Este documento aborda a ambos os casos.

Dimensionamento vertical pode ser útil quando:

* Um serviço criado nas máquinas virtuais é subutilizados (por exemplo, a fim de semana). Reduzir o tamanho VM, pode reduzir os custos mensais.
* Aumentar o tamanho VM para lidar com o maior demanda sem a criação de VMs adicionais.

Pode configurar dimensionamento vertical ser acionadas com base na métrica baseada em alertas do seu conjunto de dimensionamento de máquina virtual. Quando o alerta é ativado, ele é disparado um webhook que aciona um runbook que pode dimensionar o seu dimensionamento definido ou reduzir verticalmente. Dimensionamento vertical pode ser configurado através dos seguintes passos:

1. Crie uma conta de automatização do Azure com capacidade de executar como.
2. Importe runbooks do dimensionamento Vertical de automatização do Azure para conjuntos de dimensionamento de máquinas virtuais na sua subscrição.
3. Adicione um webhook para o runbook.
4. Adicione um alerta para o seu conjunto de dimensionamento com uma notificação de webhook.

> [!NOTE]
> Dimensionamento automático vertical só pode ocorrer dentro de determinados intervalos de tamanhos de VM. Comparar as especificações de cada tamanho antes de decidir dimensione desde uma a outra (número mais alto não sempre indica maior tamanho VM). Pode optar por dimensionar entre os seguintes pares de tamanhos:
> 
> | Dimensionar o par de tamanhos de VM |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Criar uma conta de automatização do Azure com capacidade de executar como
A primeira coisa que precisa fazer é criar uma conta de automatização do Azure que aloja os runbooks utilizados para dimensionar as instâncias de conjunto de dimensionamento de máquina virtual. Recentemente [automatização do Azure](https://azure.microsoft.com/services/automation/) introduzida a funcionalidade de "Conta Run As" que torna a definição de cópia de segurança Principal de serviço para executar automaticamente os runbooks em nome de um utilizador. Para obter mais informações, consulte:

* [Autenticar Runbooks com a conta Run As do Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar runbooks do dimensionamento Vertical de automatização do Azure na sua subscrição
Os runbooks necessários para aumentar verticalmente os seus conjuntos de dimensionamento de máquina virtual já são publicados na Galeria de Runbook da automatização do Azure. Para importá-los na sua subscrição siga os passos neste artigo:

* [Galerias de módulos e Runbooks de automatização do Azure](../automation/automation-runbook-gallery.md)

Escolha a opção de Galeria de procura no menu de Runbooks:

![Runbooks para ser importado][runbooks]

Os runbooks que precisavam ser importados são apresentados. Selecione o runbook com base em se desejar vertical de dimensionamento com ou sem reprovisionamento:

![Galeria de Runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao runbook
Depois de ter importado os runbooks, adicione um webhook para o runbook para que ele pode ser acionado por um alerta a partir de um conjunto de dimensionamento de máquina virtual. Os detalhes da criação de um webhook para o seu Runbook são descritos neste artigo:

* [Webhooks de automatização do Azure](../automation/automation-webhooks.md)

> [!NOTE]
> Certifique-se de que copiar o URI de webhook antes de fechar a caixa de diálogo do webhook, pois irá precisar deste endereço na próxima seção.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Adicionar um alerta ao conjunto de dimensionamento de máquina virtual
Abaixo está um script do PowerShell que mostra como adicionar um alerta para um dimensionamento de máquinas virtuais definido. Consulte o artigo seguinte para obter o nome da métrica para acionar o alerta em: [Métricas comuns do Azure Monitor dimensionamento automático](../azure-monitor/platform/autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Recomenda-se para configurar uma janela de tempo razoável para o alerta para evitar acionar o dimensionamento vertical e qualquer interrupção de serviço associado, com muita frequência. Considere uma janela de, pelo menos, 20 a 30 minutos ou mais. Considere se precisar de evitar interrupções de dimensionamento horizontal.
> 
> 

Para obter mais informações sobre como criar alertas, consulte os artigos seguintes:

* [Exemplos de início rápido do Azure PowerShell de Monitor](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Exemplos de início rápido do Azure CLI de várias plataformas do Monitor](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Resumo
Este artigo mostrou exemplos simples de dimensionamento verticais. Com esses blocos de construção - conta de automatização, runbooks, webhooks, alertas - pode ligar várias eventos com um conjunto personalizado de ações.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
