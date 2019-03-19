---
title: Gerenciamento de ativos e entidades relacionadas com os serviços de multimédia .NET SDK
description: Saiba como gerir recursos e entidades relacionadas com o SDK de Media Services para .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: d2651bc47f262a45a05217585eb00cbc721c01a1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165782"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Gerenciamento de ativos e entidades relacionadas com os serviços de multimédia .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

Este tópico mostra como gerir entidades de serviços de multimédia do Azure com .NET.

>[!NOTE]
> A partir de 1 de abril de 2017, qualquer Registo de tarefa na sua conta com mais de 90 dias será eliminado automaticamente, juntamente com os seus registos de Tarefas associados, mesmo se o número total de registos for inferior à quota máxima. Por exemplo, 1 de Abril de 2017, qualquer registo de tarefa na sua conta com mais de 31 de Dezembro de 2016, serão automaticamente eliminado. Se precisar de arquivar as informações de tarefas, pode usar o código descrito neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

## <a name="get-an-asset-reference"></a>Obter uma referência de recurso
É uma tarefa frequente obter uma referência a um recurso existente nos serviços de multimédia. O exemplo de código seguinte mostra como pode obter uma referência de elemento da coleção de recursos no servidor do objeto de contexto, com base num elemento de ID. O exemplo de código seguinte utiliza uma consulta Linq para obter uma referência a um objeto de IAsset existente.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Lista de todos os recursos
À medida que cresce o número de ativos que tem no armazenamento, é útil listar os seus ativos. O exemplo de código seguinte mostra como iterar na coleção de recursos no objeto de contexto do servidor. Com cada elemento, o exemplo de código também escreve alguns dos seus valores de propriedade para a consola. Por exemplo, cada elemento pode conter muitos arquivos de suporte de dados. O exemplo de código escreve todos os ficheiros associados a cada ativo.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Obter uma referência de tarefa

Ao trabalhar com tarefas no código de serviços de multimédia de processamento, muitas vezes precisa obter uma referência a uma tarefa existente com base num ID. O exemplo de código seguinte mostra como obter uma referência a um objeto de IJob da coleção de tarefas.

Poderá ter de obter uma referência de tarefa quando iniciar uma tarefa de codificação de longa execução e tem de verificar o estado da tarefa num thread. Em casos como esse, quando o método retorna a partir de um thread, terá de obter uma referência atualizada para uma tarefa.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Lista de tarefas e ativos
É uma importante tarefa relacionada a recursos de lista com o seu trabalho associado nos serviços de multimédia. O exemplo de código seguinte mostra como listar cada objeto IJob e, em seguida, para cada tarefa, apresenta as propriedades da tarefa, todas as tarefas relacionadas com, todas as entradas de recursos e todos os recursos de saída. O código neste exemplo pode ser útil para várias outras tarefas. Por exemplo, se desejar listar os recursos de saída de um ou mais tarefas de codificação que executou anteriormente, este código mostra como acessar os recursos de saída. Quando tem uma referência para um elemento de saída, em seguida, pode distribuir os conteúdos para outros utilizadores ou aplicações por baixá-lo ou fornecer URLs. 

Para obter mais informações sobre as opções para o fornecimento de recursos, consulte [entregar recursos com o SDK de serviços de multimédia para .NET](media-services-deliver-streaming-content.md).

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Listar todas as políticas de acesso
Nos Media Services, pode definir uma política de acesso num recurso ou os respetivos ficheiros. Uma política de acesso define as permissões para um ficheiro ou um ativo (que tipo de acesso e a duração). Em seu código de serviços de multimédia, normalmente define uma política de acesso ao criar um objeto de IAccessPolicy e, em seguida, associar a um recurso existente. Em seguida, cria um objeto de ILocator, que permite fornecer acesso direto a recursos nos serviços de multimédia. O projeto do Visual Studio que acompanha esta série de documentação contém vários exemplos de código que mostram como criar e atribuir políticas de acesso e localizadores de ativos.

O exemplo de código seguinte mostra como listar todas as políticas de acesso no servidor e mostra o tipo de permissões associadas a cada um. Outra forma útil para ver as políticas de acesso é listar todos os objetos de ILocator no servidor e, em seguida, para cada localizador, pode listar a sua política de acesso associados usando sua propriedade AccessPolicy.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Políticas de acesso de limite 

>[!NOTE]
> Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). 

Por exemplo, pode criar um conjunto genérico de políticas com o seguinte código que seria executada somente uma vez na sua aplicação. Pode iniciar sessão IDs de um ficheiro de registo para utilizar mais tarde:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Em seguida, pode utilizar os IDs existentes no seu código como este:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Lista de todos os localizadores
Um localizador é um URL que fornece um caminho direto para aceder a um recurso, juntamente com permissões para o elemento, conforme definido pela política de acesso associados o localizador. Cada elemento pode ter uma coleção de objetos de ILocator associados a ele em sua propriedade de localizadores. O contexto de servidor também tem uma coleção de localizadores que contém todos os localizadores.

O exemplo de código seguinte apresenta uma lista de todos os localizadores no servidor. Para cada localizador, ela mostra o Id para a política de acesso e de ativos relacionado. Também apresenta o tipo de permissões, a data de expiração e o caminho completo para o elemento.

Tenha em atenção que um caminho de localizador para um recurso é apenas um URL de base para o elemento. Para criar um caminho direto para ficheiros individuais que um utilizador ou aplicação também poderia navegar em, seu código tem de adicionar o caminho de ficheiro específico para o caminho de localizador. Para obter mais informações sobre como fazer isso, consulte o tópico [entregar recursos com o SDK de serviços de multimédia para .NET](media-services-deliver-streaming-content.md).

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerando pelas grandes coleções de entidades
Ao consultar entidades, existe um limite de 1000 entidades retornadas, ao mesmo tempo, porque a v2 REST pública limita os resultados da consulta para resultados de 1000. Tem de utilizar Skip e Take ao enumerar por meio de grandes coleções de entidades. 

A seguinte função executa loops em todas as tarefas na conta de serviços de multimédia fornecido. Serviços de multimédia devolve 1000 tarefas na coleção de tarefas. A função usa Skip e Take para se certificar de que todas as tarefas são enumerados (no caso de ter mais de 1000 tarefas na sua conta).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Eliminar um recurso
O exemplo seguinte elimina um recurso.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Eliminar uma tarefa
Para eliminar uma tarefa, tem de verificar o estado da tarefa conforme indicado na propriedade de estado. Podem ser eliminadas a tarefas que são finalizadas ou foi canceladas, enquanto as tarefas que estão em determinados Estados, como em fila, agendada ou processamento, devem ser canceladas primeiro e, em seguida, pode ser eliminados.

O exemplo de código seguinte mostra um método para eliminar uma tarefa de verificação de Estados de tarefa e, em seguida, eliminando quando o estado é concluído ou foi cancelado. Esse código depende da secção anterior deste tópico para obter uma referência a uma tarefa: Obter uma referência de tarefa.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Eliminar uma política de acesso
O exemplo de código seguinte mostra como obter uma referência a uma política de acesso com base numa política Id e, em seguida, para eliminar a política.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

