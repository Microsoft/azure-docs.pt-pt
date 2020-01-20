---
title: Usar o SDK do .NET para trabalhos do Microsoft Azure StorSimple Gerenciador de Dados
description: Saiba como usar o SDK do .NET para iniciar trabalhos de Gerenciador de Dados do StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b7cf1d3b9d4a9d751348c4792f904062b00ac104
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76270729"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Usar o SDK do .NET para iniciar a transformação de dados

## <a name="overview"></a>Visão geral

Este artigo explica como você pode usar o recurso de transformação de dados dentro do serviço de Gerenciador de Dados do StorSimple para transformar dados do dispositivo StorSimple. Os dados transformados são então consumidos por outros serviços do Azure na nuvem.

Você pode iniciar um trabalho de transformação de dados de duas maneiras:

- Utilizar o .NET SDK
- Usar o runbook de automação do Azure
 
  Este artigo fornece detalhes sobre como criar um aplicativo de console .NET de exemplo para iniciar um trabalho de transformação de dados e, em seguida, rastreá-lo para conclusão. Para saber mais sobre como iniciar a transformação de dados via automação, acesse [usar o runbook de automação do Azure para disparar trabalhos de transformação de dados](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:
*   Um computador executando:

    - Visual Studio 2012, 2013, 2015 ou 2017.

    - Azure PowerShell. [Baixe o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Uma definição de trabalho configurada corretamente no Gerenciador de Dados do StorSimple dentro de um grupo de recursos.
*   Todas as DLLs necessárias. Baixe essas DLLs do [repositório GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) script do repositório github.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

Execute as etapas a seguir para usar o .NET para iniciar um trabalho de transformação de dados.

1. Para recuperar os parâmetros de configuração, execute as seguintes etapas:
    1. Baixe o `Get-ConfigurationParams.ps1` do script de repositório GitHub no local `C:\DataTransformation`.
    1. Execute o script `Get-ConfigurationParams.ps1` no repositório GitHub. Escreva o seguinte comando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Você pode passar qualquer valor para o ActiveDirectoryKey e o AppName.

2. Esse script gera os seguintes valores:
    * ID de Cliente
    * ID do inquilino
    * Chave de Active Directory (igual à que foi inserida acima)
    * ID de Subscrição

        ![Saída de script de parâmetros de configuração](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Usando o Visual Studio 2012, 2013 ou 2015, crie C# um aplicativo de console .net.

    1. Inicie o **Visual Studio 2012/2013/2015**.
    1. Selecione **Ficheiro > Novo > Projeto**.

        ![Criar um projeto 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Selecione **modelos de > instalados > C# aplicativo de Console do Visual >** .
    3. Digite **DataTransformationApp** para o **nome**.
    4. Selecione **C:\DataTransformation** para o **local**.
    6. Clique em **OK** para criar o projeto.

        ![Criar um projeto 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Agora, adicione todas as DLLs presentes na [pasta DLLs](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) como **referências** no projeto que você criou. Para adicionar os arquivos dll, execute o seguinte:

   1. No Visual Studio, acesse **exibir > Gerenciador de soluções**.
   2. Clique na seta à esquerda do projeto de aplicativo de transformação de dados. Clique em **referências** e clique com o botão direito do mouse para **Adicionar referência**.
    
       ![Adicionar DLLs 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Navegue até o local da pasta pacotes, selecione todas as DLLs e clique em **Adicionar**e, em seguida, clique em **OK**.

       ![Adicionar DLLs 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Adicione o seguinte ao **utilizar** instruções para o ficheiro de origem (Program.cs) no projeto.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. O código a seguir inicializa a instância do trabalho de transformação de dados. Adicione isso no **método Main**. Substitua os valores dos parâmetros de configuração, conforme obtido anteriormente. Conecte os valores do **nome do grupo de recursos** e **resourceName**. O **ResourceGroupName** é associado ao Gerenciador de dados do StorSimple no qual a definição de trabalho foi configurada. O **resourceName** é o nome do seu serviço de Gerenciador de dados do StorSimple.

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
   
7. Especifique os parâmetros com os quais a definição de trabalho precisa ser executada

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    OR

    Se você quiser alterar os parâmetros de definição de trabalho durante o tempo de execução, adicione o seguinte código:

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

8. Após a inicialização, adicione o código a seguir para disparar um trabalho de transformação de dados na definição do trabalho. Conecte o nome de **definição de trabalho**apropriado.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Depois que o código for colado, Compile a solução. Aqui está uma captura de tela do trecho de código para inicializar a instância do trabalho de transformação de dados.

   ![Trecho de código para inicializar o trabalho de transformação de dados](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Esse trabalho transforma os dados que correspondem ao diretório raiz e aos filtros de arquivo no volume do StorSimple e os coloca no contêiner/compartilhamento de arquivo especificado. Quando um arquivo é transformado, uma mensagem é adicionada a uma fila de armazenamento (na mesma conta de armazenamento que o contêiner/compartilhamento de arquivos) com o mesmo nome que a definição de trabalho. Essa mensagem pode ser usada como um gatilho para iniciar qualquer processamento adicional do arquivo.

10. Depois que o trabalho for disparado, você poderá usar o código a seguir para acompanhar o trabalho para conclusão. Não é obrigatório adicionar esse código para a execução do trabalho.

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
    Aqui está uma captura de tela de todo o exemplo de código usado para disparar o trabalho usando o .NET.

    ![Trecho de código completo para disparar um trabalho .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Passos seguintes

[Use Gerenciador de dados do StorSimple interface do usuário para transformar seus dados](storsimple-data-manager-ui.md).
