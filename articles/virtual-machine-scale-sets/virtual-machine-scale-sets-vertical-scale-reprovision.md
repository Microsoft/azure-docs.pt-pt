---
title: Conjuntos de escala vertical de escala azure conjuntos de escala virtual
description: Como escalar verticalmente uma Máquina Virtual em resposta a alertas de monitorização com a Automação Azure
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 69c613de02b9601966cae2d36c13428ca6c7becc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121002"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Escala automática vertical com conjuntos de escala de máquina virtual

Este artigo descreve como escalar verticalmente conjuntos de [escala de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/) Azure com ou sem reprovisionamento. 

Escala vertical, também conhecida como *escala para cima* e escala para *baixo,* significa aumentar ou diminuir tamanhos de máquina virtual (VM) em resposta a uma carga de trabalho. Compare este comportamento com a [escala horizontal](virtual-machine-scale-sets-autoscale-overview.md), também referida como escala para *fora* e *escala dentro*, onde o número de VMs é alterado dependendo da carga de trabalho.

Reprovisionamento significa remover um VM existente e substituí-lo por um novo. Quando aumenta ou diminui o tamanho dos VMs num conjunto de escala de máquina virtual, em alguns casos pretende redimensionar os VMexistentes e reter os seus dados, enquanto noutros casos precisa de implementar novos VMs do novo tamanho. Este documento abrange ambos os casos.

A escala vertical pode ser útil quando:

* Um serviço construído sobre máquinas virtuais é subutilizado (por exemplo, aos fins de semana). A redução do tamanho do VM pode reduzir os custos mensais.
* Aumentar o tamanho do VM para fazer face a uma maior procura sem criar VMs adicionais.

Pode configurar a escala vertical para ser acionada com base em alertas métricos do conjunto de escala de máquina virtual. Quando o alerta é ativado, dispara um gancho que aciona um livro de rumantes que pode escalar a sua escala configurada para cima ou para baixo. A escala vertical pode ser configurada seguindo estes passos:

1. Crie uma conta Azure Automation com capacidade de execução.
2. Importar livros de escala vertical de automação Azure para conjuntos de escala de máquina virtual na sua subscrição.
3. Adicione um webhook ao seu livro de execução.
4. Adicione um alerta ao seu conjunto de escala de máquina virtual utilizando uma notificação de webhook.

> [!NOTE]
> Devido ao tamanho da primeira Máquina Virtual, os tamanhos a que pode ser dimensionado, podem ser limitados devido à disponibilidade dos outros tamanhos na corrente de cluster Virtual Machine. Nos livros de automação publicados usados neste artigo cuidamos deste caso e apenas escala dentro dos pares de tamanho SEm VM abaixo. Isto significa que uma máquina virtual Standard_D1v2 não será subitamente dimensionada até Standard_G5 ou reduzida a Basic_A0. Também não é suportado o tamanho da máquina virtual restrita para cima/para baixo. Pode optar por escalar entre os seguintes pares de tamanhos:
> 
> | Par de escala de tamanhos VM |  |
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

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Criar uma Conta de Automação Azure com capacidade de execução
A primeira coisa que precisa fazer é criar uma conta Azure Automation que acolhe os livros usados para escalar as instâncias de conjunto de máquinas virtuais. Recentemente, a [Azure Automation](https://azure.microsoft.com/services/automation/) introduziu a funcionalidade "Run As account" que faz com que a criação do Diretor de Serviço seja executada automaticamente os livros de execução em nome de um utilizador. Para obter mais informações, consulte:

* [Autenticar Runbooks com a conta Run As do Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar livros de escala vertical de automação azure na sua subscrição

Os livros de execução necessários para escalar verticalmente os seus conjuntos de escala de máquinas virtuais já estão publicados na Galeria de Runbook de Automação Azure. Para os importar na sua subscrição siga os passos deste artigo:

* [Runbook and module galleries for Azure Automation](../automation/automation-runbook-gallery.md) (Galerias de runbooks e módulos para a Automatização do Azure)

Escolha a opção Browse Gallery no menu Runbooks:

![Livros de execução a importar][runbooks]

Os livros que precisam de ser importados são mostrados. Selecione o livro de execução com base no facto de pretender escalonamento vertical com ou sem reprovisionamento:

![Galeria runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Adicione um webhook ao seu livro de corridas

Depois de importar os livros de execução, adicione um webhook ao livro de execução para que possa ser acionado por um alerta de um conjunto de escala de máquina virtual. Os detalhes da criação de um webhook para o seu Runbook são descritos neste artigo:

* [Webhooks Azure Automation](../automation/automation-webhooks.md)

> [!NOTE]
> Certifique-se de copiar o webhook URI antes de fechar o diálogo do webhook, uma vez que necessitará deste endereço na secção seguinte.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Adicione um alerta ao seu conjunto de escala de máquina virtual

Abaixo está um script PowerShell que mostra como adicionar um alerta a um conjunto de escala de máquina virtual. Consulte o seguinte artigo para obter o nome da métrica para disparar o alerta em: [Azure Monitor autoscalcificamétricas comuns](../azure-monitor/platform/autoscale-common-metrics.md).

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
> Recomenda-se configurar uma janela de tempo razoável para o alerta, a fim de evitar o disparo de escala vertical, e qualquer interrupção de serviço associada, demasiadas vezes. Considere uma janela de pelo menos 20-30 minutos ou mais. Considere a escala horizontal se precisar evitar qualquer interrupção.
> 
> 

Para obter mais informações sobre como criar alertas, consulte os seguintes artigos:

* [Amostras de arranque rápido do Monitor Azure PowerShell](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Amostras de arranque rápido CLI de plataforma cruzada Azure Monitor](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Resumo

Este artigo mostrou exemplos simples de escala vertical. Com estes blocos de construção - conta de automação, livros de execução, webhooks, alertas - você pode conectar uma rica variedade de eventos com um conjunto personalizado de ações.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
