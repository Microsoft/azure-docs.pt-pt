---
title: Utilize o SDK .NET para empregos do Microsoft Azure StorSimple Data Manager
description: Saiba como usar o SDK .NET para lançar empregos no StorSimple Data Manager
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b7cf1d3b9d4a9d751348c4792f904062b00ac104
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76270729"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Utilize o .NET SDK para iniciar a transformação de dados

## <a name="overview"></a>Descrição geral

Este artigo explica como pode utilizar a funcionalidade de transformação de dados dentro do serviço StorSimple Data Manager para transformar dados do dispositivo StorSimple. Os dados transformados são então consumidos por outros serviços Azure na nuvem.

Pode lançar um trabalho de transformação de dados de duas formas:

- Utilizar o .NET SDK
- Utilizar o livro de execução da Automação Azure
 
  Este artigo detalha como criar uma aplicação de consola .NET para iniciar um trabalho de transformação de dados e, em seguida, rastreá-lo para a conclusão. Para saber mais sobre como iniciar a transformação de dados via Automação, vá ao Livro de [Execução da Automação Azure para desencadear trabalhos](storsimple-data-manager-job-using-automation.md)de transformação de dados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:
*   Um computador em execução:

    - Estúdio Visual 2012, 2013, 2015 ou 2017.

    - Azure Powershell. [Baixar O Azure Powershell.](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
*   Uma definição de trabalho corretamente configurada no StorSimple Data Manager dentro de um grupo de recursos.
*   Todos os dlls necessários. Descarregue estes dlls do [repositório GitHub.](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)script do repositório GitHub.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

Execute os seguintes passos para utilizar .NET para lançar um trabalho de transformação de dados.

1. Para recuperar os parâmetros de configuração, faça os seguintes passos:
    1. Descarregue o `Get-ConfigurationParams.ps1` script de repositório GitHub no `C:\DataTransformation` local.
    1. Execute `Get-ConfigurationParams.ps1` o guião do repositório GitHub. Escreva o seguinte comando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Pode passar em quaisquer valores para o ActiveDirectoryKey e AppName.

2. Este script produz os seguintes valores:
    * ID de Cliente
    * ID do inquilino
    * Tecla de Diretório Ativo (a mesma que a que foi introduzida acima)
    * ID da subscrição

        ![Saída de script de parâmetros de configuração](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Utilizando o Visual Studio 2012, 2013 ou 2015, crie uma aplicação de consola C# .NET.

    1. Lançamento **Do Estúdio Visual 2012/2013/2015**.
    1. Selecione **Ficheiro > Novo > Projeto**.

        ![Criar um projeto 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Selecione **modelos de > instalados > aplicação visual C# > consola**.
    3. Introduza **DataTransformationApp** para o **nome**.
    4. Selecione **C:\DataTransformation** for the **Location**.
    6. Clique em **OK** para criar o projeto.

        ![Criar um projeto 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Agora, adicione todos os dlls presentes na [pasta dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) como **Referências** no projeto que criou. Para adicionar os ficheiros dll, execute o seguinte:

   1. No Estúdio Visual, vá ver **> Solution Explorer.**
   2. Clique na seta à esquerda do projeto Data Transformation App. Clique em **Referências** e, em seguida, clique à direita para **adicionar referência**.
    
       ![Adicione dlls 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Navegue na localização da pasta de pacotes, selecione todos os dlls e clique em **Adicionar**, e, em seguida, clique em **OK**.

       ![Adicione dlls 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Adicione o seguinte ao **utilizar** instruções para o ficheiro de origem (Program.cs) no projeto.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. O código seguinte inicializa a instância de emprego de transformação de dados. Adicione isto no **método principal**. Substitua os valores dos parâmetros de configuração obtidos anteriormente. Ligue os valores de Nome de Grupo de **Recursos** e Nome de **Recursos**. O **ResourceGroupName** é o associado ao StorSimple Data Manager no qual a definição de trabalho foi configurada. O **Nome de Recursos** é o nome do seu serviço StorSimple Data Manager.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Especifique os parâmetros com os quais a definição de trabalho precisa de ser executada

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OU)

    Se pretender alterar os parâmetros de definição de trabalho durante o tempo de execução, adicione o seguinte código:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Após a inicialização, adicione o seguinte código para desencadear um trabalho de transformação de dados na definição de emprego. Ligue o nome de **definição**de trabalho apropriado .

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Assim que o código for colado, construa a solução. Aqui está uma imagem do código snippet para inicializar a instância de emprego de transformação de dados.

   ![Código de corte para inicializar trabalho de transformação de dados](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Este trabalho transforma os dados que correspondem ao diretório raiz e filtros de ficheiros dentro do volume StorSimple e coloca-os no recipiente/partilha de ficheiros especificado. Quando um ficheiro é transformado, uma mensagem é adicionada a uma fila de armazenamento (na mesma conta de armazenamento que o recipiente/partilha de ficheiros) com o mesmo nome que a definição de trabalho. Esta mensagem pode ser usada como um gatilho para iniciar qualquer processamento adicional do ficheiro.

10. Uma vez que o trabalho tenha sido desencadeado, pode utilizar o seguinte código para rastrear o trabalho para a conclusão. Não é obrigatório adicionar este código para a execução de emprego.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Aqui está uma imagem de toda a amostra de código usada para desencadear o trabalho usando .NET.

    ![Corte completo de código para desencadear um trabalho .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Passos seguintes

[Utilize o StorSimple Data Manager UI para transformar os seus dados](storsimple-data-manager-ui.md).
