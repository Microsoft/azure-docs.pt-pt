---
title: Implementar o streaming de failover com a Azure Media Services Microsoft Docs
description: Este artigo mostra como implementar um cenário de streaming falhado com a Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 68cd107b2606643d712c4de94b6d1a82e8ee614a
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657265"
---
# <a name="implement-failover-streaming-with-media-services-v2"></a>Implementar streaming de failover com Serviços de Media v2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este walkthrough demonstra como copiar conteúdo (bolhas) de um ativo para outro, a fim de lidar com a redundância para o streaming a pedido. Este cenário é útil se pretender configurar a Rede de Entrega de Conteúdos Azure para falhar entre dois centros de dados, em caso de uma falha num centro de dados. Este walkthrough utiliza o Azure Media Services SDK, o Azure Media Services REST API e o Azure Storage SDK para demonstrar as seguintes tarefas:

1. Crie uma conta de Serviços de Comunicação em "Data Center A".
2. Faça o upload de um ficheiro mezanino para um ativo de origem.
3. Codificar o ativo em ficheiros MP4 de taxa multi-bit. 
4. Crie um localizador de assinatura de acesso partilhado apenas para leitura. Isto é para que o ativo de origem tenha lido o acesso ao contentor na conta de armazenamento que está associada ao ativo de origem.
5. Obtenha o nome do dado de origem do localizador de assinatura de acesso partilhado apenas de leitura criado no passo anterior. Isto é necessário para copiar bolhas entre contas de armazenamento (explicadas mais tarde no tópico.)
6. Crie um localizador de origem para o ativo que foi criado pela tarefa de codificação. 

Então, para lidar com a falha:

1. Crie uma conta de Serviços de Comunicação em "Data Center B".
2. Crie um ativo vazio alvo na conta-alvo dos Media Services.
3. Crie um localizador de assinatura de acesso partilhado por escrito. Isto é para que o ativo vazio alvo tenha acesso ao recipiente na conta de armazenamento alvo que está associado ao ativo alvo.
4. Utilize o SDK de armazenamento Azure para copiar bolhas (ficheiros de ativos) entre a conta de armazenamento de origem em "Data Center A" e a conta de armazenamento alvo em "Data Center B". Estas contas de armazenamento estão associadas aos ativos de juros.
5. Associar blobs (ficheiros de ativos) que foram copiados para o recipiente de bolha-alvo com o ativo alvo. 
6. Crie um localizador de origem para o ativo em "Data Center B", e especifique o ID localizador que foi gerado para o ativo em "Data Center A".

Isto dá-lhe os URLs de streaming onde os caminhos relativos dos URLs são os mesmos (apenas os URLs de base são diferentes). 

Em seguida, para lidar com eventuais interrupções, pode criar uma Rede de Entrega de Conteúdos em cima destes localizadores de origem. 

As seguintes considerações são aplicáveis:

* A versão atual da Media Services SDK não suporta informações de geração programática de IAssetFile que associem um ativo a ficheiros de ativos. Em vez disso, utilize a API de Serviços de Mídia CreateFileInfos REST para o fazer. 
* Os ativos encriptados de armazenamento (AssetCreationOptions.StorageEncrypted) não são suportados para replicação (porque a chave de encriptação é diferente em ambas as contas de Serviços de Mídia). 
* Se quiser aproveitar a embalagem dinâmica, certifique-se de que o ponto final de streaming a partir do qual pretende transmitir o seu conteúdo está no estado **de Funcionamento.**

## <a name="prerequisites"></a>Pré-requisitos

* Duas contas de Serviços de Mídia numa subscrição Azure nova ou existente. Ver [Como Criar uma Conta de Serviços de Mídia.](media-services-portal-create-account.md)
* Sistema operativo: Windows 7, Windows 2008 R2 ou Windows 8.
* .NET Quadro 4.5 ou .NET Quadro 4.
* Visual Studio 2010 SP1 ou versão posterior (Profissional, Premium, Ultimate ou Express).

## <a name="set-up-your-project"></a>Configurar o seu projeto

Nesta secção, cria-se e cria um projeto de Aplicação para consolas C.

1. Utilize o Visual Studio para criar uma nova solução que contenha o projeto C# Console Application. **Insira HandleRedundancyForOnDemandStreaming** para o nome e, em seguida, clique **em OK**.
2. Crie a pasta **SupportFiles** no mesmo nível que o ficheiro do projeto **HandleRedundancyForOnDemandStreaming.csproj.** Sob a pasta **SupportFiles,** crie as **pastas Desaquicha** e **MP4Files.** Copie um ficheiro .mp4 na pasta **MP4Files.** (Neste exemplo, é utilizado o **ficheiroignite.mp4.)** 
3. Utilize **o NuGet** para adicionar referências a DLLs relacionados com os Serviços de Mídia. No **Menu Principal do Estúdio Visual**, selecione **TOOLS**  >  **NuGet Package Manager** Package Manager  >  **Consola**. Na janela da consola, **escreva o Pacote de Instalação windowsazure.mediaservices** e prima Enter.
4. Adicione outras referências necessárias para este projeto: System.Runtime.Serialization e System.Web.
5. Substitua **a utilização de** declarações adicionadas ao ficheiro **Programs.cs** por predefinição com as seguintes:

```csharp
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Runtime.Serialization.Json;
```

## <a name="add-code-that-handles-redundancy-for-on-demand-streaming"></a>Adicionar código que lida com a redundância para o streaming a pedido

Nesta secção, cria-se a capacidade de lidar com a redundância.

1. Adicione os seguintes campos de nível de classe à classe Programa.

    ```csharp
    private static readonly string storageNameTarget = "amsstorageacct2";
    private static readonly string storageKeyTarget = "00000000000000000000000000000000000000000000000000000000000000000000000000000000000000==";
    private static readonly string storageNameSource = "amsstorageacct1";
    private static readonly string storageKeySource = "00000000000000000000000000000000000000000000000000000000000000000000000000000000000000==";

    private static readonly string sourceApiServer = "https://amsacct1.restv2.westus2-2.media.azure.net/api/";
    private static readonly string targetApiServer = "https://amsacct2.restv2.eastus.media.azure.net/api/";
    private static string tokenSPSource = null;
    private static string tokenSPTarget = null;

    private static CloudMediaContext contextSource = null;
    private static CloudMediaContext contextTarget = null;

    // Base support files path.  Update this field to point to the base path  
    // for the local support files folder that you create. 
    private static readonly string SupportFiles = Path.GetFullPath(@"../..\SupportFiles");

    // Paths to support files (within the above base path). 
    private static readonly string SingleInputMp4Path = Path.GetFullPath(SupportFiles + @"\MP4Files\ignite.mp4");
    private static readonly string OutputFilesFolder = Path.GetFullPath(SupportFiles + @"\OutputFiles");
    ```
2. Substitua a definição do método principal predefinido pela seguinte. As definições de método que são chamadas de Main são definidas abaixo.
        
    ```csharp
    static void Main(string[] args)
    {
        AzureAdTokenCredentials tokenCredentialsForAcct1 =
         new AzureAdTokenCredentials("microsoft.onmicrosoft.com",
             new AzureAdClientSymmetricKey("00000000-0000-0000-0000-000000000000", "00000000000000000000000000000000"),
             AzureEnvironments.AzureCloudEnvironment);

        AzureAdTokenCredentials tokenCredentialsForAcct2 =
        new AzureAdTokenCredentials("microsoft.onmicrosoft.com",
             new AzureAdClientSymmetricKey("00000000-0000-0000-0000-000000000000", "00000000000000000000000000000000"),
             AzureEnvironments.AzureCloudEnvironment);

        var tokenProviderSource = new AzureAdTokenProvider(tokenCredentialsForAcct1);
        var tokenProviderTarget = new AzureAdTokenProvider(tokenCredentialsForAcct2);
        try
        {
        tokenSPSource = tokenProviderSource.GetAccessToken().Item1.ToString();
        tokenSPTarget = tokenProviderTarget.GetAccessToken().Item1.ToString();

        contextSource = new CloudMediaContext(new Uri(sourceApiServer), tokenProviderSource);
        contextTarget = new CloudMediaContext(new Uri(targetApiServer), tokenProviderTarget);


        IAsset assetSingleFile = CreateAssetAndUploadSingleFile(contextSource,
                         AssetCreationOptions.None,
                         SingleInputMp4Path);

         IJob job = CreateEncodingJob(contextSource, assetSingleFile);

        if (job.State != JobState.Error)
        {
            IAsset sourceOutputAsset = job.OutputMediaAssets[0];
            // Get the locator for Smooth Streaming
            var sourceOriginLocator = GetStreamingOriginLocator(contextSource, sourceOutputAsset);

            Console.WriteLine("Locator Id: {0}", sourceOriginLocator.Id);

            // 1.Create a read-only SAS locator for the source asset to have read access to the container in the source Storage account (associated with the source Media Services account)
            var readSasLocator = GetSasReadLocator(contextSource, sourceOutputAsset);

            // 2.Get the container name of the source asset from the read-only SAS locator created in the previous step
            string containerName = (new Uri(readSasLocator.Path)).Segments[1];

            // 3.Create a target empty asset in the target Media Services account
            var targetAsset = CreateTargetEmptyAsset(contextTarget, containerName);

            // 4.Create a write SAS locator for the target empty asset to have write access to the container in the target Storage account (associated with the target Media Services account)
            ILocator writeSasLocator = CreateSasWriteLocator(contextTarget, targetAsset);

            // Get asset container name.
            string targetContainerName = (new Uri(writeSasLocator.Path)).Segments[1];
            Console.WriteLine(targetContainerName);

            // 5.Copy the blobs in the source container (source asset) to the target container (target empty asset)
            CopyBlobsFromDifferentStorage(containerName, targetContainerName, storageNameSource, storageKeySource, storageNameTarget, storageKeyTarget);

            // 6.Use the CreateFileInfos Media Services REST API to automatically generate all the IAssetFile’s for the target asset. 
            //      This API call is not supported in the current Media Services SDK for .NET. 
            CreateFileInfos(targetApiServer, tokenSPTarget, targetAsset.Id);
            // Check if the AssetFiles are now  associated with the asset.
            Console.WriteLine("Asset files associated with the {0} asset:", targetAsset.Name);
            foreach (var af in targetAsset.AssetFiles)
            {
            Console.WriteLine(af.Name);
            }

            // 7.Copy the Origin locator of the source asset to the target asset by using the same Id
            var replicatedLocatorPath = CreateTargetOriginLocatorWithRest(contextSource, contextTarget, sourceOriginLocator.Id, targetAsset.Id);

            // Create a full URL to the manifest file. Use this for playback
            // in streaming media clients. 
            string originalUrlForClientStreaming = sourceOriginLocator.Path + GetPrimaryFile(sourceOutputAsset).Name + "/manifest";

            Console.WriteLine("Original Locator Path: {0}\n", originalUrlForClientStreaming);

            string replicatedUrlForClientStreaming = replicatedLocatorPath + GetPrimaryFile(sourceOutputAsset).Name + "/manifest";

            Console.WriteLine("Replicated Locator Path: {0}", replicatedUrlForClientStreaming);

            readSasLocator.Delete();
            writeSasLocator.Delete();
        }

        }
        catch (Exception e)
        {
        Console.WriteLine("Exception:" + e.ToString());
        }
    }
    ```
3. As seguintes definições de método são chamadas de Main. Consulte os comentários para mais detalhes sobre cada método.

    >[!NOTE]
    >Existe um limite de 1.000.000 políticas para diferentes políticas de Serviços de Comunicação (por exemplo, para a política de Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver sempre a utilizar os mesmos dias e permissões de acesso. Por exemplo, utilize o mesmo ID para políticas para localizadores que se destinem a permanecer no lugar por um longo período de tempo (políticas de não carregamento). Para mais informações, consulte [este tópico.](media-services-dotnet-manage-entities.md#limit-access-policies)

    ```csharp
    public static IAsset CreateAssetAndUploadSingleFile(CloudMediaContext context,
                         AssetCreationOptions assetCreationOptions,
                         string singleFilePath)
    {
        var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();

        var asset = context.Assets.Create(assetName, assetCreationOptions);

        Console.WriteLine("Asset name: " + asset.Name);

        var fileName = Path.GetFileName(singleFilePath);

        var assetFile = asset.AssetFiles.Create(fileName);

        Console.WriteLine("Created assetFile {0}", assetFile.Name);

        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0}", assetFile.Name);

        return asset;
    }

    public static IJob CreateEncodingJob(CloudMediaContext context, IAsset asset)
    {
        // Declare a new job.
        IJob job = context.Jobs.Create("My encoding job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName(context,
                            "Media Encoder Standard");

        // Create a task with the encoding details, using a string preset.
        // In this case "Adaptive Streaming" preset is used.
        ITask task = job.Tasks.AddNew("My encoding task",
        processor,
        "Adaptive Streaming",
        TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is in the clear (unencrypted). 
        var outputAssetName = "OutputAsset_" + Guid.NewGuid();
        task.OutputAssets.AddNew(outputAssetName,
        AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-{JobId}.txt file 
        // in your output folder.
        LogJobDetails(context, job.Id);

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // Get an updated job reference.
        job = context.Jobs.Where(j => j.Id == job.Id).FirstOrDefault();
        // Since we the output asset contains a set of Smooth Streaming files,
        // set the .ism file to be the primary file
        if (job.State != JobState.Error)
        SetPrimaryFile(job.OutputMediaAssets[0]);

        return job;
    }

    public static ILocator GetStreamingOriginLocator(CloudMediaContext context, IAsset assetToStream)
    {
        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        IAssetFile manifestFile = GetPrimaryFile(assetToStream);

        // Create a 30-day readonly access policy. 
        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            

        IAccessPolicy policy = context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = context.Locators.CreateLocator(LocatorType.OnDemandOrigin,
        assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));

        // Return the locator. 
        return originLocator;
    }

    public static ILocator GetSasReadLocator(CloudMediaContext context, IAsset asset)
    {
        IAccessPolicy accessPolicy = context.AccessPolicies.Create("File Download Policy",
        TimeSpan.FromDays(30), AccessPermissions.Read);

        ILocator sasLocator = context.Locators.CreateLocator(LocatorType.Sas,
        asset, accessPolicy);

        return sasLocator;
    }

    public static ILocator CreateSasWriteLocator(CloudMediaContext context, IAsset asset)
    {

        IAccessPolicy writePolicy = context.AccessPolicies.Create("Write Policy",
        TimeSpan.FromDays(30), AccessPermissions.Write);

        ILocator sasLocator = context.Locators.CreateLocator(LocatorType.Sas,
        asset, writePolicy);

        return sasLocator;
    }

    public static IAsset CreateTargetEmptyAsset(CloudMediaContext context, string containerName)
    {
        // Create a new asset.
        IAsset assetToBeProcessed = context.Assets.Create(containerName,
        AssetCreationOptions.None);

        return assetToBeProcessed;
    }

    public static string CreateTargetOriginLocatorWithRest(CloudMediaContext contextSource, CloudMediaContext contextTarget,  string locatorIdToReplicate, string targetAssetId)
    {
        string locatorNewPath = "";

        if (!string.IsNullOrEmpty(tokenSPTarget))
        {
        var asset = contextTarget.Assets.Where(a => a.Id == targetAssetId).FirstOrDefault();

        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
        var accessPolicy = contextTarget.AccessPolicies.Create("RestTest", TimeSpan.FromDays(100),
                                    AccessPermissions.Read);
        if (asset != null)
        {
            string redirectedServiceUri = null;

            var xmlResponse = CreateLocator(targetApiServer, out redirectedServiceUri, tokenSPTarget,
                                asset.Id, accessPolicy.Id,
                                (int)LocatorType.OnDemandOrigin,
                                DateTime.UtcNow.AddMinutes(-10), locatorIdToReplicate);

            Console.WriteLine("Redirected to: " + redirectedServiceUri);
            if (xmlResponse != null)
            {
            Console.WriteLine(String.Format("Locator Id: {0}",
                            xmlResponse.GetElementsByTagName("Id")[0].InnerText));
            Console.WriteLine(String.Format("Locator Path: {0}",
                xmlResponse.GetElementsByTagName("Path")[0].InnerText));

            locatorNewPath = xmlResponse.GetElementsByTagName("Path")[0].InnerText;
            }
        }
        }

        return locatorNewPath;
    }

    public static void SetPrimaryFile(IAsset asset)
    {
        var ismAssetFiles = asset.AssetFiles.ToList().
            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase));

        if (ismAssetFiles.Count() != 1)
        throw new ArgumentException("The asset should have only one, .ism file");

        ismAssetFiles.First().IsPrimary = true;
        ismAssetFiles.First().Update();
    }

    public static IAssetFile GetPrimaryFile(IAsset asset)
    {
        // Cast the reference to a true IAssetFile type. 
        IAssetFile theManifest = asset.AssetFiles.ToList().
            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).
            FirstOrDefault();

        return theManifest;
    }

    public static void CopyBlobsFromDifferentStorage(string sourceContainerName, string targetContainerName,
                        string srcAccountName, string srcAccountKey,
                        string destAccountName, string destAccountKey)
    {
        var srcAccount = new CloudStorageAccount(new StorageCredentials(srcAccountName, srcAccountKey), true);
        var destAccount = new CloudStorageAccount(new StorageCredentials(destAccountName, destAccountKey), true);

        var cloudBlobClient = srcAccount.CreateCloudBlobClient();
        var targetBlobClient = destAccount.CreateCloudBlobClient();

        var sourceContainer = cloudBlobClient.GetContainerReference(sourceContainerName);
        var targetContainer = targetBlobClient.GetContainerReference(targetContainerName);
        targetContainer.CreateIfNotExists();

        string blobToken = sourceContainer.GetSharedAccessSignature(new SharedAccessBlobPolicy()
        {
        // Specify the expiration time for the signature.
        SharedAccessExpiryTime = DateTime.Now.AddDays(1),
        // Specify the permissions granted by the signature.
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read
        });

        foreach (var sourceBlob in sourceContainer.ListBlobs())
        {
        string fileName = (sourceBlob as ICloudBlob).Name;
        var sourceCloudBlob = sourceContainer.GetBlockBlobReference(fileName);
        sourceCloudBlob.FetchAttributes();

        if (sourceCloudBlob.Properties.Length > 0)
        {
            // In Azure Media Services, the files are stored as block blobs. 
            // Page blobs are not supported by Azure Media Services.  
            var destinationBlob = targetContainer.GetBlockBlobReference(fileName);
            destinationBlob.StartCopy(new Uri(sourceBlob.Uri.AbsoluteUri + blobToken));

            while (true)
            {
            // The StartCopyFromBlob is an async operation, 
            // so we want to check if the copy operation is completed before proceeding. 
            // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
            destinationBlob.FetchAttributes();
            if (destinationBlob.CopyState.Status != CopyStatus.Pending)
            {
                break;
            }
            //It's still not completed. So wait for some time.
            System.Threading.Thread.Sleep(1000);
            }
        }

        Console.WriteLine(fileName);
        }

        Console.WriteLine("Done copying.");
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(CloudMediaContext context, string mediaProcessorName)
    {

        var processor = context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

    // This method is a handler for events that track job progress.   
    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);

        switch (e.CurrentState)
        {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Cast sender as a job.
            IJob job = (IJob)sender;
            // Display or log error details as needed.
            LogJobStop(null, job.Id);
            break;
        default:
            break;
        }
    }

    private static void LogJobStop(CloudMediaContext context, string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();

        builder.AppendLine("\nThe job stopped due to cancellation or an error.");
        builder.AppendLine("***************************");
        builder.AppendLine("Job ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job State: " + job.State.ToString());
        builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
        // Log job errors if they exist.  
        if (job.State == JobState.Error)
        {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
            builder.AppendLine("  Task Id: " + task.Id);
            builder.AppendLine("    Error Code: " + detail.Code);
            builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
        }
        builder.AppendLine("***************************\n");
        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobStop-{JobId}.txt
        string outputFile = OutputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }

    private static void LogJobDetails(CloudMediaContext context, string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();

        builder.AppendLine("\nJob ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());

        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobDetails-{JobId}.txt
        string outputFile = OutputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }

    // Replace ":" with "_" in Job id values so they can 
    // be used as log file names.  
    private static string JobIdAsFileName(string jobID)
    {
        return jobID.Replace(":", "_");
    }

    // Write method output to the output files folder.
    private static void WriteToFile(string outFilePath, string fileContent)
    {
        StreamWriter sr = File.CreateText(outFilePath);
        sr.WriteLine(fileContent);
        sr.Close();
    }

    //////////////////////////////////////////////////////
    /// The following methods use REST calls.
    //////////////////////////////////////////////////////
    public static XmlDocument CreateLocator(string mediaServicesApiServerUri,
                        out string redirectedMediaServicesApiServerUri,
                        string bearerToken, string assetId,
                        string accessPolicyId, int locatorType,
                        DateTime startTime, string locatorIdToReplicate = null,
                        bool autoRedirect = true)
    {
        if (string.IsNullOrEmpty(mediaServicesApiServerUri))
        {
        mediaServicesApiServerUri = "https://media.windows.net/api/";
        }
        if (!mediaServicesApiServerUri.EndsWith("/"))
        mediaServicesApiServerUri = mediaServicesApiServerUri + "/";

        if (string.IsNullOrEmpty(bearerToken)) throw new ArgumentNullException("BearerToken");
        if (string.IsNullOrEmpty(assetId)) throw new ArgumentNullException("assetId");
        if (string.IsNullOrEmpty(accessPolicyId)) throw new ArgumentNullException("accessPolicyId");

        redirectedMediaServicesApiServerUri = null;
        XmlDocument xmlResponse = null;

        StringBuilder sb = new StringBuilder();
        sb.Append("{ \"AssetId\" : \"" + assetId + "\"");
        sb.Append(", \"AccessPolicyId\" : \"" + accessPolicyId + "\"");
        sb.Append(", \"Type\" : \"" + locatorType + "\"");
        if (startTime != DateTime.MinValue)
        sb.Append(", \"StartTime\" : \"" + startTime.ToString("G", CultureInfo.CreateSpecificCulture("en-us")) + "\"");
        if (!string.IsNullOrEmpty(locatorIdToReplicate))
        sb.Append(", \"Id\" : \"" + locatorIdToReplicate + "\"");
        sb.Append("}");

        string requestbody = sb.ToString();

        try
        {
        var request = GenerateRequest("POST", mediaServicesApiServerUri, "Locators",
            null, bearerToken, requestbody);
        var response = (HttpWebResponse)request.GetResponse();

        switch (response.StatusCode)
        {
            case HttpStatusCode.MovedPermanently:
            //Recurse once with the mediaServicesApiServerUri redirect Location:
            if (autoRedirect)
            {
                redirectedMediaServicesApiServerUri = response.Headers["Location"];
                string secondRedirection = null;
                xmlResponse = CreateLocator(redirectedMediaServicesApiServerUri,
                            out secondRedirection, bearerToken,
                            assetId, accessPolicyId, locatorType,
                            startTime, locatorIdToReplicate, false);
            }
            else
            {
                Console.WriteLine("Redirection to {0} failed.",
                mediaServicesApiServerUri);
                return null;
            }
            break;
            case HttpStatusCode.Created:
            using (Stream responseStream = response.GetResponseStream())
            {
                using (StreamReader stream = new StreamReader(responseStream))
                {
                string responseString = stream.ReadToEnd();
                var reader = JsonReaderWriterFactory.
                    CreateJsonReader(Encoding.UTF8.GetBytes(responseString),
                    new XmlDictionaryReaderQuotas());

                xmlResponse = new XmlDocument();
                reader.Read();
                xmlResponse.LoadXml(reader.ReadInnerXml());
                }
            }
            break;

            default:
            Console.WriteLine(response.StatusDescription);
            break;
        }
        }
        catch (WebException ex)
        {
        Console.WriteLine(ex.Message);
        }

        return xmlResponse;
    }

    public static void CreateFileInfos(string mediaServicesApiServerUri,
                    string bearerToken,
                    string assetId
                    )
    {
        if (!mediaServicesApiServerUri.EndsWith("/"))
        mediaServicesApiServerUri = mediaServicesApiServerUri + "/";

        if (String.IsNullOrEmpty(bearerToken)) throw new ArgumentNullException("bearerToken");
        if (String.IsNullOrEmpty(assetId)) throw new ArgumentNullException("assetId");

        try
        {
        var request = GenerateRequest("GET", mediaServicesApiServerUri, "CreateFileInfos",
            String.Format(CultureInfo.InvariantCulture, "assetid='{0}'", assetId), bearerToken, null);

        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            if (response.StatusCode == HttpStatusCode.MovedPermanently)
            {
            string redirectedMediaServicesApiUrl = response.Headers["Location"];

            CreateFileInfos(redirectedMediaServicesApiUrl, bearerToken, assetId);
            }
            else if ((response.StatusCode != HttpStatusCode.OK) &&
            (response.StatusCode != HttpStatusCode.Accepted) &&
            (response.StatusCode != HttpStatusCode.Created) &&
            (response.StatusCode != HttpStatusCode.NoContent))
            {
            throw new Exception("Invalid response received ");
            }
        }
        }
        catch (WebException ex)
        {
        Console.WriteLine(ex.Message);
        }
    }

    private static HttpWebRequest GenerateRequest(string verb,
                            string mediaServicesApiServerUri,
                            string resourcePath, string query,
                            string bearerToken, string requestbody)
    {
        var uriBuilder = new UriBuilder(mediaServicesApiServerUri);
        uriBuilder.Path += resourcePath;
        if (query != null)
        {
        uriBuilder.Query = query;
        }

        HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uriBuilder.Uri);
        request.AllowAutoRedirect = false; //We manage our own redirects.
        request.Method = verb;

        if (resourcePath == "$metadata")
        request.MediaType = "application/xml";
        else
        {
        request.ContentType = "application/json;odata=verbose";
        request.Accept = "application/json;odata=verbose";
        }

        request.Headers.Add("DataServiceVersion", "3.0");
        request.Headers.Add("MaxDataServiceVersion", "3.0");
        request.Headers.Add("x-ms-version", "2.19");
        request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + bearerToken);

        if (requestbody != null)
        {
        var requestBytes = Encoding.ASCII.GetBytes(requestbody);
        request.ContentLength = requestBytes.Length;

        var requestStream = request.GetRequestStream();
        requestStream.Write(requestBytes, 0, requestBytes.Length);
        requestStream.Close();
        }
        else
        {
        request.ContentLength = 0;
        }
        return request;
    }
    ```
    
## <a name="content-protection"></a>Proteção de conteúdo

O exemplo neste tópico mostra um streaming claro. Se quiser fazer streaming protegido, há algumas outras coisas que precisa de configurar, precisa de utilizar o mesmo **AssetDeliveryPolicy**, o mesmo **ContentKeyAuthorizationPolicy** ou URL de servidor de chave externa, e precisa de duplicar as teclas de conteúdo com o mesmo identificador.

Para obter mais informações sobre a proteção de conteúdos, consulte [a encriptação dinâmica AES-128 e o serviço de entrega de chaves](media-services-protect-with-aes128.md).

## <a name="see-also"></a>Ver também

[Use webhooks Azure para monitorizar notificações de emprego dos Media Services](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="next-steps"></a>Passos seguintes

Agora pode utilizar um gestor de tráfego para encaminhar pedidos entre os dois centros de dados, e assim falhar em caso de interrupções.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

