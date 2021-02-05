---
title: Faça o upload de grandes quantidades de dados aleatórios em paralelo ao Azure Storage
description: Saiba como usar a biblioteca de clientes do Azure Storage para carregar grandes quantidades de dados aleatórios em paralelo a uma conta de Armazenamento Azure
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ed7020a58f3f15403108934bcc3fab644bd1b627
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584469"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Carregar grandes quantidades de dados aleatórios em paralelo para o armazenamento do Azure

Este tutorial é a segunda parte de uma série. Este tutorial mostra como implementar uma aplicação que carrega grandes quantidades de dados aleatórios para uma conta de armazenamento do Azure.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Configurar a cadeia de ligação
> * Criar a aplicação
> * Executar a aplicação
> * Validar o número de ligações

O Microsoft Azure Blob Storage fornece um serviço escalável para armazenar os seus dados. Para garantir que a aplicação tem o melhor desempenho possível, recomenda-se uma compreensão sobre como funciona o armazenamento de blobs. O conhecimento dos limites para as bolhas Azure é importante, para saber mais sobre estes limites de visita: [Escalabilidade e metas de desempenho para armazenamento Blob](../blobs/scalability-targets.md).

[O nome da partição](../blobs/storage-performance-checklist.md#partitioning) é outro fator potencialmente importante ao conceber uma aplicação de alto desempenho utilizando bolhas. Para tamanhos de blocos superiores ou iguais a 4 MiB, são [utilizadas bolhas de blocos de alta potência](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) e o nome da partição não impactará o desempenho. Para tamanhos de bloco inferiores a 4 MiB, o armazenamento Azure utiliza um esquema de divisórias baseado em gama para escalar e carregar o equilíbrio. Esta configuração significa que os ficheiros com as convenções de nomenclatura ou prefixos semelhantes vão para a mesma partição. Esta lógica inclui o nome do contentor para o qual os ficheiros estão a ser carregados. Neste tutorial, utilize os ficheiros que tenham GUIDs como nomes, bem como conteúdo gerado aleatoriamente. Em seguida, são carregados para cinco contentores com nomes aleatórios diferentes.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de concluir o tutorial de armazenamento anterior: [Criar uma máquina virtual e uma conta de armazenamento para uma aplicação dimensionável][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Aceder remotamente à máquina virtual

Utilize o seguinte comando no computador local para criar uma sessão de ambiente de trabalho remoto com a máquina virtual. Substitua o endereço IP pelo publicIPAddress da máquina virtual. Quando lhe for pedido, introduza as credenciais utilizadas ao criar a máquina virtual.

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Configurar a cadeia de ligação

No portal do Azure, navegue para a sua conta de armazenamento. Selecione **Chaves de acesso**, em **Definições**, na conta de armazenamento. Copie a **cadeia de ligação** da chave primária ou secundária. Inicie sessão na máquina virtual criada no tutorial anterior. Abra uma **Linha de Comandos** como um administrador e execute o comando `setx` com o comutador `/m`, este comando guarda uma variável de ambiente de definição do computador. A variável de ambiente não está disponível enquanto não recarregar a **Linha de Comandos**. Substitua **\<storageConnectionString\>** na seguinte amostra:

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

Quando terminar, abra outra **Linha de Comandos**, navegue até `D:\git\storage-dotnet-perf-scale-app` e escreva `dotnet build` para criar a aplicação.

## <a name="run-the-application"></a>Executar a aplicação

Navegue para `D:\git\storage-dotnet-perf-scale-app`.

Escreva `dotnet run` para executar a aplicação. Da primeira vez que executar `dotnet`, preenche a cache do pacote local para melhorar a velocidade de restauro e ativar o acesso offline. Este comando demora cerca de um minuto e só ocorre uma vez.

```console
dotnet run
```

A aplicação cria cinco contentores aleatoriamente nomeados e começa a carregar os ficheiros no diretório faseado para a conta de armazenamento.

O `UploadFilesAsync` método é indicado no seguinte exemplo:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

O número mínimo e máximo de fios é definido para 100 para garantir que um grande número de ligações simultâneas são permitidas.

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```
Além de configurar as definições de threading e de ligação, as [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) do método [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) estão configuradas para utilizar o paralelismo e desativar a validação do hash MD5. Os ficheiros são carregados em blocos de 100 MB e esta configuração proporciona um melhor desempenho, mas poderá ser dispendiosa se utilizar uma rede com um desempenho fraco, uma vez que se houver uma falha, todo o bloco de 100 MB é repetido.

|Propriedade|Valor|Descrição|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| A definição divide o blob em blocos ao carregar. Para o maior desempenho, este valor deve ser oito vezes superior ao número de núcleos. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Esta propriedade desativa a verificação do hash MD5 do conteúdo carregado. Desativar a validação MD5 permite uma transferência mais rápida. Mas não confirma a validade nem a integridade dos ficheiros que estão a ser transferidos.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Esta propriedade determina se um hash MD5 é calculado e armazenado com o ficheiro.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| Término de 2 segundos com máximo de 10 repetições |Determina a política de repetição dos pedidos. As falhas de ligação são repetidas, neste exemplo, uma política[ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) é configurada com um término de 2 segundos e uma contagem de repetições máxima de 10. Esta definição é importante quando a sua aplicação se aproxima de atingir os alvos de escalabilidade para armazenamento Blob. Para obter mais informações, consulte [os objetivos de Escalabilidade e desempenho para o armazenamento blob.](../blobs/scalability-targets.md)  |

---

O exemplo seguinte é uma saída da aplicação truncada em execução num sistema Windows.

```console
Created container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Created container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Created container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Created container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Created container 92480da9-f695-4a42-abe8-fb35e71eb887
Iterating in directory: C:\git\myapp\upload
Found 5 file(s)
Uploading 1d596d16-f6de-4c4c-8058-50ebd8141e4d.pdf to container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Uploading 242ff392-78be-41fb-b9d4-aee8152a6279.pdf to container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Uploading 38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.pdf to container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Uploading 45930d63-b0d0-425f-a766-cda27ff00d32.pdf to container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Uploading 5129b385-5781-43be-8bac-e2fbb7d2bd82.pdf to container 92480da9-f695-4a42-abe8-fb35e71eb887
Uploaded 5 files in 16.9552163 seconds
```

### <a name="validate-the-connections"></a>Validar as ligações

Enquanto os ficheiros estão a ser carregados, pode verificar o número de ligações em simultâneo para a conta de armazenamento. Abra uma janela e tipo de `netstat -a | find /c "blob:https"` consola. Este comando mostra o número de ligações que estão atualmente abertas. Como pode ver pelo exemplo seguinte, 800 ligações estavam abertas ao carregar os ficheiros aleatórios para a conta de armazenamento. Este valor é alterado ao longo da execução do carregamento. Através do carregamento em segmentos de blocos em paralelo, o período de tempo que é preciso para transferir o conteúdo é significativamente reduzido.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Passos seguintes

Na segunda parte da série, aprendeu a carregar grandes quantidades de dados aleatórios para uma conta de armazenamento em paralelo, tais como:

> [!div class="checklist"]
> * Configurar a cadeia de ligação
> * Criar a aplicação
> * Executar a aplicação
> * Validar o número de ligações

Avance para a terceira parte da série para transferir grandes quantidades de dados a partir de uma conta de armazenamento.

> [!div class="nextstepaction"]
> [Transferir grandes quantidades de dados aleatórios do armazenamento do Azure](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
