---
title: Visão geral do Azure Relay .NET ApIs padrão Microsoft Docs
description: Este artigo resume algumas das principais uma visão geral das Ligações Híbridas Azure Relay .NET Standard API.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 18eaf2d2daae817107be6cdb0da9359bb5f9b4e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514540"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Conexões Híbridas Azure Relay .NET Standard API visão geral

Este artigo resume algumas das principais Ligações Híbridas Azure Relay .NET Standard [cliente APIs](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Classe Relay Connection String Builder

As cordas de ligação de formatos de classe [RelayConnectionStringBuilder][RelayConnectionStringBuilder] são específicas para ligações híbridas reléreas. Pode usá-lo para verificar o formato de uma cadeia de ligação ou para construir uma cadeia de ligação a partir do zero. Consulte o seguinte código, por exemplo:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Também pode passar uma corda de `RelayConnectionStringBuilder` ligação diretamente ao método. Esta operação permite verificar se a cadeia de ligação está num formato válido. Se algum dos parâmetros for inválido, o `ArgumentException`construtor gera um .

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Fluxo de conexão híbrida

A classe [HybridConnectionStream][HCStream] é o principal objeto utilizado para enviar e receber dados de um ponto final do Azure Relay, quer esteja a trabalhar com um [HybridConnectionClient,][HCClient]ou um [HybridConnectionListener.][HCListener]

### <a name="getting-a-hybrid-connection-stream"></a>Obtenção de um fluxo de conexão híbrido

#### <a name="listener"></a>Serviço de Escuta

Utilizando um objeto [HybridConnectionListener,][HCListener] `HybridConnectionStream` pode obter um objeto da seguinte forma:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Cliente

Utilizando um objeto [HybridConnectionClient,][HCClient] `HybridConnectionStream` pode obter um objeto da seguinte forma:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Dados de receção

A classe [HybridConnectionStream][HCStream] permite uma comunicação bidirecional. Na maioria dos casos, recebe continuamente do riacho. Se estiver a ler texto a partir do fluxo, também poderá querer utilizar um objeto [StreamReader,](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) que permite uma análise mais fácil dos dados. Por exemplo, pode ler os dados `byte[]`como texto, em vez de como .

O seguinte código lê linhas individuais de texto a partir do fluxo até que seja solicitado um cancelamento:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Envio de dados

Uma vez estabelecida uma ligação, pode enviar uma mensagem para o ponto final do Retransmissor. Como o objeto de ligação herda `byte[]` [o Stream,](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)envie os seus dados como a . O exemplo que se segue mostra como fazê-lo:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

No entanto, se pretender enviar texto diretamente, sem precisar de codificar a corda de cada vez, pode embrulhar o `hybridConnectionStream` objeto com um objeto [StreamWriter.](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx)

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Relay, visite estes links:

* [Referência Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [O que é Azure Relay?](relay-what-is-it.md)
* [APIs de retransmissão disponível](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener