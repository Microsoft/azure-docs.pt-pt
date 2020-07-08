---
title: Crie um serviço SAS para um recipiente ou blob com .NET
titleSuffix: Azure Storage
description: Saiba como criar uma assinatura de acesso partilhado de serviço (SAS) para um recipiente ou blob utilizando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 6f1beea12b33dcc3c0111e26d79daf096c1eedcb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808419"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>Crie um serviço SAS para um recipiente ou blob com .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como utilizar a chave da conta de armazenamento para criar um serviço SAS para um recipiente ou blob com a biblioteca cliente de [Armazenamento Azure para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-a-service-sas-for-a-blob-container"></a>Criar um serviço SAS para um recipiente blob

Para criar um serviço SAS para um recipiente, ligue para o método [CloudBlobContainer.GetSharedAccessSignature.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature)

O seguinte exemplo de código cria um SAS num recipiente. Se for fornecida a denominação de uma política de acesso armazenada existente, essa política está associada ao SAS. Se não for fornecida nenhuma política de acesso armazenada, o código cria um SAS ad hoc no recipiente.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

## <a name="create-a-service-sas-for-a-blob"></a>Criar um serviço SAS para uma bolha

Para criar um serviço SAS para uma bolha, ligue para o método [CloudBlob.GetSharedAccessSsssignature.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)

O exemplo de código a seguir cria um SAS numa bolha. Se for fornecida a denominação de uma política de acesso armazenada existente, essa política está associada ao SAS. Se não for fornecida nenhuma política de acesso armazenada, o código cria um SAS ad hoc na bolha.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Próximos passos

- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md)
- [Create a service SAS](/rest/api/storageservices/create-service-sas) (Criar uma SAS de serviço)
