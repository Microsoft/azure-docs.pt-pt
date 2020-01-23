---
title: Visão geral das APIs de .NET Standard de retransmissão do Azure | Microsoft Docs
description: Este artigo resume algumas das principais uma visão geral da API de .NET Standard de retransmissão do Azure Conexões Híbridas.
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
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514540"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Visão geral da API de .NET Standard de retransmissão do Azure Conexões Híbridas

Este artigo resume algumas das principais Conexões Híbridas de retransmissão do Azure .NET Standard [APIs de cliente](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Classe do construtor de cadeia de conexão de retransmissão

A classe [RelayConnectionStringBuilder][RelayConnectionStringBuilder] formata cadeias de conexão específicas para conexões híbridas de retransmissão. Você pode usá-lo para verificar o formato de uma cadeia de conexão ou para criar uma cadeia de conexão do zero. Consulte o código a seguir para obter um exemplo:

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

Você também pode passar uma cadeia de conexão diretamente para o método `RelayConnectionStringBuilder`. Essa operação permite que você verifique se a cadeia de conexão está em um formato válido. Se qualquer um dos parâmetros for inválido, o Construtor gerará um `ArgumentException`.

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

A classe [HybridConnectionStream][HCStream] é o objeto principal usado para enviar e receber dados de um ponto de extremidade de retransmissão do Azure, se você estiver trabalhando com um [HybridConnectionClient][HCClient]ou um [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Obtendo um fluxo de conexão híbrida

#### <a name="listener"></a>Serviço de Escuta

Usando um objeto [HybridConnectionListener][HCListener] , você pode obter um objeto `HybridConnectionStream` da seguinte maneira:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Cliente

Usando um objeto [HybridConnectionClient][HCClient] , você pode obter um objeto `HybridConnectionStream` da seguinte maneira:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Recebendo dados

A classe [HybridConnectionStream][HCStream] habilita a comunicação bidirecional. Na maioria dos casos, você recebe continuamente do fluxo. Se você estiver lendo o texto do fluxo, talvez também queira usar um objeto [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) , que permite a análise mais fácil dos dados. Por exemplo, você pode ler dados como texto, em vez de `byte[]`.

O código a seguir lê linhas individuais de texto do fluxo até que um cancelamento seja solicitado:

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

Depois de estabelecer uma conexão, você pode enviar uma mensagem para o ponto de extremidade de retransmissão. Como o objeto de conexão herda o [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), envie seus dados como um `byte[]`. O exemplo a seguir mostra como fazer isso:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

No entanto, se você quiser enviar texto diretamente, sem a necessidade de codificar a cadeia de caracteres a cada vez, você poderá encapsular o objeto `hybridConnectionStream` com um objeto [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) .

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a retransmissão do Azure, visite estes links:

* [Referência do Microsoft. Azure. Relay](/dotnet/api/microsoft.azure.relay)
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [APIs de retransmissão disponíveis](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener