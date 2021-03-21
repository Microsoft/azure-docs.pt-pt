---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 08/16/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 2684f89088854ecf05c547c77852155168baa38d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184494"
---
### <a name="create-a-console-application"></a>Criar uma aplicação de consola

No Visual Studio, crie um novo projeto de **Aplicação de Consola (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Adicionar o pacote NuGet de Reencaminhamento

1. Clique com o botão direito do rato no projeto recém-criado e, em seguida, selecione **Gerir Pacotes NuGet**.
2. Selecione a opção **Incluir pré-lançamento**. 
3. Selecione **Procurar** e, em seguida, procure **Microsoft.Azure.Relay**. Nos resultados da pesquisa, selecione  **Microsoft Azure Relay**.
4. Para a versão, selecione **2.0.0-preview1-20180523**. 
5. Selecione **Instalar** para concluir a instalação. Feche a caixa de diálogo.

### <a name="write-code-to-receive-messages"></a>Escrever código para receber mensagens

1. Na parte superior do ficheiro Program.cs, substitua as instruções `using` existentes pelas seguintes instruções `using`:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Net;
    using Microsoft.Azure.Relay;
    ```
2. Adicione constantes à classe `Program`, para obter os detalhes da ligação híbrida. Substitua os marcadores de posição entre parênteses retos pelos valores que obteve quando criou a ligação híbrida. Certifique-se de que utiliza o nome de espaço de nomes totalmente qualificado.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Adicione o método `ProcessMessagesOnConnection` à classe `Program`:
   
    ```csharp
    // The method initiates the connection.
    private static async void ProcessMessagesOnConnection(HybridConnectionStream relayConnection, CancellationTokenSource cts)
    {
        Console.WriteLine("New session");
   
        // The connection is a fully bidrectional stream. 
        // Put a stream reader and a stream writer over it.  
        // This allows you to read UTF-8 text that comes from 
        // the sender, and to write text replies back.
        var reader = new StreamReader(relayConnection);
        var writer = new StreamWriter(relayConnection) { AutoFlush = true };
        while (!cts.IsCancellationRequested)
        {
            try
            {
                // Read a line of input until a newline is encountered.
                var line = await reader.ReadLineAsync();
   
                if (string.IsNullOrEmpty(line))
                {
                    // If there's no input data, signal that 
                    // you will no longer send data on this connection,
                    // and then break out of the processing loop.
                    await relayConnection.ShutdownAsync(cts.Token);
                    break;
                }
   
                // Write the line on the console.
                Console.WriteLine(line);
   
                // Write the line back to the client, prepended with "Echo:"
                await writer.WriteLineAsync($"Echo: {line}");
            }
            catch (IOException)
            {
                // Catch an I/O exception. This likely occurred when
                // the client disconnected.
                Console.WriteLine("Client closed connection");
                break;
            }
        }
   
        Console.WriteLine("End session");
   
        // Close the connection.
        await relayConnection.CloseAsync(cts.Token);
    }
    ```
4. Adicione o método `RunAsync` à classe `Program`:
   
    ```csharp
    private static async Task RunAsync()
    {
        var cts = new CancellationTokenSource();
   
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Subscribe to the status events.
        listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
        listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
        listener.Online += (o, e) => { Console.WriteLine("Online"); };
   
        // Opening the listener establishes the control channel to
        // the Azure Relay service. The control channel is continuously 
        // maintained, and is reestablished when connectivity is disrupted.
        await listener.OpenAsync(cts.Token);
        Console.WriteLine("Server listening");
   
        // Provide callback for the cancellation token that will close the listener.
        cts.Token.Register(() => listener.CloseAsync(CancellationToken.None));
   
        // Start a new thread that will continuously read the console.
        new Task(() => Console.In.ReadLineAsync().ContinueWith((s) => { cts.Cancel(); })).Start();
   
        // Accept the next available, pending connection request. 
        // Shutting down the listener allows a clean exit. 
        // This method returns null.
        while (true)
        {
            var relayConnection = await listener.AcceptConnectionAsync();
            if (relayConnection == null)
            {
                break;
            }
   
            ProcessMessagesOnConnection(relayConnection, cts);
        }
   
        // Close the listener after you exit the processing loop.
        await listener.CloseAsync(cts.Token);
    }
    ```
5. Adicione a seguinte linha de código ao método `Main` na classe `Program`:
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    O ficheiro Program.cs completo deve ter o seguinte aspeto:
   
    ```csharp
    namespace Server
    {
        using System;
        using System.IO;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Relay;
   
        public class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            public static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                var cts = new CancellationTokenSource();
   
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
                // Subscribe to the status events.
                listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
                listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
                listener.Online += (o, e) => { Console.WriteLine("Online"); };
   
                // Opening the listener establishes the control channel to
                // the Azure Relay service. The control channel is continuously 
                // maintained, and is reestablished when connectivity is disrupted.
                await listener.OpenAsync(cts.Token);
                Console.WriteLine("Server listening");
   
                // Provide callback for a cancellation token that will close the listener.
                cts.Token.Register(() => listener.CloseAsync(CancellationToken.None));
   
                // Start a new thread that will continuously read the console.
                new Task(() => Console.In.ReadLineAsync().ContinueWith((s) => { cts.Cancel(); })).Start();
   
                // Accept the next available, pending connection request. 
                // Shutting down the listener allows a clean exit. 
                // This method returns null.
                while (true)
                {
                    var relayConnection = await listener.AcceptConnectionAsync();
                    if (relayConnection == null)
                    {
                        break;
                    }
   
                    ProcessMessagesOnConnection(relayConnection, cts);
                }
   
                // Close the listener after you exit the processing loop.
                await listener.CloseAsync(cts.Token);
            }
   
            private static async void ProcessMessagesOnConnection(HybridConnectionStream relayConnection, CancellationTokenSource cts)
            {
                Console.WriteLine("New session");
   
                // The connection is a fully bidrectional stream. 
                // Put a stream reader and a stream writer over it.  
                // This allows you to read UTF-8 text that comes from 
                // the sender, and to write text replies back.
                var reader = new StreamReader(relayConnection);
                var writer = new StreamWriter(relayConnection) { AutoFlush = true };
                while (!cts.IsCancellationRequested)
                {
                    try
                    {
                        // Read a line of input until a newline is encountered.
                        var line = await reader.ReadLineAsync();
   
                        if (string.IsNullOrEmpty(line))
                        {
                            // If there's no input data, signal that 
                            // you will no longer send data on this connection.
                            // Then, break out of the processing loop.
                            await relayConnection.ShutdownAsync(cts.Token);
                            break;
                        }
   
                        // Write the line on the console.
                        Console.WriteLine(line);
   
                        // Write the line back to the client, prepended with "Echo:"
                        await writer.WriteLineAsync($"Echo: {line}");
                    }
                    catch (IOException)
                    {
                        // Catch an I/O exception. This likely occurred when
                        // the client disconnected.
                        Console.WriteLine("Client closed connection");
                        break;
                    }
                }
   
                Console.WriteLine("End session");
   
                // Close the connection.
                await relayConnection.CloseAsync(cts.Token);
            }
        }
    }
    ```

