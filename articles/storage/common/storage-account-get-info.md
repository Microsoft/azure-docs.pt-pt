---
title: Obtenha o tipo de conta de armazenamento e o nome SKU com .NET
titleSuffix: Azure Storage
description: Saiba como obter o tipo de conta azure storage e nome SKU usando a biblioteca de clientes .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79137063"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Obtenha o tipo de conta de armazenamento e o nome SKU com .NET

Este artigo mostra como obter o tipo de conta De armazenamento Azure e o nome SKU para uma bolha usando a [biblioteca de clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

A informação da conta está disponível nas versões de serviço a partir da versão 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Sobre o tipo de conta e o nome SKU

Tipo de **conta:** `BlobStorage`Os `BlockBlobStorage` `FileStorage`tipos `Storage`de `StorageV2`conta válidos incluem, , , e . A visão geral da conta de [armazenamento Azure](storage-account-overview.md) tem mais informações, incluindo descrições das várias contas de armazenamento.

**Nome SKU:** Os nomes `Premium_ZRS` `Standard_GRS`SKU válidos `Standard_ZRS`incluem, `Premium_LRS`, `Standard_GZRS`, `Standard_LRS`, `Standard_RAGRS`, `Standard_RAGZRS`e . Os nomes SKU são sensíveis a casos e são campos de cordas na [Classe SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Recuperar informações sobre contas

Para obter o tipo de conta de armazenamento e o nome SKU associado a uma bolha, ligue para o método [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) ou [GetAccountPropertiesAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet)

O exemplo de código que se segue recupera e exibe as propriedades da conta de leitura.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outras operações que pode realizar numa conta de armazenamento através do [portal Azure](https://portal.azure.com) e da API Azure REST.

- [Obter Operação informação sobre conta (REST)](/rest/api/storageservices/get-account-information)
