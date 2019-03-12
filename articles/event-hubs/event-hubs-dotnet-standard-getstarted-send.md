---
title: Enviar eventos com .NET Core - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece um passo a passo para criar uma aplicação .NET Core para enviar eventos para Hubs de eventos do Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 2bf5825d3f13044d1c9bde2b05d1c93ecd967a0f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782042"
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-core"></a>Introdução ao envio de mensagens para Hubs de eventos do Azure no .NET Core
Os Event Hubs são um serviço que processa grandes quantidades de dados de eventos (telemetria) em aplicações e dispositivos ligados. Depois de recolher os dados para os Event Hubs, pode armazená-los utilizando um cluster de armazenamento ou transformá-los utilizando um fornecedor de análise em tempo real. Esta capacidade de processamento e recolha de eventos de grande escala é um componente importante das arquiteturas das aplicações modernas, incluindo a Internet das Coisas (IoT). Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial mostra como enviar eventos para um hub de eventos através de uma aplicação de consola escrita no C# com o .NET Core. 

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

## <a name="prerequisites"></a>Pré-requisitos
* [Microsoft Visual Studio 2015 ou 2017](https://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017, mas o Visual Studio 2015 também é suportado.
* [Ferramentas do .NET Core Visual Studio 2015 ou 2017](https://www.microsoft.com/net/core). 

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um espaço de nomes de Hubs de Eventos e um hub de eventos
O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nomes e um hub de eventos, siga o procedimento [este artigo](event-hubs-create.md).

Obter a cadeia de ligação para o espaço de nomes do hub de eventos ao seguir as instruções do artigo: [Obter cadeia de ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Irá utilizar a cadeia de ligação mais tarde neste tutorial. 

## <a name="create-a-console-application"></a>Criar uma aplicação de consola

Inicie o Visual Studio. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**. Criar uma aplicação de consola .NET Core.

![Novo projeto][1]

## <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

Adicionar a [ `Microsoft.Azure.EventHubs` ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) pacote NuGet da biblioteca .NET Core ao seu projeto através dos seguintes passos: 

1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
2. Clique no separador **Procurar** e, em seguida, procure "Microsoft.Azure.EventHubs" e, em seguida, selecione o pacote **Microsoft.Azure.EventHubs**. Clique em **Instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

1. Adicione as seguinte declarações `using` à parte superior do ficheiro Program.cs:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Adicione constantes à classe `Program` para a cadeia de ligação dos Hubs de Eventos e o caminho da entidade (nome do hub de eventos individual). Substitua os marcadores de posição entre parênteses retos pelos valores adequados que foram obtidos ao criar o hub de eventos. Certifique-se de que `{Event Hubs connection string}` é a cadeia de ligação ao nível do espaço de nomes e não é a cadeia de hub de eventos. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Adicione um método novo denominado `MainAsync` à classe `Program`, da seguinte forma:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Adicione um método novo denominado `SendMessagesToEventHub` à classe `Program`, da seguinte forma:

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Adicione o seguinte código ao método `Main` na classe `Program`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   O ficheiro Program.cs deve ter o seguinte aspeto.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Execute o programa e certifique-se de que não existem erros.

Parabéns! Enviou agora mensagens para um hub de eventos.

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, enviou mensagens para um hub de eventos com o .NET Core. Para saber como receber eventos de um hub de eventos com o .NET Core, veja [receber eventos do hub de eventos - .NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md).

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png
