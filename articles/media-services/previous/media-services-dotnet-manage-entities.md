---
title: Gestão de Ativos e Entidades Relacionadas com Serviços de Mídia .NET SDK
description: Saiba como gerir ativos e entidades relacionadas com o Media Services SDK para .NET.
author: IngridAtMicrosoft
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
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b859ff49edb495be15cdf76719c3aaaf1ac72a6e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013776"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Gestão de Ativos e Entidades Relacionadas com Serviços de Mídia .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Este tópico mostra como gerir as entidades Azure Media Services com .NET.

A partir de 1 de abril de 2017, qualquer Registo de tarefa na sua conta com mais de 90 dias será eliminado automaticamente, juntamente com os seus registos de Tarefas associados, mesmo se o número total de registos for inferior à quota máxima. Por exemplo, no dia 1 de abril de 2017, qualquer registo de Emprego na sua conta com mais de 31 de dezembro de 2016 será automaticamente eliminado. Se precisar de arquivar as informações de trabalho/tarefa, pode utilizar o código descrito neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

## <a name="get-an-asset-reference"></a>Obtenha uma referência de ativo
Uma tarefa frequente é obter uma referência a um ativo existente nos Serviços de Media. O exemplo de código que se segue mostra como pode obter uma referência de ativo a partir da recolha de Ativos no objeto de contexto do servidor, com base num Id de ativo. O exemplo de código a seguir utiliza uma consulta Linq para obter uma referência a um objeto IAsset existente.

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

## <a name="list-all-assets"></a>Listar todos os ativos
À medida que o número de ativos que tem em armazenamento cresce, é útil listar os seus ativos. O exemplo de código que se segue mostra como iteração através da recolha de Ativos no objeto de contexto do servidor. A cada ativo, o exemplo de código também escreve alguns dos seus valores de propriedade para a consola. Por exemplo, cada ativo pode conter muitos ficheiros de mídia. O exemplo de código escreve todos os ficheiros associados a cada ativo.

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

## <a name="get-a-job-reference"></a>Obtenha uma referência de trabalho

Quando trabalha com tarefas de processamento no código dos Serviços de Comunicação Social, muitas vezes precisa de obter uma referência a um trabalho existente baseado num ID. O seguinte exemplo de código mostra como obter uma referência a um objeto IJob da coleção Jobs.

Pode precisar de obter uma referência de emprego ao iniciar um trabalho de codificação de longa duração, e precisa verificar o estado do trabalho por um fio. Em casos como este, quando o método regressa de um fio, você precisa recuperar uma referência renovada a um trabalho.

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

## <a name="list-jobs-and-assets"></a>Lista de Empregos e Ativos
Uma tarefa importante relacionada é listar os ativos com o seu trabalho associado nos Serviços de Media. O exemplo de código que se segue mostra como listar cada objeto IJob e, em seguida, para cada trabalho, apresenta propriedades sobre o trabalho, todas as tarefas relacionadas, todos os ativos de entrada e todos os ativos de saída. O código neste exemplo pode ser útil para muitas outras tarefas. Por exemplo, se pretender listar os ativos de produção de um ou mais postos de trabalho codificadores que executou anteriormente, este código mostra como aceder aos ativos de saída. Quando tem uma referência a um ativo de saída, pode então entregar o conteúdo a outros utilizadores ou aplicações, descarregando-o ou fornecendo URLs. 

Para obter mais informações sobre as opções de entrega de ativos, consulte [Entregar Ativos com o SDK dos Serviços de Mídia para .NET](media-services-deliver-streaming-content.md).

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
Nos Serviços de Comunicação Social, pode definir uma política de acesso a um ativo ou aos seus ficheiros. Uma política de acesso define as permissões para um ficheiro ou um ativo (que tipo de acesso e duração). No seu código de Serviços de Comunicação, normalmente define uma política de acesso criando um objeto IAccessPolicy e associando-o a um ativo existente. Em seguida, cria um objeto ILocator, que lhe permite fornecer acesso direto a ativos nos Serviços de Media. O projeto Visual Studio que acompanha esta série de documentação contém vários exemplos de código que mostram como criar e atribuir políticas de acesso e localizadores a ativos.

O exemplo de código que se segue mostra como listar todas as políticas de acesso no servidor e mostra o tipo de permissões associadas a cada uma delas. Outra forma útil de visualizar as políticas de acesso é listar todos os objetos ILocator no servidor e, em seguida, para cada localizador, pode listar a sua política de acesso associada utilizando a sua propriedade AccessPolicy.

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
    
## <a name="limit-access-policies"></a>Políticas de acesso limitado 

>[!NOTE]
> Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). 

Por exemplo, pode criar um conjunto genérico de políticas com o seguinte código que só seria executado uma vez na sua aplicação. Pode registar IDs num ficheiro de registo para utilização posterior:

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

Em seguida, pode usar as iDs existentes no seu código assim:

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

## <a name="list-all-locators"></a>Listar todos os localizadores
Um localizador é um URL que fornece um caminho direto para aceder a um ativo, juntamente com permissões ao ativo, tal como definidas pela política de acesso associada do localizador. Cada ativo pode ter uma coleção de objetos ILocator associados a ele na sua propriedade localizadors. O contexto do servidor também tem uma coleção de Localizadores que contém todos os localizadores.

O exemplo de código a seguir lista todos os localizadores do servidor. Para cada localizador, mostra o ID para o ativo relacionado e a política de acesso. Também exibe o tipo de permissões, a data de validade e o caminho completo para o ativo.

Note que um caminho localizador para um ativo é apenas um URL base para o ativo. Para criar um caminho direto para ficheiros individuais que um utilizador ou aplicação possa navegar, o seu código deve adicionar o caminho de ficheiro específico à trajetória do localizador. Para obter mais informações sobre como fazê-lo, consulte o tópico [Entregar Ativos com o Serviço de Mídia SDK para .NET](media-services-deliver-streaming-content.md).

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

## <a name="enumerating-through-large-collections-of-entities"></a>Enumeração através de grandes coleções de entidades
Ao consultar entidades, há um limite de 1000 entidades devolvidas de uma só vez porque o REST v2 público limita os resultados da consulta a 1000 resultados. Você precisa usar Skip and Take ao enumerar através de grandes coleções de entidades. 

A função seguinte passa por todos os postos de trabalho na Conta de Serviços de Mídia fornecida. A Media Services devolve 1000 postos de trabalho na Jobs Collection. A função faz uso de Skip and Take para garantir que todos os empregos são enumerados (caso tenha mais de 1000 empregos na sua conta).

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

## <a name="delete-an-asset"></a>Excluir um Ativo
O exemplo a seguir elimina um ativo.

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

## <a name="delete-a-job"></a>Excluir um Trabalho
Para apagar um trabalho, você deve verificar o estado do trabalho como indicado na propriedade do Estado. Os postos de trabalho que estejam terminados ou cancelados podem ser eliminados, enquanto os postos de trabalho que se encontram noutros Estados, como a fila, o programado ou o processamento, devem ser primeiro cancelados e, em seguida, podem ser eliminados.

O exemplo de código que se segue mostra um método para eliminar um emprego verificando estados de trabalho e, em seguida, eliminando quando o Estado está terminado ou cancelado. Este código depende da secção anterior neste tópico para obter uma referência a um trabalho: Obter uma referência de trabalho.

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


## <a name="delete-an-access-policy"></a>Eliminar uma Política de Acesso
O exemplo de código que se segue mostra como obter uma referência a uma política de acesso baseada numa identificação de política e, em seguida, eliminar a política.

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
