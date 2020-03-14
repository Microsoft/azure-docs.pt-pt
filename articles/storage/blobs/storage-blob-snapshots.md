---
title: Criar e gerir um instantâneo blob em .NET - Armazenamento Azure
description: Aprenda a criar uma foto apenas de uma bolha para fazer o back-up dos dados blob num dado momento no tempo.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: d9650db601426cef195c27e6eb874ec26cf13bb8
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137684"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Crie e gerencie um instantâneo blob em .NET

Um instantâneo é uma versão só de leitura de uma bolha que é tirada em um momento de tempo. As fotos são úteis para apoiar bolhas. Este artigo mostra como criar e gerir imagens blob usando a [biblioteca de clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-blob-snapshots"></a>Sobre fotos blob

Uma imagem de uma bolha é idêntica à sua bolha base, exceto que o blob URI tem um valor **DateTime** anexado ao blob URI para indicar o tempo em que o instantâneo foi tirado. Por exemplo, se uma página blob URI for `http://storagesample.core.blob.windows.net/mydrives/myvhd`, o uri instantâneo é semelhante ao `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Todos os instantâneos partilham o URI da bolha base. A única distinção entre a bolha base e o instantâneo é o valor de **DataTime** anexado.
>

Uma bolha pode ter várias fotos. Os instantâneos persistem até serem explicitamente eliminados, o que significa que um instantâneo não pode sobreviver à sua bolha base. Pode enumerar as imagens associadas à bolha base para rastrear as suas imagens atuais.

Quando se cria uma imagem instantânea de uma bolha, as propriedades do sistema da bolha são copiadas para o instantâneo com os mesmos valores. Os metadados da bolha base também são copiados para o instantâneo, a menos que especifique metadados separados para o instantâneo quando o criar. Depois de criar uma imagem instantânea, pode ler, copiar ou apagar, mas não pode modificá-la.

Quaisquer locações associadas à bolha base não afetam o instantâneo. Não pode adquirir um contrato de arrendamento numa foto.

Um ficheiro VHD é utilizado para armazenar as informações e o estado atuais para um disco VM. Pode desmontar um disco de dentro do VM ou desligar o VM e, em seguida, tirar uma foto do seu ficheiro VHD. Pode utilizar esse ficheiro instantâneo mais tarde para recuperar o ficheiro VHD nesse momento e recriar o VM.

## <a name="create-a-snapshot"></a>Criar um instantâneo

Para criar uma imagem instantânea de uma bolha de bloco, utilize um dos seguintes métodos:

- [Criar Snapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

O exemplo de código que se segue mostra como criar um instantâneo. Este exemplo especifica metadados adicionais para o instantâneo quando é criado.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Eliminar instantâneos

Para eliminar uma bolha, primeiro deve eliminar quaisquer imagens dessa bolha. Pode eliminar um instantâneo individualmente ou especificar que todas as imagens sejam eliminadas quando a bolha de origem for eliminada. Se tentar apagar uma bolha que ainda tem instantâneos, resulta um erro.

Para eliminar as imagens blob, utilize um dos seguintes métodos de eliminação de blob e inclua o [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) enum.

- [Eliminar](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [Eliminarifexista](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [Eliminar IfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

O exemplo de código que se segue mostra como eliminar uma bolha e as suas imagens em .NET, onde `blockBlob` é um objeto do tipo [CloudBlockBlob:][dotnet_CloudBlockBlob]

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Devolva o URI absoluto a um instantâneo

O seguinte exemplo de código cria um instantâneo e escreve o URI absoluto para a localização primária.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Entenda como as imagens acumulam encargos

A criação de um instantâneo, que é uma cópia apenas de uma bolha, pode resultar em custos adicionais de armazenamento de dados na sua conta. Ao conceber a sua aplicação, é importante estar ciente de como estes encargos podem acumular-se para que possa minimizar os custos.

### <a name="important-billing-considerations"></a>Considerações importantes de faturação

A lista seguinte inclui pontos-chave a considerar na criação de um instantâneo.

- A sua conta de armazenamento incorre em encargos para blocos ou páginas únicos, quer estejam na bolha ou no instantâneo. A sua conta não incorre em encargos adicionais para instantâneos associados a uma bolha até atualizar a bolha em que se baseiam. Depois de atualizar a bolha base, diverge das suas imagens. Quando isto acontece, é cobrado pelos blocos ou páginas únicos em cada bolha ou instantâneo.
- Quando se substitui um bloco dentro de uma bolha de bloco, esse bloco é posteriormente carregado como um bloco único. Isto é verdade mesmo que o bloco tenha o mesmo ID de bloco e os mesmos dados que tem no instantâneo. Depois de o bloco ser novamente comprometido, diverge da sua congénere em qualquer instantâneo, e será cobrado pelos seus dados. O mesmo se aplica a uma página numa página blob que é atualizada com dados idênticos.
- Substituir uma bolha de bloco, ligando para o [uploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], ou [uploadFromByteArray,][dotnet_UploadFromByteArray] substitui todos os blocos da bolha. Se tiver uma foto associada a essa bolha, todos os blocos na bolha base e instantâneo saem agora, e será cobrado por todos os blocos em ambas as bolhas. Isto é verdade mesmo que os dados na bolha base e no instantâneo permaneçam idênticos.
- O serviço Azure Blob não dispõe de meios para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e comprometido é tratado como único, mesmo que tenha os mesmos dados e o mesmo bloco DE IDENTIFICAção. Como as taxas acumulam-se para blocos únicos, é importante considerar que atualizar uma bolha que tem um instantâneo resulta em blocos únicos adicionais e encargos adicionais.

### <a name="minimize-cost-with-snapshot-management"></a>Minimizar o custo com a gestão instantânea

Recomendamos que gere cuidadosamente as suas fotos para evitar encargos adicionais. Pode seguir estas boas práticas para ajudar a minimizar os custos incorridos pelo armazenamento das suas fotos:

- Elimine e recrie instantâneos associados a uma bolha sempre que atualizar a bolha, mesmo que esteja a atualizar com dados idênticos, a menos que o design da sua aplicação exija que mantenha instantâneos. Ao eliminar e recriar as imagens da bolha, pode garantir que a bolha e as imagens não divergem.
- Se estiver a manter instantâneos para uma bolha, evite ligar para [uploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]ou [UploadFromByteArray][dotnet_UploadFromByteArray] para atualizar a bolha. Estes métodos substituem todos os blocos da bolha, fazendo com que a sua bolha base e os seus instantâneos diverjam significativamente. Em vez disso, atualize o menor número possível de blocos utilizando os métodos [PutBlock][dotnet_PutBlock] e [PutBlockList.][dotnet_PutBlockList]

### <a name="snapshot-billing-scenarios"></a>Cenários de faturação instantânea

Os seguintes cenários demonstram como as taxas acumulam-se para uma bolha de bloco e as suas fotos.

#### <a name="scenario-1"></a>Cenário 1

No cenário 1, a bolha base não foi atualizada após a tomada do instantâneo, pelo que as taxas são incorridas apenas para os blocos 1, 2 e 3 únicos.

![Recursos de Armazenamento Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Cenário 2

No cenário 2, a bolha base foi atualizada, mas o instantâneo não. O bloco 3 foi atualizado, e mesmo que contenha os mesmos dados e o mesmo ID, não é o mesmo que o bloco 3 no instantâneo. Como resultado, a conta é cobrada por quatro quarteirões.

![Recursos de Armazenamento Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Cenário 3

No cenário 3, a bolha base foi atualizada, mas o instantâneo não. O bloco 3 foi substituído pelo bloco 4 na bolha base, mas o instantâneo ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro quarteirões.

![Recursos de Armazenamento Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Cenário 4

No cenário 4, a bolha base foi completamente atualizada e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada por todos os oito blocos únicos. Este cenário pode ocorrer se estiver a utilizar um método de atualização como [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], ou [UploadFromByteArray,][dotnet_UploadFromByteArray]porque estes métodos substituem todos os conteúdos de uma bolha.

![Recursos de Armazenamento Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Passos seguintes

- Pode encontrar mais informações sobre o trabalho com imagens de discos de máquina virtual (VM) em discos VM não [geridos Back up Azure com instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md)

- Para exemplos adicionais de código utilizando o armazenamento Blob, consulte as amostras de [código Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Você pode baixar uma aplicação de amostra e executá-la, ou navegar no código no GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
