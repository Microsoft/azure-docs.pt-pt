---
title: Obter tipo de conta de armazenamento e nome de SKU com .NET-armazenamento do Azure
description: Saiba como obter o tipo de conta de armazenamento do Azure e o nome do SKU usando a biblioteca de cliente .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 21ecaae679ad4a5f21107ef53d3899cf03593c9d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829206"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Obter tipo de conta de armazenamento e nome de SKU com .NET

Este artigo mostra como obter o tipo de conta de armazenamento do Azure e o nome do SKU para um BLOB usando a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client).

As informações de conta estão disponíveis nas versões de serviço que começam com a versão 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Sobre o tipo de conta e o nome do SKU

**Tipo de conta**: Os tipos de conta `BlobStorage`válidos `BlockBlobStorage`incluem `FileStorage`, `Storage`,, `StorageV2`e. A [visão geral da conta de armazenamento do Azure](storage-account-overview.md) tem mais informações, incluindo descrições das várias contas de armazenamento.

**Nome do SKU**: Os nomes de SKU `Premium_LRS`válidos `Premium_ZRS`incluem `Standard_GRS`, `Standard_GZRS` ,,`Standard_LRS`,,, e`Standard_ZRS`. `Standard_RAGRS` `Standard_RAGZRS` Os nomes de SKU diferenciam maiúsculas de minúsculas e são campos de cadeia de caracteres na [classe SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Recuperar informações da conta

Para obter o tipo de conta de armazenamento e o nome do SKU associado a um [](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) BLOB, chame o método accountproperties ou [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) .

O exemplo de código a seguir recupera e exibe as propriedades da conta somente leitura.

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

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outras operações que você pode executar em uma conta de armazenamento por meio do [portal do Azure](https://portal.azure.com) e da API REST do Azure.

- [Gerenciar uma conta de armazenamento](storage-account-manage.md)
- [Atualizar uma conta de armazenamento](storage-account-upgrade.md)
- [Operação obter informações da conta (REST)](/rest/api/storageservices/get-account-information)
