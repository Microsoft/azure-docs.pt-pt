---
title: Como configurar um pipeline CI/CD para a Azure Data Lake Analytics
description: Aprenda a configurar a integração contínua e a implementação contínua para a Azure Data Lake Analytics.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 09/14/2018
ms.openlocfilehash: 95b638b85e0746d2995488f2a28a5fb2512b1063
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015269"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Como configurar um pipeline CI/CD para a Azure Data Lake Analytics  

Neste artigo, aprende-se a configurar um pipeline de integração e implantação contínua (CI/CD) para empregos U-SQL e bases de dados U-SQL.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Utilize CI/CD para trabalhos U-SQL

A azure Data Lake Tools for Visual Studio fornece o tipo de projeto U-SQL que o ajuda a organizar scripts U-SQL. A utilização do projeto U-SQL para gerir o seu código U-SQL facilita ainda mais os cenários ci/CD.

## <a name="build-a-u-sql-project"></a>Construir um projeto U-SQL

Um projeto U-SQL pode ser construído com o Microsoft Build Engine (MSBuild) passando os parâmetros correspondentes. Siga os passos deste artigo para configurar um processo de construção para um projeto U-SQL.

### <a name="project-migration"></a>Migração de projetos

Antes de configurar uma tarefa de construção para um projeto U-SQL, certifique-se de que tem a versão mais recente do projeto U-SQL. Abra o ficheiro do projeto U-SQL no seu editor e verifique se tem estes itens de importação:

```xml
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
```

Caso contrário, tem duas opções para migrar o projeto:

- Opção 1: Alterar o artigo de importação antigo para o anterior.
- Opção 2: Abra o antigo projeto no Azure Data Lake Tools for Visual Studio. Utilize uma versão mais recente que 2.3000.0. O modelo de projeto antigo será atualizado automaticamente para a versão mais recente. Novos projetos criados com versões mais recentes do que 2.3000.0 usam o novo modelo.

### <a name="get-nuget"></a>Obter NuGet

A MSBuild não fornece suporte integrado para projetos U-SQL. Para obter este suporte, precisa de adicionar uma referência para a sua solução ao pacote [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet que adiciona o serviço de idiomas necessário.

Para adicionar a referência do pacote NuGet, clique com o botão direito na solução no Visual Studio Solution Explorer e escolha **Gerir pacotes NuGet**. Ou pode adicionar um ficheiro chamado `packages.config` na pasta da solução e colocar nele os seguintes conteúdos:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
```

### <a name="manage-u-sql-database-references"></a>Gerir referências de base de dados U-SQL

Os scripts U-SQL num projeto U-SQL podem ter declarações de consulta para objetos de base de dados U-SQL. Nesse caso, é necessário fazer referência ao projeto de base de dados U-SQL correspondente que inclui a definição dos objetos antes de construir o projeto U-SQL. Um exemplo é quando consulta uma tabela U-SQL ou referencia um conjunto.

Saiba mais sobre o [projeto de base de dados U-SQL.](data-lake-analytics-data-lake-tools-develop-usql-database.md)

>[!NOTE]
> A declaração DROP pode causar uma supressão acidental. Para ativar a declaração DROP, tem de especificar explicitamente os argumentos msBuild. **O AllowDropStatement** permitirá a operação DROP não relacionada com dados, como a montagem de drop e função de valor estimado da tabela de queda. **O AllowDataDropStatement** permitirá a operação DROP relacionada com dados, como tabela de lançamento e esquema de queda. Tem de ativar o AllowDropStatement antes de utilizar o AllowDataDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Construa um projeto U-SQL com a linha de comando MSBuild

Primeiro migrar o projeto e obter o pacote NuGet. Em seguida, ligue para a linha de comando padrão MSBuild com os seguintes argumentos adicionais para construir o seu projeto U-SQL:

```console
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
```

A definição e os valores dos argumentos são os seguintes:

- **USQLSDKPath= \<U-SQL Nuget package> \build\runtime**. Este parâmetro refere-se ao caminho de instalação do pacote NuGet para o serviço de linguagem U-SQL.
- **USQLTargetType=Merge ou SyntaxCheck**:

  - **Fusão.** O modo de fusão compila ficheiros por trás do código. Exemplos são **.cs**, **.py** e **ficheiros .r.** Inlineia a biblioteca de códigos definida pelo utilizador resultante no script U-SQL. Exemplos são um dll binary, Python, ou código R.

  - **SintaxeCheck**. O modo SyntaxCheck funde primeiro ficheiros por trás do código no script U-SQL. Em seguida, compila o script U-SQL para validar o seu código.

- **DataRoot= \<DataRoot path>**. DataRoot é necessário apenas para o modo SyntaxCheck. Quando constrói o script com o modo SyntaxCheck, o MSBuild verifica as referências a objetos de base de dados no script. Antes de construir, crie um ambiente local correspondente que contenha os objetos referenciados da base de dados U-SQL na pasta DataRoot da máquina de construção. Também pode gerir estas dependências de bases de dados [fazendo referência a um projeto de base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). A MSBuild verifica apenas referências de objetos de base de dados, não ficheiros.

- **Ativar Deployment=verdadeiro** ou **falso**. ActivarDeployment indica se é permitido implementar bases de dados U-SQL referenciadas durante o processo de construção. Se você referenciar um projeto de base de dados U-SQL e consumir os objetos de base de dados no seu script U-SQL, desafaça este parâmetro como **verdadeiro**.

### <a name="continuous-integration-through-azure-pipelines"></a>Integração contínua através de Gasodutos Azure

Além da linha de comando, também pode utilizar a Construção de Estúdio Visual ou uma tarefa MSBuild para construir projetos U-SQL em Azure Pipelines. Para configurar um pipeline de construção, certifique-se de adicionar duas tarefas no pipeline de construção: uma tarefa de restauro NuGet e uma tarefa MSBuild.

![Tarefa MSBuild para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Adicione uma tarefa de restauro nuGet para obter o pacote NuGet referenciado por solução que inclui `Azure.DataLake.USQL.SDK` , para que o MSBuild possa encontrar os alvos de linguagem U-SQL. Desave **o**  >  **diretório de destino** avançado para se `$(Build.SourcesDirectory)/packages` pretender utilizar a amostra de argumentos MSBuild diretamente no passo 2.

   ![Tarefa de restauro do NuGet para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Deite os argumentos msBuild em ferramentas de construção de Estúdio Visual ou numa tarefa MSBuild, como mostrado no exemplo seguinte. Ou pode definir variáveis para estes argumentos nos gasodutos Azure.

   ![Definir variáveis CI/CD MSBuild para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
   ```

### <a name="u-sql-project-build-output"></a>Produção de construção de projeto U-SQL

Depois de executar uma construção, todos os scripts do projeto U-SQL são construídos e a saída para um ficheiro zip chamado `USQLProjectName.usqlpack` . A estrutura da pasta no seu projeto é mantida na saída de construção com fecho.

> [!NOTE]
> Os ficheiros por detrás de código para cada script U-SQL serão fundidos como uma declaração inline à saída de construção de script.

## <a name="test-u-sql-scripts"></a>Testar scripts U-SQL

O Azure Data Lake fornece projetos de teste para scripts U-SQL e C# UDO/UDAG/UDF:

- Saiba como [adicionar casos de teste para scripts U-SQL e código C# estendido](data-lake-analytics-cicd-test.md#test-u-sql-scripts)

- Saiba como [executar casos de teste em Gasodutos Azure](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Implementar um trabalho U-SQL

Depois de verificar código através do processo de construção e teste, pode submeter trabalhos U-SQL diretamente da Azure Pipelines através de uma tarefa Azure PowerShell. Também pode implementar o script para a Azure Data Lake Store ou para o armazenamento Azure Blob e [executar os trabalhos programados através da Azure Data Factory.](../data-factory/transform-data-using-data-lake-analytics.md)

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Submeter empregos U-SQL através de Azure Pipelines

A produção de construção do projeto U-SQL é um ficheiro zip chamado **USQLProjectName.usqlpack**. O ficheiro zip inclui todos os scripts U-SQL do projeto. Pode utilizar a [tarefa Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell) em Pipelines com a seguinte amostra de script PowerShell para submeter trabalhos U-SQL diretamente a partir de Azure Pipelines.

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

>[!NOTE]
> Os comandos: `Submit-AzDataLakeAnalyticsJob` e `Wait-AzDataLakeAnalyticsJob` são ambos cmdlets Azure PowerShell para Azure Data Lake Analytics no quadro do Azure Resource Manager. Vais ter uma estação de trabalho com o Azure PowerShell instalado. Pode consultar a lista de [comandos](/powershell/module/Az.DataLakeAnalytics) para mais comandos e exemplos.
>

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Implementar empregos U-SQL através da Azure Data Factory

Pode submeter trabalhos U-SQL diretamente da Azure Pipelines. Ou pode fazer o upload dos scripts construídos para a Azure Data Lake Store ou para o armazenamento Azure Blob e [executar os trabalhos programados através da Azure Data Factory.](../data-factory/transform-data-using-data-lake-analytics.md)

Utilize a [tarefa Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell) em Azure Pipelines com a seguinte amostra de script PowerShell para carregar os scripts U-SQL para uma conta Azure Data Lake Store:

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

A azure Data Lake Tools for Visual Studio fornece modelos de projeto de base de dados U-SQL que o ajudam a desenvolver, gerir e implementar bases de dados U-SQL. Saiba mais sobre um [projeto de base de dados U-SQL.](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="build-u-sql-database-project"></a>Construir projeto de base de dados U-SQL

### <a name="get-the-nuget-package"></a>Obtenha o pacote NuGet

A MSBuild não fornece suporte integrado para projetos de base de dados U-SQL. Para obter esta capacidade, precisa adicionar uma referência para a sua solução ao pacote [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet que adiciona o serviço de idiomas necessário.

Para adicionar a referência do pacote NuGet, clique com o botão direito na solução no Visual Studio Solution Explorer. Escolha **Gerir pacotes nuget**. Em seguida, procure e instale o pacote NuGet. Ou pode adicionar um ficheiro chamado **packages.config** na pasta da solução e colocar nele os seguintes conteúdos:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Construa u-SQL um projeto de base de dados com a linha de comando MSBuild

Para construir o seu projeto de base de dados U-SQL, ligue para a linha de comando padrão MSBuild e passe a referência do pacote U-SQL SDK NuGet como um argumento adicional. Veja o seguinte exemplo: 

```console
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

O argumento `USQLSDKPath=<U-SQL Nuget package>\build\runtime` refere-se ao caminho de instalação do pacote NuGet para o serviço de idioma U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Integração contínua com os Pipelines do Azure

Além da linha de comando, pode utilizar a Visual Studio Build ou uma tarefa MSBuild para construir projetos de base de dados U-SQL em Azure Pipelines. Para configurar uma tarefa de construção, certifique-se de adicionar duas tarefas no pipeline de construção: uma tarefa de restauro nuGet e uma tarefa MSBuild.

   ![Tarefa CI/CD MSBuild para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Adicione uma tarefa de restauro nuGet para obter o pacote NuGet referenciado por solução, que `Azure.DataLake.USQL.SDK` inclui, para que o MSBuild possa encontrar os alvos de linguagem U-SQL. Desave **o**  >  **diretório de destino** avançado para se `$(Build.SourcesDirectory)/packages` pretender utilizar a amostra de argumentos MSBuild diretamente no passo 2.

   ![Tarefa CI/CD NuGet para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Deite os argumentos msBuild em ferramentas de construção de Estúdio Visual ou numa tarefa MSBuild, como mostrado no exemplo seguinte. Ou pode definir variáveis para estes argumentos nos gasodutos Azure.

   ![Definir variáveis CI/CD MSBuild para um projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```

### <a name="u-sql-database-project-build-output"></a>Projeto de base de dados U-SQL constrói saída

A saída de construção para um projeto de base de dados U-SQL é um pacote de implementação de base de dados U-SQL, nomeado com o sufixo `.usqldbpack` . O `.usqldbpack` pacote é um ficheiro zip que inclui todas as declarações de DDL num único script U-SQL numa pasta DDL. Inclui todos os **ficheiros .dlls** e ficheiros adicionais para montagem numa pasta temporária.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Funções de valor da tabela de teste e procedimentos armazenados

A adição de casos de teste para funções valorizadas por tabelas e procedimentos armazenados diretamente não é suportada atualmente. Como solução, pode criar um projeto U-SQL que tenha scripts U-SQL que chamem essas funções e escrevam casos de teste para eles. Tomar as seguintes medidas para configurar os casos de teste para funções valorizadas por tabela e procedimentos armazenados definidos no projeto de base de dados U-SQL:

1. Crie um projeto U-SQL para fins de teste e escreva scripts U-SQL chamando as funções de valor da tabela e procedimentos armazenados.

1. Adicione uma referência de base de dados ao projeto U-SQL. Para obter a função valorizada da tabela e a definição de procedimento armazenado, é necessário fazer referência ao projeto de base de dados que contém a declaração DDL. Saiba mais sobre [referências de bases de dados.](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)

1. Adicione casos de teste para scripts U-SQL que chamem funções valorizadas por tabela e procedimentos armazenados. Saiba como [adicionar casos de teste para scripts U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Implementar base de dados U-SQL através de Gasodutos Azure

`PackageDeploymentTool.exe` fornece as interfaces de programação e linha de comando que ajudam a implementar pacotes de implementação de base de dados U-SQL, **.usqldbpack**. O SDK está incluído no [pacote U-SQL SDK NuGet,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)localizado na **construção/runtime/PackageDeploymentTool.exe**. Ao `PackageDeploymentTool.exe` utilizar, pode implementar bases de dados U-SQL tanto para a Azure Data Lake Analytics como para as contas locais.

> [!NOTE]
>
> O suporte da linha de comando PowerShell e o suporte de tarefas da Azure Pipelines para a implementação da base de dados U-SQL estão atualmente pendentes.
>

Tome as seguintes medidas para configurar uma tarefa de implantação de bases de dados em Pipelines Azure:

1. Adicione uma tarefa powerShell Script num oleoduto de construção ou libertação e execute o seguinte script PowerShell. Esta tarefa ajuda a obter as dependências da Azure SDK para `PackageDeploymentTool.exe` e `PackageDeploymentTool.exe` . Pode definir os **parâmetros -AzureSDK** e **-DBDeploymentTool** para carregar as dependências e a ferramenta de implantação para pastas específicas. Passe o caminho **-AzureSDK** para `PackageDeploymentTool.exe` o parâmetro **-AzureSDKPath** no passo 2.

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

2. Adicione uma **tarefa de Linha de Comando** num oleoduto de construção ou libertação e preencha o script chamando `PackageDeploymentTool.exe` . `PackageDeploymentTool.exe` está localizada sob a pasta **$DBDeploymentTool** definida. O guião da amostra é o seguinte: 

   - Implementar uma base de dados U-SQL localmente:

      ```cmd
      PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
      ```

   - Utilize o modo de autenticação interativa para implementar uma base de dados U-SQL numa conta Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
      ```

   - Utilize a autenticação **secreta** para implantar uma base de dados U-SQL numa conta Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
      ```

   - Utilize a autenticação **certFile** para implementar uma base de dados U-SQL numa conta Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
      ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>PackageDeploymentTool.exe descrições de parâmetros

#### <a name="common-parameters"></a>Parâmetros comuns

| Parâmetro | Descrição | Valor Predefinido | Necessário |
|---------|-----------|-------------|--------|
|Pacote|O caminho do pacote de implantação da base de dados U-SQL a ser implantado.|nulo|true|
|Base de Dados|O nome da base de dados a ser implantado ou criado.|master|false|
|Ficheiro de Registo|O caminho do ficheiro para o registo. Predefinição para padrão (consola).|nulo|false|
|Nível de Registo|Nível de registo: Verbose, Normal, Aviso ou Erro.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Parâmetro para implantação local

|Parâmetro|Descrição|Valor Predefinido|Necessário|
|---------|-----------|-------------|--------|
|DataRoot|O caminho da pasta de raiz de dados local.|nulo|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parâmetros para implantação do Azure Data Lake Analytics

|Parâmetro|Descrição|Valor Predefinido|Necessário|
|---------|-----------|-------------|--------|
|Conta|Especifica qual a conta Azure Data Lake Analytics para implementar pelo nome da conta.|nulo|true|
|ResourceGroup|O nome do grupo de recursos Azure para a conta Azure Data Lake Analytics.|nulo|true|
|SubscriptionId|O ID de assinatura Azure para a conta Azure Data Lake Analytics.|nulo|true|
|Inquilino|O nome do inquilino é o nome de domínio Azure Ative Directory (Azure AD). Encontre-o na página de gestão de subscrição no portal Azure.|nulo|true|
|AzuresdkPath|O caminho para procurar conjuntos dependentes no Azure SDK.|nulo|true|
|Interativo|Se utilizar ou não o modo interativo para a autenticação.|false|false|
|ClientId|O ID da aplicação Azure necessário para a autenticação não interativa.|nulo|Requerido para a autenticação não interativa.|
|Segredo|O secrete ou palavra-passe para autenticação não interativa. Deve ser utilizado apenas num ambiente de confiança e seguro.|nulo|Requerido para autenticação não interativa, ou então utilize o SecreteFile.|
|SecreteFile|O ficheiro guarda o secrete ou a palavra-passe para a autenticação não interativa. Certifique-se de que o mantém legível apenas pelo utilizador atual.|nulo|Requerido para autenticação não interativa, ou então utilize o Secrete.|
|CertFile|O ficheiro guarda a certificação X.509 para autenticação não interativa. O padrão é usar a autenticação secreta do cliente.|nulo|false|
| EmpregoPrefixo | O prefixo para a implantação de bases de dados de um trabalho de DDL U-SQL. | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>Passos seguintes

- [Como testar o seu código Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Executar o script U-SQL na sua máquina local.](data-lake-analytics-data-lake-tools-local-run.md)
- [Utilize o projeto de base de dados U-SQL para desenvolver a base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).