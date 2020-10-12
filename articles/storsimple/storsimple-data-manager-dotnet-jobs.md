---
title: Utilize .NET SDK para trabalhos do Microsoft Azure StorSimple Data Manager
description: Saiba como utilizar o .NET SDK dentro do serviço StorSimple Data Manager para transformar dados do dispositivo StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: e34701640de24a4c3c13f8d10073b4392af2f28a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88183654"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Utilizar o SDK .NET para iniciar a transformação de dados

## <a name="overview"></a>Descrição geral

Este artigo explica como pode utilizar a funcionalidade de transformação de dados dentro do serviço StorSimple Data Manager para transformar dados do dispositivo StorSimple. Os dados transformados são então consumidos por outros serviços Azure na nuvem.

Pode lançar um trabalho de transformação de dados de duas formas:

- Utilizar o .NET SDK
- Use o runbook da Azure Automation
 
  Este artigo detalha como criar uma aplicação de consola .NET para iniciar um trabalho de transformação de dados e, em seguida, rastreá-lo para a conclusão. Para saber mais sobre como iniciar a transformação de dados através da Automação, vá ao [runbook da Azure Automation para desencadear trabalhos de transformação de dados.](storsimple-data-manager-job-using-automation.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:
*   Um computador a funcionar:

    - Visual Studio 2012, 2013, 2015 ou 2017.

    - Azure Powershell. [Baixar Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Uma definição de emprego corretamente configurada no StorSimple Data Manager dentro de um grupo de recursos.
*   Todos os dlls necessários. Descarregue estes dlls do [repositório GitHub.](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) guião do repositório gitHub.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

Execute os seguintes passos para utilizar .NET para lançar um trabalho de transformação de dados.

1. Para recuperar os parâmetros de configuração, faça os seguintes passos:
    1. Descarregue o `Get-ConfigurationParams.ps1` script do repositório gitHub no `C:\DataTransformation` local.
    1. Executar o `Get-ConfigurationParams.ps1` guião do repositório gitHub. Escreva o seguinte comando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Pode passar em quaisquer valores para o ActiveDirectoryKey e AppName.

2. Este script produz os seguintes valores:
    * ID de Cliente
    * ID do inquilino
    * Chave de diretório ativo (igual à que foi apresentada acima)
    * ID da subscrição

        ![Saída do script de parâmetros de configuração](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Utilizando o Visual Studio 2012, 2013 ou 2015, crie uma aplicação de consola C# .NET.

    1. Lançamento **Visual Studio 2012/2013/2015**.
    1. Selecione **Ficheiro > Novo > Projeto**.

        ![Criar um projeto 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Selecione **modelos de > instalados > aplicação de consola visual C# >**.
    3. Introduza **dataTransformationApp** para o **nome**.
    4. Selecione **C:\DataTransformation** para a **localização**.
    6. Clique em **OK** para criar o projeto.

        ![Criar um projeto 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Agora, adicione todos os dlls presentes na [pasta dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) como **Referências** no projeto que criou. Para adicionar os ficheiros dll, execute o seguinte:

   1. No Visual Studio, vá ao **View > Solution Explorer**.
   2. Clique na seta à esquerda do projeto Data Transformation App. Clique em **Referências** e, em seguida, clique no botão direito para **adicionar referência.**
    
       ![Adicionar dlls 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Navegue pela localização da pasta de pacotes, selecione todos os dlls e clique em **Adicionar**e clique **em OK**.

       ![Adicionar dlls 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Adicione o seguinte ao **utilizar** instruções para o ficheiro de origem (Program.cs) no projeto.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. O código seguinte rubrica a fase de trabalho de transformação de dados. Adicione isto no **método principal.** Substitua os valores dos parâmetros de configuração obtidos anteriormente. Ligue os valores do **Nome do Grupo** de Recursos e nome de **recursos**. O **ResourceGroupName** é o associado ao Gestor de Dados StorSimple no qual a definição de trabalho foi configurada. O **Nome de Recursos** é o nome do seu serviço StorSimple Data Manager.

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
   
7. Especificar os parâmetros com os quais a definição de trabalho deve ser executada

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OU)

    Se quiser alterar os parâmetros de definição de trabalho durante o tempo de execução, adicione o seguinte código:

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

8. Após a inicialização, adicione o seguinte código para desencadear um trabalho de transformação de dados na definição de trabalho. Ligue o nome de **definição de emprego**apropriado .

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Uma vez colado o código, construa a solução. Aqui está uma imagem do código snippet para inicializar a instância de trabalho de transformação de dados.

   ![Corte de código para inicializar trabalho de transformação de dados](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Este trabalho transforma os dados que correspondem ao diretório de raiz e aos filtros de ficheiros dentro do volume StorSimple e coloca-os na partilha de contentores/ficheiros especificados. Quando um ficheiro é transformado, uma mensagem é adicionada a uma fila de armazenamento (na mesma conta de armazenamento que o contentor/partilha de ficheiros) com o mesmo nome que a definição de trabalho. Esta mensagem pode ser usada como gatilho para iniciar qualquer processamento adicional do ficheiro.

10. Uma vez que o trabalho tenha sido desencadeado, pode usar o seguinte código para rastrear o trabalho para ser concluído. Não é obrigatório adicionar este código para a execução do trabalho.

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
