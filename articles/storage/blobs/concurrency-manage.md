---
title: Gestão da concordância no armazenamento blob
titleSuffix: Azure Storage
description: Aprenda a gerir vários escritores a uma bolha implementando a concordância otimista ou pessimista na sua aplicação. A concordância otimista verifica o valor ETag de uma bolha e compara-a com o ETag fornecido. A concordância pessimista usa um contrato de arrendamento exclusivo para bloquear a bolha a outros escritores.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96523521"
---
# <a name="managing-concurrency-in-blob-storage"></a>Gestão da Concurrency no armazenamento blob

As aplicações modernas têm frequentemente vários utilizadores a visualizar e atualizar dados simultaneamente. Os desenvolvedores de aplicações precisam de pensar cuidadosamente sobre como fornecer uma experiência previsível aos seus utilizadores finais, especialmente para cenários onde vários utilizadores podem atualizar os mesmos dados. Existem três principais estratégias de concuência de dados que os desenvolvedores normalmente consideram:  

- **Concordância otimista**: Uma aplicação que executa uma atualização determinará, como parte da sua atualização, se os dados mudaram desde a última leitura da aplicação. Por exemplo, se dois utilizadores que visualizam uma página wiki fizerem uma atualização para essa página, então a plataforma wiki deve garantir que a segunda atualização não substitui a primeira atualização. Deve também garantir que ambos os utilizadores compreendam se a sua atualização foi bem sucedida. Esta estratégia é mais utilizada em aplicações web.

- **Concunciência pessimista**: Uma aplicação que pretenda executar uma atualização irá bloquear um objeto que impede outros utilizadores de atualizar os dados até que o bloqueio seja lançado. Por exemplo, num cenário de replicação de dados primário/secundário em que apenas o primário executa atualizações, o primário normalmente detém um bloqueio exclusivo nos dados por um longo período de tempo para garantir que ninguém mais pode atualizá-lo.

- **Último escritor ganha**: Uma abordagem que permite que as operações de atualização prossigam sem antes determinar se outra aplicação atualizou os dados desde que foi lido. Esta abordagem é normalmente utilizada quando os dados são divididos de forma a que vários utilizadores não acedam aos mesmos dados ao mesmo tempo. Também pode ser útil onde os fluxos de dados de curta duração estão sendo processados.

O Azure Storage apoia as três estratégias, embora seja distinto na sua capacidade de fornecer apoio total para a concordância otimista e pessimista. O Azure Storage foi concebido para abraçar um modelo de forte consistência que garante que após o serviço realizar uma operação de inserção ou atualização, as operações de leitura subsequentes devolvem a última atualização.

Além de selecionarem uma estratégia de concordância adequada, os desenvolvedores também devem estar cientes de como uma plataforma de armazenamento isola as mudanças, particularmente alterações no mesmo objeto através de transações. O Azure Storage utiliza o isolamento instantâneo para permitir a leitura das operações em simultâneo com operações de escrita dentro de uma única divisória. O isolamento instantâneo garante que todas as operações de leitura devolvem uma imagem consistente dos dados mesmo quando estão a ocorrer atualizações.

Pode optar por utilizar modelos de conuncy otimistas ou pessimistas para gerir o acesso a bolhas e contentores. Se não especificar explicitamente uma estratégia, então, por defeito, o último escritor ganha.  

## <a name="optimistic-concurrency"></a>Concordância otimista

O Azure Storage atribui um identificador a todos os objetos armazenados. Este identificador é atualizado sempre que uma operação de escrita é realizada num objeto. O identificador é devolvido ao cliente como parte de uma resposta HTTP GET no cabeçalho ETag que é definido pelo protocolo HTTP.

Um cliente que esteja a realizar uma atualização pode enviar o ETag original juntamente com um cabeçalho condicional para garantir que uma atualização só ocorrerá se uma determinada condição tiver sido cumprida. Por exemplo, se o cabeçalho **If-Match** for especificado, o Azure Storage verifica que o valor do ETag especificado no pedido de atualização é o mesmo que o ETag para o objeto que está a ser atualizado. Para obter mais informações sobre cabeçalhos condicional, consulte [os cabeçalhos condicional para as operações de serviço blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).

O esboço deste processo é o seguinte:  

1. Recupere uma bolha do Azure Storage. A resposta inclui um valor http ETag header que identifica a versão atual do objeto.
1. Quando atualizar a bolha, inclua o valor ETag que recebeu no passo 1 no cabeçalho condicional **if-Match** do pedido de escrita. O Azure Storage compara o valor ETag no pedido com o valor ETag atual da bolha.
1. Se o valor ETag atual da bolha diferir do especificado no cabeçalho condicional **If-Match** fornecido no pedido, então o Azure Storage devolve o código de estado HTTP 412 (Pré-condição Falhada). Este erro indica ao cliente que outro processo atualizou a bolha desde que o cliente o recuperou pela primeira vez.
1. Se o valor ETag atual da bolha for a mesma versão que o ETag no cabeçalho condicional **If-Match** no pedido, o Azure Storage executa a operação solicitada e atualiza o valor ETag atual da bolha.  

Os exemplos de código que se seguem mostram como construir uma condição **If-Match** no pedido de escrita que verifica o valor ETag para uma bolha. O Azure Storage avalia se o atual ETag da bolha é o mesmo que o ETag fornecido no pedido e executa a operação de escrita apenas se os dois valores ETag corresponderem. Se outro processo tiver atualizado a bolha durante o período intercalar, o Azure Storage devolve uma mensagem de estado HTTP 412 (Pré-condição Falhada).  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

O Azure Storage também suporta outros cabeçalhos condicional, incluindo **se-Modificado-Since**, **If-Unmodified-Since** and **If-None-Match**. Para obter mais informações, consulte [especificar cabeçalhos condicional para operações de serviço de bolhas](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

## <a name="pessimistic-concurrency-for-blobs"></a>Concuência pessimista para bolhas

Para bloquear uma bolha para uso exclusivo, pode adquirir um contrato de arrendamento. Ao adquirir o contrato de arrendamento, especifique a duração do contrato de arrendamento. Um arrendamento finito pode ser válido entre 15 a 60 segundos. Um arrendamento também pode ser infinito, o que equivale a uma fechadura exclusiva. Você pode renovar um contrato de arrendamento finito para estendê-lo, e você pode liberar o arrendamento quando você terminar com ele. O Azure Storage liberta automaticamente locações finitas quando expiram.  

Os contratos de arrendamento permitem apoiar diferentes estratégias de sincronização, incluindo operações exclusivas de escrita/leitura partilhada, operações exclusivas de escrita/leitura exclusiva e operações de leitura partilhadas/exclusivas. Quando existe um contrato de arrendamento, a Azure Storage impõe o acesso exclusivo a operações de escrita para o arrendatário. No entanto, garantir a exclusividade das operações de leitura requer que o desenvolvedor certifique-se de que todas as aplicações do cliente usam uma identificação de locação e que apenas um cliente de cada vez tem uma identificação de locação válida. Leia as operações que não incluem um ID de arrendamento resultam em leituras partilhadas.  

Os exemplos de código que se seguem mostram como adquirir um arrendamento exclusivo numa bolha, atualizar o conteúdo da bolha fornecendo o ID do arrendamento e, em seguida, lançar o arrendamento. Se o arrendamento estiver ativo e o ID do arrendamento não for fornecido por um pedido de escrita, então a operação de escrita falha com o código de erro 412 (Pré-condição Falhada).  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>Concordância pessimista para contentores

As locações em contentores permitem as mesmas estratégias de sincronização suportadas por bolhas, incluindo leitura exclusiva escrita/partilha, leitura exclusiva/exclusiva e leitura partilhada/exclusiva. No entanto, para os contentores, o bloqueio exclusivo é aplicado apenas em operações de eliminação. Para eliminar um recipiente com um arrendamento ativo, um cliente deve incluir o ID de locação ativa com o pedido de eliminação. Todas as outras operações de contentores serão bem sucedidas num contentor alugado sem a identificação do arrendamento.  

## <a name="next-steps"></a>Passos seguintes

* [Especificação de cabeçalhos condicionais para operações de serviço Blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Recipiente de Arrendamento](/rest/api/storageservices/lease-container)
* [Lease Blob](/rest/api/storageservices/lease-blob) (Blob de Concessão)
