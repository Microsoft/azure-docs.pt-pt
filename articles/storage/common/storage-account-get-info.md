---
title: Obtenha o tipo de conta de armazenamento e o nome SKU com .NET
titleSuffix: Azure Storage
description: Saiba como obter o tipo de conta de armazenamento Azure e o nome SKU utilizando a biblioteca de clientes .NET.
services: storage
author: twooley
ms.author: twooley
ms.date: 11/12/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 26ce4a77e02e62a1a204529ce8652fbba3aab2c9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277152"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Obtenha o tipo de conta de armazenamento e o nome SKU com .NET

Este artigo mostra como obter o tipo de conta de armazenamento Azure e o nome SKU para uma bolha, utilizando a biblioteca de [clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage).

## <a name="about-account-type-and-sku-name"></a>Sobre o tipo de conta e o nome SKU

**Tipo de conta**: Os tipos de conta válidos incluem, `BlobStorage` , , e `BlockBlobStorage` `FileStorage` `Storage` `StorageV2` . [A visão geral da conta de armazenamento Azure](storage-account-overview.md) tem mais informações, incluindo descrições das várias contas de armazenamento.

**Nome SKU**: Nomes válidos da SKU `Premium_LRS` incluem, , , , , , , `Premium_ZRS` e `Standard_GRS` `Standard_GZRS` `Standard_LRS` `Standard_RAGRS` `Standard_RAGZRS` `Standard_ZRS` . Os nomes SKU são sensíveis a casos e são campos de cordas na [Classe SkuName.](/dotnet/api/microsoft.azure.management.storage.models.skuname)

## <a name="retrieve-account-information"></a>Recuperar informações sobre contas

O exemplo de código a seguir recupera e exibe as propriedades da conta apenas de leitura.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para obter o tipo de conta de armazenamento e o nome SKU associado a uma bolha, ligue para o método [GetAccountInfo](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) ou [GetAccountInfoAsync.](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Account.cs" id="Snippet_GetAccountInfo":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para obter o tipo de conta de armazenamento e o nome SKU associado a uma bolha, ligue para o método [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) ou [GetAccountPropertiesAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync)

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outras operações que pode realizar numa conta de armazenamento através do [portal Azure](https://portal.azure.com) e da API Azure REST.

- [Obter operação de Informação de Conta (REST)](/rest/api/storageservices/get-account-information)
