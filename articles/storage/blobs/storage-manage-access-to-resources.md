---
title: Gerir o acesso de leitura pública para contentores e bolhas
titleSuffix: Azure Storage
description: Saiba como disponibilizar contentores e bolhas para acesso anónimo e como acessá-los programáticamente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: 61f27d81cda2e57eb5730016dcc22252508fdd01
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809008"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gerir o acesso de leitura anónimo a contentores e blobs

Pode ativar o acesso de leitura público anónimo a um contentor e aos blobs no Armazenamento de blobs do Azure. Ao fazê-lo, pode conceder acesso só de leitura a estes recursos sem partilhar a sua chave de conta e sem exigir uma assinatura de acesso partilhado (SAS).

O acesso à leitura pública é o melhor para cenários em que se quer que determinadas bolhas estejam sempre disponíveis para acesso de leitura anónima. Para um controlo mais fino, pode criar uma assinatura de acesso partilhado. As assinaturas de acesso partilhado permitem-lhe fornecer acesso restrito utilizando diferentes permissões, durante um período de tempo específico. Para obter mais informações sobre a criação de assinaturas de acesso partilhado, consulte [utilizar assinaturas de acesso partilhado (SAS) no Azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permissões de utilizadores anónimos a contentores e bolhas

Por predefinição, um contentor e quaisquer blobs dentro do mesmo podem apenas ser acedidos por um utilizador ao qual foram dadas as permissões adequadas. Para conceder aos utilizadores anónimos acesso de leitura a um contentor e aos blobs, pode definir o nível de acesso público para o contentor. Quando concede acesso público a um contentor, os utilizadores anónimos podem ler os blobs dentro de um contentor publicamente acessível sem autorizar o pedido.

Pode configurar um recipiente com as seguintes permissões:

- **Sem acesso público:** O recipiente e as suas bolhas só podem ser acedidos pelo proprietário da conta de armazenamento. Este é o padrão para todos os novos recipientes.
- **O público lê o acesso apenas para bolhas:** As bolhas dentro do recipiente podem ser lidas por pedido anónimo, mas os dados do contentor não estão disponíveis. Os clientes anónimos não podem enumerar as bolhas dentro do contentor.
- **O público lê o acesso ao contentor e às suas bolhas:** Todos os dados do recipiente e do blob podem ser lidos por pedido anónimo. Os clientes podem enumerar bolhas dentro do contentor por pedido anónimo, mas não podem enumerar contentores dentro da conta de armazenamento.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Definir nível de acesso público de contentores no portal Azure

A partir do [portal Azure,](https://portal.azure.com)pode atualizar o nível de acesso público para um ou mais contentores:

1. Navegue para a sua conta de armazenamento no portal Azure.
1. Sob o **serviço Blob** na lâmina do menu, selecione **Blobs**.
1. Selecione os recipientes para os quais pretende definir o nível de acesso público.
1. Utilize o botão **de nível de acesso Alterar** para visualizar as definições de acesso do público.
1. Selecione o nível de acesso público desejado a partir do **nível** de acesso do Público e clique no botão OK para aplicar a alteração nos recipientes selecionados.

A imagem que se segue mostra como alterar o nível de acesso público aos recipientes selecionados.

![Screenshot mostrando como definir o nível de acesso público no portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Não é possível alterar o nível de acesso público a uma bolha individual. O nível de acesso público é definido apenas ao nível do contentor.

### <a name="set-container-public-access-level-with-net"></a>Definir nível de acesso público de contentor com .NET

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Para definir as permissões para um recipiente, ligue para o método [BlobContainerClient.SetAccessPolicy.](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) 

O exemplo a seguir define as permissões do contentor para o acesso total ao público. Para definir permissões para o acesso público apenas para bolhas, passe o campo **PublicAccessType.Blob** para o método [BlobContainerClient.SetAccessPolicy.](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) Para remover todas as permissões para utilizadores anónimos, utilize o campo **BlobContainerPublicAccesType.None.**

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_SetPublicContainerPermissions":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

Para definir as permissões para um recipiente que utilize a biblioteca cliente de Armazenamento Azure para .NET, primeiro recupere as permissões existentes do recipiente, chamando um dos seguintes métodos:

- [Obter AsPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Em seguida, descreva a propriedade **PublicAccess** no objeto [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) que é devolvido pelo método **GetPermissions.**

Por fim, ligue para um dos seguintes métodos para atualizar as permissões do contentor:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

O exemplo a seguir define as permissões do contentor para o acesso total ao público. Para definir permissões para o público ler apenas o acesso para bolhas, decreva a propriedade **PublicAccess** para **BlobContainerPublicAccesType.Blob**. Para remover todas as permissões para utilizadores anónimos, descreva a propriedade para **BlobContainerPublicAccesType.Off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

---

## <a name="access-containers-and-blobs-anonymously"></a>Recipientes de acesso e bolhas anonimamente

Um cliente que acede a contentores e bolhas anonimamente pode usar construtores que não requerem credenciais. Os exemplos a seguir mostram algumas formas diferentes de referenciar contentores e bolhas de forma anónima.

### <a name="create-an-anonymous-client-object"></a>Criar um objeto cliente anónimo

Pode criar um novo objeto de cliente de serviço para acesso anónimo, fornecendo o ponto final de armazenamento Blob para a conta. No entanto, também deve saber o nome de um contentor nessa conta que está disponível para acesso anónimo.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
``` 

---

### <a name="reference-a-container-anonymously"></a>Referenciar um recipiente anonimamente

Se tiver o URL num recipiente que esteja disponível anonimamente, pode usá-lo para fazer referência direta ao recipiente.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
``` 

---

### <a name="reference-a-blob-anonymously"></a>Referência uma bolha anonimamente

Se tiver o URL para uma bolha disponível para acesso anónimo, pode fazer referência à bolha diretamente utilizando esse URL:

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
``` 

---

## <a name="next-steps"></a>Próximos passos

- [Autorizar o acesso ao Armazenamento Azure](../common/storage-auth.md)
- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md)
- [API REST de Serviço Blob](/rest/api/storageservices/blob-service-rest-api)
