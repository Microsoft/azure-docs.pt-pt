---
title: Análise de utilização com Azure Application Insights | Docs da Microsoft
description: Compreenda os seus utilizadores e o que fazem com a sua aplicação.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 633d35ec16f5eb9de664421c38cd4c824dc240cf
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233853"
---
# <a name="usage-analysis-with-application-insights"></a>Análise de utilização com o Application Insights

Quais as funcionalidades da sua web ou aplicação móvel mais populares? Os seus utilizadores alcançam os seus objetivos com a sua app? Desempatam em determinados pontos, e voltam mais tarde?  [O Azure Application Insights](./app-insights-overview.md) ajuda-o a obter informações poderosas sobre como as pessoas usam a sua aplicação. Sempre que atualizar a sua aplicação, pode avaliar o quão bem funciona para os utilizadores. Com este conhecimento, pode tomar decisões orientadas para dados sobre os seus próximos ciclos de desenvolvimento.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Envie telemetria a partir da sua app

A melhor experiência obtém-se através da instalação de Application Insights tanto no código do servidor da sua aplicação como nas suas páginas web. Os componentes do cliente e do servidor da sua aplicação enviam a telemetria de volta para o portal Azure para análise.

1. **Código do servidor:** Instale o módulo apropriado para o seu [ASP.NET](./asp-net.md), [Azure,](./app-insights-overview.md) [Java,](./java-get-started.md) [Node.js](./nodejs.md)ou [outra](./platforms.md) aplicação.

    * *Não quer instalar o código do servidor? Basta [criar um recurso Azure Application Insights](./create-new-resource.md).*

2. **Código da página web:** Adicione o seguinte script à sua página web antes do fecho ``</head>`` . Substitua a tecla de instrumentação pelo valor adequado para o seu recurso Application Insights:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Para obter configurações mais avançadas para monitorizar websites, consulte o [artigo de referência javaScript SDK](./javascript.md).

3. **Código de aplicativo móvel:** Utilize o App Center SDK para recolher eventos da sua aplicação e, em seguida, envie cópias destes eventos para a Aplicação Insights para [análise, seguindo este guia.](../learn/mobile-center-quickstart.md)

4. **Obter telemetria:** Execute o seu projeto em modo de depurar durante alguns minutos e, em seguida, procure resultados na lâmina de visão geral em Application Insights.

    Publique a sua aplicação para monitorizar o desempenho da sua aplicação e descubra o que os seus utilizadores estão a fazer com a sua aplicação.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Inclua o ID do utilizador e da sessão na sua telemetria
Para rastrear os utilizadores ao longo do tempo, o Application Insights requer uma forma de os identificar. A ferramenta Eventos é a única ferramenta de utilização que não requer um ID do utilizador ou um ID de sessão.

Comece a enviar iDs de utilizador e de sessão utilizando [este processo.](./usage-send-user-context.md)

## <a name="explore-usage-demographics-and-statistics"></a>Explorar a demografia de utilização e as estatísticas
Descubra quando as pessoas usam a sua app, quais as páginas em que estão mais interessados, onde os seus utilizadores estão localizados, quais os navegadores e sistemas operativos que utilizam. 

Os relatórios de Utilizadores e Sessões filtram os seus dados por páginas ou eventos personalizados, e segmentam-nos por propriedades como localização, ambiente e página. Também pode adicionar os seus próprios filtros.

![A captura de ecrã mostra a página de Visão Geral dos Utilizadores para uma empresa fictícia.](./media/usage-overview/users.png)  

Insights sobre a direita apontam padrões interessantes no conjunto de dados.  

* O relatório **dos Utilizadores** conta os números de utilizadores únicos que acedem às suas páginas dentro dos períodos de tempo escolhidos. Para aplicações web, os utilizadores são contados usando cookies. Se alguém aceder ao seu site com diferentes navegadores ou máquinas de clientes, ou limpar os seus cookies, então serão contados mais de uma vez.
* O relatório **sessions** conta o número de sessões de utilizador que acedem ao seu site. Uma sessão é um período de atividade por um utilizador, terminado por um período de inatividade superior a meia hora.

[Mais sobre as ferramentas de Utilizadores, Sessões e Eventos](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retenção - quantos utilizadores voltam?

A retenção ajuda-o a perceber com que frequência os seus utilizadores voltam a usar a sua app, com base em coortes de utilizadores que realizaram alguma ação comercial durante um determinado balde de tempo. 

- Entenda que funcionalidades específicas fazem com que os utilizadores voltem mais do que outros 
- Hipóteses de formulário com base em dados reais do utilizador 
- Determine se a retenção é um problema no seu produto 

![A captura do ecrã mostra a página "Retenção Geral", que exibe informações sobre a frequência com que os utilizadores voltam a utilizar a sua aplicação.](./media/usage-overview/retention.png) 

Os controlos de retenção em cima permitem definir eventos específicos e intervalo de tempo para calcular a retenção. O gráfico no meio dá uma representação visual da percentagem global de retenção pelo intervalo de tempo especificado. O gráfico na parte inferior representa a retenção individual num determinado período de tempo. Este nível de detalhe permite-lhe entender o que os seus utilizadores estão a fazer e o que pode afetar o retorno dos utilizadores numa granularidade mais detalhada.  

[Mais sobre a ferramenta de retenção](usage-retention.md)

## <a name="custom-business-events"></a>Eventos de negócios personalizados

Para obter uma compreensão clara do que os utilizadores fazem com a sua aplicação, é útil inserir linhas de código para registar eventos personalizados. Estes eventos podem rastrear qualquer coisa, desde ações detalhadas do utilizador, como clicar em botões específicos, até eventos de negócio mais significativos, como fazer uma compra ou ganhar um jogo.

Também pode utilizar o [Plugin de recolha automática click Analytics](javascript-click-analytics-plugin.md) para recolher eventos personalizados.

Embora, em alguns casos, as vistas de página possam representar eventos úteis, não é verdade em geral. Um utilizador pode abrir uma página de produto sem comprar o produto. 

Com eventos de negócios específicos, pode traçar o progresso dos seus utilizadores através do seu site. Você pode descobrir suas preferências para diferentes opções, e onde eles desempoiam ou têm dificuldades. Com este conhecimento, pode tomar decisões informadas sobre as prioridades no seu atraso de desenvolvimento.

Os eventos podem ser registados do lado cliente da aplicação:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Ou do lado do servidor:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Pode anexar valores de propriedade a estes eventos, para que possa filtrar ou dividir os eventos quando os inspeciona no portal. Além disso, um conjunto padrão de propriedades é anexado a cada evento, como iD de utilizador anónimo, que permite rastrear a sequência de atividades de um utilizador individual.

Saiba mais sobre eventos e [propriedades](./api-custom-events-metrics.md#properties) [personalizadas.](./api-custom-events-metrics.md#trackevent)

### <a name="slice-and-dice-events"></a>Eventos de fatias e dados

Nas ferramentas Utilizadores, Sessões e Eventos, pode cortar e picar eventos personalizados pelo utilizador, nome do evento e propriedades.
![A captura de ecrã mostra a página de Visão Geral dos Utilizadores para uma empresa fictícia.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Desenhe a telemetria com a app

Quando estiver a desenhar cada funcionalidade da sua app, considere como vai medir o seu sucesso com os seus utilizadores. Decida quais eventos de negócios precisa de gravar e codi venda as chamadas de rastreio para esses eventos na sua app desde o início.

## <a name="a--b-testing"></a>Um | B Testes
Se não sabe qual a variante de uma funcionalidade que será mais bem sucedida, liberte ambas, tornando cada uma acessível a diferentes utilizadores. Meça o sucesso de cada um e, em seguida, passe para uma versão unificada.

Para esta técnica, você anexa valores de propriedade distintos a toda a telemetria que é enviada por cada versão da sua app. Pode fazê-lo definindo propriedades no TelemetriaContexto ativo. Estas propriedades predefinidos são adicionadas a todas as mensagens de telemetria que a aplicação envia - não apenas as suas mensagens personalizadas, mas também a telemetria padrão.

No portal Application Insights, filtre e divida os seus dados sobre os valores da propriedade, de modo a comparar as diferentes versões.

Para tal, [crie um inicializador de telemetria:](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

**Aplicações ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

No inicializador de aplicações web, como Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Aplicações ASP.NET Core**

> [!NOTE]
> Adicionar inicializador usando `ApplicationInsights.config` ou usando `TelemetryConfiguration.Active` não é válido para aplicações core ASP.NET. 

Para ASP.NET aplicações [Core,](asp-net-core.md#adding-telemetryinitializers) a adição de um novo `TelemetryInitializer` é feita adicionando-a ao recipiente de Injeção de Dependência, como mostrado abaixo. Isto é feito no `ConfigureServices` método da sua `Startup.cs` classe.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Todos os novos TelemetriaClients adicionam automaticamente o valor da propriedade que especifica. Os eventos individuais de telemetria podem sobrepor-se aos valores predefinidos.

## <a name="next-steps"></a>Passos seguintes
   - [Utilizadores, Sessões, Eventos](usage-segmentation.md)
   - [Funis](usage-funnels.md)
   - [Retenção](usage-retention.md)
   - [Fluxos de Utilizador](usage-flows.md)
   - [Livros](../platform/workbooks-overview.md)
   - [Adicionar contexto de utilizador](usage-send-user-context.md)

