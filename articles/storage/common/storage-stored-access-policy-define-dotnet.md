---
title: Defina uma política de acesso armazenada com .NET - Armazenamento Azure
description: Saiba como definir uma política de acesso armazenada utilizando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990744"
---
# <a name="define-a-stored-access-policy-with-net"></a>Defina uma política de acesso armazenada com .NET

Uma política de acesso armazenada fornece um nível adicional de controlo sobre as assinaturas de acesso partilhado sem nível de serviço (SAS) do lado do servidor. A definição de uma política de acesso armazenada serve para agrupar assinaturas de acesso partilhado e para fornecer restrições adicionais às assinaturas de acesso partilhado que estão vinculadas à política. Pode utilizar uma política de acesso armazenada para alterar o tempo de início, o tempo de validade ou as permissões para um SAS, ou para a revogar após a sua emissão.
  
 Os seguintes recursos de armazenamento suportam políticas de acesso armazenadas:  
  
- Recipientes blob  
- Partilhas de Ficheiros  
- Filas  
- Tabelas  
  
> [!NOTE]
> Uma política de acesso armazenada num recipiente pode ser associada a uma assinatura de acesso partilhado que concede permissões ao próprio contentor ou às bolhas que contém. Da mesma forma, uma política de acesso armazenada numa parte de ficheiro pode ser associada a uma assinatura de acesso partilhado que concede permissões à própria partilha ou aos ficheiros que contém.  
>
> As políticas de acesso armazenadas são suportadas apenas para um serviço SAS. As políticas de acesso armazenadas não são suportadas para a conta SAS ou delegação de utilizadores SAS.  

## <a name="create-a-stored-access-policy"></a>Criar uma política de acesso armazenada

O seguinte código cria uma política de acesso armazenada num recipiente. Pode utilizar a política de acesso para especificar constrangimentos para um Serviço SAS no recipiente ou nas suas bolhas.

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

- [Conceder acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)
- [Definir uma política de acesso armazenada](/rest/api/storageservices/define-stored-access-policy)

