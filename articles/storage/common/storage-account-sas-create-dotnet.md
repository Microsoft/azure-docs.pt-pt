---
title: Criar uma conta SAS com .NET
titleSuffix: Azure Storage
description: Saiba como criar uma assinatura de acesso partilhado (SAS) através da biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2918b845430a6fc6dc59eca7041c114fc9d06515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092215"
---
# <a name="create-an-account-sas-with-net"></a>Criar uma conta SAS com .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar a chave da conta de armazenamento para criar uma conta SAS com a biblioteca de [clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage).

## <a name="create-an-account-sas"></a>Create an account SAS (Criar uma SAS de conta)

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Uma conta SAS é assinada com a chave de acesso à conta. Utilize a classe [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) para criar a credencial que é usada para assinar o SAS. Em seguida, crie um novo objeto [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) e ligue para os [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) para obter a cadeia de ficha SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

## <a name="use-an-account-sas-from-a-client"></a>Use uma conta SAS de um cliente

Para utilizar a conta SAS para aceder a APIs de nível de serviço para o serviço Blob, construa um objeto cliente de serviço Blob utilizando o SAS e o ponto final de armazenamento Blob para a sua conta de armazenamento.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Neste corte, substitua o `<storage-account>` espaço reservado pelo nome da sua conta de armazenamento.

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

---

## <a name="next-steps"></a>Passos seguintes

- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)
- [Create an account SAS](/rest/api/storageservices/create-account-sas) (Criar uma SAS de conta)
