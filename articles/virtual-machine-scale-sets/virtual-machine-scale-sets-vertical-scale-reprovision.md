---
title: Verticalmente escala conjuntos de escala de máquina virtual Azure
description: Como escalar verticalmente uma Máquina Virtual em resposta a alertas de monitorização com a Azure Automation
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 37602f7b9a8669ce0e8db984f7f7617cffdd431c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87029285"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Autoescala vertical com conjuntos de escala de máquina virtual

Este artigo descreve como escalar verticalmente [conjuntos de balança de máquina virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/) Azure com ou sem reprovisionamento. 

A escala vertical, também conhecida como *escala para cima* e para *baixo,* significa aumentar ou diminuir os tamanhos da máquina virtual (VM) em resposta a uma carga de trabalho. Compare este comportamento com [a escala horizontal,](virtual-machine-scale-sets-autoscale-overview.md)também referida como *escala para fora* e escala *em*, onde o número de VMs é alterado dependendo da carga de trabalho.

Reprovisionar significa remover um VM existente e substituí-lo por um novo. Quando aumenta ou diminui o tamanho dos VMs num conjunto de escala de máquina virtual, em alguns casos pretende redimensionar os VM existentes e reter os seus dados, enquanto noutros casos precisa de implantar novos VMs do novo tamanho. Este documento abrange ambos os casos.

A escala vertical pode ser útil quando:

* Um serviço construído em máquinas virtuais é subutilado (por exemplo, aos fins de semana). A redução do tamanho de VM pode reduzir os custos mensais.
* Aumentar o tamanho dos VM para fazer face a uma maior procura sem criar VMs adicionais.

Pode configurar a escala vertical para ser acionada com base em alertas métricos do seu conjunto de escala de máquina virtual. Quando o alerta é ativado, dispara um webhook que aciona um livro de execução que pode escalar a sua escala configurada ou para baixo. A escala vertical pode ser configurada seguindo estes passos:

1. Crie uma conta Azure Automation com capacidade de funcionamento.
2. Importar livros de escala vertical de automação Azure para conjuntos de escala de máquinas virtuais na sua subscrição.
3. Adicione um webhook ao seu runbook.
4. Adicione um alerta ao seu conjunto de escala de máquina virtual usando uma notificação webhook.

> [!NOTE]
> Devido ao tamanho da primeira Máquina Virtual, os tamanhos a que pode ser dimensionado, podem ser limitados devido à disponibilidade dos outros tamanhos na corrente do cluster da Máquina Virtual. Nos livros de automação publicados utilizados neste artigo cuidamos deste caso e apenas escala dentro dos pares de tamanho vM abaixo. Isto significa que uma máquina virtual Standard_D1v2 não será subitamente dimensionada até Standard_G5 ou reduzida a Basic_A0. Também os tamanhos da Máquina Virtual também limitados escalam para cima/para baixo não é suportado. Pode optar por escalar entre os seguintes pares de tamanhos:
> 
> | Membro de par de pares de tamanhos VM | Membro |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Criar uma Conta de Automação Azure com capacidade de funcionamento
A primeira coisa que precisa de fazer é criar uma conta Azure Automation que hospeda os runbooks usados para escalar as instâncias de escala de máquina virtual. Recentemente, a [Azure Automation](https://azure.microsoft.com/services/automation/) introduziu a funcionalidade "Executar como conta" que faz com que a criação do Diretor de Serviço para executar automaticamente os livros de execução em nome de um utilizador. Para obter mais informações, consulte:

* [Autenticar Runbooks com a conta Run As do Azure](../automation/manage-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar livros de escala vertical da Azure Automation na sua subscrição

Os livros de execução necessários para escalar verticalmente os seus conjuntos de escala de máquinas virtuais já estão publicados na Galeria runbook da Azure Automation. Para importá-los na sua subscrição siga os passos deste artigo:

* [Runbook and module galleries for Azure Automation](../automation/automation-runbook-gallery.md) (Galerias de runbooks e módulos para a Automatização do Azure)

Escolha a opção Browse Gallery no menu Runbooks:

![Livros de execução a importar][runbooks]

Os livros que precisam de ser importados são mostrados. Selecione o livro de execução com base na possibilidade de uma escala vertical com ou sem reprovisionamento:

![Galeria de runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Adicione um webhook ao seu runbook

Depois de importar os livros de execução, adicione um webhook ao runbook para que possa ser acionado por um alerta de um conjunto de escala de máquina virtual. Os detalhes da criação de um webhook para o seu Runbook são descritos neste artigo:

* [Webhooks da Azure Automation](../automation/automation-webhooks.md)

> [!NOTE]
> Certifique-se de que copia o webhook URI antes de fechar o diálogo webhook, pois necessitará deste endereço na secção seguinte.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Adicione um alerta ao seu conjunto de escala de máquina virtual

Abaixo está um script PowerShell que mostra como adicionar um alerta a um conjunto de escala de máquina virtual. Consulte o seguinte artigo para obter o nome da métrica para disparar o alerta em: [Azure Monitor autoscaling métricas comuns](../azure-monitor/platform/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
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
> Recomenda-se configurar um intervalo de tempo razoável para o alerta, a fim de evitar o desencadear da escala vertical e qualquer interrupção de serviço associada, demasiadas vezes. Considere uma janela de pelo menos 20-30 minutos ou mais. Considere a escala horizontal se precisar de evitar qualquer interrupção.
> 
> 

Para obter mais informações sobre como criar alertas, consulte os seguintes artigos:

* [Amostras de PowerShell monitor Azure Monitor](../azure-monitor/samples/powershell-samples.md)
* [Amostras de CLI de plataforma cruzada Azure Monitor](../azure-monitor/samples/cli-samples.md)

## <a name="summary"></a>Resumo

Este artigo mostrou exemplos de escala vertical simples. Com estes blocos de construção - conta de automação, runbooks, webhooks, alertas - você pode conectar uma grande variedade de eventos com um conjunto personalizado de ações.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
