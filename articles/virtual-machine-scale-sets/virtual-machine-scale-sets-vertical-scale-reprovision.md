---
title: Dimensionar verticalmente os conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Como dimensionar verticalmente uma máquina virtual em resposta a alertas de monitoramento com a automação do Azure
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
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: d12fde33ec9d55c891c801f1b89143b4db6f8ae7
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035761"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Dimensionamento automático vertical com conjuntos de dimensionamento de máquinas virtuais

Este artigo descreve como dimensionar verticalmente os conjuntos de dimensionamento de [máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/) do Azure com ou sem reprovisionamento. Para o dimensionamento vertical de VMs que não estão em conjuntos de dimensionamento, consulte [dimensionar verticalmente a máquina virtual do Azure com a automação do Azure](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

O dimensionamento vertical, também conhecido como *scale up* e *Scale down*, significa aumentar ou diminuir os tamanhos de VM (máquina virtual) em resposta a uma carga de trabalho. Compare esse comportamento com o [dimensionamento horizontal](virtual-machine-scale-sets-autoscale-overview.md), também conhecido como *scale out* e *Scale horizontal*, em que o número de VMs é alterado dependendo da carga de trabalho.

O reprovisionamento significa remover uma VM existente e substituí-la por uma nova. Quando você aumenta ou diminui o tamanho das VMs em um conjunto de dimensionamento de máquinas virtuais, em alguns casos você deseja redimensionar as VMs existentes e manter seus dados, enquanto em outros casos você precisa implantar novas VMs do novo tamanho. Este documento aborda os dois casos.

O dimensionamento vertical pode ser útil quando:

* Um serviço criado em máquinas virtuais é subutilizado (por exemplo, nos fins de semana). Reduzir o tamanho da VM pode reduzir os custos mensais.
* Aumentar o tamanho da VM para lidar com maior demanda sem criar VMs adicionais.

Você pode configurar o dimensionamento vertical para ser disparado com base em alertas com base em métrica do seu conjunto de dimensionamento de máquinas virtuais. Quando o alerta é ativado, ele dispara um webhook que dispara um runbook que pode dimensionar seu conjunto de dimensionamento para cima ou para baixo. O dimensionamento vertical pode ser configurado seguindo estas etapas:

1. Crie uma conta de automação do Azure com a funcionalidade executar como.
2. Importe runbooks de escala vertical da automação do Azure para conjuntos de dimensionamento de máquinas virtuais em sua assinatura.
3. Adicione um webhook ao seu runbook.
4. Adicione um alerta ao seu conjunto de dimensionamento de máquinas virtuais usando uma notificação de webhook.

> [!NOTE]
> Devido ao tamanho da primeira máquina virtual, os tamanhos que podem ser dimensionados podem ser limitados devido à disponibilidade dos outros tamanhos no cluster em que a máquina virtual atual está implantada. Nos runbooks de automação publicados usados neste artigo, cuidamos desse caso e só dimensionamos nos pares de tamanho de VM abaixo. Isso significa que uma máquina virtual Standard_D1v2 não será expandida repentinamente para Standard_G5 ou reduzida para Basic_A0. Também não há suporte para expansão/redução de tamanhos de máquina virtual restritos. Você pode optar por dimensionar entre os seguintes pares de tamanhos:
> 
> | Par de dimensionamento de tamanhos de VM |  |
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

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Criar uma conta de automação do Azure com capacidade de execução
A primeira coisa que você precisa fazer é criar uma conta de automação do Azure que hospede os runbooks usados para dimensionar as instâncias do conjunto de dimensionamento de máquinas virtuais. Recentemente, a [automação do Azure](https://azure.microsoft.com/services/automation/) introduziu o recurso "conta Executar como" que torna a configuração da entidade de serviço para executar automaticamente os runbooks em nome de um usuário. Para obter mais informações, consulte:

* [Autenticar Runbooks com a conta Run As do Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar runbooks de escala vertical da automação do Azure para sua assinatura

Os runbooks necessários para dimensionar verticalmente seus conjuntos de dimensionamento de máquinas virtuais já estão publicados na Galeria de runbook da automação do Azure. Para importá-los para sua assinatura, siga as etapas neste artigo:

* [Runbook and module galleries for Azure Automation](../automation/automation-runbook-gallery.md) (Galerias de runbooks e módulos para a Automatização do Azure)

Escolha a opção procurar na galeria no menu Runbooks:

![Runbooks a serem importados][runbooks]

Os runbooks que precisam ser importados são mostrados. Selecione o runbook com base em se você deseja o dimensionamento vertical com ou sem o reprovisionamento:

![Galeria de Runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao seu runbook

Depois de importar os runbooks, adicione um webhook ao runbook para que ele possa ser disparado por um alerta de um conjunto de dimensionamento de máquinas virtuais. Os detalhes da criação de um webhook para o runbook são descritos neste artigo:

* [WebHooks de automação do Azure](../automation/automation-webhooks.md)

> [!NOTE]
> Certifique-se de copiar o URI do webhook antes de fechar a caixa de diálogo do webhook, pois será necessário esse endereço na próxima seção.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Adicionar um alerta ao conjunto de dimensionamento de máquinas virtuais

Abaixo está um script do PowerShell que mostra como adicionar um alerta a um conjunto de dimensionamento de máquinas virtuais. Consulte o seguinte artigo para obter o nome da métrica na qual o alerta será acionado: [Azure monitor métricas comuns](../azure-monitor/platform/autoscale-common-metrics.md)de dimensionamento automático.

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
> É recomendável configurar uma janela de tempo razoável para o alerta a fim de evitar o disparo de dimensionamento vertical e qualquer interrupção de serviço associada, com muita frequência. Considere uma janela de pelo menos 20-30 minutos ou mais. Considere o dimensionamento horizontal se você precisar evitar qualquer interrupção.
> 
> 

Para obter mais informações sobre como criar alertas, consulte os seguintes artigos:

* [Exemplos de início rápido do Azure Monitor PowerShell](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Exemplos de início rápido da CLI de plataforma cruzada Azure Monitor](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Resumo

Este artigo mostrou exemplos de dimensionamento vertical simples. Com esses blocos de construção – conta de automação, runbooks, WebHooks, alertas – você pode conectar uma grande variedade de eventos com um conjunto personalizado de ações.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
