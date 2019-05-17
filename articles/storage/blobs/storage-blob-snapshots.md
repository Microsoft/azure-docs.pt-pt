---
title: Criar um instantâneo só de leitura de um blob no armazenamento do Azure | Documentos da Microsoft
description: Saiba como criar um instantâneo de um blob para fazer cópias de segurança de dados de BLOBs num determinado momento no tempo. Compreenda como são cobrados as instantâneos e como utilizá-los para minimizar os custos de capacidade.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9c24f613de8bf26331f6fe328358aaf8a320d522
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794236"
---
# <a name="create-a-blob-snapshot"></a>Criar um instantâneo de blob

Um instantâneo é uma versão só de leitura de um blob que está a ser utilizado num ponto no tempo. Os instantâneos são úteis para criar cópias de blobs. Depois de criar um instantâneo, pode ler, copiar ou eliminá-lo, mas não é possível modificá-lo.

Um instantâneo de um blob é idêntico ao seu blob de base, exceto pelo fato de URI de blob tem um **DateTime** valor acrescentado para o blob URI para indicar a hora em que o instantâneo foi tirado. Por exemplo, se o URI de blob de uma página é `http://storagesample.core.blob.windows.net/mydrives/myvhd`, o instantâneo, URI é semelhante ao `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Todos os instantâneos partilham o URI de base do blob. A única diferença entre o blob de base e o instantâneo é o anexado **DateTime** valor.
>

Um blob pode ter qualquer número de instantâneos. Instantâneos de persistirem até que eles são eliminados de forma explícita. Um instantâneo não é possível sobreviverem às seu blob de base. Pode enumerar os instantâneos associados com o blob de base para controlar seu instantâneos atuais.

Quando cria um instantâneo de um blob, propriedades do sistema do blob são copiadas para o instantâneo com os mesmos valores. Metadados do blob base é também copiado para o instantâneo, a menos que especifique metadados separado para o instantâneo quando a criar.

Qualquer concessões associadas com o blob de base não afetam o instantâneo. Não é possível adquirir uma concessão num instantâneo.

Um ficheiro VHD é utilizado para armazenar as informações atuais e o estado de um disco da VM. Pode desanexar um disco de dentro da VM ou encerre a VM e, em seguida, tirar um instantâneo de seu arquivo VHD. Pode utilizar esse ficheiro de instantâneo mais tarde para recuperar o ficheiro VHD nesse ponto no tempo e recriar a VM.

## <a name="create-a-snapshot"></a>Criar um instantâneo
O exemplo de código seguinte mostra como criar um instantâneo, utilizando o [biblioteca de clientes de armazenamento do Azure para .NET](/dotnet/api/overview/azure/storage/client). Este exemplo Especifica metadados adicionais para o instantâneo quando é criado.

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
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
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

## <a name="copy-snapshots"></a>Instantâneos de cópia
Operações de cópia que envolvem blobs e instantâneos, siga estas regras:

* Pode copiar um instantâneo sobre seu blob de base. Promovendo um instantâneo para a posição do base blob, pode restaurar uma versão anterior de um blob. O instantâneo permanece, mas a base de blob é substituído com uma cópia gravável do instantâneo.
* Pode copiar um instantâneo para um blob de destino com um nome diferente. O blob de destino resultante é um blob gravável e não um instantâneo.
* Quando um blob de origem é copiado, quaisquer instantâneos do blob de origem não são copiados para o destino. Quando um blob de destino é substituído com uma cópia, quaisquer instantâneos associados com o blob de destino original permanecem intactos.
* Quando cria um instantâneo de um blob de blocos, lista de bloqueios de compromisso do blob é também copiada para o instantâneo. Qualquer não consolidados blocos não são copiados.

## <a name="specify-an-access-condition"></a>Especifique uma condição de acesso
Quando chama [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], pode especificar uma condição de acesso para que o instantâneo é criado apenas se for cumprida uma condição. Para especificar uma condição de acesso, utilize o [AccessCondition] [ dotnet_AccessCondition] parâmetro. Se não for cumprida a condição especificada, o instantâneo não é criado e o serviço Blob retorna o código de estado [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Eliminar instantâneos
Não é possível eliminar um blob com instantâneos, a menos que os instantâneos também são eliminados. Pode eliminar um instantâneo individualmente ou especificar que todos os instantâneos eliminados quando o blob de origem é eliminado. Se tentar eliminar um blob que ainda tem instantâneos, os resultados um erro.

O exemplo de código seguinte mostra como eliminar um blob e os respetivos instantâneos no .NET, onde `blockBlob` é um objeto do tipo [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Instantâneos de armazenamento Premium do Azure
Quando utilizar instantâneos com o armazenamento Premium, aplicam-se as seguintes regras:

* O número máximo de instantâneos por blob de páginas numa conta de armazenamento premium é 100. Se esse limite for excedido, a operação de instantâneo de Blob retorna o código de erro 409 (`SnapshotCountExceeded`).
* Pode tirar um instantâneo de um blob de páginas numa conta de armazenamento premium a cada 10 minutos. Se essa taxa for excedida, a operação de instantâneo de Blob retorna o código de erro 409 (`SnapshotOperationRateExceeded`).
* Para ler um instantâneo, pode usar a operação de Blob de cópia para copiar um instantâneo para outra página blob na conta. O blob de destino para a operação de cópia não deve ter quaisquer instantâneos existentes. Se o blob de destino tiver instantâneos, em seguida, a operação de Blob de cópia retorna o código de erro 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Devolver o URI absoluto para um instantâneo
Este exemplo de código em C# cria um instantâneo e escreve o URI absoluto para a localização principal.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Compreender como instantâneos de acumulam despesas
Criar um instantâneo, o que é uma cópia só de leitura de um blob, pode resultar em encargos de armazenamento de dados adicionais à sua conta. Ao conceber a sua aplicação, é importante estar ciente de como estes custos podem acumular, para que pode minimizar os custos.

### <a name="important-billing-considerations"></a>Considerações importantes sobre faturação
A lista seguinte inclui os pontos importantes a considerar ao criar um instantâneo.

* Sua conta de armazenamento leva a custos para blocos exclusivos ou páginas, quer estejam no blob ou no instantâneo. Sua conta não incorrem em custos adicionais para instantâneos associados um blob até que Atualize o blob em que se baseiam. Depois de atualizar o blob de base, ele difere respetivos instantâneos. Quando isto acontecer, é-lhe cobrada a páginas em cada blob ou um instantâneo ou blocos exclusivos.
* Se substituir um bloco dentro de um blob de blocos, esse bloco, em seguida, é cobrado como um bloco exclusivo. Isso é verdadeiro, mesmo que o bloco tenha o mesmo ID de bloco e os mesmos dados, porque esta tem no instantâneo. Depois do bloco tem o compromisso novamente, ele difere de sua contraparte em qualquer instantâneo e lhe será cobrado seus dados. O mesmo se aplica a uma página num blob de página é atualizado com dados idênticos.
* Substituir um blob de blocos ao chamar o [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream] [ dotnet_UploadFromStream], ou [UploadFromByteArray] [ dotnet_UploadFromByteArray] método substitui todos os blocos no blob. Se tiver um instantâneo associado a esse blob, todos os blocos no base blob e instantâneo agora divergem e lhe será cobrado todos os blocos em ambos os blobs. Isso vale mesmo que os dados no blob de base e o instantâneo permanecem idênticos.
* O serviço de Blobs do Azure não tem um meio para determinar se dois blocos contiverem dados idênticos. Cada bloco que é carregado e confirmado é tratado como exclusivo, mesmo que ele tem os mesmos dados e o mesmo ID de bloco. Uma vez que os encargos acumulam para os blocos exclusivos, é importante considerar que a atualizar um blob que tenha um resultados de instantâneo em blocos adicionais de exclusivos e custos adicionais.

### <a name="minimize-cost-with-snapshot-management"></a>Minimizar os custos com a gestão de instantâneo

Recomendamos a gerenciar sua instantâneos com cuidado para evitar custos adicionais. Pode seguir estas melhores práticas para ajudar a minimizar a custos incorridos pelo armazenamento de instantâneos de sua:

* Eliminar e voltar a criar instantâneos associados com um blob, sempre que atualizar o blob, mesmo se estiver a atualizar com dados idênticos, a menos que o design do aplicativo requer que mantenha instantâneos. Ao eliminar e voltar a criar instantâneos do blob, pode certificar-se de que o blob e os instantâneos não divergem.
* Se estiver a manter instantâneos de um blob, evitar chamar [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], ou [UploadFromByteArray] [ dotnet_UploadFromByteArray] para atualizar o blob. Esses métodos substituem todos os blocos no blob, fazendo com que o blob de base e os respetivos instantâneos para divergem significativamente. Em vez disso, atualize o menor número possível de blocos utilizando o [PutBlock] [ dotnet_PutBlock] e [PutBlockList] [ dotnet_PutBlockList] métodos.

### <a name="snapshot-billing-scenarios"></a>Cenários de faturação de instantâneo
Os cenários seguintes demonstram como os encargos acumulam para um blob de blocos e os respetivos instantâneos.

**Cenário 1**

Cenário 1, o blob de base não foi atualizado depois do instantâneo foi criado, pelo que são cobradas taxas apenas de blocos exclusivos 1, 2 e 3.

![Recursos de armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Cenário 2**

Cenário 2, o blob de base foi atualizado, mas o instantâneo não tem. 3 de bloco foi atualizado e, mesmo que contém os mesmos dados e o mesmo ID, não é igual ao bloquear 3 no instantâneo. Como resultado, a conta é cobrada de quatro blocos.

![Recursos de armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Cenário 3**

No cenário 3, o blob de base foi atualizado, mas o instantâneo não tem. 3 de bloco foi substituído pelo bloco 4 no blob de base, mas o instantâneo ainda reflete o bloco de 3. Como resultado, a conta é cobrada de quatro blocos.

![Recursos de armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Cenário de 4**

Cenário 4, o blob de base foi totalmente atualizado e contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada de todos os oito blocos exclusivos. Este cenário pode ocorrer se estiver a utilizar como um método de atualização [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], ou [UploadFromByteArray][dotnet_UploadFromByteArray], pois esses métodos substituem todo o conteúdo de um blob.

![Recursos de armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Passos Seguintes

* Pode encontrar mais informações sobre como trabalhar com instantâneos de disco da máquina virtual (VM) na [cópia de segurança do Azure discos VM não geridos com instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md)

* Para exemplos de código adicionais a utilizar o armazenamento de BLOBs, veja [exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Pode transferir uma aplicação de exemplo e executá-la ou procurar o código no GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.generatedblobs.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext