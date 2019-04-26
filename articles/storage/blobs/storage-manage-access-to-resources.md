---
title: Ativar o acesso de leitura público para contentores e blobs no armazenamento de Blobs do Azure | Documentos da Microsoft
description: Saiba como tornar disponível para acesso anónimo a contentores e blobs e como devem ser acessados por meio de programação.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: 3996f22db2f5dc597939995a2699c4fe228821e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392576"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gerir o acesso de leitura anónimo a contentores e blobs
Pode ativar o acesso de leitura anónimo, público para um contentor e respetivos blobs no armazenamento de Blobs do Azure. Ao fazê-lo, pode conceder acesso só de leitura a esses recursos sem partilhar a sua chave de conta e sem a necessidade de uma assinatura de acesso partilhado (SAS).

Acesso de leitura público é melhor para cenários onde pretende que determinados blobs para estar sempre disponíveis para acesso de leitura anónimo. Para um controlo mais detalhado, pode criar uma assinatura de acesso partilhado. Assinaturas de acesso partilhado permitem-lhe proporcionar acesso restrito com permissões diferentes, para um período de tempo específico. Para obter mais informações sobre a criação de partilhado assinaturas de acesso, consulte [Using partilhado assinaturas de acesso (SAS) no armazenamento do Azure](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permissões de utilizadores anónimos a contentores e blobs
Por predefinição, um contentor e os blobs dentro da mesma podem ser acedidos apenas pelo proprietário da conta de armazenamento. Para conceder aos utilizadores anónimos permissões de leitura para um contentor e respetivos blobs, pode definir as permissões de contentor para permitir o acesso público. Utilizadores anónimos podem ler blobs num contentor acessível ao público sem autenticar o pedido.

Pode configurar um contentor com as seguintes permissões:

* **Sem acesso de leitura público:** O contentor e respetivos blobs podem ser acedidos apenas pelo proprietário da conta de armazenamento. Esta é a predefinição para todos os novos contentores.
* **Acesso de leitura público para blobs apenas:** Os BLOBs no contentor podem ser lidos por pedido anónimo, mas os dados de contentor não estão disponíveis. Clientes anônimos não é possível enumerar os blobs no contentor.
* **Acesso de leitura público completa:** Todos os dados de BLOBs e contentores podem ser lidos por pedido anónimo. Os clientes podem enumerar os blobs no contentor ao pedido anónimo, mas não é possível enumerar os contentores na conta de armazenamento.

Pode utilizar o seguinte para definir permissões de contentor:

* [Portal do Azure](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [CLI do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Por meio de programação, ao utilizar uma das bibliotecas de cliente do armazenamento ou a API REST

### <a name="set-container-permissions-in-the-azure-portal"></a>Definir permissões de contentor no portal do Azure
Para definir permissões de contentor [portal do Azure](https://portal.azure.com), siga estes passos:

1. Abra sua **conta de armazenamento** painel no portal. Pode encontrar a sua conta de armazenamento selecionando **contas de armazenamento** no painel do menu principal do portal.
1. Sob **serviço BLOB** no painel de menu, selecione **Blobs**.
1. Faça duplo clique na linha de contentor ou selecione as reticências para abrir o contêiner **menu de contexto**.
1. Selecione **política de acesso** no menu de contexto.
1. Selecione um **acessar tipo** no menu pendente.

    ![Editar caixa de diálogo de metadados do contentor](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Definir permissões de contentor com o .NET
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
Um cliente que acede aos contentores e blobs anonimamente pode utilizar construtores que não necessitam de credenciais. Os exemplos seguintes mostram algumas formas diferentes para fazer referência a recursos de serviço Blob anonimamente.

### <a name="create-an-anonymous-client-object"></a>Criar um objeto de cliente anônimo
Pode criar um novo objeto de cliente de serviço para acesso anónimo ao fornecer o ponto final de serviço de BLOBs para a conta. No entanto, também deve saber o nome de um contentor nessa conta que está disponível para acesso anónimo.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
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
A tabela seguinte mostra as operações que podem ser chamadas por usuários anônimos, quando a ACL de um contentor está definido para permitir o acesso público.

| Operação REST | Permissão com acesso de leitura público completa | Permissão com acesso de leitura público para blobs apenas |
| --- | --- | --- |
| Lista de contentores |Apenas o proprietário |Apenas o proprietário |
| Criar contentor |Apenas o proprietário |Apenas o proprietário |
| Obter as propriedades do contentor |Todos |Apenas o proprietário |
| Obter metadados do contentor |Todos |Apenas o proprietário |
| Metadados do conjunto de contentor |Apenas o proprietário |Apenas o proprietário |
| Obter o contentor ACL |Apenas o proprietário |Apenas o proprietário |
| Definir o contentor de ACL |Apenas o proprietário |Apenas o proprietário |
| Eliminar contentor |Apenas o proprietário |Apenas o proprietário |
| Listar os Blobs |Todos |Apenas o proprietário |
| Colocar o Blob |Apenas o proprietário |Apenas o proprietário |
| Obter o Blob |Todos |Todos |
| Obter as propriedades do Blob |Todos |Todos |
| Definir as propriedades do Blob |Apenas o proprietário |Apenas o proprietário |
| Obter Metadados do Blob |Todos |Todos |
| Definir metadados do Blob |Apenas o proprietário |Apenas o proprietário |
| Colocar o bloco |Apenas o proprietário |Apenas o proprietário |
| Obter lista de bloqueios (apenas blocos confirmados) |Todos |Todos |
| Obter a lista de bloqueios (apenas os blocos não consolidados ou todos os blocos) |Apenas o proprietário |Apenas o proprietário |
| Colocar a lista de bloqueios |Apenas o proprietário |Apenas o proprietário |
| Eliminar Blob |Apenas o proprietário |Apenas o proprietário |
| Copiar Blob |Apenas o proprietário |Apenas o proprietário |
| Blob de instantâneo |Apenas o proprietário |Apenas o proprietário |
| Blob de concessão |Apenas o proprietário |Apenas o proprietário |
| Colocar a página |Apenas o proprietário |Apenas o proprietário |
| Os intervalos obter páginas |Todos |Todos |
| Acrescentar Blob |Apenas o proprietário |Apenas o proprietário |

## <a name="next-steps"></a>Passos Seguintes

* [Autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Utilizar assinaturas de acesso partilhado (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delegar Acesso com uma Assinatura de Acesso Partilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx)
