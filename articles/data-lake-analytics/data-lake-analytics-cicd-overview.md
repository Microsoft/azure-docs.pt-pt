---
title: Como configurar um pipeline CI/CD para o Azure Data Lake Analytics
description: Saiba como configurar a integração contínua e implementação contínua para Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: b6c5df1ef0c93508595e27cbda315281aa3461b5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124291"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Como configurar um pipeline CI/CD para o Azure Data Lake Analytics  

Neste artigo, saiba como configurar um pipeline de implementação (CI/CD) para tarefas de U-SQL e bases de dados U-SQL e a integração contínua.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Utilize o CI/CD para tarefas de U-SQL

O Azure Data Lake Tools para Visual Studio fornece o tipo de projeto U-SQL que o ajuda a organizar os scripts U-SQL. Usando o projeto de U-SQL para gerir o seu código de U-SQL facilita ainda mais cenários de CI/CD.

## <a name="build-a-u-sql-project"></a>Criar um projeto U-SQL

Um projeto U-SQL pode ser criado com o Microsoft Build Engine (MSBuild), passando parâmetros correspondentes. Siga os passos neste artigo para configurar um processo de criação de um projeto U-SQL.

### <a name="project-migration"></a>Migração do projeto

Antes de configurar uma tarefa de compilação para um projeto U-SQL, certifique-se de que tem a versão mais recente do projeto U-SQL. Abra o ficheiro de projeto U-SQL no seu editor e certifique-se de que tem estes importar itens:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Caso contrário, tem duas opções para migrar o projeto:

- Opção 1: Altere o item de importação antigo para o anterior.
- Opção 2: Abra o projeto antigo nas ferramentas do Azure Data Lake para Visual Studio. Utilize uma versão mais recente do que 2.3.3000.0. O modelo de projeto antigo será atualizado automaticamente para a versão mais recente. Novos projetos criados com versões mais recentes do que 2.3.3000.0 utilizam o novo modelo.

### <a name="get-nuget"></a>Obter o NuGet

MSBuild não fornece suporte interno para projetos de U-SQL. Para obter esse suporte, precisa adicionar uma referência para a sua solução para o [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) pacote NuGet que adiciona o serviço de idiomas necessários.

Para adicionar a referência de pacote do NuGet, a solução no Explorador de soluções do Visual Studio com o botão direito e escolher **gerir pacotes NuGet**. Ou pode adicionar um arquivo chamado `packages.config` na pasta de soluções e coloque o seguinte conteúdo para o mesmo:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Gerir as referências de base de dados U-SQL

Scripts de U-SQL num projeto U-SQL podem ter declarações de consulta para objetos de banco de dados U-SQL. Nesse caso, terá de referenciar o projeto de banco de dados U-SQL correspondente que inclui a definição dos objetos antes de criar o projeto de U-SQL. Um exemplo é quando consulta uma tabela de U-SQL ou referenciar um assembly. 

Saiba mais sobre [projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>Projeto de banco de dados U-SQL está atualmente em pré-visualização pública. Se tiver a instrução DROP no projeto, a compilação falha. A instrução DROP será permitida em breve.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Criar um projeto U-SQL com a linha de comando do MSBuild

Primeiro, migre o projeto e obter o pacote NuGet. Em seguida, chame a linha de comando do MSBuild padrão com os argumentos adicionais seguintes para criar o seu projeto de U-SQL: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

A definição de argumentos e valores são os seguintes:

* **USQLSDKPath = < pacote Nuget do U-SQL > \build\runtime**. Este parâmetro refere-se para o caminho de instalação do pacote NuGet para o serviço de linguagem U-SQL.
* **USQLTargetType = intercalação ou SyntaxCheck**:
    * **Intercalar**. Modo de intercalação compila arquivos code-behind. Os exemplos são **. cs**, **. PY**, e **. r** ficheiros. Ele linhas internas da biblioteca de código definido pelo utilizador resultante para o script de U-SQL. Os exemplos são um binário de dll, Python ou R código.
    * **SyntaxCheck**. Modo de SyntaxCheck mescla primeiro arquivos code-behind para o script de U-SQL. Em seguida, ele compila o script de U-SQL para validar o seu código.
* **DataRoot=<DataRoot path>**. DataRoot é necessário apenas para o modo de SyntaxCheck. Quando ele cria o script com o modo de SyntaxCheck, MSBuild verifica as referências a objetos de banco de dados no script. Antes de criar, configure um ambiente local correspondente que contém os objetos referenciados da base de dados U-SQL na pasta DataRoot de máquina de compilação. Também pode gerir estas dependências de base de dados por [referenciar um projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild verifica apenas referências de objeto de base de dados, não os ficheiros.
* **EnableDeployment = true** ou **false**. EnableDeployment indica se é permitido para implementar bases de dados de U-SQL referenciados durante o processo de compilação. Se referenciar um projeto de banco de dados U-SQL e consumir os objetos de base de dados no seu script de U-SQL, defina este parâmetro como **true**.

### <a name="continuous-integration-through-azure-pipelines"></a>Integração contínua através de Pipelines do Azure

Além da linha de comandos, também pode utilizar a compilação do Visual Studio ou de uma tarefa do MSBuild para criar projetos de U-SQL nos Pipelines do Azure. Para configurar um pipeline de compilação, certifique-se de adicionar duas tarefas no pipeline de compilação: uma tarefa de restauro do NuGet e uma tarefa do MSBuild.

![Tarefa do MSBuild para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Adicionar uma tarefa de restauro do NuGet para obter o pacote NuGet referenciado de solução, que inclui `Azure.DataLake.USQL.SDK`, para que o MSBuild, pode encontrar os destinos de linguagem U-SQL. Definir **avançadas** > **diretório de destino** para `$(Build.SourcesDirectory)/packages` se pretender utilizar o exemplo de argumentos de MSBuild diretamente no passo 2.

    ![Tarefa de restauro do NuGet para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Defina argumentos de MSBuild em ferramentas de compilação do Visual Studio ou numa tarefa do MSBuild, conforme mostrado no exemplo a seguir. Ou pode definir as variáveis para esses argumentos no pipeline de compilação Pipelines do Azure.

    ![Definir variáveis MSBuild de CI/CD para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>Saída de compilação do projeto U-SQL

Depois de executar uma compilação, todos os scripts no projeto U-SQL criados e de saída para um arquivo zip chamado `USQLProjectName.usqlpack`. A estrutura de pastas no seu projeto é mantida na saída da compilação zipados.

> [!NOTE]
>
> Arquivos Code-behind para cada script de U-SQL serão Unidos como uma instrução de inline para a saída de compilação de scripts.
>

## <a name="test-u-sql-scripts"></a>Testar scripts U-SQL

O Azure Data Lake fornece projetos de teste para scripts U-SQL e c# UDO/UDAG/UDF:
* Saiba como [adicionar casos de teste de scripts U-SQL e o código c# expandido](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Saiba como [executa casos de teste no Azure Pipelines](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Implementar uma tarefa U-SQL

Depois de verificar o código no processo de compilação e teste, pode submeter tarefas de U-SQL diretamente a partir de Pipelines do Azure através de uma tarefa do Azure PowerShell. Também pode implementar o script para o armazenamento do Azure Data Lake Store ou de Blobs do Azure e [executar as tarefas agendadas através do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Submeter tarefas de U-SQL através de Pipelines do Azure

A compilação de saída do projeto U-SQL é um ficheiro zip chamado **USQLProjectName.usqlpack**. O ficheiro zip inclui todos os scripts U-SQL no projeto. Pode utilizar o [tarefas de Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) em Pipelines com o seguinte script do PowerShell de exemplo para submeter tarefas de U-SQL diretamente a partir de Pipelines do Azure.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Implementar tarefas de U-SQL através do Azure Data Factory

Pode submeter tarefas de U-SQL diretamente a partir de Pipelines do Azure. Ou pode carregar os scripts criados para o armazenamento do Azure Data Lake Store ou de Blobs do Azure e [executar as tarefas agendadas através do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Utilize o [tarefas de Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) nos Pipelines do Azure com o seguinte script do PowerShell de exemplo para carregar os scripts U-SQL para uma conta do Azure Data Lake Store:

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD para uma base de dados U-SQL

O Azure Data Lake Tools para Visual Studio fornece modelos de projeto de banco de dados U-SQL que o ajudam a desenvolveram, gerir e implementar bases de dados U-SQL. Saiba mais sobre uma [projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Criar projeto de banco de dados U-SQL

### <a name="get-the-nuget-package"></a>Obter o pacote de NuGet

MSBuild não fornece suporte interno para projetos de banco de dados U-SQL. Para obter essa capacidade, precisa adicionar uma referência para a sua solução para o [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) pacote NuGet que adiciona o serviço de idiomas necessários.

Para adicionar a referência de pacote do NuGet, com o botão direito a solução no Explorador de soluções do Visual Studio. Escolher **gerir pacotes NuGet**. Em seguida, procure e instale o pacote NuGet. Ou pode adicionar um ficheiro chamado **Packages** na pasta de soluções e coloque o seguinte conteúdo para o mesmo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Criar um projeto de banco de dados com a linha de comando do MSBuild de U-SQL

Para criar seu projeto de banco de dados U-SQL, chamar a linha de comando do MSBuild padrão e passar a referência de pacote NuGet do SDK U-SQL como um argumento adicional. Veja o seguinte exemplo: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

O argumento `USQLSDKPath=<U-SQL Nuget package>\build\runtime` refere-se para o caminho de instalação do pacote NuGet para o serviço de linguagem U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Integração contínua com Pipelines do Azure

Para além da linha de comandos, pode utilizar a compilação do Visual Studio ou de uma tarefa do MSBuild para criar projetos de banco de dados U-SQL nos Pipelines do Azure. Para configurar uma tarefa de compilação, certifique-se de adicionar duas tarefas no pipeline de compilação: uma tarefa de restauro do NuGet e uma tarefa do MSBuild.

   ![Tarefa do MSBuild de CI/CD para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1. Adicionar uma tarefa de restauro do NuGet para obter o pacote de NuGet referenciado de solução, que inclui `Azure.DataLake.USQL.SDK`, para que o MSBuild, pode encontrar os destinos de linguagem U-SQL. Definir **avançadas** > **diretório de destino** para `$(Build.SourcesDirectory)/packages` se pretender utilizar o exemplo de argumentos de MSBuild diretamente no passo 2.

   ![Tarefa de CI/CD NuGet para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Defina argumentos de MSBuild em ferramentas de compilação do Visual Studio ou numa tarefa do MSBuild, conforme mostrado no exemplo a seguir. Ou pode definir as variáveis para esses argumentos no pipeline de compilação Pipelines do Azure.

   ![Definir variáveis MSBuild de CI/CD para um projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

   ```
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```
 
### <a name="u-sql-database-project-build-output"></a>Saída de compilação do projeto de banco de dados do U-SQL

A compilação de saída para um projeto de banco de dados U-SQL é um pacote de implementação da base de dados U-SQL, com o nome com o sufixo `.usqldbpack`. O `.usqldbpack` pacote é um ficheiro zip que inclui todas as instruções DDL num único script de U-SQL numa pasta DDL. Ele inclui todos os **DLLs** e outros ficheiros para o assembly numa pasta temporária.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Funções valorizadas por tabela de teste e procedimentos armazenados

Atualmente não é suportada a adição de casos de teste de funções com valor de tabela e procedimentos armazenados diretamente. Como solução, pode criar um projeto de U-SQL que tem scripts U-SQL que chamam essas funções e escrever casos de teste para os mesmos. Siga os passos seguintes para configurar a casos de teste de funções com valor de tabela e procedimentos armazenados definidos no projeto de banco de dados U-SQL:

1.  Criar um projeto de U-SQL para fins de teste e escrever scripts U-SQL, chamando as funções com valor de tabela e os procedimentos armazenados.
2.  Adicione uma referência de base de dados ao projeto U-SQL. Para obter a função com valor de tabela e a definição de procedimento armazenado, precisa referenciar o projeto de banco de dados que contém a instrução DDL. Saiba mais sobre [referências de base de dados](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Adicione casos de teste de scripts de U-SQL que chamam funções com valor de tabela e procedimentos armazenados. Saiba como [adicionar casos de teste de scripts U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Implementar a base de dados U-SQL através de Pipelines do Azure

`PackageDeploymentTool.exe` Fornece as interfaces de linha de comandos que o ajudam a implementar pacotes de implementação do U-SQL da base de dados, e programação **.usqldbpack**. O SDK está incluído nos [pacote NuGet do SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), localizado na **build/runtime/PackageDeploymentTool.exe**. Ao utilizar `PackageDeploymentTool.exe`, pode implementar bases de dados U-SQL para o Azure Data Lake Analytics e contas locais.

> [!NOTE]
>
> Suporte de linha de comandos do PowerShell e a tarefa de lançamento de Pipelines do Azure o suporte de implementação do U-SQL da base de dados está atualmente pendente.
>

Siga os passos seguintes para configurar uma tarefa de implementação de base de dados em Pipelines do Azure:

1. Adicionar uma tarefa de Script do PowerShell numa compilação ou pipeline de versões e execute o seguinte script do PowerShell. Esta tarefa ajuda a obter dependências de SDK do Azure para `PackageDeploymentTool.exe` e `PackageDeploymentTool.exe`. Pode definir o **- AzureSDK** e **- DBDeploymentTool** parâmetros ao carregar as dependências e a ferramenta de implementação para pastas específicas. Passar o **- AzureSDK** caminho `PackageDeploymentTool.exe` como a **- AzureSDKPath** parâmetro no passo 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Adicionar uma **tarefa da linha de comandos** num pipeline de compilação ou de versão e preencha o script chamando `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` está localizada sob o definido **$DBDeploymentTool** pasta. O script de exemplo é o seguinte: 

    * Implemente uma base de dados do U-SQL localmente:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Utilize o modo de autenticação interativa para implementar uma base de dados U-SQL para uma conta do Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Uso **secrete** autenticação para implementar uma base de dados U-SQL para uma conta do Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Uso **certFile** autenticação para implementar uma base de dados U-SQL para uma conta do Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Descrições de parâmetros de PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Parâmetros comuns

| Parâmetro | Descrição | Valor Predefinido | Necessário |
|---------|-----------|-------------|--------|
|Pacote|O caminho do pacote de implementação do U-SQL da base de dados a serem implantados.|nulo|true|
|Base de Dados|O nome de base de dados a ser implementado ou criado.|master|false|
|LogFile|O caminho do ficheiro de registo. Padrão standard out (consola).|nulo|false|
|LogLevel|Nível de registo: Verboso, Normal, aviso ou erro.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Parâmetro de implementação de local

|Parâmetro|Descrição|Valor Predefinido|Necessário|
|---------|-----------|-------------|--------|
|DataRoot|O caminho da pasta de raiz de dados local.|nulo|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parâmetros para a implementação do Azure Data Lake Analytics

|Parâmetro|Descrição|Valor Predefinido|Necessário|
|---------|-----------|-------------|--------|
|Conta|Especifica a conta do Azure Data Lake Analytics para implementar em, pelo nome da conta.|nulo|true|
|ResourceGroup|O nome do grupo de recursos do Azure para a conta do Azure Data Lake Analytics.|nulo|true|
|SubscriptionId|O ID de subscrição do Azure para a conta do Azure Data Lake Analytics.|nulo|true|
|Inquilino|O nome do inquilino é o nome de domínio do Azure Active Directory (Azure AD). Encontrá-lo na página de gestão da subscrição no portal do Azure.|nulo|true|
|AzureSDKPath|O caminho para pesquisar os conjuntos de módulos dependentes no SDK do Azure.|nulo|true|
|Interativo|Se deve ou não a utilizar o modo interativo para autenticação.|false|false|
|ClientId|O ID da aplicação do Azure AD necessários para a autenticação não interativa.|nulo|Necessário para a autenticação não interativa.|
|Secrete|O secrete ou a palavra-passe para a autenticação não interativa. Ele deve ser usado apenas num ambiente seguro e fiável.|nulo|É necessário para a autenticação não interativa, caso contrário, utilize SecreteFile.|
|SecreteFile|O ficheiro salva o secrete ou a palavra-passe para a autenticação não interativa. Certifique-se de mantê-lo legíveis apenas pelo utilizador atual.|nulo|É necessário para a autenticação não interativa, caso contrário, utilize Secrete.|
|CertFile|O ficheiro guarda a certificação X.509 para a autenticação não interativa. A predefinição é utilizar o cliente secrete autenticação.|nulo|false|
| JobPrefix | O prefixo para a implementação de base de dados de uma tarefa de DDL do U-SQL. | Deploy_ + DateTime. Now | false |

## <a name="next-steps"></a>Passos Seguintes

- [Como testar seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Execute o script de U-SQL no seu computador local](data-lake-analytics-data-lake-tools-local-run.md).
- [Projeto de banco de dados de U-SQL de uso para desenvolver a base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).
