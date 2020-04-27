---
title: Como criar um oleoduto CI/CD para o Azure Data Lake Analytics
description: Aprenda a configurar a integração contínua e a implantação contínua para o Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: b035be727df2dfecb613da79681affd740c69bec
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60333865"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Como criar um oleoduto CI/CD para o Azure Data Lake Analytics  

Neste artigo, aprende-se a criar um oleoduto de integração e implantação contínua (CI/CD) para empregos U-SQL e bases de dados U-SQL.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Utilize CI/CD para trabalhos U-SQL

As Ferramentas do Lago de Dados Azure para o Estúdio Visual fornecem o tipo de projeto U-SQL que o ajuda a organizar scripts U-SQL. A utilização do projeto U-SQL para gerir o seu código U-SQL facilita mais cenários de CI/CD.

## <a name="build-a-u-sql-project"></a>Construir um projeto U-SQL

Um projeto U-SQL pode ser construído com o Microsoft Build Engine (MSBuild) passando os parâmetros correspondentes. Siga os passos deste artigo para criar um processo de construção para um projeto U-SQL.

### <a name="project-migration"></a>Migração do projeto

Antes de configurar uma tarefa de construção para um projeto U-SQL, certifique-se de que tem a versão mais recente do projeto U-SQL. Abra o ficheiro do projeto U-SQL no seu editor e verifique se tem estes itens de importação:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Caso contrário, tem duas opções para migrar o projeto:

- Opção 1: Alterar o antigo item de importação para o anterior.
- Opção 2: Abra o antigo projeto no Azure Data Lake Tools for Visual Studio. Utilize uma versão mais recente do que 2.3.3000.0. O antigo modelo de projeto será atualizado automaticamente para a versão mais recente. Novos projetos criados com versões mais recentes do que 2.3.3000.0 usam o novo modelo.

### <a name="get-nuget"></a>Obter NuGet

A MSBuild não fornece apoio integrado para projetos U-SQL. Para obter este suporte, precisa adicionar uma referência para a sua solução para o pacote [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet que adiciona o serviço de idiomas necessário.

Para adicionar a referência ao pacote NuGet, clique à direita na solução no Visual Studio Solution Explorer e escolha **Gerir pacotes NuGet**. Ou pode adicionar um `packages.config` ficheiro chamado na pasta da solução e colocar o seguinte conteúdo na solução:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Gerir referências de base de dados U-SQL

Scripts U-SQL num projeto U-SQL podem ter declarações de consulta para objetos de base de dados U-SQL. Nesse caso, é necessário fazer referência ao projeto de base de dados U-SQL correspondente que inclui a definição dos objetos antes de construir o projeto U-SQL. Um exemplo é quando se consulta uma tabela U-SQL ou se refere a um conjunto. 

Saiba mais sobre o projeto de [base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>A declaração drop pode causar problema de eliminação de acidentes. Para ativar a declaração DROP, é necessário especificar explicitamente os argumentos da MSBuild. **O AllowDropStatement** ativará o funcionamento DROP não relacionado com dados, como o conjunto de lançamento e a função de valor de tabela de queda. **O AllowDataDropStatement** irá ativar a operação DROP relacionada com dados, como a tabela de lançamento e o esquema de queda. Tem de ativar o AllowDropStatement antes de utilizar o AllowDataDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Construa um projeto U-SQL com a linha de comando MSBuild

Primeiro migra o projeto e obtém o pacote NuGet. Em seguida, ligue para a linha de comando padrão MSBuild com os seguintes argumentos adicionais para construir o seu projeto U-SQL: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

A definição de argumentos e os valores são os seguintes:

* **USQLSDKPath=\<Pacote Nuget U-SQL>\build\runtime**. Este parâmetro refere-se à trajetória de instalação do pacote NuGet para o serviço de línguas U-SQL.
* **USQLTargetType=Merge or SyntaxCheck**:
    * **Fundir.** O modo de fusão compila ficheiros por trás de código. Exemplos são **.cs,** **.py**e **.r** ficheiros. Insere a biblioteca de códigos definida pelo utilizador resultante no script U-SQL. Exemplos são um dll binary, Python, ou Código R.
    * **SyntaxCheck**. O modo SyntaxCheck primeiro funde ficheiros por trás de código no script U-SQL. Em seguida, compila o script U-SQL para validar o seu código.
* **DataRoot=\<DataRoot caminho>**. DataRoot é necessário apenas para o modo SyntaxCheck. Quando constrói o script com o modo SyntaxCheck, a MSBuild verifica as referências a objetos de base de dados no script. Antes de construir, crie um ambiente local correspondente que contenha os objetos referenciados da base de dados U-SQL na pasta DataRoot da máquina de construção. Também pode gerir estas dependências de bases de dados fazendo referência a um projeto de [base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). A MSBuild verifica apenas referências de objetos de base de dados, não ficheiros.
* **Ativar A Implementação=verdadeira** ou **falsa**. O EnableDeployment indica se é permitido implementar bases de dados U-SQL referenciadas durante o processo de construção. Se fizer referência a um projeto de base de dados U-SQL e consumir os objetos de base de dados no seu script U-SQL, defina este parâmetro **como verdadeiro**.

### <a name="continuous-integration-through-azure-pipelines"></a>Integração contínua através de Oleodutos Azure

Além da linha de comando, também pode utilizar a Construção de Estúdio Visual ou uma tarefa MSBuild para construir projetos U-SQL em Pipelines Azure. Para configurar um pipeline de construção, certifique-se de adicionar duas tarefas no pipeline de construção: uma tarefa de restauro NuGet e uma tarefa MSBuild.

![Tarefa MSBuild para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Adicione uma tarefa de restauro NuGet para obter o `Azure.DataLake.USQL.SDK`pacote NuGet referenciado pela solução que inclui , para que a MSBuild possa encontrar os alvos linguísticos U-SQL. Defina o**diretório** de Destino `$(Build.SourcesDirectory)/packages` **Avançado** > para se pretender utilizar a amostra de argumentos MSBuild diretamente no passo 2.

    ![NuGet restaurar tarefa para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Defina os argumentos da MSBuild no Estúdio Visual construir ferramentas ou numa tarefa MSBuild, como mostra o exemplo seguinte. Ou pode definir variáveis para estes argumentos no pipeline de construção de Pipelines Azure.

    ![Defina variáveis CI/CD MSBuild para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>Produção de construção de projeto U-SQL

Depois de executar uma construção, todos os scripts do projeto U-SQL são construídos e saída para um ficheiro zip chamado `USQLProjectName.usqlpack`. A estrutura da pasta no seu projeto é mantida na saída de construção com fecho.

> [!NOTE]
>
> Os ficheiros por detrás de código para cada script U-SQL serão fundidos como uma declaração inline para a saída de construção do script.
>

## <a name="test-u-sql-scripts"></a>Testar scripts U-SQL

O Azure Data Lake fornece projetos de teste para scripts U-SQL e C# UDO/UDAG/UDF:
* Aprenda a adicionar casos de [teste para scripts U-SQL e código C# estendido](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Aprenda a executar casos de [teste em Pipelines Azure.](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops)

## <a name="deploy-a-u-sql-job"></a>Implementar um trabalho U-SQL

Depois de verificar o código através do processo de construção e teste, pode submeter os empregos u-SQL diretamente dos Pipelines Azure através de uma tarefa Azure PowerShell. Também pode implementar o script para a Azure Data Lake Store ou para o armazenamento azure Blob e [executar os trabalhos programados através da Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Submeta empregos U-SQL através de Pipelines Azure

A saída de construção do projeto U-SQL é um ficheiro zip chamado **USQLProjectName.usqlpack**. O ficheiro zip inclui todos os scripts U-SQL no projeto. Pode utilizar a [tarefa Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) em Pipelines com o seguinte script PowerShell de amostra para submeter empregos U-SQL diretamente dos Pipelines Azure.

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

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Implementar postos de trabalho na U-SQL através da Azure Data Factory

Pode submeter empregos u-SQL diretamente da Azure Pipelines. Ou pode enviar os scripts construídos para a Azure Data Lake Store ou para o armazenamento azure Blob e [executar os trabalhos programados através](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)da Azure Data Factory .

Utilize a [tarefa Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) em Pipelines Azure com o seguinte script PowerShell de amostra para fazer o upload dos scripts U-SQL para uma conta Azure Data Lake Store:

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

As Ferramentas do Lago de Dados Azure para o Estúdio Visual fornecem modelos de projetode base de dados U-SQL que o ajudam a desenvolver, gerir e implementar bases de dados U-SQL. Saiba mais sobre um [projeto de base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Construir projeto de base de dados U-SQL

### <a name="get-the-nuget-package"></a>Obtenha o pacote NuGet

A MSBuild não fornece suporte incorporado para projetos de base de dados U-SQL. Para obter esta capacidade, precisa adicionar uma referência para a sua solução para o pacote [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet que adiciona o serviço de idiomas necessário.

Para adicionar a referência do pacote NuGet, clique à direita na solução no Visual Studio Solution Explorer. Escolha **gerir pacotes NuGet**. Em seguida, procure e instale o pacote NuGet. Ou pode adicionar um ficheiro chamado **packages.config** na pasta de solução e colocar os seguintes conteúdos na pasta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Construa um projeto de base de dados da U-SQL com a linha de comando MSBuild

Para construir o seu projeto de base de dados U-SQL, ligue para a linha de comando padrão MSBuild e passe a referência do pacote U-SQL SDK NuGet como um argumento adicional. Veja o seguinte exemplo: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

O `USQLSDKPath=<U-SQL Nuget package>\build\runtime` argumento refere-se ao caminho de instalação do pacote NuGet para o serviço de línguas U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Integração contínua com os Pipelines do Azure

Além da linha de comando, pode utilizar a Visual Studio Build ou uma tarefa MSBuild para construir projetos de base de dados U-SQL em Pipelines Azure. Para configurar uma tarefa de construção, certifique-se de adicionar duas tarefas no pipeline de construção: uma tarefa de restauro NuGet e uma tarefa MSBuild.

   ![CI/CD MSBuild tarefa para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1. Adicione uma tarefa de restauro NuGet para obter o `Azure.DataLake.USQL.SDK`pacote NuGet referenciado pela solução, que inclui , para que a MSBuild possa encontrar os alvos linguísticos U-SQL. Defina o**diretório** de Destino `$(Build.SourcesDirectory)/packages` **Avançado** > para se pretender utilizar a amostra de argumentos MSBuild diretamente no passo 2.

   ![CI/CD NuGet tarefa para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Defina os argumentos da MSBuild no Estúdio Visual construir ferramentas ou numa tarefa MSBuild, como mostra o exemplo seguinte. Ou pode definir variáveis para estes argumentos no pipeline de construção de Pipelines Azure.

   ![Defina variáveis CI/CD MSBuild para um projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

   ```
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```
 
### <a name="u-sql-database-project-build-output"></a>Produção de construção de projeto de base de dados U-SQL

A produção de construção de um projeto de base de dados U-SQL `.usqldbpack`é um pacote de implantação de bases de dados U-SQL, nomeado com o sufixo . O `.usqldbpack` pacote é um ficheiro zip que inclui todas as declarações de DDL num único script U-SQL numa pasta DDL. Inclui todos os **.dlls** e ficheiros adicionais para montagem numa pasta temporária.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Funções avaliadas por mesa de ensaio e procedimentos armazenados

A adição de casos de teste para funções avaliadas em tabelae procedimentos armazenados diretamente não é suportado atualmente. Como uma suver, você pode criar um projeto U-SQL que tem scripts U-SQL que chamam essas funções e escrevem casos de teste para eles. Tome as seguintes medidas para configurar casos de teste para funções avaliadas em tabelas e procedimentos armazenados definidos no projeto de base de dados U-SQL:

1.  Crie um projeto U-SQL para fins de teste e escreva scripts U-SQL chamando as funções de valor de tabela e procedimentos armazenados.
2.  Adicione uma referência de base de dados ao projeto U-SQL. Para obter a função de valor de tabela e definição de procedimento armazenada, precisa de fazer referência ao projeto de base de dados que contém a declaração dDL. Saiba mais sobre [referências de base](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)de dados.
3.  Adicione casos de teste para scripts U-SQL que chamam funções de valor de tabela e procedimentos armazenados. Saiba como [adicionar casos de teste para scripts U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Implementar base de dados U-SQL através de Pipelines Azure

`PackageDeploymentTool.exe`fornece as interfaces de linha de programação e comando que ajudam a implementar pacotes de implementação de bases de dados U-SQL, **.usqldbpack**. O SDK está incluído no [pacote U-SQL SDK NuGet,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)localizado em **build/runtime/PackageDeploymentTool.exe**. Ao `PackageDeploymentTool.exe`utilizar, pode implementar bases de dados U-SQL tanto para o Azure Data Lake Analytics como para as contas locais.

> [!NOTE]
>
> O suporte da linha de comando PowerShell e o suporte de tarefade libertação de Pipelines Azure para a implantação da base de dados U-SQL estão atualmente pendentes.
>

Tome os seguintes passos para configurar uma tarefa de implantação de bases de dados em Pipelines Azure:

1. Adicione uma tarefa do PowerShell Script num pipeline de construção ou de lançamento e execute o seguinte script PowerShell. Esta tarefa ajuda a obter dependências de `PackageDeploymentTool.exe` `PackageDeploymentTool.exe`SDK Azure para e . Pode definir os parâmetros **-AzureSDK** e **-DBDeploymentTool** para carregar as dependências e ferramentade implementação para pastas específicas. Passe o caminho **-AzureSDK** até como `PackageDeploymentTool.exe` o parâmetro **-AzureSDKPath** no passo 2. 

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

2. Adicione uma **tarefa De Linha de Comando** num oleoduto de `PackageDeploymentTool.exe`construção ou de libertação e preencha o script chamando . `PackageDeploymentTool.exe`encontra-se sob a pasta **$DBDeploymentTool** definida. O script da amostra é o seguinte: 

    * Implementar uma base de dados U-SQL localmente:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Utilize o modo de autenticação interativa para implantar uma base de dados U-SQL numa conta Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Utilize a autenticação **secreta** para implantar uma base de dados U-SQL numa conta Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Utilize a autenticação **certFile** para implementar uma base de dados U-SQL numa conta Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Descrições do parâmetro PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Parâmetros comuns

| Parâmetro | Descrição | Valor Predefinido | Necessário |
|---------|-----------|-------------|--------|
|Pacote|O caminho do pacote de implantação da base de dados U-SQL a ser implementado.|nulo|true|
|Base de Dados|O nome da base de dados a ser implantado ou criado.|master|false|
|LogFile|O caminho do arquivo para a exploração madeireira. Padrão para padrão fora (consola).|nulo|false|
|Nível de Registo|Nível de registo: Verbose, Normal, Aviso ou Erro.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Parâmetro para implantação local

|Parâmetro|Descrição|Valor Predefinido|Necessário|
|---------|-----------|-------------|--------|
|DataRoot|O caminho da pasta de raiz de dados local.|nulo|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parâmetros para implantação de Azure Data Lake Analytics

|Parâmetro|Descrição|Valor Predefinido|Necessário|
|---------|-----------|-------------|--------|
|Conta|Especifica qual a conta Azure Data Lake Analytics para implementar pelo nome da conta.|nulo|true|
|ResourceGroup|O nome do grupo de recursos Azure para a conta Azure Data Lake Analytics.|nulo|true|
|SubscriptionId|O ID de subscrição azure para a conta Azure Data Lake Analytics.|nulo|true|
|Inquilino|O nome do inquilino é o nome de domínio Azure Ative Directory (Azure AD). Encontre-o na página de gestão de subscrição no portal Azure.|nulo|true|
|AzureSDKPath|O caminho para pesquisar conjuntos dependentes no Azure SDK.|nulo|true|
|Interativo|Utilizar ou não o modo interativo para a autenticação.|false|false|
|ClientId|O ID de aplicação Azure AD necessário para autenticação não interativa.|nulo|Necessário para autenticação não interativa.|
|Segredo|O segredo ou senha para autenticação não interativa. Deve ser usado apenas num ambiente de confiança e segurança.|nulo|Necessário para autenticação não interativa, ou então utilize o SecreteFile.|
|SecreteFile|O ficheiro guarda o segredo ou a palavra-passe para autenticação não interativa. Certifique-se de mantê-lo legível apenas pelo utilizador atual.|nulo|Necessário para autenticação não interativa, ou então use Secrete.|
|CertFile|O ficheiro guarda a certificação X.509 para autenticação não interativa. O padrão é usar a autenticação secreta do cliente.|nulo|false|
| JobPrefix | O prefixo para a implantação da base de dados de um trabalho de DDL U-SQL. | Deploy_ + Data.Agora | false |

## <a name="next-steps"></a>Passos seguintes

- [Como testar o seu código Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Execute o script U-SQL na sua máquina local](data-lake-analytics-data-lake-tools-local-run.md).
- Utilize o projeto de [base de dados U-SQL para desenvolver base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).
