---
title: Utilize um alerta para acionar um runbook da automatização do Azure
description: Saiba como acionar um runbook para ser executada quando é desencadeado um alerta do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8a1ae906a72d781f638fb171a409b860ffa6d501
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65517709"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Utilize um alerta para acionar um runbook da automatização do Azure

Pode usar [do Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) para monitorizar as métricas de nível de base e registos para a maioria dos serviços do Azure. Pode chamar runbooks de automatização do Azure, utilizando [grupos de ação](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) ou através de alertas clássicos para automatizar tarefas com base em alertas. Este artigo mostra-lhe como configurar e executar um runbook através de alertas.

## <a name="alert-types"></a>Tipos de alertas

Pode utilizar runbooks de automatização com três tipos de alertas:

* Alertas comuns
* Alertas do registo de atividades
* Perto de alertas de métricas em tempo real

> [!NOTE]
> O esquema comum do alerta uniformiza a experiência de consumo de notificações de alertas no Azure hoje mesmo. Historicamente, os três tipos de alertas no Azure hoje (métrica, registo e o registo de atividades) tenham tido a seus próprios modelos de e-mail, webhook esquemas, etc. Para obter mais informações, consulte [esquema comum do alerta](../azure-monitor/platform/alerts-common-schema.md)

Quando um alerta chama um runbook, a chamada real é um pedido de HTTP POST para o webhook. O corpo da solicitação POST contém um objeto JSON formatada que tem propriedades úteis relacionadas com o alerta. A tabela seguinte apresenta uma lista de ligações para o esquema do payload para cada tipo de alerta:

|Alerta  |Descrição|Esquema do payload  |
|---------|---------|---------|
|[Alerta comuns](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Esquema comum do alerta que padroniza a experiência de consumo de notificações de alertas no Azure hoje mesmo.|[Esquema comum do payload de alerta](../azure-monitor/platform/alerts-common-schema-definitions.md?toc=%2fazure%2fautomation%2ftoc.json#sample-alert-payload)|
|[Alerta de registo de atividade](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação quando qualquer novo evento no registo de atividades do Azure corresponde a condições específicas. Por exemplo, quando um `Delete VM` operação ocorre no **myProductionResourceGroup** ou quando um novo evento de estado de funcionamento de serviço de Azure com uma **Active** estado é apresentado.| [Esquema do payload de alerta de registo de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Alerta de métrica em tempo real em breve](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação mais rapidamente do que os alertas de métricas, quando uma ou mais métricas de nível de plataforma cumprem condições específicas. Por exemplo, quando o valor para **% de CPU** numa VM é superior **90**e o valor de **na rede** for maior do que **500 MB** nas últimas 5 minutos.| [Junto ao esquema do payload de alerta de métricas em tempo real](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Uma vez que os dados que são fornecidos por cada tipo de alerta são diferentes, cada tipo de alerta é processado de forma diferente. Na secção seguinte, irá aprender a criar um runbook para lidar com diferentes tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Criar um runbook para lidar com alertas

Para utilizar a automatização com alertas, terá de um runbook que tem lógica que gere o payload JSON de alerta que é passado para o runbook. O runbook de exemplo seguinte tem de ser chamado a partir de um alerta do Azure.

Conforme descrito na secção anterior, cada tipo de alerta tem um esquema diferente. O script aceita os dados de webhook no `WebhookData` parâmetro de entrada do runbook de um alerta. Em seguida, o script avalia o payload JSON para determinar que tipo de alerta foi utilizado.

Este exemplo utiliza um alerta a partir de uma VM. Obtém os dados da VM do payload e, em seguida, utiliza essas informações para parar a VM. A ligação tem de ser definida na conta de automatização em que o runbook é executado. Quando utilizar alertas para acionar runbooks, é importante verificar o estado do alerta no runbook que é acionado. O runbook irá acionar sempre que o alerta de estado é alterado. Os alertas têm vários Estados, os dois Estados mais comuns são `Activated` e `Resolved`. Verificar esse Estado na sua lógica de runbook para se certificar de que o runbook não é executado mais de uma vez. O exemplo neste artigo mostra como procurar `Activated` apenas de alertas.

O runbook utiliza a **AzureRunAsConnection** [conta Run As](automation-create-runas-account.md) para autenticar com o Azure para executar a ação de gestão na VM.

Utilize este exemplo para criar um runbook denominado **Stop-AzureVmInResponsetoVMAlert**. Pode modificar o script do PowerShell e utilizá-lo com muitos recursos diferentes.

1. Aceda à sua conta de automatização do Azure.
2. Sob **automatização de processos**, selecione **Runbooks**.
3. Na parte superior da lista de runbooks, selecione **+ criar um runbook**.
4. Sobre o **adicionar Runbook** página, introduza **Stop-AzureVmInResponsetoVMAlert** para o nome do runbook. Para o tipo de runbook, selecione **PowerShell**. Em seguida, selecione **Criar**.  
5. Copie o seguinte exemplo do PowerShell para o **editar** página.

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Selecione **publicar** para guardar e publicar o runbook.

## <a name="create-the-alert"></a>Criar o alerta

Alertas utilizam grupos de ação, o que são coleções de ações que são acionadas pelo alerta. Os Runbooks são apenas uma das muitas ações que pode utilizar com grupos de ação.

1. Na sua conta de automatização, selecione **alertas** sob **monitorização**.
1. Selecione **+ nova regra de alerta**.
1. Clique em **selecionar** sob **recurso**. Sobre o **selecionar um recurso** página, selecione a sua VM a alerta do e clique em **feito**.
1. Clique em **adicionar condição** sob **condição**. Selecione o sinal de que pretende utilizar, por exemplo **percentagem de CPU** e clique em **feito**.
1. Na **configurar lógica de sinal** página, introduza sua **valor de limiar** sob **lógica de alerta**e clique em **feito**.
1. Sob **grupos de ação**, selecione **criar nova**.
1. Sobre o **grupo de ação de adicionar** página, atribua ao grupo de ação de um nome e um nome abreviado.
1. Dê um nome de ação. Para o tipo de ação, selecione **Runbook da automatização**.
1. Selecione **editar detalhes**. Sobre o **configurar Runbook** página, em **origem do Runbook**, selecione **utilizador**.  
1. Selecione seu **subscrição** e **conta de automatização**e, em seguida, selecione o **Stop-AzureVmInResponsetoVMAlert** runbook.  
1. Selecione **Sim** para **ativar esquema comum do alerta**.
1. Para criar o grupo de ação, selecione **OK**.

    ![Adicionar página de grupo de ação](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Pode utilizar este grupo de ação no [alertas de registo de atividade](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) e [perto alertas em tempo real](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) que criar.

1. Sob **detalhes de alerta**, adicione um nome de regra de alerta e uma descrição e clique em **criar regra de alerta**.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre como iniciar um runbook da automatização ao utilizar um webhook, veja [iniciar um runbook a partir de um webhook](automation-webhooks.md).
* Para obter detalhes sobre diferentes formas de iniciar um runbook, consulte [a partir de um runbook](automation-starting-a-runbook.md).
* Para saber como criar um alerta de registo de atividade, veja [criar alertas de registo de atividades](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Para saber como criar um alerta de quase em tempo real, veja [criar uma regra de alerta no portal do Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
