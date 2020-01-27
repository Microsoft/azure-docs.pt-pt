---
title: Exemplos de início rápido do Azure Monitor PowerShell
description: Use o PowerShell para acessar recursos do Azure Monitor, como dimensionamento automático, alertas, WebHooks e pesquisar logs de atividade.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 2/14/2018
ms.openlocfilehash: d1aa4b4e2d72f10ca73616bc7e69b0d02f13a501
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551845"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Exemplos de início rápido do Azure Monitor PowerShell
Este artigo mostra exemplos de comandos do PowerShell para ajudá-lo a acessar recursos do Azure Monitor.

> [!NOTE]
> Azure Monitor é o novo nome para o que foi chamado "Azure insights" até 25 de setembro de 2016. No entanto, os namespaces e, portanto, os comandos a seguir ainda contêm a palavra "insights".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Configurar o PowerShell
Se você ainda não fez isso, configure o PowerShell para ser executado no seu computador. Para obter mais informações, consulte [como instalar e configurar o PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Exemplos neste artigo
Os exemplos no artigo ilustram como você pode usar Azure Monitor cmdlets. Você também pode examinar a lista completa de cmdlets Azure Monitor PowerShell em [cmdlets do Azure monitor (insights)](https://docs.microsoft.com/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Entrar e usar assinaturas
Primeiro, faça logon na sua assinatura do Azure.

```powershell
Connect-AzAccount
```

Você verá uma tela de entrada. Quando você entrar na sua conta, Tenantid e a ID de assinatura padrão serão exibidas. Todos os cmdlets do Azure funcionam no contexto da sua assinatura padrão. Para exibir a lista de assinaturas às quais você tem acesso, use o seguinte comando:

```powershell
Get-AzSubscription
```

Para ver seu contexto de trabalho (em que assinatura seus comandos são executados), use o seguinte comando:

```powershell
Get-AzContext
```
Para alterar seu contexto de trabalho para uma assinatura diferente, use o seguinte comando:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Recuperar o log de atividades de uma assinatura
Use o cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) .  Veja a seguir alguns exemplos comuns. O log de atividades contém os últimos 90 dias de operações. O uso de datas antes dessa hora resulta em uma mensagem de erro.  

Veja qual é a data/hora atual para verificar quais horários usar nos comandos abaixo:
```powershell
Get-Date
```

Obter entradas de log desta data/hora para apresentar:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Obter entradas de log entre um intervalo de data/hora:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log de um grupo de recursos específico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obter entradas de log de um provedor de recursos específico entre um intervalo de data/hora:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter todas as entradas de log com um chamador específico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

O comando a seguir recupera os últimos 1000 eventos do log de atividades:

```powershell
Get-AzLog -MaxRecord 10
```

o `Get-AzLog` dá suporte a muitos outros parâmetros. Consulte a referência de `Get-AzLog` para obter mais informações.

> [!NOTE]
> `Get-AzLog` fornece apenas 15 dias de histórico. O uso do parâmetro **-MaxRecords** permite consultar os últimos N eventos, além de 15 dias. Para acessar eventos com mais de 15 dias, use a API REST ou oC# SDK (exemplo usando o SDK). Se você não incluir **StartTime**, o valor padrão será **EndTime** menos uma hora. Se você não incluir **EndTime**, o valor padrão será current time. Todas as horas estão em UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Recuperar histórico de alertas
Para exibir todos os eventos de alerta, você pode consultar os logs de Azure Resource Manager usando os exemplos a seguir.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para exibir o histórico de uma regra de alerta específica, você pode usar o cmdlet `Get-AzAlertHistory`, passando a ID de recurso da regra de alerta.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

O cmdlet `Get-AzAlertHistory` dá suporte a vários parâmetros. Para obter mais informações, consulte [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Recuperar informações sobre regras de alerta
Todos os comandos a seguir agem em um grupo de recursos chamado "montest".

Exiba todas as propriedades da regra de alerta:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperar todos os alertas em um grupo de recursos:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Recupere todas as regras de alerta definidas para um recurso de destino. Por exemplo, todas as regras de alerta definidas em uma VM.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` dá suporte a outros parâmetros. Consulte [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obter mais informações.

## <a name="create-metric-alerts"></a>Criar alertas de métricas
Você pode usar o cmdlet `Add-AlertRule` para criar, atualizar ou desabilitar uma regra de alerta.

Você pode criar propriedades de email e webhook usando `New-AzAlertRuleEmail` e `New-AzAlertRuleWebhook`, respectivamente. No cmdlet de regra de alerta, atribua essas propriedades como ações à propriedade **Actions** da regra de alerta.

A tabela a seguir descreve os parâmetros e valores usados para criar um alerta usando uma métrica.

| Meter | valor |
| --- | --- |
| Nome |simpletestdiskwrite |
| Local desta regra de alerta |E.U.A. Leste |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| Metricname do alerta que é criado |\PhysicalDisk (_ total) \Bytes gravações de/s. Consulte o cmdlet `Get-MetricDefinitions` sobre como recuperar os nomes de métricas exatas |
| operador |GreaterThan |
| Valor de limite (contagem/s para esta métrica) |1 |
| WindowSize (formato hh: mm: SS) |00:05:00 |
| agregador (estatística da métrica, que usa a contagem média, neste caso) |Média |
| emails personalizados (matriz de cadeia de caracteres) |' foo@example.com ', ' bar@example.com ' |
| Enviar email para proprietários, colaboradores e leitores |-SendToServiceOwners |

Criar uma ação de email

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Criar uma ação de webhook

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra de alerta na métrica% de CPU em uma VM clássica

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperar a regra de alerta

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

O cmdlet adicionar alerta também atualizará a regra se já existir uma regra de alerta para as propriedades especificadas. Para desabilitar uma regra de alerta, inclua o parâmetro **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obter uma lista de métricas disponíveis para alertas
Você pode usar o cmdlet `Get-AzMetricDefinition` para exibir a lista de todas as métricas de um recurso específico.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

O exemplo a seguir gera uma tabela com o nome da métrica e a unidade para ela.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Uma lista completa das opções disponíveis para `Get-AzMetricDefinition` está disponível em [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Criar e gerenciar alertas do log de atividades
Você pode usar o cmdlet `Set-AzActivityLogAlert` para definir um alerta do log de atividades. Um alerta do log de atividades exige que você primeiro defina suas condições como um dicionário de condições e, em seguida, crie um alerta que use essas condições.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

As propriedades de webhook adicionais são opcionais. Você pode obter novamente o conteúdo de um alerta do log de atividades usando `Get-AzActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Criar e gerenciar configurações de autoescala
Um recurso (um aplicativo Web, uma VM, um serviço de nuvem ou um conjunto de dimensionamento de máquinas virtuais) pode ter apenas uma configuração de dimensionamento automático configurada para ele.
No entanto, cada configuração de autoescala pode ter vários perfis. Por exemplo, um para um perfil de escala com base no desempenho e um segundo para um perfil baseado em agendamento. Cada perfil pode ter várias regras configuradas nele. Para obter mais informações sobre o dimensionamento automático, consulte [como fazer o dimensionamento automático de um aplicativo](../../cloud-services/cloud-services-how-to-scale-portal.md).

Estas são as etapas a serem usadas:

1. Criar regra (s).
2. Criar perfil (s) mapeando as regras que você criou anteriormente para os perfis.
3. Opcional: Crie notificações para dimensionamento automático configurando as propriedades de webhook e email.
4. Crie uma configuração de dimensionamento automático com um nome no recurso de destino mapeando os perfis e as notificações que você criou nas etapas anteriores.

Os exemplos a seguir mostram como você pode criar uma configuração de dimensionamento automático para um conjunto de dimensionamento de máquinas virtuais para um sistema operacional Windows baseado usando a métrica de utilização da CPU.

Primeiro, crie uma regra para escalar horizontalmente, com um aumento de contagem de instâncias.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Em seguida, crie uma regra para reduzir horizontalmente, com uma diminuição de contagem de instâncias.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Em seguida, crie um perfil para as regras.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crie uma propriedade de webhook.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crie a propriedade de notificação para a configuração de dimensionamento automático, incluindo email e o webhook que você criou anteriormente.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Por fim, crie a configuração de dimensionamento automático para adicionar o perfil que você criou anteriormente. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obter mais informações sobre o gerenciamento de configurações de autoescala, consulte [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Histórico de dimensionamento automático
O exemplo a seguir mostra como você pode exibir os eventos de autoescala e de alerta recentes. Use a pesquisa de log de atividades para exibir o histórico de dimensionamento automático.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Você pode usar o cmdlet `Get-AzAutoScaleHistory` para recuperar o histórico de dimensionamento automático.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obter mais informações, consulte [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Exibir detalhes de uma configuração de dimensionamento automático
Você pode usar o cmdlet `Get-Autoscalesetting` para recuperar mais informações sobre a configuração de dimensionamento automático.

O exemplo a seguir mostra detalhes sobre todas as configurações de autoescala no grupo de recursos ' myrg1 '.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

O exemplo a seguir mostra detalhes sobre todas as configurações de autoescala no grupo de recursos ' myrg1 ' e especificamente a configuração de autoescala chamada ' MyScaleVMSSSetting '.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Remover uma configuração de dimensionamento automático
Você pode usar o cmdlet `Remove-Autoscalesetting` para excluir uma configuração de dimensionamento automático.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Gerenciar perfis de log para o log de atividades
Você pode criar um *perfil de log* e exportar dados de seu log de atividades para uma conta de armazenamento e pode configurar a retenção de dados para ele. Opcionalmente, você também pode transmitir os dados para o Hub de eventos. Este recurso está em visualização no momento e você só pode criar um perfil de log por assinatura. Você pode usar os seguintes cmdlets com sua assinatura atual para criar e gerenciar perfis de log. Você também pode escolher uma assinatura específica. Embora o PowerShell seja padronizado para a assinatura atual, você sempre pode alterar isso usando `Set-AzContext`. Você pode configurar o log de atividades para rotear dados para qualquer conta de armazenamento ou Hub de eventos dentro dessa assinatura. Os dados são gravados como arquivos de blob no formato JSON.

### <a name="get-a-log-profile"></a>Obter um perfil de log
Para buscar os perfis de log existentes, use o cmdlet `Get-AzLogProfile`.

### <a name="add-a-log-profile-without-data-retention"></a>Adicionar um perfil de log sem retenção de dados
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de log
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adicionar um perfil de log com retenção de dados
Você pode especificar a propriedade **-RetentionInDays** com o número de dias, como um inteiro positivo, onde os dados são retidos.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Adicionar perfil de log com retenção e EventHub
Além de rotear seus dados para a conta de armazenamento, você também pode transmiti-los para um hub de eventos. Nesta versão prévia, a configuração da conta de armazenamento é obrigatória, mas a configuração do hub de eventos é opcional.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurar logs de diagnóstico
Muitos serviços do Azure fornecem logs e telemetria adicionais que podem fazer um ou mais dos seguintes: 
 - ser configurado para salvar dados em sua conta de armazenamento do Azure
 - enviado aos hubs de eventos
 - enviado para um espaço de trabalho Log Analytics. 

A operação só pode ser executada em um nível de recurso. A conta de armazenamento ou o Hub de eventos deve estar presente na mesma região que o recurso de destino em que a configuração de diagnóstico está configurada.

### <a name="get-diagnostic-setting"></a>Obter configuração de diagnóstico
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Desabilitar configuração de diagnóstico

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Habilitar configuração de diagnóstico sem retenção

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Habilitar configuração de diagnóstico com retenção

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitar a configuração de diagnóstico com retenção para uma categoria de log específica

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitar configuração de diagnóstico para hubs de eventos

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Habilitar configuração de diagnóstico para Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Observe que a propriedade Workspaceid usa a *ID do recurso* do espaço de trabalho. Você pode obter a ID de recurso do seu espaço de trabalho Log Analytics usando o seguinte comando:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Esses comandos podem ser combinados para enviar dados a vários destinos.

