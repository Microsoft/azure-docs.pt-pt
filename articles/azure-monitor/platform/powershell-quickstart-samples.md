---
title: Exemplos de início rápido do Azure PowerShell de Monitor
description: Utilize o PowerShell para aceder às funcionalidades do Azure Monitor, como o dimensionamento automático, alertas, webhooks e pesquisar registos de atividades.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.subservice: ''
ms.openlocfilehash: ae06fae8aa7706428a71b8069eff58ba8bf6abb1
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307518"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Exemplos de início rápido do Azure PowerShell de Monitor
Este artigo será apresentado exemplo de comandos do PowerShell para o ajudar a aceder aos recursos do Azure Monitor.

> [!NOTE]
> O Azure Monitor é o novo nome para o que foi chamado de "Azure Insights" até dia 25 de Setembro de 2016. No entanto, os espaços de nomes e, portanto, os seguintes comandos ainda contêm a palavra "informações".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Configurar o PowerShell
Se ainda não o fez, configure o PowerShell para executar no seu computador. Para obter mais informações, consulte [como instalar e configurar o PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Exemplos neste artigo
Os exemplos neste artigo mostram como pode utilizar os cmdlets do Azure Monitor. Também pode rever a lista completa de cmdlets do PowerShell de Monitor do Azure no [Cmdlets do Azure Monitor (informações)](https://docs.microsoft.com/powershell/module/azurerm.insights).

## <a name="sign-in-and-use-subscriptions"></a>Iniciar sessão e utilizar as subscrições
Inicie sessão em primeiro lugar, na sua subscrição do Azure.

```PowerShell
Connect-AzAccount
```

Verá um ecrã de início de sessão. Uma vez que iniciar sessão na sua conta, TenantID, e o padrão de ID de subscrição são apresentados. Todos os cmdlets do Azure funciona no contexto de sua assinatura padrão. Para ver a lista de subscrições que tem acesso, utilize o seguinte comando:

```PowerShell
Get-AzSubscription
```

Para alterar o contexto de trabalho para uma subscrição diferente, utilize o seguinte comando:

```PowerShell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Obter o registo de atividades para uma subscrição
Utilize o `Get-AzLog` cmdlet.  Seguem-se alguns exemplos comuns.

Obter entradas de registo a partir desta data e hora para apresentar:

```PowerShell
Get-AzLog -StartTime 2016-03-01T10:30
```

Obter entradas de registo entre um intervalo de data/hora:

```PowerShell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log do grupo de recursos específico:

```PowerShell
Get-AzLog -ResourceGroup 'myrg1'
```

Obter entradas de registo de um fornecedor de recursos específico entre um intervalo de data/hora:

```PowerShell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter todas as entradas de log com um chamador específico:

```PowerShell
Get-AzLog -Caller 'myname@company.com'
```

O seguinte comando obtém os últimas 1 000 eventos de registo de atividades:

```PowerShell
Get-AzLog -MaxEvents 1000
```

`Get-AzLog` oferece suporte a muitos outros parâmetros. Consulte o `Get-AzLog` referência para obter mais informações.

> [!NOTE]
> `Get-AzLog` Fornece apenas 15 dias do histórico. Utilizar o **- MaxEvents** parâmetro permite-lhe consultar os eventos de N últimos, além de 15 dias. Para mais de 15 dias de eventos de acesso, utilize a REST API ou SDK (exemplo de c# com o SDK). Se não incluir **StartTime**, em seguida, o valor predefinido é **EndTime** menos uma hora. Se não incluir **EndTime**, em seguida, o valor predefinido é a hora atual. Todas as horas são em formato UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Obter histórico de alertas
Para ver todos os eventos de alerta, pode consultar os registos do Azure Resource Manager com os exemplos seguintes.

```PowerShell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para ver o histórico para uma regra de alerta específico, pode usar o `Get-AzAlertHistory` cmdlet, passando o ID de recurso de regra de alerta.

```PowerShell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

O `Get-AzAlertHistory` cmdlet oferece suporte a vários parâmetros. Obter mais informações, consulte [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Obter informações sobre regras de alerta
Todos os comandos seguintes agirem sobre um grupo de recursos com o nome "montest".

Ver todas as propriedades da regra de alerta:

```PowerShell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Obter todos os alertas num grupo de recursos:

```PowerShell
Get-AzAlertRule -ResourceGroup montest
```

Obter todas as regras de alerta definido para um recurso de destino. Por exemplo, todas as regras de alerta definido numa VM.

```PowerShell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` oferece suporte a outros parâmetros. Ver [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obter mais informações.

## <a name="create-metric-alerts"></a>Criar alertas de métricas
Pode utilizar o `Add-AlertRule` cmdlet para criar, atualizar ou desativar uma regra de alerta.

Pode criar propriedades de e-mail e webhook usando `New-AzAlertRuleEmail` e `New-AzAlertRuleWebhook`, respectivamente. O cmdlet de regra de alerta, atribuir essas propriedades como ações para o **ações** propriedade de regra de alerta.

A tabela seguinte descreve os parâmetros e valores utilizados para criar um alerta através de uma métrica.

| parâmetro | valor |
| --- | --- |
| Name |simpletestdiskwrite |
| Localização desta regra de alerta |EUA Leste |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName do alerta que é criado |\Disk \PhysicalDisk ( total) / seg. Consulte o `Get-MetricDefinitions` cmdlet sobre como obter os nomes de métricos exatos |
| Operador |GreaterThan |
| Valor de limiar (contagem por segundo para esta métrica) |1 |
| WindowSize (formato hh: mm:) |00:05:00 |
| agregador (estatística da métrica, que usa a contagem média, neste caso) |Média |
| e-mails personalizados (matriz de cadeia de caracteres) |'foo@example.com','bar@example.com' |
| enviar um e-mail para proprietários, contribuidores e leitores |-SendToServiceOwners |

Criar uma ação de E-Mail

```PowerShell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Criar uma ação de Webhook

```PowerShell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra de alerta sobre a métrica de % de CPU numa VM clássica

```PowerShell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Obter a regra de alerta

```PowerShell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

O cmdlet de alerta de adicionar também atualiza a regra se já existe uma regra de alerta para as propriedades de determinada. Para desativar uma regra de alerta, inclua o parâmetro **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obter uma lista de métricas disponíveis para alertas
Pode utilizar o `Get-AzMetricDefinition` cmdlet para ver a lista de todas as métricas para um recurso específico.

```PowerShell
Get-AzMetricDefinition -ResourceId <resource_id>
```

O exemplo a seguir gera uma tabela com a métrica de nome e a unidade para ele.

```PowerShell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Uma lista completa das opções disponíveis para `Get-AzMetricDefinition` está disponível em [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Criar e gerir alertas de registo de atividades
Pode utilizar o `Set-AzActivityLogAlert` cmdlet para definir um alerta de registo de atividades. Um alerta de registo de atividades requer que primeiro define suas condições como um dicionário de condições, em seguida, criar um alerta que utiliza essas condições.

```PowerShell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

As propriedades de webhook adicionais são opcionais. Pode obter novamente o conteúdo de um alerta de registo de atividade usando `Get-AzActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Criar e gerir definições de dimensionamento automático
Um recurso (uma aplicação Web, VM, serviço Cloud ou o conjunto de dimensionamento de máquinas virtuais) pode ter apenas uma definição de dimensionamento automático configurada para o mesmo.
No entanto, cada definição de dimensionamento automático pode ter vários perfis. Por exemplo, um para um perfil de dimensionamento baseados no desempenho e um segundo para um perfil com base na agenda. Cada perfil pode ter várias regras configuradas no mesmo. Para obter mais informações sobre o dimensionamento automático, consulte [como Dimensionar automaticamente um aplicativo](../../cloud-services/cloud-services-how-to-scale-portal.md).

Eis os passos a utilizar:

1. Crie Regra (s).
2. Crie perfis mapear as regras que criou anteriormente para os perfis.
3. Opcional: Configurar propriedades de e-mail e webhook para criar notificações do dimensionamento automático.
4. Crie uma definição de dimensionamento automático com um nome do recurso de destino, mapeando os perfis e as notificações que criou nos passos anteriores.

Os exemplos seguintes mostram como pode criar uma definição de dimensionamento automático para um conjunto de dimensionamento de Máquina Virtual para um sistema de operativo do Windows com base com a métrica de utilização da CPU.

Primeiro, crie uma regra para aumentar horizontalmente, com um aumento de contagem de instância.

```PowerShell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Em seguida, crie uma regra para reduzir horizontalmente, com uma diminuição de contagem de instância.

```PowerShell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Em seguida, crie um perfil para as regras.

```PowerShell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crie uma propriedade do webhook.

```PowerShell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crie a propriedade de notificação para a definição de dimensionamento automático, incluindo o e-mail e o webhook que criou anteriormente.

```PowerShell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Por fim, crie a definição de dimensionamento automático para adicionar o perfil que criou anteriormente. 

```PowerShell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obter mais informações sobre a gestão de definições de dimensionamento automático, consulte [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Histórico de dimensionamento automático
O exemplo seguinte mostra como pode ver o dimensionamento automático recente e eventos de alerta. Utilize a pesquisa de registos de atividade para ver o histórico de dimensionamento automático.

```PowerShell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Pode utilizar o `Get-AzAutoScaleHistory` cmdlet para obter o histórico de dimensionamento automático.

```PowerShell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obter mais informações, consulte [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Ver detalhes de uma definição de dimensionamento automático
Pode utilizar o `Get-Autoscalesetting` cmdlet para obter mais informações sobre a definição de dimensionamento automático.

O exemplo seguinte mostra detalhes sobre todas as definições de dimensionamento automático no grupo de recursos "myrg1'.

```PowerShell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

O exemplo seguinte mostra detalhes sobre todas as definições de dimensionamento automático no grupo de recursos "myrg1' e especificamente a definição de dimensionamento automático com o nome 'MyScaleVMSSSetting'.

```PowerShell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Remover uma definição de dimensionamento automático
Pode utilizar o `Remove-Autoscalesetting` cmdlet para eliminar uma definição de dimensionamento automático.

```PowerShell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Gerir perfis de registo para o registo de atividades
Pode criar uma *perfil de registo* e exportar dados a partir do seu registo de atividades para uma conta de armazenamento e podem configurar a retenção de dados para o mesmo. Opcionalmente, também pode transmitir os dados ao seu Hub de eventos. Esta funcionalidade está atualmente em pré-visualização e só pode criar um perfil de registo por subscrição. Pode utilizar os seguintes cmdlets com a sua subscrição atual para criar e gerir perfis de registo. Também pode escolher uma subscrição específica. Embora o PowerShell é predefinido para a subscrição atual, pode sempre alterar esses utilizando `Set-AzContext`. Pode configurar o registo de atividades para encaminhar os dados para qualquer conta de armazenamento ou Hub de eventos nessa subscrição. Dados são escritos como ficheiros de blob no formato JSON.

### <a name="get-a-log-profile"></a>Obter um perfil de registo
Para obter os perfis de registo existente, utilize o `Get-AzLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Adicionar um perfil de registo sem a retenção de dados
```PowerShell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de registo
```PowerShell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adicionar um perfil de registo com retenção de dados
Pode especificar a **- RetentionInDays** propriedade com o número de dias, como um número inteiro, onde os dados são retidos.

```PowerShell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Adicionar perfil de registo com retenção e de EventHub
Além do encaminhamento seus dados para a conta de armazenamento, pode também transmitir para um Hub de eventos. Nesta versão de pré-visualização, a configuração de conta de armazenamento é obrigatória, mas a configuração do Hub de eventos é opcional.

```PowerShell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurar os registos de diagnóstico
Muitos serviços do Azure fornecem registos adicionais e a telemetria que pode fazer um ou mais dos seguintes procedimentos: 
 - ser configurado para guardar os dados na sua conta de armazenamento do Azure
 - enviado para os Hubs de eventos
 - enviado para uma área de trabalho do Log Analytics. 

A operação só pode ser executada num nível de recursos. O hub de evento ou a conta de armazenamento deve estar presente na mesma região que o recurso de destino em que a definição de diagnóstico está configurada.

### <a name="get-diagnostic-setting"></a>Obter definição de diagnóstico
```PowerShell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Desativar a definição de diagnóstico

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Ativar a definição de diagnóstico sem retenção

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Ativar a definição de diagnóstico com retenção

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Ativar a definição de diagnóstico com o período de retenção para uma categoria de registo específicos

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Ativar a definição de diagnóstico para os Hubs de eventos

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Ativar a definição de diagnóstico para o Log Analytics

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Tenha em atenção que a propriedade WorkspaceId leva a *ID de recurso* da área de trabalho. Pode obter o ID de recurso da sua área de trabalho do Log Analytics com o seguinte comando:

```PowerShell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Estes comandos podem ser combinados para enviar dados para vários destinos.

