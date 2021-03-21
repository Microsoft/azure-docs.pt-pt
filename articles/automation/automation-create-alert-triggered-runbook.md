---
title: Use um alerta para ativar um runbook da Azure Automation
description: Este artigo diz como desencadear um livro de bordo para executar quando um alerta de Azure é levantado.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.openlocfilehash: 0483b171ee65ac55d65261140738bc5c1838873f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732297"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Use um alerta para ativar um runbook da Azure Automation

Pode utilizar [o Azure Monitor](../azure-monitor/overview.md) para monitorizar métricas e registos de nível base para a maioria dos serviços em Azure. Pode ligar para os runbooks da Azure Automation utilizando [grupos de ação](../azure-monitor/platform/action-groups.md) para automatizar tarefas com base em alertas. Este artigo mostra-lhe como configurar e executar um runbook usando alertas.

## <a name="alert-types"></a>Tipos de alerta

Pode utilizar livros de automatização com três tipos de alerta:

* Alertas comuns
* Alertas do registo de atividades
* Alertas métricos em tempo real

> [!NOTE]
> O esquema de alerta comum normaliza a experiência de consumo para notificações de alerta hoje em Azure. Historicamente, os três tipos de alerta em Azure hoje em dia (métrica, log e log de atividade) tiveram os seus próprios modelos de e-mail, esquemas webhook, etc. Para saber mais, consulte [o esquema de alerta comum](../azure-monitor/alerts/alerts-common-schema.md)

Quando um alerta chama um runbook, a chamada real é um pedido HTTP POST para o webhook. O corpo do pedido POST contém um objeto em formatado JSON que tem propriedades úteis que estão relacionadas com o alerta. A tabela que se segue lista as ligações ao esquema de carga útil para cada tipo de alerta:

|Alerta  |Description|Esquema de carga útil  |
|---------|---------|---------|
|[Alerta comum](../azure-monitor/alerts/alerts-common-schema.md)|O esquema comum de alerta que normaliza a experiência de consumo para notificações de alerta hoje em Azure.|Esquema de carga útil de alerta comum|
|[Alerta de registo de atividade](../azure-monitor/alerts/activity-log-alerts.md)    |Envia uma notificação quando qualquer novo evento no registo de atividades do Azure corresponde a condições específicas. Por exemplo, quando uma `Delete VM` operação ocorre no **myProductionResourceGroup** ou quando um novo evento de Saúde do Serviço Azure com um estado Ativo aparece.| [Esquema de carga útil de alerta de atividade](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Alerta métrico perto do tempo real](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |Envia uma notificação mais rapidamente do que os alertas métricos quando uma ou mais métricas de nível de plataforma satisfazem as condições especificadas. Por exemplo, quando o valor para **CPU %** num VM é superior a 90, e o valor para **a Rede In** é superior a 500 MB nos últimos 5 minutos.| [Esquema de carga útil de alerta métrico próximo em tempo real](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Como os dados fornecidos por cada tipo de alerta são diferentes, cada tipo de alerta é tratado de forma diferente. Na secção seguinte, aprende-se a criar um livro de bordo para lidar com diferentes tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Criar um livro de corridas para lidar com alertas

Para utilizar a Automatização com alertas, precisa de um runbook que tenha lógica que gere a carga útil JSON de alerta que é passada para o runbook. O seguinte livro de exemplo deve ser chamado de um alerta Azure.

Como descrito na secção anterior, cada tipo de alerta tem um esquema diferente. O script retira os dados do webhook de um alerta no parâmetro de entrada do `WebhookData` runbook. Em seguida, o script avalia a carga útil JSON para determinar que tipo de alerta está a ser usado.

Este exemplo usa um alerta de um VM. Recupera os dados VM da carga útil e, em seguida, utiliza essa informação para parar o VM. A ligação deve ser configurada na conta Automation onde o livro é executado. Ao utilizar alertas para ativar os runbooks, é importante verificar o estado de alerta no livro de verificação que é acionado. O livro de bordo dispara cada vez que o estado de alerta muda de estado. Os alertas têm vários estados, sendo os dois mais comuns ativados e resolvidos. Verifique se está o estado na sua lógica de runbook para garantir que o livro de recortes não funciona mais do que uma vez. O exemplo neste artigo mostra como procurar alertas apenas com estado ativado.

O runbook utiliza o ativo de ligação `AzureRunAsConnection` [Run As account](./automation-security-overview.md) to authenticate with Azure to execute a ação de gestão contra o VM.

Utilize este exemplo para criar um livro de recortes chamado **Stop-AzureVmInResponsetoVMAlert**. Pode modificar o script PowerShell e usá-lo com muitos recursos diferentes.

1. Vá à sua conta Azure Automation.
2. Em **Automatização de Processos,** selecione **Runbooks**.
3. No topo da lista de runbooks, selecione **+ Crie um livro de bordo.**
4. Na página **Add Runbook,** insira **Stop-AzureVmInResponsetoVMAlert** para o nome do livro de verificação. Para o tipo de livro de execução, selecione **PowerShell**. Em seguida, selecione **Criar**.  
5. Copie o exemplo PowerShell a seguir na página **Editar.**

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
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
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

6. **Selecione Publicar** para guardar e publicar o livro de recortes.

## <a name="create-the-alert"></a>Criar o alerta

Os alertas utilizam grupos de ação, que são coleções de ações que são desencadeadas pelo alerta. Os runbooks são apenas uma das muitas ações que você pode usar com grupos de ação.

1. Na sua conta Demôm automação, selecione **Alertas** em **Monitorização**.
1. Selecione **+ Nova regra de alerta**.
1. Clique em **Selecionar** em **Recurso.** Na página **'Selecione' um recurso,** selecione o seu VM para alertar e clique em **'Fazer'.**
1. Clique **em Adicionar condição** em **condição**. Selecione o sinal que pretende utilizar, por **exemplo, Percentagem CPU** e clique em **Fazer**.
1. Na página lógica de **sinal configurar,** insira o seu **valor Threshold** na lógica **de Alerta** e clique em **'Fazer'.**
1. Em **grupos de Ação**, selecione Create **New**.
1. Na página do **grupo de ação Add,** dê ao seu grupo de ação um nome e um nome curto.
1. Dê um nome à ação. Para o tipo de ação, **selecione Automation Runbook**.
1. Selecione **Detalhes de Edição**. Na página **Configure Runbook,** na **fonte Runbook**, selecione **User**.  
1. Selecione a sua conta **de Subscrição** e **Automação** e, em seguida, selecione o runbook **Stop-AzureVmInResponsetoVMAlert.**  
1. Selecione **Sim** para **Ativar o esquema de alerta comum**.
1. Para criar o grupo de ação, selecione **OK**.

    ![Adicionar página de grupo de ação](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Pode utilizar este grupo de ação nos alertas de [registo de atividade e alertas](../azure-monitor/alerts/activity-log-alerts.md) de quase tempo [real](../azure-monitor/alerts/alerts-overview.md) que cria.

1. Em **Detalhes de Alerta**, adicione um nome e descrição da regra de alerta e clique em Criar regra de **alerta**.

## <a name="next-steps"></a>Passos seguintes

* Para iniciar um runbook utilizando um webhook, consulte [Iniciar um livro de formulários a partir de um webhook](automation-webhooks.md).
* Para descobrir diferentes formas de iniciar um livro de bordo, consulte [Iniciar um livro de recortes](./start-runbooks.md).
* Para criar um alerta de registo de atividade, consulte [Criar alertas de registo de atividades](../azure-monitor/alerts/activity-log-alerts.md).
* Para aprender a criar um alerta quase em tempo real, consulte [Criar uma regra de alerta no portal Azure](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
