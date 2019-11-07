---
title: Como dimensionar com várias instâncias do serviço de Signaler do Azure
description: Em muitos cenários de dimensionamento, o cliente geralmente precisa provisionar várias instâncias e configurar para usá-las juntas, para criar uma implantação em larga escala. Por exemplo, a fragmentação requer suporte a várias instâncias.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 1e31bc4133cced793d793c07d2e0ee3df29efddb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672342"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Como dimensionar o serviço de Signalr com várias instâncias?
O SDK do serviço de sinalização mais recente dá suporte a vários pontos de extremidade para instâncias de serviço do Signalr. Você pode usar esse recurso para dimensionar as conexões simultâneas ou usá-las para mensagens entre regiões.

## <a name="for-aspnet-core"></a>Para ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos de extremidade da configuração?

Configuração com chave `Azure:SignalR:ConnectionString` ou `Azure:SignalR:ConnectionString:` para a cadeia de conexão do serviço Signalr.

Se a chave começar com `Azure:SignalR:ConnectionString:`, ela deverá estar no formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, em que `Name` e `EndpointType` são propriedades do objeto `ServiceEndpoint` e podem ser acessadas do código.

Você pode adicionar várias cadeias de conexão de instância usando os seguintes comandos de `dotnet`:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos de extremidade do código?

Uma classe `ServicEndpoint` é introduzida para descrever as propriedades de um ponto de extremidade do serviço de Signaler do Azure.
Você pode configurar vários pontos de extremidade de instância ao usar o SDK do serviço de Signaler do Azure por meio de:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Como personalizar o roteador de ponto de extremidade?

Por padrão, o SDK usa o [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) para escolher pontos de extremidade.

#### <a name="default-behavior"></a>Comportamento predefinido 
1. Roteamento de solicitação do cliente

    Quando o cliente `/negotiate` com o servidor de aplicativos. Por padrão, o SDK **seleciona aleatoriamente** um ponto de extremidade do conjunto de pontos de extremidade de serviço disponíveis.

2. Roteamento de mensagens do servidor

    Quando * enviar mensagem para uma conexão * * específica * * * e a conexão de destino for roteada para o servidor atual, a mensagem vai diretamente para esse ponto de extremidade conectado. Caso contrário, as mensagens são transmitidas para todos os pontos de extremidade do Signalr do Azure.

#### <a name="customize-routing-algorithm"></a>Personalizar o algoritmo de roteamento
Você pode criar seu próprio roteador quando tiver conhecimento especial para identificar para quais pontos de extremidade as mensagens devem ir.

Um roteador personalizado é definido abaixo como um exemplo quando os grupos que começam com `east-` sempre vão para o ponto de extremidade chamado `east`:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Outro exemplo abaixo, que substitui o comportamento de negociação padrão, para selecionar os pontos de extremidade depende de onde o servidor de aplicativos está localizado.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Não se esqueça de registrar o roteador no contêiner DI usando:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Para ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos de extremidade da configuração?

Configuração com chave `Azure:SignalR:ConnectionString` ou `Azure:SignalR:ConnectionString:` para a cadeia de conexão do serviço Signalr.

Se a chave começar com `Azure:SignalR:ConnectionString:`, ela deverá estar no formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, em que `Name` e `EndpointType` são propriedades do objeto `ServiceEndpoint` e podem ser acessadas do código.

Você pode adicionar várias cadeias de conexão de instância a `web.config`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos de extremidade do código?

Uma classe `ServicEndpoint` é introduzida para descrever as propriedades de um ponto de extremidade do serviço de Signaler do Azure.
Você pode configurar vários pontos de extremidade de instância ao usar o SDK do serviço de Signaler do Azure por meio de:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Como personalizar o roteador?

A única diferença entre o Signalr ASP.NET e o Signalr ASP.NET Core é o tipo de contexto http para `GetNegotiateEndpoint`. Para o Signalr ASP.NET, é do tipo [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) .

Veja abaixo o exemplo de negociação personalizada para o Signalr ASP.NET:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Não se esqueça de registrar o roteador no contêiner DI usando:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Configuração em cenários entre regiões

O objeto `ServiceEndpoint` tem uma propriedade `EndpointType` com o valor `primary` ou `secondary`.

`primary` pontos de extremidade são pontos de extremidade preferenciais para receber o tráfego do cliente e são considerados conexões de rede mais confiáveis; `secondary` pontos de extremidade são considerados como tendo conexões de rede menos confiáveis e são usados apenas para levar o servidor ao tráfego do cliente, por exemplo, transmitir mensagens, não para levar o cliente ao tráfego do servidor.

Em casos entre regiões, a rede pode ser instável. Para um servidor de aplicativos localizado no *leste dos EUA*, o ponto de extremidade do serviço signalr localizado na mesma região *leste dos EUA* pode ser configurado como `primary` e pontos de extremidade em outras regiões marcadas como `secondary`. Nessa configuração, os pontos de extremidade de serviço em outras regiões podem **receber** mensagens desse servidor de aplicativos *leste dos EUA* , mas não haverá clientes **entre regiões** roteados para esse servidor de aplicativos. A arquitetura é mostrada no diagrama a seguir:

![Infraestrutura de Geografia cruzado](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quando um cliente tenta `/negotiate` com o servidor de aplicativos, com o roteador padrão, o SDK **seleciona aleatoriamente** um ponto de extremidade a partir do conjunto de pontos de extremidade de `primary` disponíveis. Quando o ponto de extremidade primário não estiver disponível, o SDK **selecionará aleatoriamente** de todos os pontos de extremidades de `secondary` disponíveis. O ponto de extremidade é marcado como **disponível** quando a conexão entre o servidor e o ponto de extremidade de serviço está ativa.

No cenário entre regiões, quando um cliente tenta `/negotiate` com o servidor de aplicativos hospedado no *leste dos EUA*, por padrão ele sempre retorna o ponto de extremidade `primary` localizado na mesma região. Quando todos os pontos de extremidade do *leste dos EUA* não estiverem disponíveis, o cliente será redirecionado para pontos de extremidade em outras regiões. A seção de failover abaixo descreve o cenário em detalhes.

![Negociação normal](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Failover

Quando todos os pontos de extremidade de `primary` não estão disponíveis, os `/negotiate` do cliente são escolhidos dos pontos de extremidade disponíveis do `secondary`. Esse mecanismo de failover requer que cada ponto de extremidade sirva como `primary` ponto de extremidade para pelo menos um servidor de aplicativos.

![Failover](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia, você aprendeu sobre como configurar várias instâncias no mesmo aplicativo para dimensionamento, fragmentação e cenários entre regiões.

Vários pontos de extremidade oferecem suporte também podem ser usados em cenários de alta disponibilidade e recuperação de desastres.

> [!div class="nextstepaction"]
> [Configurar o serviço de sinalização para recuperação de desastre e alta disponibilidade](./signalr-concept-disaster-recovery.md)
