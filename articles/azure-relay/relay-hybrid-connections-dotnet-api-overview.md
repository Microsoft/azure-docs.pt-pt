---
title: Visão geral do Azure Relay .NET Standard APIs / Microsoft Docs
description: Este artigo resume algumas das chaves uma visão geral das Ligações Híbridas Azure Relay .NET Standard API.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 578d0fd2bbf8b9bb897a79e88399dee3711f5990
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85316833"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Conexões híbridas Azure Relay .NET Standard API visão geral

Este artigo resume algumas das principais ligações híbridas Azure Relay .NET Standard [client APIs](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Classe de construtor de cordas de ligação de retransmissão

As cadeias de ligação da classe [RelayConnectionStringBuilder][RelayConnectionStringBuilder] específicas das ligações híbridas de retransmissão. Pode usá-lo para verificar o formato de uma cadeia de ligação, ou para construir uma cadeia de ligação a partir do zero. Consulte o seguinte código para um exemplo:

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

Também pode passar uma cadeia de ligação diretamente para o `RelayConnectionStringBuilder` método. Esta operação permite-lhe verificar se a cadeia de ligação se encontra num formato válido. Se algum dos parâmetros for inválido, o construtor gera um `ArgumentException` .

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

## <a name="hybrid-connection-stream"></a>Fluxo de conexão híbrido

A classe [HybridConnectionStream][HCStream] é o principal objeto utilizado para enviar e receber dados de um ponto final do Azure Relay, quer esteja a trabalhar com um [HybridConnectionClient,][HCClient]ou um [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Obtenção de um fluxo de conexão híbrido

#### <a name="listener"></a>Serviço de Escuta

Utilizando um objeto [HíbridoConnectionListener,][HCListener] pode obter um `HybridConnectionStream` objeto da seguinte forma:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Cliente

Utilizando um objeto [HíbridoConnectionClient,][HCClient] pode obter um `HybridConnectionStream` objeto da seguinte forma:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Receber dados

A classe [HybridConnectionStream][HCStream] permite uma comunicação bidirecional. Na maioria dos casos, recebes continuamente do fluxo. Se estiver a ler texto a partir do stream, também poderá querer utilizar um objeto [StreamReader,](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) que permite uma análise mais fácil dos dados. Por exemplo, pode ler dados como texto, em vez de como `byte[]` .

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

Uma vez estabelecida uma ligação, pode enviar uma mensagem para o ponto final do Retransmissor. Como o objeto de ligação herda [o Stream,](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)envie os seus dados como um `byte[]` . O exemplo a seguir mostra como fazê-lo:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

No entanto, se pretender enviar texto diretamente, sem precisar de codificar a cadeia de cada vez, pode embrulhar o `hybridConnectionStream` objeto com um objeto [StreamWriter.](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx)

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o Azure Relay, visite estes links:

* [Referência Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [APIs de retransmissão disponível](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener