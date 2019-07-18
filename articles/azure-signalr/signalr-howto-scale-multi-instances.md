---
title: Como dimensionar com várias instâncias para o serviço Azure SignalR
description: Em muitos cenários de dimensionamento, cliente, muitas vezes, tem de aprovisionar várias instâncias e configurar usá-los em conjunto, para criar uma implementação em grande escala. Por exemplo, a fragmentação requer que suportam de várias instâncias.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809196"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Como dimensionar o serviço SignalR com várias instâncias?
O SDK de serviço mais recente do SignalR suporta vários pontos de extremidade para instâncias do serviço de SignalR. Pode utilizar esta funcionalidade para dimensionar as ligações simultâneas, ou usá-lo para mensagens entre regiões.

## <a name="for-aspnet-core"></a>Para o ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos de extremidade a partir da configuração?

Configuração com a chave `Azure:SignalR:ConnectionString` ou `Azure:SignalR:ConnectionString:` para a cadeia de ligação de serviço SignalR.

Se a chave começa com `Azure:SignalR:ConnectionString:`, deve estar no formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, onde `Name` e `EndpointType` são propriedades do `ServiceEndpoint` de objeto e estão acessíveis a partir do código.

Pode adicionar várias cadeias de ligação de instância utilizando os seguintes `dotnet` comandos:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos de extremidade do código?

A `ServicEndpoint` classe é introduzida para descrever as propriedades de um ponto de extremidade do serviço Azure SignalR.
Pode configurar vários pontos de extremidade de instância ao utilizar o SDK do serviço Azure SignalR por meio de:
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

### <a name="how-to-customize-endpoint-router"></a>Como personalizar o router de ponto final?

Por predefinição, o SDK utiliza o [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) de retirada de pontos de extremidade.

#### <a name="default-behavior"></a>Comportamento predefinido 
1. Encaminhamento de pedidos de cliente

    Quando cliente `/negotiate` com o servidor de aplicação. Por predefinição, o SDK **seleciona aleatoriamente** um ponto final do conjunto de pontos finais de serviço disponíveis.

2. Roteamento de mensagens de servidor

    Quando *enviar mensagem para um específico **ligação*** e a ligação de destino é encaminhado para o servidor atual, a mensagem vai diretamente para esse ponto de extremidade conectado. Caso contrário, as mensagens são difundiu para cada ponto de final do Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Personalizar o algoritmo de encaminhamento
Pode criar seu próprio router quando tem conhecimento especial para identificar as mensagens devem ir para os pontos de extremidade.

Um roteador personalizado definido abaixo como exemplo quando grupos que comecem pela `east-` sempre ir para o ponto final com o nome `east`:

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

Comportamento de negociação de outro exemplo abaixo, que substitui a predefinição selecionar os pontos de extremidade depende de onde está localizado o servidor de aplicações.

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

Não se esqueça de registar o router para a utilização de contêiner de injeção de dependência:

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

## <a name="for-aspnet"></a>Para o ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos de extremidade a partir da configuração?

Configuração com a chave `Azure:SignalR:ConnectionString` ou `Azure:SignalR:ConnectionString:` para a cadeia de ligação de serviço SignalR.

Se a chave começa com `Azure:SignalR:ConnectionString:`, deve estar no formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, onde `Name` e `EndpointType` são propriedades do `ServiceEndpoint` de objeto e estão acessíveis a partir do código.

Pode adicionar várias cadeias de ligação de instância para `web.config`:

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

A `ServicEndpoint` classe é introduzida para descrever as propriedades de um ponto de extremidade do serviço Azure SignalR.
Pode configurar vários pontos de extremidade de instância ao utilizar o SDK do serviço Azure SignalR por meio de:

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

A única diferença entre o SignalR de ASP.NET e ASP.NET Core SignalR é o tipo de contexto de http para `GetNegotiateEndpoint`. Para o SignalR de ASP.NET, é de [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) tipo.

Segue-se o personalizado negociar exemplo para o SignalR de ASP.NET:

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

Não se esqueça de registar o router para a utilização de contêiner de injeção de dependência:

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

O `ServiceEndpoint` objeto tem uma `EndpointType` propriedade com o valor `primary` ou `secondary`.

`primary` pontos finais são pontos de extremidade preferidos para receber o tráfego de cliente e são considerados ter ligações de rede mais confiáveis; `secondary` pontos finais são considerados como tendo menos fiáveis ligações de rede e são utilizados apenas para o servidor do levando ao tráfego de cliente como, por exemplo, mensagens de difusão, não para o cliente e tirar para tráfego de servidor.

Em casos em várias regiões, a rede pode ser instável. Para o servidor de um aplicativo localizado em *E.U.A. Leste*, o ponto de extremidade de serviço SignalR localizada na mesma *E.U.A. Leste* região pode ser configurada como `primary` e pontos de extremidade noutras regiões está marcada como `secondary`. Nesta configuração, os pontos finais de serviço noutras regiões podem **receber** mensagens nesta *E.U.A. Leste* servidor de aplicações, mas haverá ser nenhum **entre regiões** clientes encaminhado para Este servidor de aplicação. A arquitetura é mostrada no diagrama abaixo:

![Cross-Geo Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quando um cliente tenta `/negotiate` com o servidor de aplicações, com o roteador padrão, o SDK **seleciona aleatoriamente** um ponto final do conjunto de disponibilidade `primary` pontos de extremidade. Quando o ponto final está disponível, em seguida, o SDK **seleciona aleatoriamente** disponíveis de `secondary` pontos finais. O ponto final está marcado como **disponível** quando a ligação entre o servidor e o ponto final de serviço está ativa.

Cenário entre regiões, quando um cliente tenta `/negotiate` com o servidor de aplicação alojado num *E.U.A. Leste*, ao padrão sempre retorna o `primary` endpoint localizado na mesma região. Quando todos os *E.U.A. Leste* pontos finais não estão disponíveis, o cliente é redirecionado para pontos de extremidade noutras regiões. Secção de ativação pós-falha abaixo descreve o cenário em detalhes.

![Negociar normal](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Ativação pós-falha

Quando todos os `primary` não estão disponíveis, pontos de extremidade do cliente `/negotiate` seleciona disponíveis do `secondary` pontos finais. Esse mecanismo de ativação pós-falha requer que cada ponto de extremidade deve servir como `primary` ponto final para o servidor de pelo menos uma aplicação.

![Ativação pós-falha](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu sobre como configurar várias instâncias no mesmo aplicativo para dimensionamento, a fragmentação e cenários entre regiões.

Suporta a vários pontos de extremidade também pode ser utilizada em cenários de recuperação após desastre e de disponibilidade elevados.

> [!div class="nextstepaction"]
> [Configurar o serviço SignalR para recuperação após desastre e elevada disponibilidade](./signalr-concept-disaster-recovery.md)
