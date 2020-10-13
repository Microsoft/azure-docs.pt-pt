---
title: Criar uma conta SAS com .NET
titleSuffix: Azure Storage
description: Saiba como criar uma assinatura de acesso partilhado (SAS) através da biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 102248882e1ec123ee74eedd74730c12a991fb86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021192"
---
# <a name="create-an-account-sas-with-net"></a>Criar uma conta SAS com .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar a chave da conta de armazenamento para criar uma conta SAS com a biblioteca de [clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-an-account-sas"></a>Create an account SAS (Criar uma SAS de conta)

Para criar uma conta SAS para um recipiente, ligue para o método [CloudStorageAccount.GetSharedAccessSignature.](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature)

O exemplo de código a seguir cria uma conta SAS que é válida para os serviços Blob e File, e dá ao cliente permissões de leitura, escrita e lista para aceder a APIs de nível de serviço. A conta SAS restringe o protocolo a HTTPS, pelo que o pedido deve ser feito com HTTPS. Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

## <a name="use-an-account-sas-from-a-client"></a>Use uma conta SAS de um cliente

Para utilizar a conta SAS para aceder a APIs de nível de serviço para o serviço Blob, construa um objeto cliente de serviço Blob utilizando o SAS e o ponto final de armazenamento Blob para a sua conta de armazenamento. Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

## <a name="next-steps"></a>Passos seguintes

- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)
- [Create an account SAS](/rest/api/storageservices/create-account-sas) (Criar uma SAS de conta)
