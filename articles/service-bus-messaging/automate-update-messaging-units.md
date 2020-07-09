---
title: Azure Service Bus - Atualizar automaticamente as unidades de mensagens
description: Este artigo mostra-lhe como pode utilizar um runbook da Azure Automation para atualizar automaticamente as unidades de mensagens de um espaço de nomes de Service Bus.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341493"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Atualizar automaticamente as unidades de mensagens de um espaço de nomes do Azure Service Bus 
Este artigo mostra-lhe como pode atualizar automaticamente [as unidades](service-bus-premium-messaging.md) de mensagens de um espaço de nomes do Service Bus com base no uso de recursos (CPU ou memória). 

O exemplo neste artigo mostra como aumentar as unidades de mensagens para um espaço de nomes de service bus quando o uso do CPU do espaço de nomes ultrapassa os 75%. Os passos de alto nível são:

1. Crie um runbook Azure Automation com script PowerShell que escala (aumenta) as unidades de mensagens para um espaço de nomes de Service Bus. 
2. Crie um alerta de utilização do CPU no espaço de nomes do Service Bus, que invoca o script PowerShell quando o uso do CPU de espaço de nome for superior a 75%. 

> [!IMPORTANT]
> Este artigo aplica-se apenas ao nível **premium** da Azure Service Bus. 


## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus
Crie um espaço de nomes de serviço de serviço de nível principal. Siga os passos do Create a namespace no artigo [do portal Azure](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) para criar o espaço de nome. 

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure
Crie uma conta Azure Automation seguindo as instruções do artigo [da conta Azure Automation.](../automation/automation-quickstart-create-account.md) 

## <a name="import-azservice-module-from-gallery"></a>Import Az.Service module da galeria
Importar `Az.Accounts` e `Az.ServiceBus` módulos da galeria para a conta de automação. O `Az.ServiceBus` módulo depende do `Az.Accounts` módulo, pelo que deve ser instalado primeiro. 

Para obter instruções passo a passo, consulte [Importar um módulo da galeria do módulo.](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal)

## <a name="create-and-publish-a-powershell-runbook"></a>Criar e publicar um livro de execução PowerShell

1. Crie um livro de execução PowerShell seguindo as instruções no artigo [do powerShell.](../automation/automation-quickstart-create-runbook.md) 

    Aqui está uma amostra do script PowerShell que podes usar para aumentar as unidades de mensagens para um espaço de nomes de Service Bus. Este script PowerShell num livro de automação aumenta os MUs de 1 para 2, 2 a 4 ou 4 a 8. Os valores permitidos para esta propriedade são: 1, 2, 4 e 8. Pode criar outro runbook para diminuir as unidades de mensagens.

    Os **parâmetros namespaceName** e **resourceGroupName** são utilizados para testar o script separadamente do cenário de alerta. 
    
    O parâmetro **WebHookData** é para que o alerta passe informações como nome de grupo de recursos, nome de recursos, etc. no tempo de execução. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Teste o livro especificando](../automation/manage-runbooks.md#test-a-runbook) valores para os parâmetros **namespaceName** e **resourceGroupName.** Confirme que as unidades de mensagens no espaço de nomes estão atualizadas. 
3. Depois de testar com sucesso, [publique o livro](..//automation/manage-runbooks.md#publish-a-runbook) de trabalho para que esteja disponível para adicionar como uma ação para um alerta no espaço de nome mais tarde. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Crie um alerta no espaço de nomes para ativar o livro de bordo
Consulte um alerta para acionar um artigo [de runbook da Azure Automation](../automation/automation-create-alert-triggered-runbook.md) para configurar um alerta no seu espaço de nomes service bus para acionar o livro de verificação de automação que criou. Por exemplo, pode criar um alerta sobre **a utilização do CPU por espaço de nome** ou utilização do tamanho da memória por métrica do espaço de **identificação,** e adicionar uma ação para desencadear o livro de verificação de automação que criou. Para obter mais informações sobre estas métricas, consulte [as métricas de utilização do recurso.](service-bus-metrics-azure-monitor.md#resource-usage-metrics)

O procedimento a seguir mostra como criar um alerta que desencadeie o livro de verificação da automatização quando o uso do **CPU de** espaço de nome ultrapassa **os 75%.**

1. Na página **Service Bus Namespace** para o seu espaço de nome, selecione **Alertas** no menu esquerdo e, em seguida, selecione **+ Nova regra de alerta** na barra de ferramentas. 
    
    ![Página de alertas - Novo botão de regra de alerta](./media/automate-update-messaging-units/alerts-page.png)
2. Na página **'Criar regra de alerta',** clique **em Selecionar a condição.** 

    ![Criar página de regra de alerta - selecione condição](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. Na página lógica de **sinal de configuração,** selecione **CPU** para o sinal. 

    ![Lógica de sinal de configuração - selecione CPU](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Introduza um **valor limiar** (neste exemplo, é **de 75%**), e selecione **Feito**. 

    ![Sinal de CPU de configuração](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. Agora, na **página de alerta Criar,** clique em Selecionar grupo de **ação**.
    
    ![Selecione grupo de ação](./media/automate-update-messaging-units/select-action-group-button.png)
6. Selecione Criar o botão **de grupo de ação** na barra de ferramentas. 

    ![Criar botão de grupo de ação](./media/automate-update-messaging-units/create-action-group-button.png)
7. Na página do **grupo de ação Add,** faça os seguintes passos:
    1. Insira um **nome** para o grupo de ação. 
    2. Insira um **nome curto** para o grupo de ação.
    3. Selecione a **subscrição** na qual pretende criar este grupo de ação.
    4. Selecione o **grupo de recursos**. 
    5. Na secção **Ações,** insira um **nome para a ação**e selecione **o Manual de Automação** para **Tipo de Ação**. 

        ![Adicionar página de grupo de ação](./media/automate-update-messaging-units/add-action-group-page.png)
8. Na página **Configure Runbook,** faça os seguintes passos:
    1. Para **obter a origem do Runbook**, selecione **Utilizador**. 
    2. Para **subscrição**, selecione a **subscrição** Azure que contém a conta de automação. 
    3. Para **a conta Demôm automação,** selecione a sua **conta de automação**.
    4. Para **Runbook,** selecione o seu runbook. 
    5. Selecione **OK** na página **Configure Runbook.** 
        ![Livro de configure](./media/automate-update-messaging-units/configure-runbook.png)
9. Selecione **OK** na página do **grupo de ação Add.** 
5. Agora, na página **'Criar regra de alerta',** introduza um **nome para a regra**e, em seguida, selecione Criar a regra de **alerta**. 
    ![Criar regra de alerta](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > Agora, quando o uso do CPU de espaço de nome ultrapassa os 75, o alerta desencadeia o runbook de automação, o que aumenta as unidades de mensagens do espaço de nomes do Service Bus. Da mesma forma, pode criar um alerta para outro runbook de automação, que diminui as unidades de mensagens se o uso do CPU de espaço de nome for inferior a 25. 

## <a name="next-steps"></a>Próximos passos
Para conhecer as unidades de mensagens, consulte as [mensagens Premium](service-bus-premium-messaging.md)
