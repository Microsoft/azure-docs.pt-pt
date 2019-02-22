---
title: Tutorial – Atualizar variedade de inventário de comércio a retalho utilizando canais de publicação/subscrição e filtros de tópico com o portal do Azure | Microsoft Docs
description: Neste tutorial, aprende a enviar e receber mensagens de um tópico e subscrição e como adicionar e utilizar regras de filtro utilizando .NET
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: c04b9b04a14e5cba205db5e0fa86094ef098bc7b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585878"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Tutorial: Atualizar inventário com o portal do Azure e tópicos/subscrições

O Microsoft Azure Service Bus é um serviço de mensagens na cloud multi-inquilino que envia informações entre aplicações e serviços. As operações assíncronas permitem o envio flexível de mensagens mediadas, juntamente com mensagens FIFO (first in, first out) e funcionalidades de publicação/subscrição. Este tutorial mostra como utilizar tópicos e subscrições do Service Bus num cenário de inventário de comércio a retalho, com canais de publicação/subscrição utilizando o portal do Azure e .NET.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um tópico e uma ou mais subscrições do Service Bus para esse tópico com o portal do Azure
> * Adicionar filtros de tópico com o código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar que chegaram nas subscrições previstas
> * Receber mensagens das subscrições

Um exemplo deste cenário é uma atualização da variedade de inventário para várias lojas de comércio a retalho. Neste cenário, cada loja ou grupo de lojas recebe mensagens dirigidas a elas para atualizar a respetiva variedade. Este tutorial mostra como implementar este cenário com subscrições e filtros. Primeiro, cria um tópico com três subscrições, adiciona algumas regras e filtros e, em seguida, envia e recebe mensagens do tópico e subscrições.

![tópico](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita][] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem instalada:

- [Visual Studio 2017 Atualização 3 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
- [SDK NET Core](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="service-bus-topics-and-subscriptions"></a>Tópicos e subscrições do Service Bus

Cada [subscrição de um tópico](service-bus-messaging-overview.md#topics) pode receber uma cópia de cada mensagem. Os tópicos são totalmente compatíveis no que diz respeito a protocolo e semântica com as filas do Service Bus. Os tópicos do Service Bus suportam uma vasta gama de regras de seleção com condições de filtro, com ações opcionais que definem ou modificam propriedades de mensagem. Sempre que uma regra tem correspondência, é criada uma mensagem. Para saber mais sobre regras, filtros e ações, clique nesta [hiperligação](topic-filters.md).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>Criar regras de filtro nas subscrições

Depois do aprovisionamento do espaço de nomes e tópico/subscrições e de ter as credenciais necessárias, está pronto para criar regras de filtro nas subscrições e enviar e receber mensagens. Pode examinar o código nesta [pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

### <a name="send-and-receive-messages"></a>Enviar e receber mensagens

Para executar o código, faça o seguinte:

1. Numa linha de comandos ou na linha de comandos do PowerShell, clone o [repositório do GitHub do Service Bus](https://github.com/Azure/azure-service-bus/) indicando o seguinte comando:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Navegue para a pasta de exemplo `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters`.

3. Obtenha a cadeia de ligação que copiou para o Bloco de Notas na secção [Obter as credenciais de gestão](#obtain-the-management-credentials) deste tutorial. Também necessita do nome do tópico criado na secção anterior.

4. Na linha de comandos, escreva o seguinte comando:

   ```shell
   dotnet build
   ```

5. Navegue para a pasta `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0`.

6. Escreva o seguinte comando para executar o programa. Não se esqueça de substituir `myConnectionString` pelo valor obtido anteriormente e `myTopicName` pelo nome do tópico que criou:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Siga as instruções na consola para selecionar a criação do filtro primeiro. Parte da criação de filtros consiste na remoção dos filtros predefinidos. Quando utiliza PowerShell ou CLI não necessita de remover o filtro predefinido, mas se o fizer no código, terá de o remover. Os comandos 1 e 3 da consola ajudam a gerir os filtros nas subscrições criadas anteriormente:

   - Execute 1: para remover os filtros predefinidos.
   - Execute 2: para adicionar os seus filtros.
   - Execute 3: para remover opcionalmente os seus filtros. Tenha em atenção que isto não volta a criar os filtros predefinidos.

    ![Mostrar saída de 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Depois da criação do filtro, pode enviar mensagens. Prima 4 e observe o envio de 10 mensagens para o tópico:

    ![Saída de envio](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Prima 5 e observe as mensagens a serem recebidas. Se não receber 10 mensagens, prima "m" para apresentar o menu e volte a premir 5.

    ![Saída de receção](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o espaço de nomes e a fila. Para tal, selecione estes recursos no portal e clique em **Eliminar**.

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Esta secção contém mais detalhes sobre o que faz o código de exemplo.

### <a name="get-connection-string-and-topic"></a>Obter a cadeia de ligação e o tópico

Primeiro, o código declara um conjunto de variáveis, que aciona a execução restante do programa.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

A cadeia de ligação e o nome do tópico são transmitidos através dos parâmetros da linha de comandos como mostrado e são lidos no método `Main()`:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Remover filtros predefinidos

Quando cria uma subscrição, o Service Bus cria um filtro predefinido por subscrição. Este tópico permite a receção de cada mensagem enviada para o tópico. Se pretender utilizar filtros personalizados, poderá remover o filtro predefinido, como mostrado no código seguinte:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Criar filtros

O código seguinte adiciona os filtros personalizados definidos neste tutorial:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Remover os filtros criados personalizados

Se pretender remover todos os filtros na sua subscrição, o código seguinte mostrará como fazê-lo:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Enviar mensagens

O envio de mensagens para um tópico é semelhante ao envio de mensagens para uma fila. Este exemplo mostra como enviar mensagens, utilizando uma lista de tarefas e processamento assíncrono:

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Receber mensagens

As mensagens são novamente recebidas através de uma lista de tarefas e o código utiliza a criação de lotes. Pode enviar e receber utilizando a criação de lotes, mas este exemplo só mostra como receber lotes. Na verdade, não interromperia o ciclo, mas continuaria o ciclo e definia um período de tempo maior, como um minuto. A chamada de receção para o mediador é mantida aberta durante este tempo e, se chegarem mensagens, serão devolvidas imediatamente e será emitida uma nova chamada de receção. Este conceito é denominado *consulta longa*. Utilizar a bomba de receção que pode ver no [início rápido](service-bus-quickstart-portal.md), e noutros vários exemplos no repositório, é uma opção mais típica.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprovisionou recursos utilizando o portal do Azure e enviou e recebeu mensagens de um tópico do Service Bus e respetivas subscrições. Aprendeu a:

> [!div class="checklist"]
> * Criar um tópico e uma ou mais subscrições do Service Bus para esse tópico com o portal do Azure
> * Adicionar filtros de tópico com o código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar que chegaram nas subscrições previstas
> * Receber mensagens das subscrições

Para ver mais exemplos sobre o envio e receção de mensagens, comece com os [exemplos do Service Bus do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Avance para o próximo tutorial para saber mais sobre a utilização de capacidades de publicação/subscrição do Service Bus.

> [!div class="nextstepaction"]
> [Atualizar inventário com o PowerShell e tópicos/subscrições](service-bus-tutorial-topics-subscriptions-powershell.md)

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png
