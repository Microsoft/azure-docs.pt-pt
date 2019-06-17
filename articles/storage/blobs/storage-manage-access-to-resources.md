---
title: Ativar o acesso de leitura público para contentores e blobs no armazenamento de Blobs do Azure | Documentos da Microsoft
description: Saiba como tornar disponível para acesso anónimo a contentores e blobs e como devem ser acessados por meio de programação.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: e0f93b0a95a228b26fae266129aea4b595b05e0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148364"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gerir o acesso de leitura anónimo a contentores e blobs

Pode ativar o acesso de leitura anónimo, público para um contentor e respetivos blobs no armazenamento de Blobs do Azure. Ao fazê-lo, pode conceder acesso só de leitura a esses recursos sem partilhar a sua chave de conta e sem a necessidade de uma assinatura de acesso partilhado (SAS).

Acesso de leitura público é melhor para cenários onde pretende que determinados blobs para estar sempre disponíveis para acesso de leitura anónimo. Para um controlo mais detalhado, pode criar uma assinatura de acesso partilhado. Assinaturas de acesso partilhado permitem-lhe proporcionar acesso restrito com permissões diferentes, para um período de tempo específico. Para obter mais informações sobre a criação de partilhado assinaturas de acesso, consulte [Using partilhado assinaturas de acesso (SAS) no armazenamento do Azure](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permissões de utilizadores anónimos a contentores e blobs

Por predefinição, um contentor e os blobs dentro da mesma podem ser acedidos apenas por um utilizador que tenha recebido as permissões adequadas. Para conceder acesso de leitura de usuários anônimos para um contentor e respetivos blobs, pode definir o nível de acesso público do contentor. Quando concede acesso público a um contentor, usuários anônimos podem ler blobs num contentor acessível ao público sem autorizar a solicitação.

Pode configurar um contentor com as seguintes permissões:

* **Sem acesso de leitura público:** O contentor e respetivos blobs podem ser acedidos apenas pelo proprietário da conta de armazenamento. Esta é a predefinição para todos os novos contentores.
* **Acesso de leitura público para blobs apenas:** Os BLOBs no contentor podem ser lidos por pedido anónimo, mas os dados de contentor não estão disponíveis. Clientes anônimos não é possível enumerar os blobs no contentor.
* **Acesso para o contentor e respetivos blobs de leitura pública:** Todos os dados de BLOBs e contentores podem ser lidos por pedido anónimo. Os clientes podem enumerar os blobs no contentor ao pedido anónimo, mas não é possível enumerar os contentores na conta de armazenamento.

Pode utilizar o seguinte para definir permissões de contentor:

* [Portal do Azure](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [CLI do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Por meio de programação, ao utilizar uma das bibliotecas de cliente do armazenamento ou a API REST

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Definir o nível de acesso público do contentor no portal do Azure

Partir do [portal do Azure](https://portal.azure.com), pode atualizar o nível de acesso público para um ou mais contentores:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Sob **serviço Blob** no painel de menu, selecione **Blobs**.
1. Selecione os contentores para o qual pretende definir o nível de acesso público.
1. Utilize o **nível de acesso de alteração** botão para apresentar as definições de acesso público.
1. Selecione o nível de acesso de público pretendido do **nível de acesso público** lista pendente e clique no botão OK para aplicar a alteração para os contentores selecionados.

Captura de ecrã seguinte mostra como alterar o nível de acesso público para os contentores selecionados.

![Captura de ecrã que mostra como definir o nível de acesso público no portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Não é possível alterar o nível de acesso público para um blob individual. Nível de acesso público é definido apenas ao nível do contentor.

### <a name="set-container-public-access-level-with-net"></a>Definir o nível de acesso público do contentor com o .NET

Para definir permissões para um contentor com o c# e a biblioteca de cliente de armazenamento para .NET, obter permissões existentes que o contentor ao chamar o **GetPermissions** método. Em seguida, defina o **PublicAccess** propriedade para o **BlobContainerPermissions** objeto devolvido pelos **GetPermissions** método. Por fim, chame o **SetPermissions** método com as permissões atualizadas.

O exemplo seguinte define as permissões o contentor para acesso de leitura público completa. Para definir permissões para acesso de leitura público para blobs apenas, defina o **PublicAccess** propriedade **BlobContainerPublicAccessType.Blob**. Para remover todas as permissões para utilizadores anónimos, defina a propriedade **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Aceder anonimamente a contentores e blobs

Um cliente que acede aos contentores e blobs anonimamente pode utilizar construtores que não necessitam de credenciais. Os exemplos seguintes mostram algumas formas diferentes de fazer referência a contentores e blobs anonimamente.

### <a name="create-an-anonymous-client-object"></a>Criar um objeto de cliente anônimo

Pode criar um novo objeto de cliente de serviço para acesso anónimo ao fornecer o ponto final de armazenamento de BLOBs para a conta. No entanto, também deve saber o nome de um contentor nessa conta que está disponível para acesso anónimo.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Fazer referência a um contentor anonimamente

Se tiver o URL para um contentor que está disponível de forma anônima, pode utilizá-lo para referenciar diretamente o contentor.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Um blob de referência anonimamente

Se tiver o URL para um blob que está disponível para acesso anónimo, pode referenciar o blob diretamente usando esse URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Recursos disponíveis para utilizadores anónimos

A tabela seguinte mostra as operações que podem ser chamadas anonimamente quando um contentor está configurado para acesso público.

| Operação REST | Acesso de leitura público ao contentor | Acesso de leitura público para blobs apenas |
| --- | --- | --- |
| Lista de contentores | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Criar contentor | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Obter as propriedades do contentor | Pedidos anónimos permitidos | Apenas pedidos autorizados |
| Obter metadados do contentor | Pedidos anónimos permitidos | Apenas pedidos autorizados |
| Metadados do conjunto de contentor | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Obter o contentor ACL | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Definir o contentor de ACL | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Eliminar contentor | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Listar os Blobs | Pedidos anónimos permitidos | Apenas pedidos autorizados |
| Colocar o Blob | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Obter o Blob | Pedidos anónimos permitidos | Pedidos anónimos permitidos |
| Obter as propriedades do Blob | Pedidos anónimos permitidos | Pedidos anónimos permitidos |
| Definir as propriedades do Blob | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Obter metadados do Blob | Pedidos anónimos permitidos | Pedidos anónimos permitidos |
| Definir metadados do Blob | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Colocar o bloco | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Obter lista de bloqueios (apenas blocos confirmados) | Pedidos anónimos permitidos | Pedidos anónimos permitidos |
| Obter a lista de bloqueios (apenas os blocos não consolidados ou todos os blocos) | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Colocar a lista de bloqueios | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Eliminar Blob | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Copiar Blob | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Blob de instantâneo | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Blob de concessão | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Colocar a página | Apenas pedidos autorizados | Apenas pedidos autorizados |
| Os intervalos obter páginas | Pedidos anónimos permitidos | Pedidos anónimos permitidos |
| Blob de acréscimo | Apenas pedidos autorizados | Apenas pedidos autorizados |

## <a name="next-steps"></a>Passos Seguintes

* [Autorização para os serviços de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Utilizar assinaturas de acesso partilhado (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)