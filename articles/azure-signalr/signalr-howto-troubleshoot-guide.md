---
title: Troubleshooting guide (Guia de resolução de problemas) do Azure SignalR Service
description: Saiba como resolver problemas comuns
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: 11ea348a80bc226b6a96bea1e7c023ee9c06b13a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684122"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Guia de resolução de problemas para problemas comuns do Serviço Azure SignalR

Esta orientação é para fornecer um guia útil de resolução de problemas com base nas questões comuns que os clientes encontraram e resolveram nos últimos anos.

## <a name="access-token-too-long"></a>Ficha de acesso demasiado longa

### <a name="possible-errors"></a>Possíveis erros:

* Lado cliente `ERR_CONNECTION_`
* 414 URI Demasiado longo
* 413 Carga Útil Demasiado Grande
* O Token de acesso não deve ser superior a 4K. 413 Entidade de Pedido Demasiado Grande

### <a name="root-cause"></a>Causa raiz:

Para HTTP/2, o comprimento máximo de um único cabeçalho é **de 4 K**, por isso, se utilizar o navegador para aceder ao serviço Azure, haverá um erro `ERR_CONNECTION_` para esta limitação.

Para os clientes HTTP/1.1 ou C#, o comprimento máximo de URI é **de 12 K,** o comprimento máximo do cabeçalho é **de 16 K**.

Com a versão SDK **1.0.6** ou superior, `/negotiate` lançará `413 Payload Too Large` quando o token de acesso gerado for maior que **4 K**.

### <a name="solution"></a>Solução:

Por padrão, as reclamações `context.User.Claims` são incluídas ao gerar o token de acesso JWT à **ASRS****(A** zure **S** ignal **R** **S** ervice), de modo a que as reclamações sejam preservadas e possam ser passadas da **ASRS** para `Hub` a quando o cliente se liga ao `Hub` .

Em alguns casos, `context.User.Claims` são alavancados para armazenar muitas informações para o servidor de aplicações, a maioria dos quais não são usados por `Hub` s mas por outros componentes.

O token de acesso gerado é passado através da rede, e para as ligações WebSocket/SSE, os tokens de acesso são passados através de cadeias de consulta. Assim, como as melhores práticas, sugerimos apenas passar as reclamações **necessárias** do cliente através da **ASRS** para o seu servidor de aplicações quando o Hub precisar.

Existe uma `ClaimsProvider` para você personalizar as reclamações que passam para **ASRS** dentro do token de acesso.

Para ASP.NET Core:
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

Para ASP.NET:
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

## <a name="tls-12-required"></a>TLS 1.2 necessário

### <a name="possible-errors"></a>Possíveis erros:

* ASP.NET erro "Sem servidor [disponível" #279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "A ligação não está ativa, os dados não podem ser enviados para o serviço." [#324](https://github.com/Azure/azure-signalr/issues/324) erro
* "Ocorreu um erro ao fazer o pedido HTTP para https:// <API endpoint> . Este erro pode dever-se ao facto de o certificado do servidor não estar configurado corretamente com HTTP.SYS no caso HTTPS. Este erro também pode ser causado por uma incompatibilidade entre a ligação de segurança entre o cliente e o servidor."

### <a name="root-cause"></a>Causa raiz:

O Serviço Azure só suporta TLS1.2 por questões de segurança. Com o quadro .NET, é possível que o TLS1.2 não seja o protocolo predefinido. Como resultado, as ligações do servidor ao ASRS não podem ser estabelecidas com sucesso.

### <a name="troubleshooting-guide"></a>Guia de resolução de problemas

1. Se este erro puder ser reproduzido localmente, desmarque *o Just My Code* e atire todas as exceções clR e desorga o servidor de aplicações localmente para ver que exceções lança.
    * Desmarque *apenas o meu código*

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Desmarque apenas o meu código":::

    * Lançar exceções CLR

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="Lançar exceções CLR":::

    * Veja o lançamento das exceções ao depurar o código do servidor da aplicação:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="Arremessos de exceção":::

2. Para ASP.NET, também pode adicionar o seguinte código ao seu `Startup.cs` para ativar o rastreio detalhado e ver os erros do registo.
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>Solução:

Adicione o seguinte código ao seu Arranque:
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

## <a name="400-bad-request-returned-for-client-requests"></a>400 Mau Pedido devolvido para pedidos de clientes

### <a name="root-cause"></a>Causa raiz

Verifique se o seu pedido de cliente tem `hub` várias cadeias de consultas. `hub` é um parâmetro de consulta preservado e 400 lançarão se o serviço detetar mais de um `hub` na consulta.

## <a name="401-unauthorized-returned-for-client-requests"></a>401 Unauthorized returned for client requests (Erro 401 não autorizado devolvido para pedidos de cliente)

### <a name="root-cause"></a>Causa raiz

Atualmente, o valor padrão da vida útil do JWT é de 1 hora.

Para ASP.NET Core SignalR, quando está a utilizar o tipo de transporte WebSocket, está OK.

Para ASP.NET outro tipo de transporte do Core SignalR, SSE e sondagens longas, isto significa que, por defeito, a ligação pode persistir durante 1 hora.

Para ASP.NET SignalR, o cliente envia um `/ping` pedido KeepAlive ao serviço de vez em quando, quando o `/ping` cliente falha, o cliente **aborta** a ligação e nunca mais se reconecta. Isto significa que, para ASP.NET SignalR, o tempo de vida útil do símbolo padrão faz com que a ligação dure **no máximo** 1 hora para todo o tipo de transporte.

### <a name="solution"></a>Solução

Por questões de segurança, o alargamento da TTL não é encorajado. Sugerimos adicionar a lógica de reconexão do cliente para reiniciar a ligação quando tal 401 ocorrer. Quando o cliente reiniciar a ligação, negociará com o servidor de aplicações para obter o token JWT novamente e obter um token renovado.

Consulte [aqui](#restart_connection) como reiniciar as ligações com o cliente.

## <a name="404-returned-for-client-requests"></a>404 returned for client requests (404 devolvido para pedidos de cliente)

Para uma ligação persistente signalR, primeiro `/negotiate` ao serviço Azure SignalR e depois estabelece a ligação real ao serviço Azure SignalR.

### <a name="troubleshooting-guide"></a>Guia de resolução de problemas

* Seguindo [como visualizar os pedidos de saída](#view_request) para receber o pedido do cliente ao serviço.
* Verifique o URL do pedido quando ocorrer 404. Se o URL estiver direcionado para a sua aplicação web, e similar `{your_web_app}/hubs/{hubName}` a, verifique se o cliente `SkipNegotiation` está `true` . Ao utilizar o Azure SignalR, o cliente recebe URL de redirecionamento quando negoceia pela primeira vez com o servidor de aplicações. O cliente **NÃO** deve faltar à negociação quando utilizar o Azure SignalR.
* Outros 404 podem acontecer quando o pedido de ligação é tratado mais de **5** segundos depois `/negotiate` de ser chamado. Verifique a data do pedido do cliente e abra-nos um problema se o pedido ao serviço tiver uma resposta lenta.

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>404 devolvidos para ASP.NET pedido de reconexão da SignalR

Para ASP.NET SignalR, quando a ligação do [cliente cai,](#client_connection_drop)volta a ligar-se utilizando o mesmo `connectionId` durante três vezes antes de parar a ligação. `/reconnect` pode ajudar se a ligação for abandonada devido a problemas intermitentes de rede que `/reconnect` podem restabelecer a ligação persistente com sucesso. Noutras circunstâncias, por exemplo, a ligação ao cliente é abandonada devido à ligação do servidor rotato ser abandonada, ou o Serviço SignalR tem alguns erros internos como o reinício/falha/implementação, a ligação já não existe, assim `/reconnect` derresto `404` . É o comportamento esperado para `/reconnect` e depois de três vezes tentar novamente a ligação parar. Sugerimos que a [lógica de reinício da ligação](#restart_connection) quando a ligação parar.

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (Pedidos demasiados) devolvidos para pedidos de clientes

429 devoluções se a sua contagem de ligação **simultânea** exceder o limite.

Para casos **gratuitos,** o limite de contagem de ligação **simultânea** é de 20 Para instâncias **standard,** o limite de contagem de ligação **simultânea** **por unidade** é de 1 K, o que significa que a Unit100 permite ligações simultâneas de 100 K.

As ligações incluem ligações de cliente e servidor. consulte [aqui](https://docs.microsoft.com/azure/azure-signalr/signalr-concept-messages-and-connections#how-connections-are-counted) como as ligações são contadas.

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500 Erro ao negociar: O Serviço Azure SignalR ainda não está ligado, tente novamente mais tarde.

### <a name="root-cause"></a>Causa raiz

Este erro é reportado quando não existe ligação do servidor ao Serviço Azure SignalR ligado.

### <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Ativar o rastreio do lado do servidor para descobrir os detalhes de erro quando o servidor tenta ligar-se ao Serviço Azure SignalR.

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Ativar a sessão de registo do lado do servidor para ASP.NET Core SignalR

A sessão de registo do lado do servidor para ASP.NET o Core SignalR integra-se com a `ILogger` [registo baseada](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&tabs=aspnetcore2x) fornecida na estrutura do Núcleo de ASP.NET. Pode ativar a sessão do lado do servidor utilizando `ConfigureLogging` uma utilização da amostra da seguinte forma:
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
As categorias de logger para Azure SignalR começam sempre com `Microsoft.Azure.SignalR` . Para permitir registos detalhados do Azure SignalR, configuure os prefixos anteriores para `Debug` nivelar no seuappsettings.js **em** ficheiros como abaixo:
```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Ativar os vestígios do lado do servidor para ASP.NET SignalR

Ao utilizar a versão SDK >`1.0.0` =, pode ativar vestígios adicionando o seguinte a `web.config` :[(Detalhes)](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102)
```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

## <a name="client-connection-drops"></a>Gotas de ligação ao cliente

Quando o cliente está ligado ao Azure SignalR, a ligação persistente entre o cliente e o Azure SignalR pode por vezes cair por razões diferentes. Esta secção descreve várias possibilidades que causam a queda de tal ligação e fornece algumas orientações sobre como identificar a causa raiz.

### <a name="possible-errors-seen-from-the-client-side"></a>Possíveis erros vistos do lado do cliente

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Causa raiz:

As ligações com o cliente podem cair em várias circunstâncias:
* Quando `Hub` lança exceções com o pedido de entrada.
* Quando a ligação do servidor, para a qual o cliente encaminhou, cair, consulte a secção seguinte para ver se os detalhes da [ligação do servidor cai](#server_connection_drop).
* Quando um problema de conectividade de rede acontece entre o cliente e o Serviço SignalR.
* Quando o Serviço SignalR tem alguns erros internos como o reinício de instâncias, falha, implementação e assim por diante.

### <a name="troubleshooting-guide"></a>Guia de resolução de problemas

1. Abra o registo do servidor do servidor de aplicações para ver se algo anormal ocorreu
2. Verifique o registo de eventos do lado do servidor da aplicação para ver se o servidor da aplicação reiniciou
3. Crie um problema para nós fornecendo o prazo, e envie-nos o nome do recurso


## <a name="client-connection-increases-constantly"></a>A ligação ao cliente aumenta constantemente

Pode ser causado por uma utilização inadequada da ligação ao cliente. Se alguém se esquecer de parar/eliminar o cliente SignalR, a ligação permanece aberta.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Possíveis erros observados a partir das métricas do SignalR que estão na secção de monitorização do menu de recursos do portal Azure

As ligações com o cliente aumentam constantemente durante muito tempo nas Métricas do Azure SignalR.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="Ligação com o cliente aumentando constantemente":::

### <a name="root-cause"></a>Causa raiz:

A ligação ao cliente signalR `DisposeAsync` nunca é chamada, a ligação mantém-se aberta.

### <a name="troubleshooting-guide"></a>Guia de resolução de problemas

1. Verifique se o cliente SignalR **nunca** fecha.

### <a name="solution"></a>Solução

Verifique se fecha a ligação. Ligue manualmente `HubConnection.DisposeAsync()` para parar a ligação depois de a utilizar.

Por exemplo:

```C#
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Uso comum de conexão inadequada do cliente

#### <a name="azure-function-example"></a>Exemplo da função Azure 

Este problema ocorre frequentemente quando alguém estabelece a ligação do cliente SignalR no método Azure Function em vez de torná-lo um membro estático para a sua classe Fun. Pode esperar que apenas uma ligação ao cliente seja estabelecida, mas vê a contagem de ligações do cliente aumentar constantemente em Métricas que está na secção de Monitorização do menu de recursos do portal Azure, todas estas ligações caem apenas após o reinício do serviço Azure Function ou Azure SignalR. Isto porque, para **cada** pedido, a Azure Function cria **uma** ligação ao cliente, se não parar a ligação do cliente no método função, o cliente mantém as ligações vivas ao serviço Azure SignalR.

#### <a name="solution"></a>Solução

* Lembre-se de fechar a ligação ao cliente se utilizar clientes SignalR na função Azure ou utilizar o cliente SignalR como singleton.
* Em vez de utilizar clientes SignalR na função Azure, pode criar clientes SignalR em qualquer outro lugar e utilizar [as Ligações de Funções Azure para o Serviço Azure SignalR](https://github.com/Azure/azure-functions-signalrservice-extension) para [negociar](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) o cliente com o Azure SignalR. E também pode utilizar a ligação para [enviar mensagens.](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40) As amostras para negociar o cliente e enviar mensagens podem ser [encontradas aqui.](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples) Mais informações podem ser [encontradas aqui.](https://github.com/Azure/azure-functions-signalrservice-extension)
* Quando utiliza clientes SignalR na função Azure, pode haver uma melhor arquitetura para o seu cenário. Verifique se concebe uma arquitetura adequada sem servidor. Pode consultar [aplicações sem servidor em tempo real com as ligações do Serviço SignalR em Funções Azure](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService).

<a name="server_connection_drop"></a>

## <a name="server-connection-drops"></a>Gotas de ligação do servidor

Quando o servidor de aplicações começa, em segundo plano, o Azure SDK começa a iniciar ligações de servidor ao remoto Azure SignalR. Conforme descrito em [Internals of Azure SignalR Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md), Azure SignalR liga tráfegos de clientes a estas ligações de servidor. Uma vez que uma ligação do servidor é largada, todas as ligações do cliente que serve também serão fechadas.

Uma vez que as ligações entre o servidor de aplicações e o Serviço SignalR são ligações persistentes, podem experimentar problemas de conectividade de rede. No SDK do servidor, temos sempre a estratégia **de reconectar-nos** às ligações do servidor. Como as melhores práticas, também encorajamos os utilizadores a adicionar lógica de reconexão contínua aos clientes com um tempo de atraso aleatório para evitar pedidos simultâneos maciços ao servidor.

Regularmente, existem novas versões para o Serviço Azure SignalR e, por vezes, o sistema operativo em todo o sistema Azure ou atualizações ou, ocasionalmente, a interrupção dos nossos serviços dependentes. Estes podem trazer um curto período de interrupção de serviço, mas enquanto o lado do cliente tiver o mecanismo de desconexão/reconexão, o impacto é mínimo como qualquer ligação desconectação causada pelo cliente.

Esta secção descreve várias possibilidades que conduzem à queda da ligação do servidor, e fornece algumas orientações sobre como identificar a causa raiz.

### <a name="possible-errors-seen-from-server-side"></a>Possíveis erros vistos do lado do servidor:

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Causa raiz:

A ligação de serviço do servidor é fechada por **ASRS****(Um** zure **S** ignal **R** **S** ervice).

### <a name="troubleshooting-guide"></a>Guia de resolução de problemas

1. Abra o registo do servidor do servidor de aplicações para ver se algo anormal ocorreu
2. Verifique o registo de eventos do lado do servidor da aplicação para ver se o servidor da aplicação reiniciou
3. Crie um problema para nós fornecendo o prazo, e envie-nos o nome do recurso

## <a name="tips"></a>Sugestões

<a name="view_request"></a>

* Como visualizar o pedido de saída do cliente?
Tome ASP.NET Core um, por exemplo (ASP.NET um é semelhante):
    * Do navegador:

        Tome o Chrome como um exemplo, pode usar **o F12** para abrir a janela da consola e mudar para o separador **Rede.** Poderá ser necessário atualizar a página utilizando o **F5** para capturar a rede desde o início.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Rede de visualização do Chrome":::

    * Do cliente C#:

        Pode ver os tráfegos web locais usando [o Fiddler.](https://www.telerik.com/fiddler) Os tráfegos WebSocket são suportados desde o Fiddler 4.5.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Rede de visualização de violino" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* Como reiniciar a ligação ao cliente?
    
    Aqui estão os [códigos de amostra](https://github.com/Azure/azure-signalr/tree/dev/samples) que contêm a lógica de ligação de reinicialização com a estratégia *ALWAYS RETRY:*

    * [ASP.NET Cliente Core C#](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Cliente JavaScript Core](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET Cliente C#](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET JavaScript Cliente](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu sobre como lidar com as questões comuns. Também pode aprender métodos mais genéricos de resolução de problemas. 

> [!div class="nextstepaction"]
> [Como resolver problemas de conectividade e problemas de entrega de mensagens](./signalr-howto-troubleshoot-method.md)
