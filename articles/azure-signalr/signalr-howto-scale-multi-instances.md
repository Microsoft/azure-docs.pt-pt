---
title: Escala com múltiplas instâncias - Serviço De Sinalização Azure
description: Em muitos cenários de escala, o cliente muitas vezes precisa fornecer múltiplas instâncias e configurar para usá-los juntos, para criar uma implementação em larga escala. Por exemplo, sharding requer apoio de vários casos.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158155"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Como escalar o Serviço SignalR com múltiplas instâncias?
O mais recente SignalR Service SDK suporta vários pontos finais para as instâncias do Serviço SignalR. Pode utilizar esta funcionalidade para escalar as ligações simultâneas ou usá-la para mensagens transversais.

## <a name="for-aspnet-core"></a>Para ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos finais da config?

Config com `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` chave ou para a cadeia de ligação signalR service.

Se a chave `Azure:SignalR:ConnectionString:`começar com, `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`deve `Name` estar `EndpointType` em formato, onde e são propriedades do `ServiceEndpoint` objeto, e estão acessíveis a partir do código.

Pode adicionar cordas de ligação `dotnet` de várias instâncias utilizando os seguintes comandos:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos finais do código?

Uma `ServicEndpoint` classe é introduzida para descrever as propriedades de um ponto final do Serviço De Sinalização Azure.
Pode configurar vários pontos finais de instância ao utilizar o Serviço De Sinalização Azure SDK através de:
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

### <a name="how-to-customize-endpoint-router"></a>Como personalizar o router endpoint?

Por predefinição, o SDK utiliza o [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) para captar pontos finais.

#### <a name="default-behavior"></a>Comportamento predefinido 
1. Encaminhamento de pedidos de cliente

    Quando `/negotiate` cliente com o servidor de aplicações. Por predefinição, o SDK **seleciona aleatoriamente** um ponto final do conjunto de pontos finais de serviço disponíveis.

2. Encaminhamento de mensagem do servidor

    Quando *enviando mensagem para uma ligação específica ****** e a ligação do alvo é encaminhada para o servidor atual, a mensagem vai diretamente para esse ponto final conectado. Caso contrário, as mensagens são transmitidas para todos os pontos finais do Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Personalizar algoritmo de encaminhamento
Pode criar o seu próprio router quando tiver conhecimentos especiais para identificar quais os pontos finais a que as mensagens devem ir.

Um router personalizado é definido abaixo como `east-` um exemplo quando os `east`grupos que começam com sempre vão para o ponto final chamado:

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

Outro exemplo abaixo, que sobrepõe o comportamento de negociação padrão, para selecionar os pontos finais depende do local onde o servidor da aplicação está localizado.

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

Não se esqueça de registar o router no recipiente DI utilizando:

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

## <a name="for-aspnet"></a>Por ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos finais da config?

Config com `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` chave ou para a cadeia de ligação signalR service.

Se a chave `Azure:SignalR:ConnectionString:`começar com, `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`deve `Name` estar `EndpointType` em formato, onde e são propriedades do `ServiceEndpoint` objeto, e estão acessíveis a partir do código.

Pode adicionar cordas de ligação de várias instâncias a: `web.config`

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos finais do código?

Uma `ServicEndpoint` classe é introduzida para descrever as propriedades de um ponto final do Serviço De Sinalização Azure.
Pode configurar vários pontos finais de instância ao utilizar o Serviço De Sinalização Azure SDK através de:

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

### <a name="how-to-customize-router"></a>Como personalizar o router?

A única diferença entre ASP.NET SignalR e ASP.NET Core `GetNegotiateEndpoint`SignalR é o tipo de contexto http para . Para ASP.NET SignalR, é do tipo [IOwinContext.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)

Abaixo está o exemplo de negociação personalizado para ASP.NET SignalR:

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

Não se esqueça de registar o router no recipiente DI utilizando:

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

## <a name="configuration-in-cross-region-scenarios"></a>Configuração em cenários transversais

O `ServiceEndpoint` objeto `EndpointType` tem uma `primary` `secondary`propriedade com valor ou .

`primary`os pontos finais são pontos finais preferidos para receber o tráfego do cliente, e são considerados como tendo ligações de rede mais fiáveis; `secondary` os pontos finais são considerados como tendo ligações de rede menos fiáveis e são usados apenas para levar o servidor ao tráfego do cliente, por exemplo, mensagens de radiodifusão, não para levar o cliente ao tráfego do servidor.

Em casos transversais, a rede pode ser instável. Para um servidor de aplicações localizado no *Leste dos EUA,* o ponto final `primary` do SignalR Service localizado na mesma região leste dos *EUA* pode ser configurado como e pontos finais em outras regiões marcadas como `secondary`. Nesta configuração, os pontos finais do serviço noutras regiões podem **receber** mensagens deste servidor de aplicações *dos EUA Orientais,* mas não haverá clientes **transversais** encaminhados para este servidor de aplicações. A arquitetura é mostrada no diagrama abaixo:

![Infra-geo-transversal](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quando um `/negotiate` cliente tenta com o servidor de aplicações, com o router predefinido, `primary` o SDK **seleciona aleatoriamente** um ponto final do conjunto de pontos finais disponíveis. Quando o ponto final primário não está disponível, o SDK **seleciona aleatoriamente** de todos os pontos finais disponíveis. `secondary` O ponto final é marcado como **disponível** quando a ligação entre o servidor e o ponto final do serviço está viva.

No cenário transversal, quando um `/negotiate` cliente tenta com o servidor de aplicações `primary` hospedado no Leste dos *EUA,* por padrão, devolve sempre o ponto final localizado na mesma região. Quando todos os pontos finais *dos EUA Não* estão disponíveis, o cliente é redirecionado para pontos finais noutras regiões. A secção de falha abaixo descreve o cenário em detalhe.

![Negociação Normal](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Falha

Quando `primary` todos os pontos finais `/negotiate` não estão `secondary` disponíveis, as escolhas do cliente dos pontos finais disponíveis. Este mecanismo de falha requer que cada `primary` ponto final sirva de ponto final para pelo menos um servidor de aplicações.

![Falha](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a configurar múltiplos casos na mesma aplicação para cenários de escala, sharding e cross-region.

Vários suportes de pontos finais também podem ser usados em cenários de alta disponibilidade e recuperação de desastres.

> [!div class="nextstepaction"]
> [Serviço de Sinalização de Configuração para recuperação de desastres e elevada disponibilidade](./signalr-concept-disaster-recovery.md)
