---
title: Azure Service Bus - Atualizar automaticamente as unidades de mensagens
description: Este artigo mostra-lhe como pode usar um livro de execução da Automatização Azure para atualizar automaticamente as unidades de mensagens de um espaço de nome de autocarro de serviço.
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: spelluru
ms.topic: how-to
ms.date: 05/14/2020
ms.author: spelluru
ms.openlocfilehash: 5ece7beaea709c9b1e52cf2130484663da0aa4ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664551"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Atualizar automaticamente as unidades de mensagens de um espaço de nome do Azure Service Bus 
Este artigo mostra-lhe como pode atualizar automaticamente [as unidades](service-bus-premium-messaging.md) de mensagens de um espaço de nome do Bus de Serviço com base no uso de recursos (CPU ou memória). 

O exemplo neste artigo mostra como aumentar as unidades de mensagens para um espaço de nome de autocarro de serviço quando o uso do CPU do espaço de nome sai acima dos 75%. Os passos de alto nível são:

1. Crie um livro de execução da Automação Azure com script PowerShell que aumente (aumenta) unidades de mensagens para um espaço de nome de ônibus de serviço. 
2. Crie um alerta de utilização do CPU no espaço de nome do Autocarro de Serviço, que invoca o script PowerShell quando o uso do CPU do espaço de nome sai acima dos 75%. 

> [!IMPORTANT]
> Este artigo aplica-se apenas ao nível **premium** do Azure Service Bus. 


## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus
Crie um espaço de nome de ônibus de serviço de nível principal. Siga os passos do Espaço criar um espaço de [nome no portal Azure](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) para criar o espaço de nome. 

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure
Crie uma conta De Automação Azure seguindo instruções do artigo da [conta Create a Azure Automation.](../automation/automation-quickstart-create-account.md) 

## <a name="import-azservice-module-from-gallery"></a>Módulo de serviço Import Az.Service da galeria
Importação `Az.Accounts` e `Az.ServiceBus` módulos da galeria para a conta de automação. O `Az.ServiceBus` módulo depende do `Az.Accounts` módulo, pelo que deve ser instalado primeiro. 

Para obter instruções passo a passo, consulte [importar um módulo da galeria do módulo](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal).

## <a name="create-and-publish-a-powershell-runbook"></a>Criar e publicar um livro de execução powerShell

1. Crie um livro de execução PowerShell seguindo instruções no artigo [Create a PowerShell.](../automation/automation-quickstart-create-runbook.md) 

    Aqui está um script powerShell de amostra que pode usar para aumentar as unidades de mensagens para um espaço de nome de ônibus de serviço. Este script PowerShell num livro de automação aumenta as UD de 1 para 2, 2 para 4 ou 4 para 8. Os valores permitidos para esta propriedade são: 1, 2, 4 e 8. Pode criar outro livro de execução para diminuir as unidades de mensagens.

    Os **parâmetros namespaceName** e **resourceGroupName** são utilizados para testar o script separadamente do cenário de alerta. 
    
    O parâmetro **WebHookData** destina-se ao alerta para passar informações como nome de grupo de recursos, nome de recurso, etc. no tempo de execução. 

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
            $resourceGroupName = $AlertContext.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $AlertContext.resourceName
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
2. [Teste o livro](../automation/manage-runbooks.md#test-a-runbook) especificando valores para os parâmetros **namespaceName** e **resourceGroupName.** Confirme que as unidades de mensagens no espaço de nome estão atualizadas. 
3. Depois de testar com sucesso, [publique o livro](..//automation/manage-runbooks.md#publish-a-runbook) de trabalho para que esteja disponível para adicionar como uma ação para um alerta no espaço de nome sem estiveros mais tarde. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Crie um alerta sobre o espaço de nome para desencadear o livro de corridas
Consulte Utilize um alerta para desencadear um artigo do livro de [execução da Azure Automation](../automation/automation-create-alert-triggered-runbook.md) para configurar um alerta no seu espaço de nome do Bus de Serviço para desencadear o livro de automação que criou. Por exemplo, pode criar um alerta sobre o uso de **CPU por espaço** de nome ou uso do tamanho da memória por métrica de **espaço de nome,** e adicionar uma ação para desencadear o livro de automação que criou. Para mais detalhes sobre estas métricas, consulte [as métricas](service-bus-metrics-azure-monitor.md#resource-usage-metrics)de utilização do Recurso .

O procedimento seguinte mostra como criar um alerta que desencadeie o livro de execução da automatização quando o uso do **CPU** do espaço de nome sai acima de **75%.**

1. Na página **Service Bus Namespace** para o seu espaço de nome, selecione **Alertas** no menu esquerdo e, em seguida, selecione **+ Nova regra** de alerta na barra de ferramentas. 
    
    ![Página de alertas - Novo botão de regra de alerta](./media/automate-update-messaging-units/alerts-page.png)
2. Na página de **regra de alerta Criar,** clique em **Selecionar a condição**. 

    ![Criar página de regra de alerta - condição selecionada](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. Na página lógica do **sinal Configurar,** selecione **CPU** para o sinal. 

    ![Configure lógica de sinal - selecione CPU](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Introduza um **valor limiar** (neste exemplo, é **75%**), e selecione **Done**. 

    ![Configurar o sinal CPU](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. Agora, na **página de alerta Criar,** clique em Selecionar grupo de **ação**.
    
    ![Selecione grupo de ação](./media/automate-update-messaging-units/select-action-group-button.png)
6. Selecione Criar o botão **de grupo** de ação na barra de ferramentas. 

    ![Criar botão de grupo de ação](./media/automate-update-messaging-units/create-action-group-button.png)
7. Na página do **grupo de ação Add,** faça os seguintes passos:
    1. Insira um **nome** para o grupo de ação. 
    2. Insira um **pequeno nome** para o grupo de ação.
    3. Selecione a **subscrição** na qual pretende criar este grupo de ação.
    4. Selecione o grupo de **recursos**. 
    5. Na secção **Ações,** introduza um **nome para a ação**e selecione Automation **Runbook** for **Action type**. 

        ![Adicionar página de grupo de ação](./media/automate-update-messaging-units/add-action-group-page.png)
8. Na página **Configure Runbook,** faça os seguintes passos:
    1. Para obter o Livro de **Recorridas,** selecione **User**. 
    2. Para **Subscrição,** selecione a sua **subscrição** Azure que contenha a conta de automação. 
    3. Para **a conta Automation**, selecione a sua conta de **automação**.
    4. Para **Runbook,** selecione o seu livro de execução. 
    5. Selecione **OK** na página **Configure Runbook.** 
        ![Configure livro de execução](./media/automate-update-messaging-units/configure-runbook.png)
9. Selecione **OK** na página do **grupo de ação Adicionar.** 
5. Agora, na página de regra de **alerta Criar,** introduza um **nome para a regra**, e, em seguida, selecione Criar regra de **alerta**. 
    ![Criar regra de alerta](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > Agora, quando o uso do CPU do espaço de nome sai acima dos 75, o alerta despoleta o livro de execução da automatização, que aumenta as unidades de mensagens do espaço de nome do Bus de Serviço. Da mesma forma, pode criar um alerta para outro livro de execução de automatização, que diminui as unidades de mensagens se o uso do CPU do espaço de nome for inferior a 25. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre unidades de mensagens, consulte as [mensagens Premium](service-bus-premium-messaging.md)