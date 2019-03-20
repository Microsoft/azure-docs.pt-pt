---
title: Utilizar o .NET SDK para as tarefas do Microsoft Azure StorSimple Data Manager | Documentos da Microsoft
description: Saiba como utilizar o SDK do .NET para iniciar tarefas do Gestor de dados do StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 80f01a926b94deebab59f8ef91bfc36a4600b5f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885055"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Utilizar o SDK .NET para iniciar a transformação de dados

## <a name="overview"></a>Descrição geral

Este artigo explica como pode utilizar a funcionalidade de transformação de dados no serviço StorSimple Data Manager para transformar dados de dispositivos do StorSimple. Os dados transformados, em seguida, estão a ser consumidos por outros serviços do Azure na cloud.

Pode iniciar uma tarefa de transformação de dados de duas formas:

- Utilizar o .NET SDK
- Utilizar o runbook de automatização do Azure
 
  Este artigo fornece detalhes sobre como criar uma aplicação de consola .NET de exemplo para iniciar uma tarefa de transformação de dados e, em seguida, controle-a para a conclusão. Para saber mais sobre como iniciar a transformação de dados por meio da automação, aceda a [runbook de automatização do Azure de utilização para tarefas de transformação de dados de Acionador](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:
*   Um computador com:

    - O Visual Studio 2012, 2013, 2015 ou 2017.

    - O Azure Powershell. [Transferir o Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Uma definição de tarefa corretamente configurada no Gestor de dados do StorSimple num grupo de recursos.
*   Todas as dlls necessárias. Baixe este dll a partir da [repositório do GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) script a partir do repositório do GitHub.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

Execute os seguintes passos para utilizar o .NET para iniciar uma tarefa de transformação de dados.

1. Para obter os parâmetros de configuração, siga os passos abaixo:
    1. Transfira o `Get-ConfigurationParams.ps1` do script do repositório do GitHub no `C:\DataTransformation` localização.
    1. Execute o `Get-ConfigurationParams.ps1` script a partir do repositório do GitHub. Escreva o seguinte comando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Pode passar em todos os valores para o ActiveDirectoryKey e AppName.

2. Este script produz os seguintes valores:
    * ID de Cliente
    * ID do inquilino
    * Chave de diretório Active Directory (igual da introduzido acima)
    * ID da subscrição

        ![Saída de script de parâmetros de configuração](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Com o Visual Studio 2012, 2013 ou 2015, crie um C# aplicação de consola .NET.

    1. Inicie **Visual Studio 2012/2013/2015**.
    1. Selecione **Ficheiro > Novo > Projeto**.

        ![Criar um projeto 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Selecione **instalado > modelos > Visual C# > aplicação de consola**.
    3. Introduza **DataTransformationApp** para o **nome**.
    4. Selecione **C:\DataTransformation** para o **localização**.
    6. Clique em **OK** para criar o projeto.

        ![Criar um projeto de 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Agora, adicione todas as dlls presentes no [pasta de dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) como **referências** no projeto que criou. Para adicionar os arquivos de dll, execute o seguinte:

   1. No Visual Studio, aceda a **vista > Explorador de soluções**.
   2. Clique na seta à esquerda do projeto de aplicação de transformação de dados. Clique em **referências** e, em seguida, clique com botão direito para **Add Reference**.
    
       ![Adicionar dlls 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Navegue até à localização da pasta de pacotes, selecione todas as dlls e clique em **Add**e, em seguida, clique em **OK**.

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
    
6. O código a seguir inicializa a instância de tarefa de transformação de dados. Adicione-a na **método Main**. Substitua os valores dos parâmetros de configuração que de obteve anteriormente. Plug-in os valores dos **nome do grupo de recursos** e **ResourceName**. O **ResourceGroupName** é associada com o StorSimple Data Manager na qual foi configurada a definição de tarefa. O **ResourceName** é o nome do seu serviço do StorSimple Data Manager.

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
   
7. Especifique os parâmetros com a qual a definição de tarefa precisa ser executado

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OR)

    Se pretender alterar os parâmetros de definição de tarefa durante o tempo de execução, em seguida, adicione o seguinte código:

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

8. Após a inicialização, adicione o seguinte código para acionar uma tarefa de transformação de dados sobre a definição de tarefa. Plug-in apropriado **nome da definição de tarefa**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Assim que o código é colado, compile a solução. Eis uma captura de ecrã do fragmento de código para inicializar a instância de tarefa de transformação de dados.

   ![Trecho de código para inicializar a tarefa de transformação de dados](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Esta tarefa transforma os dados que corresponde ao diretório de raiz e o ficheiro filtra dentro do volume do StorSimple e o coloca na partilha de ficheiros/contentor especificado. Quando um ficheiro é transformado, uma mensagem é adicionada a uma fila de armazenamento (na mesma conta de armazenamento como a partilha de ficheiros/contentor) com o mesmo nome que a definição de tarefa. Esta mensagem pode ser utilizada como acionador para iniciar qualquer processamento adicional do arquivo.

10. Assim que a tarefa foi acionada, pode utilizar o seguinte código para controlar o trabalho para a conclusão. Não é obrigatório para adicionar este código para a execução de tarefa.

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
    Eis uma captura de ecrã do exemplo de código completo utilizado para acionar a tarefa com o .NET.

    ![O trecho de código completo de código para acionar uma tarefa de .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Passos Seguintes

[Use o StorSimple Data Manager interface de Usuário para transformar os seus dados](storsimple-data-manager-ui.md).
