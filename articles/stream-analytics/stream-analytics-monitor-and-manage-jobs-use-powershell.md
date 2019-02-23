---
title: Monitorizar e gerir tarefas de Azure Stream Analytics com o PowerShell
description: Este artigo descreve como utilizar o Azure PowerShell e cmdlets para monitorizar e gerir tarefas do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 2755064190e787baa31ea0da7c375365fa6c29b5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733338"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorizar e gerir tarefas do Stream Analytics com cmdlets do Azure PowerShell
Saiba como monitorizar e gerir os recursos de Stream Analytics com cmdlets do Azure PowerShell e scripts do powershell que são executadas tarefas básicas do Stream Analytics.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Pré-requisitos para executar os cmdlets do Azure PowerShell para o Stream Analytics
* Crie um grupo de recursos do Azure na sua subscrição. Segue-se um exemplo de script do Azure PowerShell. Para obter informações do Azure PowerShell, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview);  

O Azure PowerShell 0.9.8:  

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

O Azure PowerShell 1.0:

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
> Criado por meio de programação de tarefas do Stream Analytics não tem monitorização ativada por predefinição.  Pode ativar manualmente a monitorização no Portal do Azure ao navegar para a página de Monitor da tarefa e clicar no botão Ativar ou pode fazer isso programaticamente ao seguir os passos localizados em [Azure Stream Analytics - monitorizar tarefas do Stream Analytics Por meio de programação](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Cmdlets do Azure PowerShell para o Stream Analytics
Os seguintes cmdlets PowerShell do Azure pode ser utilizados para monitorizar e gerir tarefas do Azure Stream Analytics. Observe que o Azure PowerShell tem versões diferentes. 
**Os exemplos apresentados o primeiro comando é para o Azure PowerShell 0.9.8, o segundo comando é para o Azure PowerShell 1.0.** Os comandos do Azure PowerShell 1.0 sempre terá "AzureRM" no comando.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Apresenta uma lista de todas as tarefas do Stream Analytics definidas na subscrição do Azure ou do grupo de recursos especificado ou obtém as informações da tarefa sobre uma tarefa específica num grupo de recursos.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

O Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Este comando do PowerShell devolve informações sobre todas as tarefas do Stream Analytics na subscrição do Azure.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

O Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Este comando do PowerShell devolve informações sobre todas as tarefas do Stream Analytics, no grupo de recursos StreamAnalytics-predefinido-Central-US.

**Exemplo 3**

O Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

O Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Este comando do PowerShell devolve informações sobre a tarefa de Stream Analytics StreamingJob no grupo de recursos StreamAnalytics-predefinido-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Apresenta uma lista de todas as entradas que são definidas numa tarefa do Stream Analytics especificada ou obtém informações sobre uma entrada específica.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

O Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Este comando do PowerShell devolve informações sobre todas as entradas definidas na tarefa StreamingJob.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

O Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

Este comando do PowerShell devolve informações sobre a entrada com o nome EntryStream definido na tarefa StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Apresenta uma lista de todas as saídas que estão definidas numa tarefa do Stream Analytics especificada ou obtém informações sobre uma saída específica.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

O Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Este comando do PowerShell devolve informações sobre as saídas definidas na tarefa StreamingJob.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

O Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

Este comando do PowerShell devolve informações sobre os nomeados de saída definida no trabalho StreamingJob de saída.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Obtém informações sobre a quota de transmissão em fluxo unidades numa determinada região.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota –Location "Central US" 
```

O Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota –Location "Central US" 
```

Este comando do PowerShell devolve informações sobre a quota e utilização de unidades de transmissão em fluxo na região E.U.A. Central.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Obtém informações sobre uma transformação específica definida numa tarefa do Stream Analytics.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

O Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

Este comando do PowerShell devolve informações sobre a transformação chamada StreamingJob na tarefa StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Cria uma nova entrada dentro de uma tarefa do Stream Analytics ou atualiza uma existente entrada especificada.

O nome da entrada pode ser especificado no ficheiro. JSON ou na linha de comandos. Se ambas são especificadas, o nome na linha de comando tem de ser o mesmo que no ficheiro.

Se especificar uma entrada que já existe e não especificar o parâmetro – Force, o cmdlet irá perguntar se deve ou não substitua a entrada existente.

Se especificar – forçar o parâmetro e especificar um existente introduzir o nome, a entrada será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura de ficheiros JSON e o conteúdo, consulte o [entrada de criar (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-input] secção o [referência de API do REST de gestão do Stream Analytics Biblioteca][stream.analytics.rest.api.reference].

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

O Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

Este comando do PowerShell cria uma nova entrada do ficheiro Input. Se uma entrada existente com o nome especificado no ficheiro de definição de entrada já está definida, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

O Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

Este comando do PowerShell cria uma nova entrada no trabalho chamado EntryStream. Se uma entrada existente com este nome já está definida, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 3**

O Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

O Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

Este comando do PowerShell substitui a definição de origem de entrada existente chamada EntryStream com a definição do ficheiro.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Cria uma nova tarefa de Stream Analytics no Microsoft Azure ou atualiza a definição de uma tarefa especificada existente.

O nome da tarefa pode ser especificado no ficheiro. JSON ou na linha de comandos. Se ambas são especificadas, o nome na linha de comando tem de ser o mesmo que no ficheiro.

Se especificar um nome de tarefa já existe e não especificar o parâmetro – Force, o cmdlet irá perguntar se deve ou não substitua a tarefa existente.

Se especificar – forçar o parâmetro e especificar um nome de tarefa existente, a definição de tarefa irá ser substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura de ficheiros JSON e o conteúdo, consulte a [criar tarefa do Stream Analytics] [ msdn-rest-api-create-stream-analytics-job] seção o [Stream Analytics Management REST API biblioteca de referência] [stream.analytics.rest.api.reference].

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

O Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

Este comando do PowerShell cria uma nova tarefa a partir da definição no jobdefinition. JSON. Se uma tarefa existente com o nome especificado no ficheiro de definição de tarefa já está definida, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

O Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

Este comando do PowerShell substitui a definição de tarefa para StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Cria uma nova saída dentro de uma tarefa do Stream Analytics ou atualiza uma saída existente.  

O nome da saída pode ser especificado no ficheiro. JSON ou na linha de comandos. Se ambas são especificadas, o nome na linha de comando tem de ser o mesmo que no ficheiro.

Se especificar uma saída que já existe e não especificar o parâmetro – Force, o cmdlet irá perguntar se deve ou não substituir a saída existente.

Se especificar – forçar o parâmetro e especificar o nome de saída de um existente, a saída irá ser substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura de ficheiros JSON e o conteúdo, consulte a [criar saída (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-output] seção o [referência de API do REST de gestão do Stream Analytics Biblioteca][stream.analytics.rest.api.reference].

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

O Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

Este comando do PowerShell cria uma nova saída chamada de "saída" na tarefa StreamingJob. Se uma saída existente com este nome já está definida, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

O Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

Este comando do PowerShell substitui a definição de "saída" na tarefa StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Cria uma nova transformação dentro de uma tarefa do Stream Analytics ou atualiza a transformação existente.

O nome da transformação pode ser especificado no ficheiro. JSON ou na linha de comandos. Se ambas são especificadas, o nome na linha de comando tem de ser o mesmo que no ficheiro.

Se especificar uma transformação que já existe e não especificar o parâmetro – Force, o cmdlet irá perguntar se deve ou não substitua a transformação existente.

Se especificar – forçar o parâmetro e especificar um nome de transformação existente, a transformação irá ser substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura de ficheiros JSON e o conteúdo, consulte a [transformação de criar (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-transformation] secção do [API de REST de gestão do Stream Analytics Biblioteca de referência][stream.analytics.rest.api.reference].

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

O Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

Este comando do PowerShell cria uma nova transformação chamada StreamingJobTransform na tarefa StreamingJob. Se uma transformação existente já está definida com este nome, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

O Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

 Este comando do PowerShell substitui a definição de StreamingJobTransform na tarefa StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Exclui de forma assíncrona uma introdução específica de uma tarefa do Stream Analytics no Microsoft Azure.  
Se especificar o parâmetro – Force, será eliminada a entrada sem confirmação.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

O Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

Este comando do PowerShell remove a entrada EventStream na tarefa StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Exclui uma tarefa específica do Stream Analytics no Microsoft Azure de forma assíncrona.  
Se especificar o parâmetro – Force, a tarefa será eliminada sem confirmação.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

O Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Este comando do PowerShell remove a tarefa StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Exclui de forma assíncrona uma saída específica de uma tarefa do Stream Analytics no Microsoft Azure.  
Se especificar o parâmetro – Force, a saída será eliminada sem confirmação.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

O Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Este remove de comando do PowerShell a saída de saída da tarefa StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Modo assíncrono, implementa e inicia uma tarefa de Stream Analytics no Microsoft Azure.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

O Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Este comando do PowerShell inicia a tarefa de StreamingJob com uma hora de início da saída personalizado definido como 12 de Dezembro de 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Modo assíncrono interrompe uma tarefa do Stream Analytics sejam executadas no Microsoft Azure e anula a alocação recursos que estavam que estavam sendo usados. A definição de tarefa e os metadados permanecerão disponíveis na sua subscrição através do portal do Azure e a gestão de APIs, que a tarefa pode ser editada e reiniciada. Não são cobradas para uma tarefa no estado de paragem.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

O Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Este comando do PowerShell para a tarefa StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testar a capacidade do Stream Analytics para ligar a uma entrada especificada.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

O Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

Este comando do PowerShell testa o estado da ligação da entrada EntryStream em StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testar a capacidade do Stream Analytics para ligar a uma saída especificada.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

O Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Este testes de comando do PowerShell o estado da ligação da saída de saída no StreamingJob.  

## <a name="get-support"></a>Obter suporte
Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
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

