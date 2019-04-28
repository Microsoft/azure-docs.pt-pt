---
title: Utilize um alerta para acionar um runbook da automatização do Azure
description: Saiba como acionar um runbook para ser executada quando é desencadeado um alerta do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 88fe7740170638e9e0d7398a02dcf83ab81f6ffc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073861"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Utilize um alerta para acionar um runbook da automatização do Azure

Pode usar [do Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) para monitorizar as métricas de nível de base e registos para a maioria dos serviços do Azure. Pode chamar runbooks de automatização do Azure, utilizando [grupos de ação](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) ou através de alertas clássicos para automatizar tarefas com base em alertas. Este artigo mostra-lhe como configurar e executar um runbook através de alertas.

## <a name="alert-types"></a>Tipos de alerta

Pode utilizar runbooks de automatização com três tipos de alertas:
* Alertas de métricas clássicas
* Alertas do registo de atividades
* Perto de alertas de métricas em tempo real

Quando um alerta chama um runbook, a chamada real é um pedido de HTTP POST para o webhook. O corpo da solicitação POST contém um objeto JSON formatada que tem propriedades úteis relacionadas com o alerta. A tabela seguinte apresenta uma lista de ligações para o esquema do payload para cada tipo de alerta:

|Alerta  |Descrição|Esquema do payload  |
|---------|---------|---------|
|[Alerta de métrica clássica](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação quando qualquer métrica de nível de plataforma cumpre uma condição específica. Por exemplo, quando o valor para **% de CPU** numa VM é superior **90** durante os últimos 5 minutos.| [Esquema do payload de alerta de métrica de classe](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Alerta de registo de atividade](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação quando qualquer novo evento no registo de atividades do Azure corresponde a condições específicas. Por exemplo, quando um `Delete VM` operação ocorre no **myProductionResourceGroup** ou quando um novo evento de estado de funcionamento de serviço de Azure com uma **Active** estado é apresentado.| [Esquema do payload de alerta de registo de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Alerta de métrica em tempo real em breve](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação mais rapidamente do que os alertas de métricas, quando uma ou mais métricas de nível de plataforma cumprem condições específicas. Por exemplo, quando o valor para **% de CPU** numa VM é superior **90**e o valor de **na rede** for maior do que **500 MB** nas últimas 5 minutos.| [Junto ao esquema do payload de alerta de métricas em tempo real](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Uma vez que os dados que são fornecidos por cada tipo de alerta são diferentes, cada tipo de alerta é processado de forma diferente. Na secção seguinte, irá aprender a criar um runbook para lidar com diferentes tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Criar um runbook para lidar com alertas

Para utilizar a automatização com alertas, terá de um runbook que tem lógica que gere o payload JSON de alerta que é passado para o runbook. O runbook de exemplo seguinte tem de ser chamado a partir de um alerta do Azure.

Conforme descrito na secção anterior, cada tipo de alerta tem um esquema diferente. O script aceita os dados de webhook no `WebhookData` parâmetro de entrada do runbook de um alerta. Em seguida, o script avalia o payload JSON para determinar que tipo de alerta foi utilizado.

Este exemplo utiliza um alerta a partir de uma VM. Obtém os dados da VM do payload e, em seguida, utiliza essas informações para parar a VM. A ligação tem de ser definida na conta de automatização em que o runbook é executado. Quando utilizar alertas para acionar runbooks, é importante verificar o estado do alerta no runbook que é acionado. O runbook irá acionar sempre que o alerta de estado é alterado. Os alertas têm vários Estados, os dois Estados mais comuns são `Activated` e `Resolved`. Verificar esse Estado na sua lógica de runbook para se certificar de que o runbook não é executado mais de uma vez. O exemplo neste artigo mostra como procurar `Activated` apenas de alertas.

O runbook utiliza a **AzureRunAsConnection** [conta Run As](automation-create-runas-account.md) para autenticar com o Azure para executar a ação de gestão na VM.

Utilize este exemplo para criar um runbook denominado **Stop-AzureVmInResponsetoVMAlert**. Pode modificar o script do PowerShell e utilizá-lo com muitos recursos diferentes.

1. Aceda à sua conta de automatização do Azure.
1. Sob **AUTOMATIZAÇÃO de processos**, selecione **Runbooks**.
1. Na parte superior da lista de runbooks, selecione **adicionar um runbook**. 
1. Na página **Adicionar Runbook**, selecione **Criação Rápida**.
1. Para o nome do runbook, introduza **Stop-AzureVmInResponsetoVMAlert**. Para o tipo de runbook, selecione **PowerShell**. Em seguida, selecione **Criar**.  
1. Copie o seguinte exemplo do PowerShell para o **editar** painel. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Selecione **publicar** para guardar e publicar o runbook.

## <a name="create-an-action-group"></a>Criar um grupo de ação

Um grupo de ação é uma coleção de ações que são acionados por um alerta. Os Runbooks são apenas uma das muitas ações que pode utilizar com grupos de ação.

1. No portal do Azure, selecione **Monitor** > **definições** > **grupos de ação**.
1. Selecione **grupo de ação de adicionar**e, em seguida, introduza as informações necessárias:  
    1. Na **nome do grupo de ação** , introduza um nome.
    1. Na **nome abreviado** , introduza um nome. O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas ao utilizar este grupo de ação.
    1. O **subscrição** caixa é preenchida automaticamente com a sua subscrição atual. Esta é a subscrição na qual o grupo de ação é guardado.
    1. Selecione o grupo de recursos na qual o grupo de ação é guardado.

Neste exemplo, criar duas ações: uma ação de runbook e uma ação de notificação.

### <a name="runbook-action"></a>Ação de Runbook

Para criar uma ação de runbook no grupo de ação:

1. Sob **ações**, para **nome da ação**, introduza um nome para a ação. Para **tipo de ação**, selecione **Runbook da automatização**.
1. Sob **detalhes**, selecione **editar detalhes**.  
1. Sobre o **configurar Runbook** página, em **origem do Runbook**, selecione **utilizador**.  
1. Selecione seu **subscrição** e **conta de automatização**e, em seguida, selecione o **Stop-AzureVmInResponsetoVMAlert** runbook.  
1. Quando tiver terminado, selecione **OK**.

### <a name="notification-action"></a>Ação de notificação

Para criar uma ação de notificação no grupo de ação:

1. Sob **ações**, para **nome da ação**, introduza um nome para a ação. Para **tipo de ação**, selecione **E-Mail**.  
1. Sob **detalhes** selecionar, **editar detalhes**.  
1. Sobre o **E-Mail** página, introduza o endereço de e-mail a utilizar para notificação e, em seguida, selecione **OK**. A adição de um endereço de e-mail, além do runbook como uma ação é útil. Será notificado quando o runbook é iniciado.  

    O grupo de ação deve ter um aspeto semelhante à imagem seguinte:

   ![Adicionar página de grupo de ação](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Para criar o grupo de ação, selecione **OK**.

Pode utilizar este grupo de ação no [alertas de registo de atividade](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) e [perto alertas em tempo real](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) que criar.

## <a name="classic-alert"></a>Alerta clássica

Alertas clássicos são baseadas em métricas e não utilize grupos de ação. No entanto, pode configurar uma ação de runbook com base num alerta clássico. 

Para criar um alerta clássico:

1. Selecione **Adicionar alerta de métrica**.
1. Nome de alerta de métrica **myVMCPUAlert**. Introduza uma breve descrição do alerta.
1. Para a condição de alerta de métrica, selecione **superior a**. Para o **limiar** valor, selecione **10**. Para o **período** valor, selecione **durante os últimos cinco minutos**.
1. Sob **agir**, selecione **executar um runbook a partir deste alerta**.
1. Sobre o **configurar Runbook** página, para **origem do Runbook**, selecione **utilizador**. Escolha a sua conta de automatização e, em seguida, selecione o **Stop-AzureVmInResponsetoVMAlert** runbook. Selecione **OK**.
1. Para guardar a regra de alerta, selecione **OK**.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre como iniciar um runbook da automatização ao utilizar um webhook, veja [iniciar um runbook a partir de um webhook](automation-webhooks.md).
* Para obter detalhes sobre diferentes formas de iniciar um runbook, consulte [a partir de um runbook](automation-starting-a-runbook.md).
* Para saber como criar um alerta de registo de atividade, veja [criar alertas de registo de atividades](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Para saber como criar um alerta de quase em tempo real, veja [criar uma regra de alerta no portal do Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).

