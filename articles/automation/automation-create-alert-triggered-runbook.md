---
title: Usar um alerta para disparar um runbook de automação do Azure
description: Saiba como disparar um runbook para ser executado quando um alerta do Azure é gerado.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: df28116c588ed77f02c78a42a85feb91ca339e7b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366705"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Usar um alerta para disparar um runbook de automação do Azure

Você pode usar [Azure monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) para monitorar os logs e as métricas de nível base para a maioria dos serviços no Azure. Você pode chamar runbooks de automação do Azure usando [grupos de ação](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) ou alertas clássicos para automatizar tarefas com base em alertas. Este artigo mostra como configurar e executar um runbook usando alertas.

## <a name="alert-types"></a>Tipos de alerta

Você pode usar runbooks de automação com três tipos de alertas:

* Alertas comuns
* Alertas do registo de atividades
* Alertas de métrica quase em tempo real

> [!NOTE]
> O esquema de alerta comum padroniza a experiência de consumo para notificações de alerta no Azure hoje. Historicamente, os três tipos de alertas no Azure hoje (métrica, log e log de atividades) tiveram seus próprios modelos de email, esquemas de webhook, etc. Para saber mais, consulte [esquema de alerta comum](../azure-monitor/platform/alerts-common-schema.md)

Quando um alerta chama um runbook, a chamada real é uma solicitação HTTP POST para o webhook. O corpo da solicitação POST contém um objeto com formato JSON que tem propriedades úteis relacionadas ao alerta. A tabela a seguir lista os links para o esquema de carga para cada tipo de alerta:

|Alerta  |Descrição|Esquema de carga  |
|---------|---------|---------|
|[Alerta comum](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|O esquema de alerta comum que padroniza a experiência de consumo para notificações de alerta no Azure hoje.|Esquema de conteúdo de alerta comum|
|[Alerta do log de atividades](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação quando qualquer evento novo no log de atividades do Azure corresponde a condições específicas. Por exemplo, quando uma operação de `Delete VM` ocorre em **myProductionResourceGroup** ou quando um novo evento de integridade do serviço do Azure com um status **ativo** é exibido.| [Esquema de conteúdo de alerta do log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Alerta de métrica quase em tempo real](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação mais rápido que os alertas de métrica quando uma ou mais métricas de nível de plataforma atendem às condições especificadas. Por exemplo, quando o valor de **% de CPU** em uma VM é maior **que 90**, e o valor para a **rede no** é maior que **500 MB** nos últimos 5 minutos.| [Esquema de carga de alerta de métrica quase em tempo real](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Como os dados fornecidos por cada tipo de alerta são diferentes, cada tipo de alerta é tratado de forma diferente. Na próxima seção, você aprenderá a criar um runbook para lidar com diferentes tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Criar um runbook para manipular alertas

Para usar a automação com alertas, você precisa de um runbook que tenha lógica que gerencia o conteúdo JSON de alerta que é passado para o runbook. O runbook de exemplo a seguir deve ser chamado de um alerta do Azure.

Conforme descrito na seção anterior, cada tipo de alerta tem um esquema diferente. O script usa os dados do webhook no parâmetro de entrada `WebhookData` runbook de um alerta. Em seguida, o script avalia o conteúdo JSON para determinar qual tipo de alerta foi usado.

Este exemplo usa um alerta de uma VM. Ele recupera os dados da VM do conteúdo e, em seguida, usa essas informações para interromper a VM. A conexão deve ser configurada na conta de automação em que o runbook é executado. Ao usar alertas para disparar runbooks, é importante verificar o status do alerta no runbook que é disparado. O runbook será disparado cada vez que o estado do alerta for alterado. Os alertas têm vários Estados, os dois Estados mais comuns são `Activated` e `Resolved`. Verifique esse estado em sua lógica de runbook para garantir que o runbook não seja executado mais de uma vez. O exemplo neste artigo mostra como procurar apenas `Activated` alertas.

O runbook usa a [conta Executar como](automation-create-runas-account.md) AzureRunAsConnection para autenticar com o Azure para executar a ação de gerenciamento na VM.

Use este exemplo para criar um runbook chamado **Stop-AzureVmInResponsetoVMAlert**. Você pode modificar o script do PowerShell e usá-lo com muitos recursos diferentes.

1. Vá para sua conta de automação do Azure.
2. Em **automação de processo**, selecione **Runbooks**.
3. Na parte superior da lista de runbooks, selecione **+ criar um runbook**.
4. Na página **Adicionar runbook** , digite **Stop-AzureVmInResponsetoVMAlert** para o nome do runbook. Para o tipo de runbook, selecione **PowerShell**. Em seguida, selecione **Criar**.  
5. Copie o exemplo do PowerShell a seguir na página **Editar** .

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

6. Selecione **publicar** para salvar e publicar o runbook.

## <a name="create-the-alert"></a>Criar o alerta

Os alertas usam grupos de ações, que são coleções de ações disparadas pelo alerta. Runbooks são apenas uma das muitas ações que podem ser usadas com grupos de ação.

1. Em sua conta de automação, selecione **alertas** em **monitoramento**.
1. Selecione **+ Nova regra de alerta**.
1. Clique em **selecionar** em **recurso**. Na página **selecionar um recurso** , selecione sua VM para alertar e clique em **concluído**.
1. Clique em **Adicionar condição** em **condição**. Selecione o sinal que você deseja usar, por exemplo, **percentual de CPU** e clique em **concluído**.
1. Na página **Configurar lógica de sinal** , insira o **valor limite** em **lógica de alerta**e clique em **concluído**.
1. Em **grupos de ações**, selecione **criar novo**.
1. Na página **Adicionar grupo de ações** , dê um nome a seu grupo de ação e um nome curto.
1. Dê um nome à ação. Para o tipo de ação, selecione **runbook de automação**.
1. Selecione **Editar detalhes**. Na página **Configurar runbook** , em **origem do runbook**, selecione **usuário**.  
1. Selecione sua **assinatura** e **conta de automação**e, em seguida, selecione o runbook **Stop-AzureVmInResponsetoVMAlert** .  
1. Selecione **Sim** para **habilitar o esquema de alerta comum**.
1. Para criar o grupo de ações, selecione **OK**.

    ![Página Adicionar grupo de ações](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Você pode usar esse grupo de ações nos [alertas do log de atividades](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) e [alertas quase em tempo real](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) criados por você.

1. Em **detalhes do alerta**, adicione um nome e uma descrição da regra de alerta e clique em **criar regra de alerta**.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como iniciar um runbook de automação usando um webhook, consulte [Iniciar um runbook de um webhook](automation-webhooks.md).
* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, consulte [iniciando um runbook](automation-starting-a-runbook.md).
* Para saber como criar um alerta do log de atividades, consulte [criar alertas do log de atividades](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Para saber como criar um alerta quase em tempo real, consulte [criar uma regra de alerta no portal do Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
