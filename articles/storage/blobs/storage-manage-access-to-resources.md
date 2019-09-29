---
title: Habilitar o acesso de leitura público para contêineres e blobs no armazenamento de BLOBs do Azure | Microsoft Docs
description: Saiba como tornar contêineres e blobs disponíveis para acesso anônimo e como acessá-los programaticamente.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: d0e3121fe773a9725eb7cfd9e8b14d0ed86f3fbb
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673304"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gerir o acesso de leitura anónimo a contentores e blobs

Você pode habilitar o acesso de leitura anônimo e público a um contêiner e a seus BLOBs no armazenamento de BLOBs do Azure. Ao fazer isso, você pode conceder acesso somente leitura a esses recursos sem compartilhar sua chave de conta e sem a necessidade de uma SAS (assinatura de acesso compartilhado).

O acesso de leitura público é melhor para cenários em que você deseja que determinados BLOBs estejam sempre disponíveis para acesso de leitura anônimo. Para um controle mais refinado, você pode criar uma assinatura de acesso compartilhado. As assinaturas de acesso compartilhado permitem que você forneça acesso restrito usando permissões diferentes, por um período de tempo específico. Para obter mais informações sobre como criar assinaturas de acesso compartilhado, consulte [usando SAS (assinaturas de acesso compartilhado) no armazenamento do Azure](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permissões a usuários anônimos para contêineres e blobs

Por padrão, um contêiner e todos os BLOBs dentro dele podem ser acessados somente por um usuário que tenha recebido as permissões apropriadas. Para conceder aos usuários anônimos acesso de leitura a um contêiner e seus BLOBs, você pode definir o nível de acesso público do contêiner. Quando você concede acesso público a um contêiner, os usuários anônimos podem ler BLOBs dentro de um contêiner publicamente acessível sem autorizar a solicitação.

Você pode configurar um contêiner com as seguintes permissões:

- **Sem acesso de leitura público:** O contêiner e seus BLOBs podem ser acessados somente pelo proprietário da conta de armazenamento. Esse é o padrão para todos os novos contêineres.
- **Acesso de leitura público somente para BLOBs:** Os BLOBs dentro do contêiner podem ser lidos por solicitação anônima, mas os dados do contêiner não estão disponíveis. Clientes anônimos não podem enumerar os BLOBs dentro do contêiner.
- **Acesso de leitura público para contêiner e seus BLOBs:** Todos os dados de contêiner e BLOB podem ser lidos por solicitação anônima. Os clientes podem enumerar BLOBs dentro do contêiner por solicitação anônima, mas não podem enumerar contêineres na conta de armazenamento.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Definir o nível de acesso público do contêiner no portal do Azure

No [portal do Azure](https://portal.azure.com), você pode atualizar o nível de acesso público para um ou mais contêineres:

1. Navegue até a visão geral da sua conta de armazenamento na portal do Azure.
1. Em **serviço blob** na folha do menu, selecione **BLOBs**.
1. Selecione os contêineres para os quais você deseja definir o nível de acesso público.
1. Use o botão **alterar nível de acesso** para exibir as configurações de acesso público.
1. Selecione o nível de acesso público desejado na lista suspensa **nível de acesso público** e clique no botão OK para aplicar a alteração aos contêineres selecionados.

A captura de tela a seguir mostra como alterar o nível de acesso público para os contêineres selecionados.

![Captura de tela mostrando como definir o nível de acesso público no portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Você não pode alterar o nível de acesso público para um blob individual. O nível de acesso público é definido somente no nível de contêiner.

### <a name="set-container-public-access-level-with-net"></a>Definir o nível de acesso público do contêiner com .NET

Para definir permissões para um contêiner usando a biblioteca de cliente de armazenamento do Azure para .NET, primeiro recupere as permissões existentes do contêiner chamando um dos seguintes métodos:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Em seguida, defina a propriedade **PublicAccess** no objeto [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) que é retornado pelo método **getPermissions** .

Por fim, chame um dos seguintes métodos para atualizar as permissões do contêiner:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

O exemplo a seguir define as permissões do contêiner para acesso de leitura público completo. Para definir permissões como acesso de leitura público somente para BLOBs, defina a propriedade **PublicAccess** como **BlobContainerPublicAccessType. blob**. Para remover todas as permissões para usuários anônimos, defina a propriedade como **BlobContainerPublicAccessType. off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Acessar contêineres e blobs anonimamente

Um cliente que acessa contêineres e blobs anonimamente pode usar construtores que não exigem credenciais. Os exemplos a seguir mostram algumas maneiras diferentes de referenciar contêineres e blobs anonimamente.

### <a name="create-an-anonymous-client-object"></a>Criar um objeto de cliente anônimo

Você pode criar um novo objeto de cliente de serviço para acesso anônimo fornecendo o ponto de extremidade de armazenamento de BLOBs para a conta. No entanto, você também deve saber o nome de um contêiner na conta que está disponível para acesso anônimo.

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

### <a name="reference-a-container-anonymously"></a>Fazer referência a um contêiner anonimamente

Se você tiver a URL para um contêiner que está disponível anonimamente, poderá usá-la para fazer referência diretamente ao contêiner.

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

### <a name="reference-a-blob-anonymously"></a>Referenciar um blob anonimamente

Se você tiver a URL para um blob que está disponível para acesso anônimo, você pode referenciar o blob diretamente usando essa URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>Passos seguintes

- [Autorizando o acesso ao armazenamento do Azure](../common/storage-auth.md)
- [Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md)
- [API REST do serviço blob](/rest/api/storageservices/blob-service-rest-api)
