---
title: Use um alerta para desencadear um livro de execução da Automação Azure
description: Este artigo diz como desencadear um livro de corridas para executar quando um alerta Azure é levantado.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 1feadeaf2a905abee396c09829dab5e06c46d99c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837115"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Use um alerta para desencadear um livro de execução da Automação Azure

Pode utilizar o [Monitor Azure](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) para monitorizar métricas e registos de nível base para a maioria dos serviços em Azure. Pode ligar para os livros de execução da Automatização Azure utilizando [grupos](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) de ação ou utilizando alertas clássicos para automatizar tarefas com base em alertas. Este artigo mostra-lhe como configurar e executar um livro de corridas usando alertas.

## <a name="alert-types"></a>Tipos de alerta

Pode utilizar livros de automação com três tipos de alerta:

* Alertas comuns
* Alertas do registo de atividades
* Alertas métricos quase em tempo real

> [!NOTE]
> O esquema de alerta comum normaliza a experiência de consumo para notificações de alerta em Azure hoje. Historicamente, os três tipos de alerta em Azure hoje (registo métrico, diário e de atividade) tiveram os seus próprios modelos de e-mail, schemas webhook, etc. Para saber mais, consulte o esquema de [alerta comum](../azure-monitor/platform/alerts-common-schema.md)

Quando um alerta chama um livro de execução, a chamada real é um pedido HTTP POST para o webhook. O corpo do pedido POST contém um objeto formado pela JSON que tem propriedades úteis que estão relacionadas com o alerta. A tabela seguinte enumera as ligações ao esquema de carga útil para cada tipo de alerta:

|Alerta  |Descrição|Esquema de carga útil  |
|---------|---------|---------|
|[Alerta comum](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|O esquema de alerta comum que normaliza a experiência de consumo para notificações de alerta hoje em Azure.|Esquema de carga útil de alerta comum|
|[Alerta de registo de atividade](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação quando qualquer novo evento no registo de atividade do Azure corresponde a condições específicas. Por exemplo, quando uma `Delete VM` operação ocorre no **myProductionResourceGroup** ou quando aparece um novo evento de Saúde de Serviço Azure com um estado Ativo.| [Esquema de alerta de registo de atividade](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Alerta métrico em tempo real](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação mais rapidamente do que os alertas métricos quando uma ou mais métricas ao nível da plataforma cumprem as condições especificadas. Por exemplo, quando o valor para a **CPU %** num VM é superior a 90, e o valor para a **Rede In** é superior a 500 MB nos últimos 5 minutos.| [Esquema de carga útil de alerta métrico em tempo real](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Como os dados fornecidos por cada tipo de alerta são diferentes, cada tipo de alerta é tratado de forma diferente. Na secção seguinte, aprende-se a criar um livro de rés para lidar com diferentes tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Crie um livro de corridas para lidar com alertas

Para utilizar a Automação com alertas, é necessário um livro de execução que tenha uma lógica que gere a carga útil jSON alerta que é passada para o livro de execução. O seguinte livro de exemplo saem de um alerta Azure.

Tal como descrito na secção anterior, cada tipo de alerta tem um esquema diferente. O script retira os dados do webhook a partir de um alerta no parâmetro de entrada do `WebhookData` livro de execução. Em seguida, o script avalia a carga útil JSON para determinar que tipo de alerta está sendo usado.

Este exemplo usa um alerta de um VM. Recupera os dados vm da carga útil e, em seguida, utiliza essa informação para parar o VM. A ligação deve ser configurada na conta Automation onde o livro de execução é executado. Ao utilizar alertas para acionar os livros de execução, é importante verificar o estado de alerta no livro de execução que é acionado. O livro de execução dispara cada vez que o estado de alerta muda de estado. Os alertas têm vários estados, com os dois mais comuns a serem ativados e resolvidos. Verifique o estado na lógica do seu livro de corridas para garantir que o livro de corridas não funciona mais do que uma vez. O exemplo neste artigo mostra como procurar alertas apenas com o Estado Ativado.

O livro de execução utiliza o ativo de ligação `AzureRunAsConnection` [Run Como conta](automation-create-runas-account.md) para autenticar com o Azure para realizar a ação de gestão contra o VM.

Use este exemplo para criar um livro chamado **Stop-AzureVmInResponsetoVMAlert**. Pode modificar o script PowerShell e usá-lo com muitos recursos diferentes.

1. Vá à sua conta de Automação Azure.
2. Em Fase **de Automação de Processos,** selecione **Runbooks**.
3. No topo da lista de livros de execução, selecione **+ Crie um livro de execução**.
4. Na página **Add Runbook,** introduza **Stop-AzureVmInResponsetoVMAlert** para o nome do livro de execução. Para o tipo de livro de execução, selecione **PowerShell**. Em seguida, selecione **Criar**.  
5. Copie o seguinte exemplo PowerShell na página **Editar.**

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

6. Selecione **Publicar** para guardar e publicar o livro de execução.

## <a name="create-the-alert"></a>Criar o alerta

Os alertas utilizam grupos de ação, que são coletores de ações que são desencadeadas pelo alerta. Os livros de corridas são apenas uma das muitas ações que pode usar com grupos de ação.

1. Na sua conta de Automação, selecione **Alertas** sob **Monitorização**.
1. Selecione **+ Nova regra de alerta**.
1. Clique em **Selecionar** sob **recurso**. Na página **Selecione uma** página de recursos, selecione o seu VM para alertar e clique **em Done**.
1. Clique em **Adicionar condição** sob **condição**. Selecione o sinal que pretende utilizar, por exemplo, **percentagem de CPU** e clique **em Done**.
1. Na página lógica do **sinal Configurar,** introduza o seu **valor limiar** sob a lógica **de Alerta**, e clique em **Done**.
1. Em **grupos de ação,** selecione **Criar Novo**.
1. Na página do **grupo de ação Add,** dê ao seu grupo de ação um nome e um nome curto.
1. Dê um nome à ação. Para o tipo de ação, selecione **Automation Runbook**.
1. Selecione **Editar Detalhes**. Na página **Configure Runbook,** sob a fonte Do Livro de **Execução,** selecione **User**.  
1. Selecione a sua conta **de Subscrição** e **Automação**e, em seguida, selecione o livro de execução **Stop-AzureVmInResponsetoVMAlert.**  
1. Selecione **Sim** para Ativar o esquema de **alerta comum**.
1. Para criar o grupo de ação, selecione **OK**.

    ![Adicionar página de grupo de ação](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Pode utilizar este grupo de ação nos [alertas](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) de registo de atividade e [alertas próximos em tempo real](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) que cria.

1. Em detalhes de **alerta,** adicione um nome e descrição da regra de alerta e clique **em Criar regra**de alerta .

## <a name="next-steps"></a>Passos seguintes

* Para iniciar um livro de corridas utilizando um webhook, consulte [Iniciar um livro de execução a partir de um webhook](automation-webhooks.md).
* Para descobrir diferentes formas de iniciar um livro de corridas, consulte Iniciar um livro de [corridas](automation-starting-a-runbook.md).
* Para criar um alerta de registo de atividade, consulte [Criar alertas](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)de registo de atividade .
* Para aprender a criar um alerta quase em tempo real, consulte Criar uma regra de [alerta no portal Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
