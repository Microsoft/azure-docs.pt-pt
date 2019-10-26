---
title: Monitorar e gerenciar trabalhos de Azure Stream Analytics com o PowerShell
description: Este artigo descreve como usar Azure PowerShell e cmdlets para monitorar e gerenciar Azure Stream Analytics trabalhos.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 57dee438dca81d46d2bdfda0c7f255f32f203d60
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925132"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorar e gerenciar trabalhos de Stream Analytics com cmdlets Azure PowerShell
Saiba como monitorar e gerenciar Stream Analytics recursos com cmdlets Azure PowerShell e scripts do PowerShell que executam tarefas básicas de Stream Analytics.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Pré-requisitos para a execução de cmdlets Azure PowerShell para Stream Analytics
* Crie um grupo de recursos do Azure em sua assinatura. Veja a seguir um exemplo de script Azure PowerShell. Para Azure PowerShell informações, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview);  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1,0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription –SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Stream Analytics trabalhos criados programaticamente não têm o monitoramento habilitado por padrão.  Você pode habilitar manualmente o monitoramento no portal do Azure navegando até a página do monitor do trabalho e clicando no botão habilitar ou pode fazer isso programaticamente, seguindo as etapas localizadas em [trabalhos de Stream Analytics do monitor de Azure Stream Analytics Programaticamente](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell cmdlets para Stream Analytics
Os cmdlets Azure PowerShell a seguir podem ser usados para monitorar e gerenciar Azure Stream Analytics trabalhos. Observe que Azure PowerShell tem versões diferentes. 
**Nos exemplos listados, o primeiro comando é para Azure PowerShell 0.9.8, o segundo comando é para Azure PowerShell 1,0.** Os comandos Azure PowerShell 1,0 sempre terão "AZ" no comando.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Lista todos os trabalhos de Stream Analytics definidos na assinatura do Azure ou no grupo de recursos especificado, ou obtém informações de trabalho sobre um trabalho específico dentro de um grupo de recursos.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob
```

Esse comando do PowerShell retorna informações sobre todos os trabalhos de Stream Analytics na assinatura do Azure.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Esse comando do PowerShell retorna informações sobre todos os trabalhos de Stream Analytics no grupo de recursos StreamAnalytics-default-central-US.

**Exemplo 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Esse comando do PowerShell retorna informações sobre o trabalho de Stream Analytics StreamingJob no grupo de recursos StreamAnalytics-default-central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Lista todas as entradas que são definidas em um trabalho de Stream Analytics especificado ou obtém informações sobre uma entrada específica.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Esse comando do PowerShell retorna informações sobre todas as entradas definidas no trabalho StreamingJob.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

Esse comando do PowerShell retorna informações sobre a entrada chamada EntryStream definida no trabalho StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Lista todas as saídas que são definidas em um trabalho de Stream Analytics especificado ou obtém informações sobre uma saída específica.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Esse comando do PowerShell retorna informações sobre as saídas definidas no trabalho StreamingJob.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

Esse comando do PowerShell retorna informações sobre a saída chamada output definida no trabalho StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Obtém informações sobre a cota de unidades de streaming em uma região especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota –Location "Central US" 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsQuota –Location "Central US" 
```

Esse comando do PowerShell retorna informações sobre a cota e o uso de unidades de streaming na região de EUA Central.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Obtém informações sobre uma transformação específica definida em um trabalho de Stream Analytics.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

Esse comando do PowerShell retorna informações sobre a transformação chamada StreamingJob no trabalho StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Cria uma nova entrada dentro de um trabalho Stream Analytics ou atualiza uma entrada especificada existente.

O nome da entrada pode ser especificado no arquivo. JSON ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deverá ser o mesmo no arquivo.

Se você especificar uma entrada que já existe e não especificar o parâmetro – Force, o cmdlet perguntará se deseja ou não substituir a entrada existente.

Se você especificar o parâmetro – Force e especificar um nome de entrada existente, a entrada será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura e o conteúdo do arquivo JSON, consulte a seção [criar entrada (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] da [biblioteca de referência da API REST de gerenciamento de Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

Esse comando do PowerShell cria uma nova entrada do arquivo Input. JSON. Se uma entrada existente com o nome especificado no arquivo de definição de entrada já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

Esse comando do PowerShell cria uma nova entrada no trabalho chamada EntryStream. Se uma entrada existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

Esse comando do PowerShell substitui a definição da fonte de entrada existente chamada EntryStream com a definição do arquivo.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Cria um novo trabalho de Stream Analytics no Microsoft Azure ou atualiza a definição de um trabalho especificado existente.

O nome do trabalho pode ser especificado no arquivo. JSON ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deverá ser o mesmo no arquivo.

Se você especificar um nome de trabalho que já existe e não especificar o parâmetro – Force, o cmdlet perguntará se deseja ou não substituir o trabalho existente.

Se você especificar o parâmetro – Force e especificar um nome de trabalho existente, a definição de trabalho será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura e o conteúdo do arquivo JSON, consulte a seção [criar Stream Analytics trabalho][msdn-rest-api-create-stream-analytics-job] da [biblioteca de referência da API REST de gerenciamento de Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

Esse comando do PowerShell cria um novo trabalho a partir da definição em JobDefinition. JSON. Se um trabalho existente com o nome especificado no arquivo de definição de trabalho já estiver definido, o cmdlet perguntará se deseja ou não substituí-lo.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

Esse comando do PowerShell substitui a definição de trabalho para StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Cria uma nova saída dentro de um trabalho Stream Analytics ou atualiza uma saída existente.  

O nome da saída pode ser especificado no arquivo. JSON ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deverá ser o mesmo no arquivo.

Se você especificar uma saída que já existe e não especificar o parâmetro – Force, o cmdlet perguntará se deseja ou não substituir a saída existente.

Se você especificar o parâmetro – Force e especificar um nome de saída existente, a saída será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura e o conteúdo do arquivo JSON, consulte a seção [criar saída (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] da [biblioteca de referência da API REST de gerenciamento de Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

Esse comando do PowerShell cria uma nova saída chamada "output" no trabalho StreamingJob. Se uma saída existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

Esse comando do PowerShell substitui a definição de "output" no trabalho StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Cria uma nova transformação dentro de um trabalho Stream Analytics ou atualiza a transformação existente.

O nome da transformação pode ser especificado no arquivo. JSON ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deverá ser o mesmo no arquivo.

Se você especificar uma transformação que já existe e não especificar o parâmetro – Force, o cmdlet perguntará se deseja ou não substituir a transformação existente.

Se você especificar o parâmetro – Force e especificar um nome de transformação existente, a transformação será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura e o conteúdo do arquivo JSON, consulte a seção [criar transformação (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] da [biblioteca de referência da API REST de gerenciamento de Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

Esse comando do PowerShell cria uma nova transformação chamada StreamingJobTransform no trabalho StreamingJob. Se uma transformação existente já estiver definida com esse nome, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

 Esse comando do PowerShell substitui a definição de StreamingJobTransform no trabalho StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Exclui de forma assíncrona uma entrada específica de um trabalho de Stream Analytics no Microsoft Azure.  
Se você especificar o parâmetro – Force, a entrada será excluída sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

Esse comando do PowerShell remove a entrada EventStream no StreamingJob do trabalho.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Exclui de forma assíncrona um trabalho de Stream Analytics específico no Microsoft Azure.  
Se você especificar o parâmetro – Force, o trabalho será excluído sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Esse comando do PowerShell remove o trabalho StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Exclui de forma assíncrona uma saída específica de um trabalho de Stream Analytics no Microsoft Azure.  
Se você especificar o parâmetro – Force, a saída será excluída sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Esse comando do PowerShell remove a saída de saída no trabalho StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Implanta de forma assíncrona e inicia um trabalho de Stream Analytics no Microsoft Azure.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1,0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Esse comando do PowerShell inicia o trabalho StreamingJob com uma hora de início de saída personalizada definida para 12 de dezembro de 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Interrompe de forma assíncrona um trabalho de Stream Analytics de ser executado em Microsoft Azure e desaloca recursos que estavam sendo usados. A definição de trabalho e os metadados permanecerão disponíveis em sua assinatura por meio de APIs de portal do Azure e gerenciamento, de modo que o trabalho possa ser editado e reiniciado. Você não será cobrado por um trabalho no estado parado.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Esse comando do PowerShell para o trabalho StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testa a capacidade de Stream Analytics se conectar a uma entrada especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

Esse comando do PowerShell testa o status da conexão da entrada EntryStream em StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testa a capacidade de Stream Analytics se conectar a uma saída especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Esse comando do PowerShell testa o status da conexão da saída de saída em StreamingJob.  

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente nosso [Fórum de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

