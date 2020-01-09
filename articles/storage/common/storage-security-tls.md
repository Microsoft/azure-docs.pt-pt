---
title: Habilitar TLS seguro com .NET
titleSuffix: Azure Storage
description: Saiba como habilitar o TLS 1,2 usando a biblioteca de cliente .NET para o armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371807"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Ativar o TLS seguro para o cliente do Armazenamento do Azure

O protocolo TLS e protocolo SSL (SSL) são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores. SSL 1,0, 2,0 e 3,0 foram considerados vulneráveis. Eles foram proibidos pela RFC. O TLS 1,0 se torna inseguro para usar a codificação de bloco inseguro (DES CBC e RC2 CBC) e a codificação de fluxo (RC4). O PCI Council também sugeriu a migração para versões mais altas do TLS. Para obter mais detalhes, você pode ver [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

O armazenamento do Azure parou o SSL 3,0 desde 2015 e usa o TLS 1,2 em pontos de extremidade HTTPs públicos, mas o TLS 1,0 e o TLS 1,1 ainda têm suporte para compatibilidade com versões anteriores.

Para garantir a conexão segura e em conformidade com o armazenamento do Azure, você precisa habilitar o TLS 1,2 ou versão mais recente no lado do cliente antes de enviar solicitações para operar o serviço de armazenamento do Azure.

## <a name="enable-tls-12-in-net-client"></a>Habilitar o TLS 1,2 no cliente .NET

Para que o cliente negocie o TLS 1,2, o sistema operacional e a versão de .NET Framework precisam dar suporte a TLS 1,2. Veja mais detalhes em [suporte para TLS 1,2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

O exemplo a seguir mostra como habilitar o TLS 1,2 em seu cliente .NET.

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

## <a name="enable-tls-12-in-powershell-client"></a>Habilitar o TLS 1,2 no cliente do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

O exemplo a seguir mostra como habilitar o TLS 1,2 em seu cliente do PowerShell.

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

## <a name="verify-tls-12-connection"></a>Verificar conexão TLS 1,2

Você pode usar o Fiddler para verificar se o TLS 1,2 é usado na verdade. Abra o Fiddler para iniciar a captura do tráfego de rede do cliente e execute a amostra acima. Em seguida, você pode encontrar a versão de TLS na conexão que o exemplo faz.

A captura de tela a seguir é um exemplo para a verificação.

![captura de tela da verificação da versão de TLS no Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Ver também

* [Segurança de camada de transporte (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Conformidade com PCI no TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Habilitar TLS no cliente Java](https://www.java.com/en/configure_crypto.html)
