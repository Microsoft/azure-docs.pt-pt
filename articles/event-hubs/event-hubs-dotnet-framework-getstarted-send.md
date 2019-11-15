---
title: 'Início rápido: enviar e receber eventos usando o .NET Framework-hubs de eventos do Azure'
description: 'Início rápido: Este artigo fornece uma explicação para a criação de um aplicativo .NET Framework que envia eventos para os hubs de eventos do Azure.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 89419e9a3ef364d4095800a617a84ff2f63c09a0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720659"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Início rápido: enviar eventos para ou receber eventos dos hubs de eventos do Azure usando .NET Framework
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial mostra como criar .NET Framework aplicativos de console no C# para enviar eventos ou receber eventos de um eventhub. 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- [Microsoft Visual Studio 2019](https://visualstudio.com).
- **Crie um namespace de hubs de eventos e um hub de eventos**. O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento neste [artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de conexão para o namespace do hub de eventos** seguindo as instruções do artigo: obter a cadeia de [conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usará a cadeia de conexão mais adiante neste tutorial.

## <a name="send-events"></a>Enviar eventos 
Esta seção mostra como criar um aplicativo de console de .NET Framework para enviar eventos para um hub de eventos. 

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

No Visual Studio, crie um novo projeto de Aplicação de Ambiente de Trabalho do Visual C# com o modelo de projeto **Aplicação de Consola**. Dê ao projeto o nome **Remetente**.
   
![Criar aplicação da consola](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

1. No Explorador de Soluções, clique com o botão direito do rato no projeto **Emissor** e, em seguida, clique em **Gerir Pacotes NuGet para Solução**. 
2. Clique no separador **Procurar** e, em seguida, procure `WindowsAzure.ServiceBus`. Clique em **Instalar** e aceite os termos de utilização. 
   
    ![Instalar pacote NuGet do barramento de serviço](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    O Visual Studio transfere, instala e adiciona uma referência ao [Pacote NuGet da biblioteca do Service Bus do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adicione os campos seguintes à classe **Programa**, substituindo os valores dos marcadores de posição pelo nome do hub de eventos que criou na secção anterior e pela cadeia de ligação de ao nível do espaço de nomes que guardou anteriormente. Você pode copiar a cadeia de conexão para o Hub de eventos da **cadeia de conexão-chave primária** em **RootManageSharedAccessKey** na página hub de eventos na portal do Azure. Para obter etapas detalhadas, consulte [obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Adicione o seguinte método à classe **Programa**:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Este método envia continuamente eventos para o event hub com um atraso de 200 ms.
4. Por fim, adicione as seguintes linhas ao método **Main**:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Execute o programa e certifique-se de que não existem erros.
  
## <a name="receive-events"></a>Receber eventos
Nesta seção, você escreverá um aplicativo de console .NET Framework que recebe mensagens de um hub de eventos usando o [host do processador de eventos](event-hubs-event-processor-host.md). O [Anfitrião do Processador de Eventos](event-hubs-event-processor-host.md) é uma classe do .NET que simplifica a receção de eventos provenientes dos hubs de eventos ao gerir pontos de verificação persistentes e receções em paralelo desses hubs de eventos. Se utilizar o Anfitrião do Processador de Eventos, pode dividir eventos por múltiplos recetores, mesmo se estiverem alojados em nós diferentes. 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

No Visual Studio, crie um novo projeto de Aplicação de Ambiente de Trabalho do Visual C# com o modelo de projeto **Aplicação de Consola**. Dê ao projeto o nome **Recetor**.
   
![Criar aplicação da consola](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

1. No Explorador de Soluções, clique com o botão direito do rato no projeto **Recetor** e, em seguida, clique em **Gerir Pacotes NuGet para Solução**.
2. Clique no separador **Procurar** e, em seguida, procure `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Clique em **Instalar** e aceite os termos de utilização.
   
    ![Pesquisar pacote NuGet do host do processador de eventos](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    O Visual Studio permite transferir, instalar e adicionar uma referência ao [do Service Bus do Azure – Pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.

### <a name="implement-the-ieventprocessor-interface"></a>Implementar a interface IEventProcessor

1. Clique com o botão direito do rato no projeto **Recetor**, clique em **Adicionar** e em **Classe**. Dê à nova classe o nome **SimpleEventProcessor** e, em seguida, clique em **Adicionar** para criar a classe.
   
    ![Adicionar classe SimpleEventProcessor](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Adicione as seguintes declarações na parte superior do ficheiro SimpleEventProcessor.cs:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Substitua o seguinte código pelo corpo da classe:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Esta classe é chamada pelo **EventProcessorHost** para processar eventos recebidos do hub de eventos. A classe `SimpleEventProcessor` utiliza um cronómetro para chamar periodicamente o método de ponto de verificação no contexto do **EventProcessorHost**. Este processamento garante que, se o recetor for reiniciado, não perde mais do que cinco minutos de trabalho de processamento.

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Atualize o método Principal para utilizar SimpleEventProcessor

1. Na classe **Program**, adicione a seguinte declaração `using` na parte superior do ficheiro:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Substitua o método `Main` na classe `Program` pelo código a seguir, substituindo o nome do hub de eventos e a cadeia de conexão no nível do namespace que você salvou anteriormente, e a conta de armazenamento e a chave que você copiou nas seções anteriores. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Execute o programa e certifique-se de que não existem erros.
  
## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e terminologia nos hubs de eventos do Azure](event-hubs-features.md).
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
