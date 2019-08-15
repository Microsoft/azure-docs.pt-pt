---
title: Criar uma SAS de conta com o .NET-armazenamento do Azure
description: Saiba como criar uma assinatura de acesso compartilhado de conta (SAS) usando a biblioteca de cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8d627fd905741c76dd34eb4270f49d46b93abb1
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036107"
---
# <a name="create-an-account-sas-with-net"></a>Criar uma SAS de conta com .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar a chave da conta de armazenamento para criar uma SAS de conta com a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client).

## <a name="create-an-account-sas"></a>Criar uma SAS de conta

Para criar uma SAS de conta para um contêiner, chame o método [CloudStorageAccount. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) .

O exemplo de código a seguir cria uma SAS de conta válida para os serviços de BLOB e arquivo e fornece permissões de leitura, gravação e lista de permissões de cliente para acessar as APIs de nível de serviço. A SAS da conta restringe o protocolo para HTTPS, de modo que a solicitação deve ser feita com HTTPS. Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

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

## <a name="use-an-account-sas-from-a-client"></a>Usar uma SAS de conta de um cliente

Para usar a SAS da conta para acessar APIs de nível de serviço para o serviço BLOB, construa um objeto de cliente do serviço BLOB usando a SAS e o ponto de extremidade do armazenamento de BLOBs para sua conta de armazenamento. Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

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

## <a name="next-steps"></a>Passos Seguintes

- [Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md)
- [Criar uma SAS de conta](/rest/api/storageservices/create-account-sas)
