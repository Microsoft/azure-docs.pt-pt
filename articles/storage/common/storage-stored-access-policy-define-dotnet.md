---
title: Criar uma política de acesso armazenada com .NET
titleSuffix: Azure Storage
description: Utilize o Azure Storage e .NET para criar uma política de acesso armazenada. Exerça níveis adicionais de controlo sobre as assinaturas de acesso partilhado ao nível do serviço no servidor.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010754"
---
# <a name="create-a-stored-access-policy-with-net"></a>Criar uma política de acesso armazenada com .NET

Uma política de acesso armazenada fornece um nível adicional de controlo sobre as assinaturas de acesso partilhado ao nível do serviço (SAS) no lado do servidor. A definição de uma política de acesso armazenada serve para agrupar assinaturas de acesso partilhado e fornecer restrições adicionais para assinaturas de acesso partilhado que estão vinculadas à política. Pode utilizar uma política de acesso armazenada para alterar o tempo de início, o prazo de validade ou permissões para um SAS, ou revogá-la depois de emitida.
  
Os seguintes recursos de armazenamento Azure suportam políticas de acesso armazenadas:  
  
- Contentores de blobs  
- Partilhas de ficheiros  
- Filas  
- Tabelas  
  
> [!NOTE]
> Uma política de acesso armazenada num contentor pode ser associada a uma assinatura de acesso partilhado que concede permissões ao próprio contentor ou às bolhas que contém. Da mesma forma, uma política de acesso armazenada numa partilha de ficheiros pode ser associada a uma assinatura de acesso partilhado que concede permissões à própria partilha ou aos ficheiros que contém.  
>
> As políticas de acesso armazenadas são suportadas apenas para um serviço SAS. As políticas de acesso armazenadas não são suportadas para a conta SAS ou para a delegação de utilizadores SAS.  

Para obter mais informações sobre as políticas de acesso armazenadas, consulte [Definir uma política de acesso armazenada.](/rest/api/storageservices/define-stored-access-policy)

## <a name="create-a-stored-access-policy"></a>Criar uma política de acesso armazenada

A operação REST subjacente para criar uma política de acesso armazenada é [definir O Container ACL](/rest/api/storageservices/set-container-acl). Tem de autorizar a operação a criar uma política de acesso armazenada através da Chave Partilhada, utilizando as teclas de acesso à conta numa cadeia de ligação. Não é suportada a operação **ACL do recipiente de conjunto** com credenciais AZure AD. Para obter mais informações, consulte [permissões para ligar para as operações de dados de blob e fila.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

Os seguintes exemplos de código criam uma política de acesso armazenada num recipiente. Pode utilizar a política de acesso para especificar os constrangimentos para um serviço SAS no contentor ou nas suas bolhas.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para criar uma política de acesso armazenada num recipiente com a versão 12 da biblioteca de clientes .NET para armazenamento Azure, ligue para um dos seguintes métodos:

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

O exemplo a seguir cria uma política de acesso armazenada que está em vigor por um dia e que concede permissões de leitura/escrita:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para criar uma política de acesso armazenada num recipiente com a versão 12 da biblioteca de clientes .NET para armazenamento Azure, ligue para um dos seguintes métodos:

- [CloudBlobContainer.SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer.SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

O exemplo a seguir cria uma política de acesso armazenada que está em vigor por um dia e que concede permissões de leitura, escrita e lista:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Ver também

- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)
- [Definir uma política de acesso armazenada](/rest/api/storageservices/define-stored-access-policy)
- [Configurar cordas de conexão de armazenamento Azure](storage-configure-connection-string.md)
