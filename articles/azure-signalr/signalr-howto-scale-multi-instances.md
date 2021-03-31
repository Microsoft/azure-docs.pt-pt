---
title: Escala com múltiplas instâncias - Serviço Azure SignalR
description: Em muitos cenários de escala, o cliente precisa muitas vezes de providenciar múltiplas instâncias e configurar para usá-los em conjunto, para criar uma implementação em larga escala. Por exemplo, o fragmento requer suporte de múltiplas instâncias.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: fd6ac8c4d4fc4c3fec4f549f8ef4f955e2b1c637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89439219"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Como escalar o Serviço SignalR com várias instâncias?
O mais recente Serviço SignalR SDK suporta vários pontos finais para instâncias do Serviço SignalR. Pode utilizar esta funcionalidade para escalar as ligações simultâneas ou usá-la para mensagens transversais.

## <a name="for-aspnet-core"></a>Para ASP.NET Núcleo

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos finais da config?

Config com a chave `Azure:SignalR:ConnectionString` ou para a cadeia de `Azure:SignalR:ConnectionString:` ligação SignalR Service.

Se a chave começar `Azure:SignalR:ConnectionString:` com , deve estar em formato , onde e são propriedades do `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` `Name` `EndpointType` `ServiceEndpoint` objeto, e são acessíveis a partir de código.

Pode adicionar cadeias de ligação de vários casos utilizando os `dotnet` seguintes comandos:

```cmd
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos finais do código?

Uma `ServicEndpoint` classe é introduzida para descrever as propriedades de um ponto final do Serviço Azure SignalR.
Pode configurar vários pontos finais de instâncias ao utilizar o Serviço Azure SignalR SDK através de:
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

### <a name="how-to-customize-endpoint-router"></a>Como personalizar o router de pontos finais?

Por predefinição, o SDK utiliza o [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) para apanhar pontos de final.

#### <a name="default-behavior"></a>Comportamento predefinido 
1. Encaminhamento de pedido de cliente

    Quando o cliente `/negotiate` com o servidor de aplicações. Por predefinição, a SDK **seleciona aleatoriamente** um ponto final a partir do conjunto de pontos finais de serviço disponíveis.

2. Encaminhamento de mensagens de servidor

    Quando *enviar mensagem para uma ligação específica *****, e a ligação do alvo é encaminhada para o servidor atual, a mensagem vai diretamente para esse ponto final ligado. Caso contrário, as mensagens são transmitidas para todos os pontos finais do Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Personalizar algoritmo de encaminhamento
Pode criar o seu próprio router quando tiver conhecimentos especiais para identificar quais os pontos finais a que as mensagens devem ir.

Um router personalizado é definido abaixo como um exemplo quando os grupos que começam com `east-` sempre vão para o ponto final chamado `east` :

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

Outro exemplo abaixo, que substitui o comportamento de negociação padrão, para selecionar os pontos finais depende de onde o servidor da aplicação está localizado.

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

Não se esqueça de registar o router para o contentor DI utilizando:

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

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos finais da config?

Config com a chave `Azure:SignalR:ConnectionString` ou para a cadeia de `Azure:SignalR:ConnectionString:` ligação SignalR Service.

Se a chave começar `Azure:SignalR:ConnectionString:` com , deve estar em formato , onde e são propriedades do `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` `Name` `EndpointType` `ServiceEndpoint` objeto, e são acessíveis a partir de código.

Pode adicionar cadeias de conexão de vários casos `web.config` a:

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

Uma `ServicEndpoint` classe é introduzida para descrever as propriedades de um ponto final do Serviço Azure SignalR.
Pode configurar vários pontos finais de instâncias ao utilizar o Serviço Azure SignalR SDK através de:

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

A única diferença entre ASP.NET SignalR e ASP.NET Core SignalR é o tipo de contexto http para `GetNegotiateEndpoint` . Para ASP.NET SignalR, é do tipo [IOwinContext.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)

Abaixo está o exemplo de negociação personalizada para ASP.NET SignalR:

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

Não se esqueça de registar o router para o contentor DI utilizando:

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

## <a name="configuration-in-cross-region-scenarios"></a>Configuração em cenários de região cruzada

O `ServiceEndpoint` objeto tem uma propriedade com valor ou `EndpointType` `primary` `secondary` .

`primary` os pontos finais são os pontos finais preferidos para receber o tráfego do cliente, e são considerados como tendo ligações de rede mais fiáveis; `secondary` os pontos finais são considerados como tendo ligações de rede menos fiáveis e são usados apenas para levar o servidor ao tráfego do cliente, por exemplo, mensagens de transmissão, não para levar o cliente ao tráfego de servidores.

Em casos transversais, a rede pode ser instável. Para um servidor de aplicações localizado no *Leste dos EUA,* o ponto final do Serviço SignalR localizado na mesma região *leste dos EUA* pode ser configurado como `primary` pontos finais em outras regiões marcadas como `secondary` . Nesta configuração, os pontos finais de serviço noutras regiões podem **receber** mensagens deste servidor de aplicações *dos EUA,* mas não haverá clientes **de região cruzada** encaminhados para este servidor de aplicações. A arquitetura é mostrada no diagrama abaixo:

![Infra-Geo Transversal](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quando um cliente tenta `/negotiate` com o servidor de aplicações, com o router predefinido, o SDK **seleciona aleatoriamente** um ponto final a partir do conjunto de `primary` pontos finais disponíveis. Quando o ponto final principal não está disponível, o SDK **seleciona aleatoriamente** de todos os `secondary` pontos finais disponíveis. O ponto final é marcado como **disponível** quando a ligação entre o servidor e o ponto final de serviço está viva.

Em cenário inter-região, quando um cliente tenta `/negotiate` com o servidor de aplicações alojado no Leste dos *EUA,* por padrão, devolve sempre o `primary` ponto final localizado na mesma região. Quando todos os pontos finais *do Leste dos EUA* não estão disponíveis, o cliente é redirecionado para pontos finais noutras regiões. A secção de insusição abaixo descreve o cenário em detalhe.

![Negociação Normal](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Repse-over

Quando todos os `primary` pontos finais não estão disponíveis, as escolhas do cliente `/negotiate` entre os `secondary` pontos finais disponíveis. Este mecanismo de falha requer que cada ponto final sirva como `primary` ponto final para pelo menos um servidor de aplicações.

![Repse-over](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu sobre como configurar várias instâncias na mesma aplicação para cenários de escala, fragmentos e cross-region.

Vários suportes de pontos finais também podem ser usados em cenários de alta disponibilidade e recuperação de desastres.

> [!div class="nextstepaction"]
> [Serviço SignalR de configuração para recuperação de desastres e alta disponibilidade](./signalr-concept-disaster-recovery.md)
