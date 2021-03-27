---
title: 'Tutorial: Utilize configuração dinâmica utilizando atualização push numa aplicação .NET Core'
titleSuffix: Azure App Configuration
description: Neste tutorial, aprende-se a atualizar dinamicamente os dados de configuração para aplicações .NET Core utilizando atualização push
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: jken
ms.openlocfilehash: f330e69d37ac36e2a374e81fbffc6235c86feedd
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625726"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Tutorial: Utilize configuração dinâmica utilizando atualização push numa aplicação .NET Core

A biblioteca de clientes de Configuração de Aplicação .NET Core suporta a atualização da configuração a pedido sem causar o reinício de uma aplicação. Uma aplicação pode ser configurada para detetar alterações na Configuração de Aplicações usando uma ou ambas as seguintes abordagens.

1. Modelo de sondagem: Este é o comportamento padrão que usa as sondagens para detetar alterações na configuração. Uma vez expira o valor em cache de uma definição, a próxima chamada para `TryRefreshAsync` ou envia um pedido ao servidor para verificar se a `RefreshAsync` configuração foi alterada e puxa a configuração atualizada se necessário.

1. Push Model: Isto utiliza [eventos de configuração de aplicações](./concept-app-configuration-event.md) para detetar alterações na configuração. Uma vez configurada a Configuração de Aplicações para enviar eventos de alteração de valor chave para Azure Event Grid, a aplicação pode usar estes eventos para otimizar o número total de pedidos necessários para manter a configuração atualizada. As aplicações podem optar por subscrever estas diretamente a partir da Grade de Eventos, ou embora um dos manipuladores de [eventos suportados,](../event-grid/event-handlers.md) como um webhook, uma função Azure ou um tópico de Service Bus.

As aplicações podem optar por subscrever estes eventos diretamente da Grade de Eventos, ou através de um gancho web, ou reencaminhando eventos para a Azure Service Bus. A Azure Service Bus SDK fornece uma API para registar um manipulador de mensagens que simplifica este processo para aplicações que não têm um ponto final HTTP ou não querem sondar a grelha de eventos para alterações contínuas.

Este tutorial mostra como pode implementar atualizações de configuração dinâmica no seu código utilizando a atualização push. Baseia-se na aplicação introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação .NET Core com Configuração de Aplicação](./quickstart-dotnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para fazer os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar uma subscrição para enviar eventos de alteração de configuração da Configuração de Aplicações para um tópico de ônibus de serviço
> * Crie a sua aplicação .NET Core para atualizar a sua configuração em resposta a alterações na Configuração da Aplicação.
> * Consuma a mais recente configuração da sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Configurar o tópico e subscrição do Azure Service Bus

Este tutorial utiliza a integração de Service Bus para a Grade de Eventos para simplificar a deteção de alterações de configuração para aplicações que não desejem sondar a Configuração da App para alterações contínuas. O Azure Service Bus SDK fornece uma API para registar um manipulador de mensagens que pode ser usado para atualizar a configuração quando as alterações são detetadas na Configuração da Aplicação. Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrição](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) para criar um espaço de nome, tópico e subscrição de um serviço de ônibus.

Uma vez criados os recursos, adicione as seguintes variáveis ambientais. Estes serão utilizados para registar um manipulador de eventos para alterações de configuração no código de aplicação.

| Chave | Valor |
|---|---|
| ServiceBusConnectionString | Cadeia de conexão para o espaço de nome do ônibus de serviço |
| ServiceBusTopic | Nome do tópico do ônibus de serviço |
| Subscrição do ServiceBusSubscription | Nome da subscrição de autocarro de serviço |

## <a name="set-up-event-subscription"></a>Configurar a subscrição do Evento

1. Abra o recurso de Configuração da Aplicação no portal Azure e, em seguida, clique `+ Event Subscription` no `Events` painel.

    ![Eventos de Configuração de Aplicativos](./media/events-pane.png)

1. Insira um nome para o `Event Subscription` e o `System Topic` .

    ![Criar subscrição de eventos](./media/create-event-subscription.png)

1. Selecione o `Endpoint Type` como `Service Bus Topic` , eleja o tópico de ônibus de serviço e, em seguida, clique em `Confirm Selection` .

    ![Ponto final do serviço de subscrição de eventos](./media/event-subscription-servicebus-endpoint.png)

1. Clique `Create` para criar a subscrição do evento.

1. Clique `Event Subscriptions` no `Events` painel para validar que a subscrição foi criada com sucesso.

    ![Subscrições de eventos de configuração de aplicativos](./media/event-subscription-view.png)

> [!NOTE]
> Ao subscrever alterações de configuração, um ou mais filtros podem ser utilizados para reduzir o número de eventos enviados para a sua aplicação. Estes podem ser configurados quer como [filtros de subscrição de Event Grid,](../event-grid/event-filtering.md) quer [como filtros de subscrição de Service Bus.](../service-bus-messaging/topic-filters.md) Por exemplo, um filtro de subscrição só pode ser usado para subscrever eventos para alterações numa chave que começa com uma cadeia específica.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Registar o manipulador de eventos para recarregar dados da Configuração de Aplicações

*Programa Aberto.cs* e atualize o ficheiro com o seguinte código.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

O método [SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) é utilizado para definir o valor em cache para valores-chave registados para atualização como sujo. Isto garante que a próxima chamada para `RefreshAsync` ou `TryRefreshAsync` revalidar os valores em cache com a Configuração da Aplicação e os atualiza se necessário.

Um atraso aleatório é adicionado antes que o valor em cache seja marcado como sujo para reduzir o potencial estrangulamento no caso de várias instâncias se refrescarem ao mesmo tempo. O atraso máximo predefinido antes do valor em cache é marcado como sujo é de 30 segundos, mas pode ser ultrapassado passando um parâmetro opcional `TimeSpan` para o `SetDirty` método.

> [!NOTE]
> Para reduzir o número de pedidos para a Configuração da Aplicação ao utilizar a atualização de push, é importante ligar `SetCacheExpiration(TimeSpan cacheExpiration)` com um valor adequado de `cacheExpiration` parâmetro. Isto controla o tempo de validade da cache para a atualização de puxar e pode ser usado como uma rede de segurança no caso de haver algum problema com a subscrição do Evento ou com a subscrição do Service Bus. O valor recomendado é `TimeSpan.FromDays(30)` .

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. **DesconfigurationConnectionString,** e desafie-a para a chave de acesso à sua loja de Configuração de Aplicações. Se utilizar o pedido de comando do Windows, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração entre em vigor:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Executar o seguinte comando para construir a aplicação de consola:

    ```console
     dotnet build
    ```

1. Após a construção concluída com sucesso, executar o seguinte comando para executar a aplicação localmente:

    ```console
     dotnet run
    ```

    ![Empurre a atualização antes da atualização](./media/dotnet-core-app-pushrefresh-initial.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos** e selecione a instância da loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **Configuration Explorer** e atualize os valores das seguintes teclas:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure - Atualizado |

1. Aguarde 30 segundos para permitir que o evento seja processado e a configuração seja atualizada.

    ![Empurre a corrida de atualização após atualização](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que a sua aplicação .NET Core atualizasse dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)