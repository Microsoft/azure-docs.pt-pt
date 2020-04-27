---
title: Gerir a Análise do Azure Data Lake com o Azure PowerShell
description: Este artigo descreve como usar o Azure PowerShell para gerir contas data lake analytics, fontes de dados, utilizadores, & empregos.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 4273828c9c2bdb75fcbc1de45da55c5a03dd615f
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "66156418"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gerir a Análise do Azure Data Lake com o Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerir contas de Azure Data Lake Analytics, fontes de dados, utilizadores e empregos utilizando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para utilizar o PowerShell com data lake analytics, recolher as seguintes informações: 

* **ID de subscrição**: O ID da subscrição Azure que contém a sua conta Data Lake Analytics.
* **Grupo de recursos**: O nome do grupo de recursos Azure que contém a sua conta Data Lake Analytics.
* **Data Lake Analytics nome**da conta : O nome da sua conta Data Lake Analytics.
* Nome da **conta 'Data Lake Store' por defeito**: Cada conta data Lake Analytics tem uma conta padrão data Lake Store.
* **Localização**: A localização da sua conta Data Lake Analytics, como "East US 2" ou outros locais suportados.

Os fragmentos de PowerShell neste tutorial utilizam estas variáveis para armazenar estas informações

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

### <a name="log-in-using-interactive-user-authentication"></a>Iniciar sessão utilizando a autenticação interativa do utilizador

Iniciar sessão utilizando um ID de subscrição ou por nome de subscrição

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>Salvar o contexto de autenticação

O `Connect-AzAccount` cmdlet sempre pede credenciais. Pode evitar ser solicitado utilizando os seguintes cmdlets:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Iniciar sessão utilizando uma identidade principal de serviço (SPI)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Gerir contas


### <a name="list-accounts"></a>Contas de lista

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Criar uma conta

Cada conta de Data Lake Analytics requer uma conta de Data Lake Store predefinida que utiliza para armazenar registos. Pode reutilizar uma conta existente ou criar uma conta. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Obter informações sobre contas

Obtenha detalhes sobre uma conta.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Verifique se existe uma conta

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Gerir origens de dados
O Azure Data Lake Analytics suporta atualmente as seguintes fontes de dados:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento Azure](../storage/common/storage-introduction.md)

Todas as contas data Lake Analytics têm uma conta padrão data lake store. A conta padrão data lake store é usada para armazenar metadados de trabalho e registos de auditoria de emprego. 

### <a name="find-the-default-data-lake-store-account"></a>Encontre a conta padrão data lake store

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Pode encontrar a conta padrão data lake store filtrando `IsDefault` a lista de fontes de dados pela propriedade:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Adicionar uma origem de dados

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Listar fontes de dados

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Submeter tarefas U-SQL

### <a name="submit-a-string-as-a-u-sql-job"></a>Submeta uma corda como um trabalho U-SQL

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Submeta um ficheiro como um trabalho U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Lista de empregos

A saída inclui as tarefas atualmente em execução e as tarefas que foram concluídas recentemente.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Listar os melhores empregos n

Por defeito, a lista de postos de trabalho está classificada no prazo de apresentação. Assim, os trabalhos mais recentemente apresentados aparecem primeiro. Por padrão, a conta ADLA lembra-se de empregos durante 180 dias, mas o cmdlet Get-AdlJob por padrão devolve apenas os primeiros 500. Utilizar -Parâmetro de topo para listar um número específico de empregos.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Lista de empregos por estado de emprego

Usando `-State` o parâmetro. Pode combinar qualquer um destes valores:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Lista de empregos por resultado do emprego

Utilize `-Result` o parâmetro para detetar se os trabalhos terminados foram concluídos com sucesso. Tem estes valores:

* Cancelada
* Falhou
* Nenhuma
* Bem-sucedido

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Lista de empregos por submissão de emprego

O `-Submitter` parâmetro ajuda-o a identificar quem submeteu um emprego.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Listar empregos por tempo de submissão

O `-SubmittedAfter` é útil para filtrar para um intervalo de tempo.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Obter o estatuto de emprego

Obtenha o estado de uma tarefa específica.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```


### <a name="cancel-a-job"></a>Cancelar um trabalho

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Espere um trabalho para terminar

Em vez `Get-AdlAnalyticsJob` de repetir até que um `Wait-AdlJob` trabalho termine, pode usar o cmdlet para esperar que o trabalho termine.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analisar o histórico de empregos

Usar o Azure PowerShell para analisar a história dos empregos que têm corrido na análise do Data Lake é uma técnica poderosa. Pode usá-lo para obter insights sobre o uso e o custo. Você pode aprender mais olhando para a amostra de análise de história de [trabalho repo](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Lista de gasodutos e recorrências de emprego

Utilize `Get-AdlJobPipeline` o cmdlet para ver as informações do gasoduto anteriormente submetidas.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Utilize `Get-AdlJobRecurrence` o cmdlet para ver as informações de recorrência para trabalhos previamente apresentados.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>Gerir políticas de computação

### <a name="list-existing-compute-policies"></a>Enumerar as políticas de cálculo existentes

O `Get-AdlAnalyticsComputePolicy` cmdlet recupera informações sobre políticas de computação para uma conta data lake analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Criar uma política de computação

O `New-AdlAnalyticsComputePolicy` cmdlet cria uma nova política de cálculo para uma conta data lake analytics. Este exemplo define a Máxima DeUs disponível para o utilizador especificado para 50, e a prioridade mínima de trabalho para 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>Gerir ficheiros

### <a name="check-for-the-existence-of-a-file"></a>Verifique a existência de um ficheiro.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Upload e download

Faça upload de um ficheiro.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Faça upload de uma pasta inteira de forma recursiva.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Faça o download de um ficheiro.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Descarregue uma pasta inteira de forma recursiva.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Se o processo de upload ou descarregamento for interrompido, pode tentar ``-Resume`` retomar o processo executando novamente o cmdlet com a bandeira.

## <a name="manage-the-u-sql-catalog"></a>Gerir o catálogo U-SQL

O catálogo U-SQL é usado para estruturar dados e códigopara que possam ser partilhados por scripts U-SQL. O catálogo permite o maior desempenho possível com dados no Lago de Dados Azure. Para obter mais informações, veja [Utilizar catálogo U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Listar itens no catálogo U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Enumerar todas as assemblagens do catálogo U-SQL

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Obtenha detalhes sobre um item de catálogo

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Armazenar credenciais no catálogo

Dentro de uma base de dados U-SQL, crie um objeto credencial para uma base de dados hospedada em Azure. Atualmente, as credenciais U-SQL são o único tipo de artigo de catálogo que pode criar através do PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Gerir as regras de firewall

### <a name="list-firewall-rules"></a>Lista rindo regras de firewall

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Adicione uma regra de firewall

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Modificar uma regra de firewall

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Remover uma regra de firewall

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Permitir endereços IP Azure

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Trabalhar com Azure

### <a name="get-error-details"></a>Obtenha detalhes de erro

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Verifique se está a funcionar como Administrador na sua máquina Windows

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Encontre um TenantID

A partir de um nome de subscrição:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

A partir de um ID de subscrição:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

De um endereço de domínio como "contoso.com"

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Enumere todas as suas subscrições e iDs de inquilino

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Crie uma conta Data Lake Analytics usando um modelo

Também pode usar um modelo do Grupo de Recursos Azure usando a seguinte amostra: [Criar uma conta Data Lake Analytics usando um modelo](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Inicie-se com data lake analytics usando o [portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [Azure CLI](data-lake-analytics-get-started-cli.md)
* Gerencie o Azure Data Lake Analytics utilizando o [portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
