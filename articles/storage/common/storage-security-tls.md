---
title: Ativar TLS seguro com .NET
titleSuffix: Azure Storage
description: Saiba como ativar o TLS 1.2 utilizando a biblioteca de clientes .NET para o Armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "75371807"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Ativar o TLS seguro para o cliente do Armazenamento do Azure

A Segurança da Camada de Transporte (TLS) e a Camada de Tomadas Seguras (SSL) são protocolos criptográficos que fornecem segurança de comunicações sobre uma rede informática. SSL 1.0, 2.0 e 3.0 foram considerados vulneráveis. Foram proibidos pela RFC. TLS 1.0 torna-se inseguro para a utilização de cifra de blocoinsegura (DES CBC e RC2 CBC) e cifra stream (RC4). O conselho do PCI também sugeriu a migração para versões TLS mais altas. Para mais detalhes, pode ver a [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

O Azure Storage parou o SSL 3.0 desde 2015 e utiliza TLS 1.2 em pontos finais públicos de HTTPs, mas tLS 1.0 e TLS 1.1 ainda são suportados para retrocompatibilidade.

Para garantir uma ligação segura e conforme ao Armazenamento Azure, é necessário ativar a versão TLS 1.2 ou mais recente do lado do cliente antes de enviar pedidos para operar o serviço de Armazenamento Azure.

## <a name="enable-tls-12-in-net-client"></a>Ativar o TLS 1.2 no cliente .NET

Para que o cliente negoceie TLS 1.2, o SISTEMA e a versão .NET Framework precisam ambos de suportar tLS 1.2. Consulte mais detalhes no [Suporte para TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

A amostra que se segue mostra como ativar o TLS 1.2 no seu cliente .NET.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>Ativar o TLS 1.2 no cliente PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

A amostra que se segue mostra como ativar o TLS 1.2 no seu cliente PowerShell.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>Verifique a ligação TLS 1.2

Pode utilizar o Fiddler para verificar se o TLS 1.2 é realmente utilizado. Abra o Fiddler para começar a capturar o tráfego da rede de clientes e depois executar acima da amostra. Em seguida, pode encontrar a versão TLS na ligação que a amostra faz.

A imagem que se segue é uma amostra para a verificação.

![screenshot de verificação da versão TLS no Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Consulte também

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Conformidade com o PCI em TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Ativar TLS no cliente Java](https://www.java.com/en/configure_crypto.html)
