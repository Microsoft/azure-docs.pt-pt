---
title: Configure a Segurança da Camada de Transporte (TLS) para uma aplicação ao cliente
titleSuffix: Azure Storage
description: Configure uma aplicação do cliente para comunicar com o Azure Storage utilizando uma versão mínima de Segurança da Camada de Transporte (TLS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89001812"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>Configure a Segurança da Camada de Transporte (TLS) para uma aplicação ao cliente

Por razões de segurança, uma conta de Armazenamento Azure pode exigir que os clientes utilizem uma versão mínima de Segurança da Camada de Transporte (TLS) para enviar pedidos. As chamadas para o Azure Storage falharão se o cliente estiver a utilizar uma versão de TLS inferior à versão mínima exigida. Por exemplo, se uma conta de armazenamento requer TLS 1.2, então um pedido enviado por um cliente que esteja a usar o TLS 1.1 falhará.

Este artigo descreve como configurar uma aplicação do cliente para usar uma versão específica do TLS. Para obter informações sobre como configurar uma versão mínima exigida de TLS para uma conta de Armazenamento Azure, consulte a [versão mínima exigida de Segurança da Camada de Transporte (TLS) para uma conta de armazenamento](transport-layer-security-configure-minimum-version.md).

## <a name="configure-the-client-tls-version"></a>Configurar a versão TLS do cliente

Para que um cliente envie um pedido com uma versão específica do TLS, o sistema operativo deve suportar essa versão.

Os exemplos a seguir mostram como definir a versão TLS do cliente para 1.2 da PowerShell ou .NET. O Quadro .NET utilizado pelo cliente deve suportar o TLS 1.2. Para obter mais informações, consulte [o Suporte para TLS 1.2](/dotnet/framework/network-programming/tls#support-for-tls-12).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A amostra a seguir mostra como permitir o TLS 1.2 num cliente PowerShell:

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

A amostra que se segue mostra como permitir o TLS 1.2 num cliente .NET utilizando a versão 12 da biblioteca do cliente do Azure Storage:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

A amostra que se segue mostra como permitir o TLS 1.2 num cliente .NET utilizando a versão 11 da biblioteca do cliente do Azure Storage:

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>Verifique a versão TLS utilizada por um cliente

Para verificar se a versão especificada do TLS foi utilizada pelo cliente para enviar um pedido, pode utilizar [o Fiddler](https://www.telerik.com/fiddler) ou uma ferramenta similar. Abra o Violinista para começar a capturar o tráfego da rede de clientes e, em seguida, execute um dos exemplos na secção anterior. Veja o traço do Fiddler para confirmar que a versão correta do TLS foi usada para enviar o pedido, como mostra a imagem seguinte.

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="Screenshot mostrando traços de Fiddler que indicam a versão TLS usada a pedido":::

## <a name="next-steps"></a>Passos seguintes

- [Configure a versão mínima exigida da Segurança da Camada de Transporte (TLS) para uma conta de armazenamento](transport-layer-security-configure-minimum-version.md)
- [Recomendações de segurança para armazenamento blob](../blobs/security-recommendations.md)
