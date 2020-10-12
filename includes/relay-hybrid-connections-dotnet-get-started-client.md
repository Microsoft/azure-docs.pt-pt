---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 2d6836b2bf667e4170e67a95dc1daad72a769eb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184493"
---
### <a name="create-a-console-application"></a>Criar uma aplicação de consola

No Visual Studio, crie um novo projeto de **Aplicação de Consola (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Adicionar o pacote NuGet de Reencaminhamento

1. Clique com o botão direito do rato no projeto recém-criado e, em seguida, selecione **Gerir Pacotes NuGet**.
2. Selecione **Procurar** e, em seguida, procure **Microsoft.Azure.Relay**. Nos resultados da pesquisa, selecione **Reencaminhamento do Microsoft Azure**. 
3. Selecione **Instalar** para concluir a instalação. Feche a caixa de diálogo.

### <a name="write-code-to-send-messages"></a>Escrever código para enviar mensagens

1. Na parte superior do ficheiro Program.cs, substitua as instruções `using` existentes pelas seguintes instruções `using`:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. Adicione constantes à classe `Program`, para obter os detalhes da ligação híbrida. Substitua os marcadores de posição entre parênteses retos pelos valores que obteve quando criou a ligação híbrida. Certifique-se de que utiliza o nome de espaço de nomes totalmente qualificado.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Adicione o seguinte método à classe `Program`:
   
    ```csharp
    private static async Task RunAsync()
    {
        Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
        // Create a new hybrid connection client.
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Initiate the connection.
        var relayConnection = await client.CreateConnectionAsync();
   
        // Run two concurrent loops on the connection. One 
        // reads input from the console and writes it to the connection 
        // with a stream writer. The other reads lines of input from the 
        // connection with a stream reader and writes them to the console. 
        // Entering a blank line shuts down the write task after 
        // sending it to the server. The server then cleanly shuts down
        // the connection, which terminates the read task.
   
        var reads = Task.Run(async () => {
            // Initialize the stream reader over the connection.
            var reader = new StreamReader(relayConnection);
            var writer = Console.Out;
            do
            {
                // Read a full line of UTF-8 text up to newline.
                string line = await reader.ReadLineAsync();
                // If the string is empty or null, you are done.
                if (String.IsNullOrEmpty(line))
                    break;
                // Write to the console.
                await writer.WriteLineAsync(line);
            }
            while (true);
        });
   
        // Read from the console and write to the hybrid connection.
        var writes = Task.Run(async () => {
            var reader = Console.In;
            var writer = new StreamWriter(relayConnection) { AutoFlush = true };
            do
            {
                // Read a line from the console.
                string line = await reader.ReadLineAsync();
                // Write the line out, also when it's empty.
                await writer.WriteLineAsync(line);
                // Quit when the line is empty,
                if (String.IsNullOrEmpty(line))
                    break;
            }
            while (true);
        });
   
        // Wait for both tasks to finish.
        await Task.WhenAll(reads, writes);
        await relayConnection.CloseAsync(CancellationToken.None);
    }
    ```
4. Adicione a seguinte linha de código ao método `Main` na classe `Program`.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    O ficheiro Program.cs deve ter o seguinte aspeto:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
                // Create a new hybrid connection client.
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
                // Initiate the connection.
                var relayConnection = await client.CreateConnectionAsync();
   
                // Run two concurrent loops on the connection. One 
                // reads input from the console and then writes it to the connection 
                // with a stream writer. The other reads lines of input from the 
                // connection with a stream reader and then writes them to the console. 
                // Entering a blank line shuts down the write task after 
                // sending it to the server. The server then cleanly shuts down
                // the connection, which terminates the read task.
   
                var reads = Task.Run(async () => {
                    // Initialize the stream reader over the connection.
                    var reader = new StreamReader(relayConnection);
                    var writer = Console.Out;
                    do
                    {
                        // Read a full line of UTF-8 text up to newline.
                        string line = await reader.ReadLineAsync();
                        // If the string is empty or null, you are done.
                        if (String.IsNullOrEmpty(line))
                            break;
                        // Write to the console.
                        await writer.WriteLineAsync(line);
                    }
                    while (true);
                });
   
                // Read from the console and write to the hybrid connection.
                var writes = Task.Run(async () => {
                    var reader = Console.In;
                    var writer = new StreamWriter(relayConnection) { AutoFlush = true };
                    do
                    {
                        // Read a line from the console.
                        string line = await reader.ReadLineAsync();
                        // Write the line out, also when it's empty.
                        await writer.WriteLineAsync(line);
                        // Quit when the line is empty.
                        if (String.IsNullOrEmpty(line))
                            break;
                    }
                    while (true);
                });
   
                // Wait for both tasks to finish.
                await Task.WhenAll(reads, writes);
                await relayConnection.CloseAsync(CancellationToken.None);
            }
        }
    }
    ```

