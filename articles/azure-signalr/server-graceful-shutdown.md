---
title: Pare o seu servidor de aplicações graciosamente.
description: Este artigo fornece informações sobre o servidor de aplicações SignalR de encerramento graciosamente
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201676"
---
# <a name="server-graceful-shutdown"></a>Paragem graciosa do servidor
O Microsoft Azure SignalR Service fornece dois modos para desligar graciosamente um servidor. 

A principal vantagem de utilizar esta funcionalidade é evitar que o seu cliente experimente quedas de ligação inesperadas. 

Em vez disso, pode esperar que as ligações do seu cliente se fechem em relação à sua lógica de negócio, ou até mesmo migrar a ligação do cliente para outro servidor sem perder dados. 

## <a name="how-it-works"></a>Como funciona

Em geral, haverá quatro etapas num processo gracioso de encerramento:

1. **Desative o servidor**

    Significa que não haverá mais ligações com o cliente para este servidor.

2. **Ganchos `OnShutdown` de gatilho**

    Pode registar ganchos de paragem para cada centro que possuiu no seu servidor.
    Serão chamados no que diz respeito à encomenda registada logo após termos recebido uma resposta **FINACK** do nosso Serviço Azure SignalR, o que significa que este servidor foi desligado no Serviço Azure SignalR.

    Pode transmitir mensagens ou fazer alguns trabalhos de limpeza nesta fase, uma vez executados todos os ganchos de encerramento, passaremos à fase seguinte.

3. **Aguarde até que todas as ligações do cliente estejam terminadas,** depende do modo que escolher, pode ser:

    **Modo definido para WaitForClientsToClose**

    O Serviço Azure SignalR irá deter clientes existentes.

    Pode ter de desenhar uma forma, como transmitir uma mensagem de encerramento a todos os clientes e, em seguida, deixar que os seus clientes decidam quando fechar/reconectar-se.

    Leia [o ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) para utilização de amostras, que transmitimos uma mensagem de "saída" para ativar o cliente no gancho de encerramento.

    **Modo definido para MigrateClients**

    O Serviço Azure SignalR tentará redirecionar a ligação do cliente neste servidor para outro servidor válido. 
    
    Neste cenário, `OnConnectedAsync` será `OnDisconnectedAsync` ativado no novo servidor e no antigo servidor, respectivamente, com um `IConnectionMigrationFeature` conjunto no , que pode ser utilizado para identificar se a `HttpContext` ligação ao cliente estava a ser migrada para a nossa migração. Pode ser útil especialmente para cenários imponentes.

    A ligação ao cliente será imediatamente migrada após a entrega da mensagem atual, o que significa que a próxima mensagem será encaminhada para o novo servidor.

4. **Parar as ligações do servidor**

    Depois de todas as ligações do cliente terem sido fechadas/migradas, ou o tempo limite (30s por defeito) exceder,

    O SignalR Server SDK irá proceder ao processo de paragem para esta fase e fechará todas as ligações do servidor.

    As ligações do cliente continuarão a ser eliminadas se não forem fechadas/migradas. Por exemplo, nenhum servidor alvo adequado / mensagem cliente-servidor atual ainda não terminou.

## <a name="sample-codes"></a>Códigos de amostra.

Adicione as seguintes opções `AddAzureSignalR` quando:

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>configurar `OnConnected` e `OnDisconnected` ao mesmo tempo definir o modo de paragem graciosa para `MigrateClients` .

Introduzimos uma "IConnectionMigrationFeature" para indicar se uma ligação estava a ser migrada para dentro/para fora.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```