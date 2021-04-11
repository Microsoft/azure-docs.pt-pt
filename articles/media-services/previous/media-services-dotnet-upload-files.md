---
title: Faça o upload de ficheiros numa conta de Serviços de Mídia utilizando .NET | Microsoft Docs
description: Saiba como obter conteúdo sonoro nos Media Services criando e carregando ativos usando .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ee9bcc6861245ce40c43570126445a3613cd2744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013733"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Carregar ficheiros para uma conta dos Serviços de Multimédia com .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Nos Media Services, pode carregar (ou inserir) os seus ficheiros digitais num elemento. A entidade **Do Ativo** pode conter vídeo, áudio, imagens, recolhas de miniaturas, faixas de texto e ficheiros de legendas fechados (e os metadados sobre estes ficheiros.)  Uma vez que os ficheiros são carregados, o seu conteúdo é armazenado de forma segura na nuvem para posterior processamento e streaming.

Os ficheiros no elemento são denominados **Ficheiros de Elemento**. A instância **do AssetFile** e o ficheiro de mídia real são dois objetos distintos. A instância Do Ficheiro Do Ativo contém metadados sobre o ficheiro de mídia, enquanto o ficheiro de mídia contém o conteúdo real dos meios de comunicação.

## <a name="considerations"></a>Considerações

As seguintes considerações são aplicáveis:
 
 * Os Serviços de Comunicação Social utilizam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esta razão, não é permitido codificar por cento. O valor da propriedade **Name** não pode ter nenhum dos [seguintes caracteres reservados por cento:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'(;:@&=+$,/?%#]". Além disso, só pode haver um ''' para a extensão do nome do ficheiro.
* O comprimento do nome não deve ser superior a 260 caracteres.
* Existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos Serviços de Multimédia. Veja [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre as limitações relativas aos tamanhos de ficheiros.
* Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Quando criar ativos, pode especificar as seguintes opções de encriptação:

* **Nenhum** - Não é utilizada qualquer encriptação. Este é o valor predefinido. Ao utilizar esta opção, o seu conteúdo não está protegido em trânsito ou em repouso no armazenamento.
  Se pretender entregar um MP4 utilizando o download progressivo, utilize esta opção: 
* **CommonEncryption** - Utilize esta opção se estiver a carregar conteúdo que já tenha sido encriptado e protegido com Encriptação Comum ou PlayReady DRM (por exemplo, Streaming Suave protegido com PlayReady DRM).
* **EnvelopeEncrypted** – Use esta opção se estiver a carregar o HLS encriptado com AES. Tenha em atenção que os ficheiros têm de ser codificados e encriptados pelo Gestor de Transformação.
* **ArmazenamentoEncrypted** - Encripta o seu conteúdo claro localmente usando encriptação de bitS-256 e, em seguida, envia-o para o Azure Storage onde é armazenado encriptado em repouso. Os elementos protegidos com Encriptação do Storage são desencriptados automaticamente e colocados num sistema de ficheiros encriptados antes da codificação, sendo opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal caso de utilização da Encriptação do Storage ocorre quando pretende proteger os seus ficheiros de multimédia de entrada de alta qualidade inativos no disco com uma encriptação forte.
  
    Os Serviços de Mídia fornecem encriptação de armazenamento em disco para os seus ativos, não over-the-wire como Digital Rights Manager (DRM).
  
    Se o seu elemento estiver armazenamento encriptado, deve configurar a política de distribuição do elemento. Para obter mais informações, consulte [a política de entrega de ativos configurado.](media-services-dotnet-configure-asset-delivery-policy.md)

Se especificar para que o seu ativo seja encriptado com uma opção **CommonEncrypted** ou uma opção **EnvelopeEncrypted,** tem de associar o seu ativo a uma **Chave de Conteúdo**. Para mais informações, consulte [Como criar um ContentKey](media-services-dotnet-create-contentkey.md). 

Se especificar para o seu ativo ser encriptado com uma opção **StorageEncrypted,** o Media Services SDK for .NET cria uma **StorageEncrypted** **ContentKey** para o seu ativo.

Este artigo mostra como utilizar os Serviços de Comunicação Social .NET SDK, bem como extensões de Media Services .NET SDK para enviar ficheiros para um ativo dos Media Services.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Faça upload de um único ficheiro com Serviços de Media .NET SDK

O seguinte código utiliza .NET para carregar um único ficheiro. A AccessPolicy e o Localizador são criados e destruídos pela função Upload. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Faça upload de vários ficheiros com Serviços de Mídia .NET SDK
O código que se segue mostra como criar um ativo e carregar vários ficheiros.

O código faz o seguinte:

* Cria um ativo vazio utilizando o método CreateEmptyAsset definido no passo anterior.
* Cria uma instância **AccessPolicy** que define as permissões e duração do acesso ao ativo.
* Cria uma instância **localizadora** que dá acesso ao ativo.
* Cria um **caso BlobTransferClient.** Este tipo representa um cliente que opera nas bolhas Azure. Neste exemplo, o cliente monitoriza o progresso do upload. 
* Enumera através de ficheiros no diretório especificado e cria uma instância **AssetFile** para cada ficheiro.
* Envia os ficheiros para os Serviços de Comunicação usando o método **UploadAsync.** 

> [!NOTE]
> Utilize o método UploadAsync para garantir que as chamadas não estão a bloquear e que os ficheiros são carregados em paralelo.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Ao carregar um grande número de ativos, considere o seguinte:

* Crie um novo objeto **CloudMediaContext** por fio. A classe **CloudMediaContext** não é segura.
* Aumentar NúmeroOfConcurrentTransfers do valor padrão de 2 para um valor mais elevado como 5. A definição desta propriedade afeta todas as instâncias do **CloudMediaContext**. 
* Mantenha a ParallelTransferThreadCount no valor predefinido de 10.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Ingerir Ativos em Massa utilizando serviços de mídia .NET SDK
Carregar grandes ficheiros de ativos pode ser um estrangulamento durante a criação de ativos. Ingerir Ativos em Massa ou "Ingestão a Granel", envolve dissociar a criação de ativos a partir do processo de upload. Para utilizar uma abordagem de ingestão a granel, crie um manifesto (IngestManifest) que descreva o ativo e os seus ficheiros associados. Em seguida, utilize o método de upload da sua escolha para enviar os ficheiros associados para o recipiente blob do manifesto. A Microsoft Azure Media Services observa o recipiente blob associado ao manifesto. Uma vez que um ficheiro é carregado para o recipiente blob, a Microsoft Azure Media Services completa a criação de ativos com base na configuração do ativo no manifesto (IngestManifestAsset).

Para criar um novo IngestManifest, chame o método Criar exposto pela coleção IngestManifests no CloudMediaContext. Este método cria uma nova IngestManifest com o nome manifesto que fornece.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Crie os ativos associados à ingestmanifest a granel. Configure as opções de encriptação desejadas no ativo para ingerir a granel.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Um IngestManifestAsset associa um Ativo a um IngestManifest a granel para ingerir a granel. Também associa os Ficheiros Ativos que compõem cada Ativo. Para criar um IngestManifestAsset, utilize o método Criar no contexto do servidor.

O exemplo a seguir demonstra a adição de dois novos IngestManifestAssets que associam os dois ativos anteriormente criados ao manifesto de ingestão a granel. Cada IngestManifestAsset também associa um conjunto de ficheiros que são carregados para cada ativo durante a ingestão a granel.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Você pode usar qualquer aplicação de cliente de alta velocidade capaz de enviar os ficheiros de ativos para o recipiente de armazenamento blob URI fornecido pela propriedade **IIngestManifest.BlobStorageUriForUpload** da IngestManifest. 

O código que se segue mostra como utilizar .NET SDK para fazer o upload dos ficheiros ativos.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

O código para o upload dos ficheiros de ativos para a amostra utilizada neste artigo é mostrado no seguinte exemplo de código:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Você pode determinar o progresso da ingestão a granel para todos os ativos associados a um **IngestManifest,** votando a propriedade estatística do **IngestManifest.** Para atualizar as informações de progresso, deve utilizar um novo **CloudMediaContext** cada vez que pesquisar a propriedade Estatísticas.

O exemplo que se segue demonstra a sondagem de um IngestManifest pelo seu **Id.**

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Carregar ficheiros utilizando extensões .NET SDK
O exemplo a seguir mostra como carregar um único ficheiro utilizando extensões .NET SDK. Neste caso, o método **CreateFromFile** é utilizado, mas a versão assíncronea também está disponível **(CreateFromFileAsync).** O método **CreateFromFile** permite especificar o nome do ficheiro, a opção de encriptação e uma chamada de retorno para relatar o progresso do upload do ficheiro.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

O exemplo a seguir chama a função UploadFile e especifica a encriptação de armazenamento como a opção de criação de ativos.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Passos seguintes

Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)

Também pode utilizar as Funções do Azure para acionar uma tarefa de codificação num ficheiro que esteja a chegar ao contentor configurado. Para obter mais informações, veja [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passo seguinte
Agora que fez o upload de um ativo para os Media Services, vá ao artigo [Como Obter um Processador de Media.][How to Get a Media Processor]

[How to Get a Media Processor]: media-services-get-media-processor.md
