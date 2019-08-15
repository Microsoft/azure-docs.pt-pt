---
title: Definir uma política de acesso armazenada com o .NET-armazenamento do Azure
description: Saiba como definir uma política de acesso armazenada usando a biblioteca de cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990744"
---
# <a name="define-a-stored-access-policy-with-net"></a>Definir uma política de acesso armazenada com o .NET

Uma política de acesso armazenada fornece um nível adicional de controle sobre SAS (assinaturas de acesso compartilhado) de nível de serviço no lado do servidor. Definir uma política de acesso armazenada serve para agrupar assinaturas de acesso compartilhado e fornecer restrições adicionais para assinaturas de acesso compartilhado que são associadas pela política. Você pode usar uma política de acesso armazenada para alterar a hora de início, a hora de expiração ou as permissões para uma SAS ou para revogá-la após sua emissão.
  
 Os recursos de armazenamento a seguir dão suporte a políticas de acesso armazenadas:  
  
- Contêineres de BLOB  
- Partilhas de ficheiros  
- Filas  
- Tabelas  
  
> [!NOTE]
> Uma política de acesso armazenada em um contêiner pode ser associada a uma assinatura de acesso compartilhado que concede permissões ao próprio contêiner ou aos BLOBs que ele contém. Da mesma forma, uma política de acesso armazenada em um compartilhamento de arquivos pode ser associada a uma assinatura de acesso compartilhado que concede permissões ao compartilhamento em si ou aos arquivos que ele contém.  
>
> As políticas de acesso armazenadas têm suporte apenas para SAS de serviço. As políticas de acesso armazenadas não têm suporte para SAS da conta ou SAS de delegação de usuário.  

## <a name="create-a-stored-access-policy"></a>Criar uma política de acesso armazenada

O código a seguir cria uma política de acesso armazenada em um contêiner. Você pode usar a política de acesso para especificar restrições para uma SAS de serviço no contêiner ou em seus BLOBs.

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
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Consulte também

- [Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md)
- [Definir uma política de acesso armazenada](/rest/api/storageservices/define-stored-access-policy)

