---
title: Ativar TLS seguros com .NET
titleSuffix: Azure Storage
description: Saiba como ativar o TLS 1.2 utilizando a biblioteca de clientes .NET para armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75371807"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Ativar o TLS seguro para o cliente do Armazenamento do Azure

A Segurança da Camada de Transporte (TLS) e a Camada de Tomadas Seguras (SSL) são protocolos criptográficos que proporcionam segurança de comunicações sobre uma rede de computador. SSL 1.0, 2.0 e 3.0 foram considerados vulneráveis. Foram proibidos pela RFC. TLS 1.0 torna-se inseguro por utilizar a cifra do bloco insegura (DES CBC e RC2 CBC) e a cifra de fluxo (RC4). O conselho do PCI também sugeriu a migração para versões TLS mais altas. Para mais detalhes, pode ver [a Segurança da Camada de Transporte (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

O Azure Storage parou o SSL 3.0 desde 2015 e utiliza tLS 1.2 em pontos finais públicos de HTTPs, mas os TLS 1.0 e TLS 1.1 ainda são suportados para retrocompatibilidade.

Para garantir uma ligação segura e conforme ao Azure Storage, é necessário ativar a versão TLS 1.2 ou mais recente do lado do cliente antes de enviar pedidos para operar o serviço de Armazenamento Azure.

## <a name="enable-tls-12-in-net-client"></a>Ativar o TLS 1.2 no cliente .NET

Para que o cliente negocie o TLS 1.2, o SO e a versão do Application Framework têm de suportar o TLS 1.2. Ver mais detalhes no [Suporte para TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

O exemplo seguinte mostra como ativar o TLS 1.2 no cliente .NET.

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

Pode utilizar o Violinista para verificar se o TLS 1.2 é realmente utilizado. Abrir o Violino para começar a capturar o tráfego da rede de clientes e executar acima da amostra. Em seguida, pode encontrar a versão TLS na ligação que a amostra faz.

A imagem a seguir é uma amostra para a verificação.

![screenshot de verificar a versão TLS no Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Veja também

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Conformidade dos PCI no TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Ativar TLS em java cliente](https://www.java.com/en/configure_crypto.html)
