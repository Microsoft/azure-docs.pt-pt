---
title: Amostras de PowerShell monitor Azure Monitor
description: Utilize o PowerShell para aceder a funcionalidades do Azure Monitor, tais como autoescala, alertas, webhooks e registos de atividades.
ms.subservice: ''
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 2/14/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74211df6f925aaa09a4c87a518056e8ef3206b87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078406"
---
# <a name="azure-monitor-powershell-samples"></a>Amostras de PowerShell monitor Azure Monitor
Este artigo mostra-lhe a amostra de comandos PowerShell para ajudá-lo a aceder às funcionalidades do Azure Monitor.

> [!NOTE]
> Azure Monitor é o novo nome do que foi chamado de "Azure Insights" até 25 de setembro de 2016. No entanto, os espaços de nome e, portanto, os seguintes comandos ainda contêm a palavra *insights*.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Configurar PowerShell
Se ainda não o fez, configura o PowerShell para executar o seu computador. Para obter mais informações, consulte [Como Instalar e Configurar o PowerShell](/powershell/azure/).

## <a name="examples-in-this-article"></a>Exemplos neste artigo
Os exemplos do artigo ilustram como pode utilizar cmdlets do Azure Monitor. Também pode rever toda a lista de cmdlets Azure Monitor PowerShell no [Azure Monitor (Insights) Cmdlets](/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Iniciar s.000 e utilizar subscrições
Primeiro, faça login na sua assinatura Azure.

```powershell
Connect-AzAccount
```

Verá um sinal no ecrã. Assim que assinar na sua Conta, o TenantID e o ID de subscrição predefinido são apresentados. Todos os cmdlets Azure funcionam no contexto da sua subscrição padrão. Para ver a lista de subscrições a que tem acesso, utilize o seguinte comando:

```powershell
Get-AzSubscription
```

Para ver o seu contexto de trabalho (contra a subscrição dos seus comandos), utilize o seguinte comando:

```powershell
Get-AzContext
```
Para alterar o seu contexto de trabalho para uma subscrição diferente, utilize o seguinte comando:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log"></a>Registar atividades de recuperação
Utilize o [cmdlet Get-AzLog.](/powershell/module/az.monitor/get-azlog)  Seguem-se alguns exemplos comuns. O Registo de Atividades realiza os últimos 90 dias de operações. A utilização de datas antes desta hora resulta numa mensagem de erro.  

Veja qual a data/hora atuais para verificar as horas a utilizar nos comandos abaixo:
```powershell
Get-Date
```

Obtenha entradas de registo a partir desta hora/data para apresentar:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Obtenha entradas de registo entre um intervalo de hora/data:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenha entradas de registo de um grupo de recursos específico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obtenha entradas de registo de um fornecedor de recursos específico entre um intervalo de tempo/data:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenha todas as entradas de registo com um chamador específico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

O seguinte comando recupera os últimos 1000 eventos do registo de atividades:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog` suporta muitos outros parâmetros. Consulte a `Get-AzLog` referência para mais informações.

> [!NOTE]
> `Get-AzLog` só fornece 15 dias de história. A utilização do parâmetro **-MaxRecords** permite-lhe consultar os últimos eventos N, para além de 15 dias. Para aceder a eventos com mais de 15 dias, utilize a amostra REST API ou SDK (amostra C# utilizando o SDK). Se não incluir **o StartTime,** então o valor predefinido é **EndTime** menos uma hora. Se não incluir **o EndTime,** então o valor predefinido é a hora atual. Todos os tempos estão na UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Recuperar alertas histórico
Para visualizar todos os eventos de alerta, pode consultar os registos do Azure Resource Manager utilizando os seguintes exemplos.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para visualizar o histórico para uma regra de alerta específica, pode utilizar o `Get-AzAlertHistory` cmdlet, passando no ID de recurso da regra de alerta.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

O `Get-AzAlertHistory` cmdlet suporta vários parâmetros. Mais informações, consulte [a História do Alerta.](/previous-versions/azure/mt282453(v=azure.100))

## <a name="retrieve-information-on-alert-rules"></a>Recuperar informações sobre regras de alerta
Todos os seguintes comandos atuam num Grupo de Recursos chamado "montest".

Ver todas as propriedades da regra de alerta:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recupere todos os alertas de um grupo de recursos:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Recupere todas as regras de alerta definidas para um recurso alvo. Por exemplo, todas as regras de alerta definidas num VM.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` suporta outros parâmetros. Consulte [o Get-AlertRule](/previous-versions/azure/mt282459(v=azure.100)) para obter mais informações.

## <a name="create-metric-alerts"></a>Criar alertas de métricas
Pode utilizar o `Add-AlertRule` cmdlet para criar, atualizar ou desativar uma regra de alerta.

Pode criar propriedades de e-mail e webhook usando  `New-AzAlertRuleEmail` `New-AzAlertRuleWebhook` e, respectivamente. Na regra de alerta, atribuir estes imóveis como ações à propriedade **de Ações** da Regra de Alerta.

A tabela seguinte descreve os parâmetros e valores utilizados para criar um alerta utilizando uma métrica.

| parameter | valor |
| --- | --- |
| Nome |simpletestdiskwrite |
| Localização desta regra de alerta |E.U.A. Leste |
| ResourceGroup |montest |
| TargetResourceId |/subscrições/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| Nome métrico do alerta que é criado |\PhysicalDisk (_Total)\Discos de disco/seg. Veja o `Get-MetricDefinitions` cmdlet sobre como recuperar os nomes métricos exatos |
| operador |GreaterThan |
| Valor limiar (contagem/seg para esta métrica) |1 |
| JanelaSSize (formato hh:mm:ss) |00:05:00 |
| agregador (estatística da métrica, que utiliza a contagem média, neste caso) |Média |
| e-mails personalizados (matriz de cordas) |'foo@example.com','bar@example.com' |
| enviar e-mail para proprietários, colaboradores e leitores |-SendToServiceOwners |

Criar uma ação de e-mail

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Criar uma ação Webhook

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Crie a regra de alerta na métrica cpu% num VM clássico

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperar a regra de alerta

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

O Cmdlet de alerta add também atualiza a regra se já existe uma regra de alerta para as propriedades dadas. Para desativar uma regra de alerta, inclua o parâmetro **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obtenha uma lista de métricas disponíveis para alertas
Pode utilizar o `Get-AzMetricDefinition` cmdlet para visualizar a lista de todas as métricas para um recurso específico.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

O exemplo a seguir gera uma tabela com o Nome métrico e a Unidade para o mesmo.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Uma lista completa das opções disponíveis `Get-AzMetricDefinition` está disponível na [Get-MetricDefinitions](/previous-versions/azure/mt282458(v=azure.100)).

## <a name="create-and-manage-activity-log-alerts"></a>Criar e gerir alertas de Registo de Atividades
Pode utilizar o `Set-AzActivityLogAlert` cmdlet para definir um alerta de Registo de Atividade. Um alerta de Registo de Atividade requer que primeiro defina as suas condições como um dicionário de condições e, em seguida, crie um alerta que utilize essas condições.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

As propriedades adicionais do webhook são opcionais. Pode recuperar o conteúdo de um Alerta de Registo de Atividade utilizando `Get-AzActivityLogAlert` .

## <a name="create-and-manage-autoscale-settings"></a>Criar e gerir definições de AutoScale

> [!NOTE] 
> Para serviços cloud (Microsoft.ClassicCompute), a autoescala suporta um grão de tempo de 5 minutos (PT5M). Para os outros serviços, a autoescala suporta um grão de tempo de mínimo de 1 minuto (PT1M)

Um recurso (uma aplicação Web, VM, Cloud Service ou Virtual Machine Scale set) pode ter apenas uma definição de escala automática configurada para o mesmo.
No entanto, cada definição de autoescala pode ter vários perfis. Por exemplo, um para um perfil de escala baseado no desempenho e um segundo para um perfil baseado em agenda. Cada perfil pode ter várias regras configuradas nele. Para obter mais informações sobre a Autoscale, consulte [Como autoescalar uma aplicação.](../../cloud-services/cloud-services-how-to-scale-portal.md)

Aqui estão os passos a utilizar:

1. Criar regras.s.
2. Crie perfis mapeando as regras que criou anteriormente para os perfis.
3. Opcional: Criar notificações para autoescala configurando propriedades webhook e email.
4. Crie uma definição de autoescalação com um nome no recurso-alvo mapeando os perfis e notificações que criou nos passos anteriores.

Os exemplos que se seguem mostram-lhe como pode criar uma definição de escala automática para um Conjunto de Escala de Máquina Virtual para um sistema operativo Windows baseado na métrica de utilização do CPU.

Primeiro, criar uma regra para escalar, com um aumento da contagem de casos.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Em seguida, criar uma regra para escalar, com uma contagem de casos diminuir.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Então, criar um perfil para as regras.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crie uma propriedade webhook.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crie a propriedade de notificação para a definição de autoescala, incluindo o e-mail e o webhook que criou anteriormente.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Por fim, crie a definição de autoescala para adicionar o perfil que criou anteriormente. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obter mais informações sobre a gestão das definições de Autoscale, consulte [Get-AutoscaleSetting](/previous-versions/azure/mt282461(v=azure.100)).

## <a name="autoscale-history"></a>História da autoescala
O exemplo que se segue mostra-lhe como pode ver eventos recentes de autoescala e alerta. Utilize a pesquisa de registo de atividade para visualizar o histórico de autoescala.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Pode utilizar o `Get-AzAutoScaleHistory` cmdlet para recuperar o histórico da AutoScale.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para mais informações, consulte [a História da Escala Automática.](/previous-versions/azure/mt282464(v=azure.100))

### <a name="view-details-for-an-autoscale-setting"></a>Ver detalhes para uma definição de escala automática
Pode utilizar o `Get-Autoscalesetting` cmdlet para obter mais informações sobre a definição de autoescala.

O exemplo a seguir mostra detalhes sobre todas as definições de autoescala no grupo de recursos 'myrg1'.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

O exemplo a seguir mostra detalhes sobre todas as definições de autoescala no grupo de recursos 'myrg1' e especificamente a definição de autoescala denominada 'MyScaleVMSSSetting'.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Remova uma definição de autoescala
Pode utilizar o `Remove-Autoscalesetting` cmdlet para eliminar uma definição de escala automática.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Gerir perfis de registo para registo de atividade
Pode criar um perfil de *registo* e exportar dados do seu registo de atividade para uma conta de armazenamento e pode configurar a retenção de dados para o mesmo. Opcionalmente, também pode transmitir os dados para o seu Centro de Eventos. Esta funcionalidade encontra-se atualmente em Pré-visualização e só pode criar um perfil de registo por subscrição. Pode utilizar os seguintes cmdlets com a sua subscrição atual para criar e gerir perfis de registo. Também pode escolher uma subscrição específica. Embora o PowerShell predefinindo a subscrição atual, pode sempre alterar isso utilizando `Set-AzContext` . Pode configurar o registo de atividades para encaminhar dados para qualquer conta de armazenamento ou Centro de Eventos dentro dessa subscrição. Os dados são escritos como ficheiros blob no formato JSON.

### <a name="get-a-log-profile"></a>Obtenha um perfil de registo
Para obter os perfis de registo existentes, utilize o `Get-AzLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Adicionar um perfil de registo sem retenção de dados
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de registo
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adicione um perfil de registo com a retenção de dados
Pode especificar a propriedade **-RetentionInDays** com o número de dias, como um número inteiro positivo, onde os dados são retidos.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Adicione o perfil de registo com retenção e EventHub
Além de encaminhar os seus dados para a conta de armazenamento, também pode transmiti-lo para um Centro de Eventos. Nesta versão de pré-visualização, a configuração da conta de armazenamento é obrigatória, mas a configuração do Event Hub é opcional.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurar registos de diagnósticos
Muitos serviços da Azure fornecem registos e telemetria adicionais que podem fazer um ou mais dos seguintes: 
 - ser configurado para guardar dados na sua conta de Armazenamento Azure
 - enviado para Centros de Eventos
 - enviado para um espaço de trabalho Log Analytics. 

A operação só pode ser efetuada a nível de recursos. A conta de armazenamento ou o centro de eventos devem estar presentes na mesma região que o recurso-alvo onde a definição de diagnóstico está configurada.

### <a name="get-diagnostic-setting"></a>Obtenha a definição de diagnóstico
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Desativar a definição de diagnóstico

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Ativar a definição de diagnóstico sem retenção

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Ativar a definição de diagnóstico com retenção

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Ativar a definição de diagnóstico com retenção para uma categoria de registo específico

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Ativar a definição de diagnóstico para centros de eventos

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Ativar a definição de diagnóstico para o Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Note que a propriedade WorkspaceId requer o *ID* de recursos do espaço de trabalho. Pode obter o ID de recurso do seu espaço de trabalho Log Analytics utilizando o seguinte comando:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Estes comandos podem ser combinados para enviar dados para vários destinos.
