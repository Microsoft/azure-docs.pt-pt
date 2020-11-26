---
title: Prática de resolução de problemas para o Serviço Azure SignalR
description: Saiba como resolver problemas de conectividade e problemas de entrega de mensagens
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 413bb88deac96c1ca12e8a9d25fc9cd16edf4616
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183962"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Como resolver problemas de conectividade e problemas de entrega de mensagens

Esta orientação introduz várias formas de ajudar a fazer o auto-diagnóstico para encontrar a causa raiz diretamente ou reduzir a questão. O resultado do autodiagnóstico também é útil ao denunciá-lo para uma investigação mais aprofundada.

Em primeiro lugar, é necessário verificar a partir do portal Azure qual o [ServiceMode](./concept-service-mode.md) do Serviço Azure SignalR (também conhecido como **ASRS)** configurado para.

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* Para `Default` o modo, consulte a [resolução de problemas do modo predefinido](#default_mode_tsg)

* Para `Serverless` o modo, consulte a [resolução de problemas do modo sem servidor](#serverless_mode_tsg)

* Para `Classic` o modo, consulte a [resolução de problemas do modo clássico](#classic_mode_tsg)

<a name="default_mode_tsg"></a>

## <a name="default-mode-troubleshooting"></a>Resolução de problemas do modo predefinido

Quando **o ASRS** está no modo *Predefinido,* existem **três** funções: *Cliente,* *Servidor* e *Serviço*:

* *Cliente*: *Cliente* significa os clientes ligados à **ASRS**. As ligações persistentes que ligam o cliente e **a ASRS** são chamadas *de Conexões de Cliente nesta* orientação.

* *Servidor*: *O servidor* representa o servidor que serve a negociação do cliente e acolhe a lógica `Hub` SignalR. E as ligações persistentes entre *o Servidor* e **o ASRS** são chamadas *de Conexões de Servidor* nesta orientação.

* *Serviço*: *O serviço* é o nome curto para **ASRS** nesta orientação.

Consulte o [Serviço De Sinalização Azure](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) para a introdução detalhada de toda a arquitetura e fluxo de trabalho.

Há várias maneiras que podem ajudá-lo a reduzir a questão. 

* Se a questão acontecer no caminho ou for repromundo, o caminho mais a seguir é ver o tráfego em curso. 

* Se o problema for difícil de reprovar, vestígios e registos podem ajudar.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Como ver o tráfego e reduzir a questão

Capturar o tráfego em curso é a forma mais direta de reduzir a questão. Pode capturar os [vestígios da Rede](/aspnet/core/signalr/diagnostics#network-traces) utilizando as opções descritas abaixo:

* [Recolher um traço de rede com o Fiddler](/aspnet/core/signalr/diagnostics#network-traces)

* [Recolher um traço de rede com tcpdump](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Recolher vestígios de rede no navegador](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Pedidos de clientes

Para uma ligação persistente signalR, primeiro `/negotiate` para o servidor de aplicações hospedado e depois redirecionado para o serviço Azure SignalR e, em seguida, estabelece a ligação persistente real ao serviço Azure SignalR. Consulte o [Serviço De SinalR Azure](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) para obter os passos detalhados.

Com os vestígios de rede do lado do cliente na mão, verifique qual o pedido que falha com que código de estado e que resposta, e procure soluções dentro [do Guia de Resolução de Problemas](./signalr-howto-troubleshoot-guide.md).

#### <a name="server-requests"></a>Pedidos de servidor

O *SignalR Server* mantém a *ligação do servidor* entre *servidor* e *serviço*. Quando o servidor de aplicações começa, inicia a ligação **WebSocket** ao serviço Azure SignalR. Todos os tráfegos de clientes são encaminhados através do serviço Azure SignalR para estas *Ligações* de Servidor e depois enviados para o `Hub` . Quando uma *Ligação do Servidor* cair, os clientes encaminhados para esta *Ligação do Servidor* serão impactados. O nosso Azure SignalR SDK tem uma lógica "Sempre Relemque" para reconectar a *Ligação* do Servidor com um atraso de mais de 1 minuto para minimizar o impacto.

*A Ligação* do Servidor pode cair devido à instabilidade da rede ou à manutenção regular do Serviço Azure SignalR ou das atualizações/manutenção do servidor de aplicações hospedados. Enquanto o lado do cliente tiver o mecanismo de desconexão/reconexão, o impacto é mínimo como qualquer ligação desconectação causada pelo cliente.

Consulte os vestígios da rede do lado do servidor para descobrir o código de estado e o detalhe de erros por que a *Ligação do Servidor* cai ou é rejeitada pelo *Serviço*, e procure a causa principal dentro do Guia [de Resolução de Problemas](./signalr-howto-troubleshoot-guide.md).


### <a name="how-to-add-logs"></a>Como adicionar registos

Os registos podem ser úteis para diagnosticar problemas e monitorizar o estado de funcionamento.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Como ativar o registo do lado do cliente

A experiência de registo do lado do cliente é exatamente a mesma que quando se utiliza o SignalR auto-hospedado.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Ativar a sessão do lado do cliente para `ASP.NET Core SignalR`

* [Registo de clientes JavaScript](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [Registo de clientes .NET](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Ativar a sessão do lado do cliente para `ASP.NET SignalR`

* [.NET cliente](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Habilitação de rastreios em clientes Windows Phone 8](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Habilitação no cliente JavaScript](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Como ativar o registo do lado do servidor

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Ativar a sessão de sessão do lado do servidor para `ASP.NET Core SignalR`

Registo do lado do servidor para `ASP.NET Core SignalR` integração com a `ILogger` [sessão de registo](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) baseada fornecida no `ASP.NET Core` quadro. Pode ativar a sessão do lado do servidor utilizando `ConfigureLogging` uma utilização da amostra da seguinte forma:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

As categorias de logger para Azure SignalR começam sempre com `Microsoft.Azure.SignalR` . Para permitir registos detalhados do Azure SignalR, configuure os prefixos anteriores para `Information` nivelar no seuappsettings.js **em** ficheiros como abaixo:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Verifique se há registos de aviso/erro anormais registados. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Ativar vestígios do lado do servidor para `ASP.NET SignalR`

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

Verifique se há registos de aviso/erro anormais registados. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Como ativar registos dentro do serviço Azure SignalR

Também pode [ativar registos de diagnóstico](./signalr-howto-diagnostic-logs.md) para o serviço Azure SignalR, estes registos fornecem informações detalhadas de todas as ligações ligadas ao serviço Azure SignalR.

<a name="serverless_mode_tsg"></a>

## <a name="serverless-mode-troubleshooting"></a>Resolução de problemas do modo sem servidor

Quando **o ASRS** está no modo *Serverless,* apenas **ASP.NET o Core SignalR** suporta `Serverless` o modo, e ASP.NET **SignalR** **NÃO** suporta este modo.

Para diagnosticar problemas de conectividade no `Serverless` modo, o caminho mais simples para a frente é ver o [tráfego do lado do cliente](#view_traffic_client). Ativar [registos do lado do cliente](#add_logs_client) e [registos do lado do serviço](#add_logs_server) também pode ser útil.

<a name="classic_mode_tsg"></a>

## <a name="classic-mode-troubleshooting"></a>Resolução de problemas de modo clássico

`Classic` o modo é obsoleto e não é encorajado a usar. Quando neste modo, o serviço Azure SignalR utiliza as *ligações* do servidor conectadas para determinar se o serviço atual está no `default` modo ou `serverless` modo. Isto pode levar a alguns problemas de conectividade intermédia do cliente porque, quando há uma queda súbita de toda a *ligação* do servidor conectado , por exemplo devido à instabilidade da rede, o Azure SignalR acredita que agora está mudado para `serverless` o modo, e os clientes ligados durante este período nunca serão encaminhados para o servidor de aplicações hospedado. Ativar [registos do lado do serviço](#add_logs_server) e verificar se existem clientes gravados como se tivesse hospedado servidor de `ServerlessModeEntered` aplicações no entanto alguns clientes nunca chegam ao lado do servidor da aplicação. Se houver alguma, [aborte estas ligações com o cliente](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) e deixe que os clientes reiniciem pode ajudar.

Problemas de conectividade do modo de resolução de `classic` problemas e problemas de entrega de mensagens são semelhantes aos [problemas de modo predefinido](#default_mode_tsg).

## <a name="service-health"></a>Estado de funcionamento dos serviços

Você pode verificar a api de saúde para a saúde do serviço.

* Pedido: GET `https://{instance_name}.service.signalr.net/api/v1/health`

* Código de estado de resposta:
  * 200: saudável.
  * 503: o seu serviço não é saudável. Pode:
    * Aguarde alguns minutos para a autorecover.
    * O endereço IP é o mesmo que o ip do portal.
    * Ou reiniciar o exemplo.
    * Se todas as opções acima não funcionarem, contacte-nos adicionando um novo pedido de suporte no portal Azure.

Mais sobre [a recuperação de desastres.](./signalr-concept-disaster-recovery.md)

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu sobre como resolver problemas de conectividade e problemas de entrega de mensagens. Também pode aprender a lidar com as questões comuns. 

> [!div class="nextstepaction"]
> [Guia de resolução de problemas](./signalr-howto-troubleshoot-guide.md)