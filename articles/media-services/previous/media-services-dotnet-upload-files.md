---
title: Carregar ficheiros para uma conta de serviços de multimédia com .NET | Documentos da Microsoft
description: Saiba como obter o conteúdo de mídia para os serviços multimédia criando e carregando ativos.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: d03a4081e0dcc164530e5ac0a0472edbddc3c727
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845518"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Carregar ficheiros para uma conta de serviços de multimédia com .NET 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 
> 

Nos Media Services, pode carregar (ou inserir) os seus ficheiros digitais num elemento. O **Asset** entidade pode conter vídeo, áudio, imagens, coleções de miniaturas, texto faixas e legendagem de áudio ficheiros (e os metadados relativos a esses ficheiros.)  Assim que os ficheiros são carregados, o seu conteúdo é armazenado em segurança na nuvem para processamento adicional e a transmissão em fluxo.

Os ficheiros no elemento são denominados **Ficheiros de Elemento**. O **AssetFile** instância e o ficheiro de multimédia real são dois objetos distintos. A instância de AssetFile contém metadados sobre o ficheiro de multimédia, ao passo que o ficheiro de suporte de dados contém o conteúdo de mídia real.

> [!NOTE]
> As seguintes considerações aplicam-se:
> 
> * Serviços de multimédia utiliza o valor da propriedade IAssetFile.Name ao criar os URLs para o conteúdo de transmissão em fluxo (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, por cento de codificação não é permitida. O valor do **Name** propriedade não pode ter qualquer um dos seguintes [carateres por cento de codificação-reservados](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # ". Além disso, só pode existir um '.' para a extensão de nome de ficheiro.
> * O comprimento do nome não deve ser superior a 260 carateres.
> * Existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos Serviços de Multimédia. Veja [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre as limitações relativas aos tamanhos de ficheiros.
> * Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.
> 

Ao criar recursos, pode especificar as seguintes opções de encriptação:

* **Nenhum** - Não é utilizada qualquer encriptação. Este é o valor predefinido. Ao utilizar esta opção não está protegido seu conteúdo em trânsito ou inativos no armazenamento.
  Se planear distribuir um MP4 utilizando uma transferência progressiva, utilize esta opção: 
* **CommonEncryption** -Utilize esta opção se estiver a carregar o conteúdo que já tenha sido encriptado e protegido com encriptação comum ou PlayReady DRM (por exemplo, transmissão em fluxo uniforme protegida com PlayReady DRM).
* **EnvelopeEncrypted** – Utilize esta opção se estiver a carregar HLS encriptado com AES. Tenha em atenção que os ficheiros têm de ser codificados e encriptados pelo Gestor de Transformação.
* **StorageEncrypted** - criptografa o seu conteúdo transparente localmente utilizando a encriptação de AES-256 bits e, em seguida, carrega-o para o armazenamento do Azure onde estão armazenados encriptados em inatividade. Os elementos protegidos com Encriptação do Storage são desencriptados automaticamente e colocados num sistema de ficheiros encriptados antes da codificação, sendo opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal caso de utilização da Encriptação do Storage ocorre quando pretende proteger os seus ficheiros de multimédia de entrada de alta qualidade inativos no disco com uma encriptação forte.
  
    Serviços de multimédia fornece encriptação de armazenamento em disco para os seus ativos, não over-the-wire, como o Gestor de direitos digitais (DRM).
  
    Se o seu elemento estiver armazenamento encriptado, é necessário configurar a política de entrega de elementos. Para obter mais informações, consulte [configurar a política de entrega de elemento](media-services-dotnet-configure-asset-delivery-policy.md).

Se especificar para o seu elemento seja encriptado com uma **CommonEncrypted** opção, ou uma **EnvelopeEncrypted** opção, precisa associar o seu elemento com um **ContentKey**. Para obter mais informações, consulte [como criar um ContentKey](media-services-dotnet-create-contentkey.md). 

Se especificar para o seu elemento seja encriptado com uma **StorageEncrypted** opção, o SDK de Media Services para .NET cria um **StorageEncrypted** **ContentKey** para o seu elemento.

Este artigo mostra como utilizar o SDK .NET dos Media Services, bem como as extensões do SDK .NET dos Media Services para carregar ficheiros para um recurso de serviços de multimédia.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Carregar um único ficheiro com o SDK .NET dos Media Services
O código a seguir usa o .NET para carregar um único ficheiro. O localizador e AccessPolicy são criados e destruído pela função de carregamento. 

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


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Carregar vários ficheiros com o SDK .NET dos Media Services
O código a seguir mostra como criar um elemento e carregar vários ficheiros.

O código faz o seguinte:

* Cria um elemento vazio com o método de CreateEmptyAsset definido no passo anterior.
* Cria um **AccessPolicy** instância que define as permissões e a duração de acesso ao elemento.
* Cria um **localizador** instância que fornece acesso ao elemento.
* Cria um **BlobTransferClient** instância. Este tipo representa um cliente que funciona nos blobs do Azure. Neste exemplo, o cliente monitoriza o progresso do carregamento. 
* Enumera os ficheiros no diretório especificado e cria um **AssetFile** instância para cada ficheiro.
* Carrega os ficheiros para os serviços de multimédia a utilizar o **UploadAsync** método. 

> [!NOTE]
> Utilize o método UploadAsync para garantir que não estão a bloquear as chamadas e os ficheiros são carregados em paralelo.
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

* Criar uma nova **CloudMediaContext** objeto por thread. O **CloudMediaContext** classe não é thread-safe.
* Aumente NumberOfConcurrentTransfers do valor predefinido de 2 para um valor maior como 5. Definir essa propriedade afeta todas as instâncias de **CloudMediaContext**. 
* Manter ParallelTransferThreadCount com o valor predefinido de 10.

## <a id="ingest_in_bulk"></a>Inserção dos elementos em massa com o SDK .NET dos Media Services
Carregar ficheiros de recursos grandes pode ser um estrangulamento durante a criação do recurso. Inserção dos elementos em massa ou "Ingestão em massa", envolve a dissociação de criação de recurso do processo de carregamento. Para utilizar uma em massa a ingestão de abordagem, crie um manifesto (IngestManifest), que descreve o elemento e seus arquivos associados. Em seguida, use o método de carregamento da sua preferência para carregar os ficheiros associados para o contentor de Blobs do manifesto. Serviços de multimédia do Microsoft Azure controla o contentor de blob associado com o manifesto. Depois de um ficheiro é carregado para o contentor de BLOBs, serviços de multimédia do Microsoft Azure conclui a criação de recursos com base na configuração de ativo no manifesto (IngestManifestAsset).

Para criar um novo IngestManifest, chame o método de criar exposto pela coleção IngestManifests no CloudMediaContext. Este método cria um novo IngestManifest com o nome do manifesto que fornecer.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Crie os recursos que estão associados a maior parte IngestManifest. Configure as opções de encriptação desejado no ativo para a ingestão em massa.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Um IngestManifestAsset associa um recurso em massa IngestManifest para a ingestão em massa. Ele também associa o AssetFiles que compõe cada ativo. Para criar um IngestManifestAsset, utilize o método Create no contexto de servidor.

O exemplo seguinte demonstra o manifesto de ingestão de adição dois IngestManifestAssets novos que associam os dois recursos que criou anteriormente para a maior parte. Cada IngestManifestAsset associa também um conjunto de ficheiros que são carregados para cada recurso durante a ingestão em massa.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Pode usar qualquer aplicativo de alta velocidade do cliente com capacidade de carregar os ficheiros de recursos para o contentor de armazenamento de BLOBs URI fornecido pelos **IIngestManifest.BlobStorageUriForUpload** propriedade do IngestManifest. 

O código seguinte mostra como utilizar o SDK de .NET para carregar os ficheiros de recursos.

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

O código para carregar os ficheiros de recursos para o exemplo utilizada neste artigo é mostrado no exemplo de código a seguir:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Pode determinar o progresso da ingestão em massa para todos os recursos associados com uma **IngestManifest** através de consultas a propriedade de estatísticas da **IngestManifest**. Para atualizar as informações de progresso, deve usar uma nova **CloudMediaContext** sempre que consultar a propriedade de estatísticas.

O exemplo seguinte demonstra a consulta uma IngestManifest pelo respetivo **Id**.

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


## <a name="upload-files-using-net-sdk-extensions"></a>Carregar ficheiros através de extensões do SDK do .NET
O exemplo seguinte mostra como carregar um único ficheiro com as extensões do SDK do .NET. Neste caso o **CreateFromFile** método é usado, mas também está disponível a versão assíncrona (**CreateFromFileAsync**). O **CreateFromFile** método permite-lhe especificar o nome de ficheiro, a opção de encriptação e um retorno de chamada para comunicar o progresso do carregamento do ficheiro.

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

O exemplo seguinte chama a função de UploadFile e especifica a encriptação de armazenamento como a opção de criação do recurso.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Passos Seguintes

Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)

Também pode utilizar as Funções do Azure para acionar uma tarefa de codificação num ficheiro que esteja a chegar ao contentor configurado. Para obter mais informações, veja [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passo seguinte
Agora que tenha carregado um recurso para os serviços de multimédia, vá para o [como obter um processador de multimédia] [ How to Get a Media Processor] artigo.

[How to Get a Media Processor]: media-services-get-media-processor.md

