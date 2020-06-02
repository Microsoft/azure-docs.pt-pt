---
title: Azure Application Insights para aplicações web JavaScript
description: Obtenha a visualização da página e as contagens de sessão, dados do cliente web, aplicações de página única (SPA) e rastreiem padrões de utilização. Detete exceções e problemas de desempenho em páginas Web de JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: d46b9f9386e8b16d4806e054820cbd82d83ef56b
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266993"
---
# <a name="application-insights-for-web-pages"></a>Application Insights para páginas Web

Saiba mais sobre o desempenho e a utilização da sua aplicação ou página Web. Se adicionar [Insights de Aplicação](app-insights-overview.md) ao seu script de página, obtém os timings das cargas de página e chamadas, contagens e detalhes das exceções do navegador e falhas do AJAX, bem como utilizadores e contagem de sessão. Todas estas podem ser segmentadas por página, SO de cliente e versão do browser, geolocalização e outras dimensões. Pode definir alertas em contagens de falhas ou carregamento lento de página. E, ao inserir chamadas de rastreio no seu código JavaScript, pode controlar a utilização das diferentes funcionalidades da sua aplicação da página Web.

O Application Insights pode ser utilizado com quaisquer páginas Web - basta adicionar um pequeno pedaço de JavaScript. Se o seu serviço web for [Java](java-get-started.md) ou [ASP.NET,](asp-net.md)pode utilizar os SDKs do lado do servidor em conjunto com o JavaScript SDK do lado do cliente para obter uma compreensão final do desempenho da sua aplicação.

## <a name="adding-the-javascript-sdk"></a>Adicionar o JavaScript SDK

1. Primeiro precisa de um recurso Application Insights. Se ainda não tiver uma chave de recursos e instrumentação, siga a [criação de novas instruções de recurso](create-new-resource.md).
2. Copie a chave de instrumentação do recurso onde pretende que a sua telemetria JavaScript seja enviada.
3. Adicione a Aplicação Insights JavaScript SDK à sua página web ou app através de uma das duas opções seguintes:
    * [npm Configuração](#npm-based-setup)
    * [JavaScript Snippet](#snippet-based-setup)

> [!IMPORTANT]
> Utilize apenas um método para adicionar o SDK JavaScript à sua aplicação. Se utilizar a Configuração NPM, não utilize o Snippet e vice-versa.

> [!NOTE]
> A Configuração NPM instala o JavaScript SDK como uma dependência do seu projeto, permitindo ao IntelliSense, enquanto o Snippet vai buscar o SDK em tempo de execução. Ambos suportam as mesmas funcionalidades. No entanto, os desenvolvedores que desejam eventos e configurações mais personalizados geralmente optam pela Configuração NPM, enquanto os utilizadores que procuram uma ativação rápida de análises web fora da caixa optam pelo Snippet.

### <a name="npm-based-setup"></a>configuração baseada em npm

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Configuração baseada em snippet

Se a sua aplicação não utilizar o npm, pode instrumentar diretamente as suas páginas web com Insights de Aplicação, colando este corte no topo de cada uma das suas páginas. De preferência, deve ser o primeiro script na sua `<head>` secção para que possa monitorizar quaisquer problemas potenciais com todas as suas dependências. Se estiver a utilizar a Aplicação do Servidor Blazor, adicione o corte na parte superior do ficheiro `_Host.cshtml` na `<head>` secção.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Envio de telemetria para o portal Azure

Por predefinição, a Aplicação Insights JavaScript SDK recolhe automaticamente uma série de itens de telemetria que são úteis para determinar a saúde da sua aplicação e a experiência subjacente ao utilizador. Incluem-se:

- **Exceções não conseguiu** na sua aplicação, incluindo informações sobre
    - Traço de pilha
    - Detalhes de exceção e mensagem que acompanham o erro
    - Linha & número de erro da coluna
    - URL onde o erro foi levantado
- **Pedidos de Dependência de Rede** feitos pela sua app **XHR** e **Fetch** (a recolha de procura é desativada por padrão), incluem informações sobre
    - Url de fonte de dependência
    - Método & de comando usado para solicitar a dependência
    - Duração do pedido
    - Código de resultados e estado de sucesso do pedido
    - ID (se houver) do utilizador fazendo o pedido
    - Contexto de correlação (se houver) onde o pedido é feito
- **Informações do utilizador** (por exemplo, Localização, rede, IP)
- **Informações do dispositivo** (por exemplo, Browser, OS, versão, idioma, modelo)
- **Informações de sessão**

### <a name="telemetry-initializers"></a>Inicializadores de telemetria
Os inicializadores de telemetria são utilizados para modificar o conteúdo da telemetria recolhida antes de serem enviados do navegador do utilizador. Podem também ser utilizados para evitar que determinadas telemetrias sejam enviadas, `false` devolvendo. Vários inicializadores de telemetria podem ser adicionados à sua instância De Insights de Aplicação, e são executados para adicioná-los.

O argumento de entrada `addTelemetryInitializer` é um callback que toma um [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) argumento como argumento e devolve a ou `boolean` `void` . Se `false` voltar, o item da telemetria não é enviado, caso contrário, passa para o próximo inicializador de telemetria, caso exista, ou seja, enviado para o ponto final da recolha de telemetria.

Um exemplo de utilização de inicializadores de telemetria:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Configuração
A maioria dos campos de configuração são nomeados de modo a que possam ser infringidos a falsos. Todos os campos são opcionais, `instrumentationKey` exceto.

| Name | Predefinição | Descrição |
|------|---------|-------------|
| instrumentaçãoKey | nulo | **Necessário**<br>Chave de instrumentação que obteve do portal Azure. |
| accountId | nulo | Um ID de conta opcional, se a sua aplicação agru tiver em conta os utilizadores. Sem espaços, vírgulas, semi-acolchoados, iguais ou barras verticais |
| sessionRenewalMs | 1800000 | Uma sessão é registada se o utilizador estiver inativo durante este período de tempo em milissegundos. O padrão é de 30 minutos |
| sessionExpirationMs | 86400000 | Uma sessão é registada se tiver continuado por este tempo em milissegundos. O padrão é de 24 horas |
| maxBatchSizeInBytes | 10000 | Tamanho máximo do lote de telemetria. Se um lote exceder este limite, é imediatamente enviado e um novo lote é iniciado |
| maxBatchInterval | 15 000 | Quanto tempo para a telemetria de lote antes de enviar (milissegundos) |
| desativarExcepçãoTracking | false | Se for verdade, as exceções não são autocolhidas. A predefinição é falso. |
| desativar atelemetria | false | Se for verdade, a telemetria não é recolhida ou enviada. A predefinição é falso. |
| enableDebug | false | Se forem verdadeiros, os dados de depuragem **internos** são lançados como uma exceção **em vez** de serem registados, independentemente das definições de registo SDK. A predefinição é falso. <br>***Nota:*** Ativar esta definição resultará numa telemetria abandonada sempre que ocorrer um erro interno. Isto pode ser útil para identificar rapidamente problemas com a sua configuração ou utilização do SDK. Se não quiser perder a telemetria durante a depuragem, considere utilizar `consoleLoggingLevel` ou em vez de `telemetryLoggingLevel` `enableDebug` . |
| loggingLevelConsole | 0 | Regista erros **internos** de Insights de Aplicação para consolar. <br>0: desligado, <br>1: Apenas erros críticos, <br>2: Tudo (erros & avisos) |
| loggingLeemetria | 1 | Envia erros **internos** de Insights de Aplicação como telemetria. <br>0: desligado, <br>1: Apenas erros críticos, <br>2: Tudo (erros & avisos) |
| diagnósticoLogInterval | 10000 | (interno) Intervalo de votação (em ms) para a fila interna de registo |
| amostragemPercentage | 100 | Percentagem de eventos que serão enviados. O padrão é 100, o que significa que todos os eventos são enviados. Desagrei isto se pretender preservar a sua tampa de dados para aplicações em larga escala. |
| autoTrackPageVisitTime | false | Se for verdade, numa visão de página, o tempo de visualização da página instrumentada anterior é rastreado e enviado como telemetria e um novo temporizador é iniciado para a visão de página atual. A predefinição é falso. |
| desativarAjaxTracking | false | Se for verdade, as chamadas do Ajax não são automáticas. A predefinição é falso. |
| desativar O Rastreio de Deficiência | true | Se for verdade, os pedidos de procura não são automaticamente recolhidos. O padrão é verdadeiro |
| sobrerideridePageViewDuration | false | Se for verdadeiro, o comportamento predefinido do trackPageView é alterado para registar o intervalo de duração da visualização da página quando o trackPageView é chamado. Se for fornecida duração falsa e não é fornecida qualquer duração personalizada para rastrear o PageView, o desempenho da visualização da página é calculado utilizando a API de tempo de navegação. A predefinição é falso. |
| maxAjaxCallsPerView | 500 | Predefinido 500 - controla quantas chamadas do Ajax serão monitorizadas por visualização de página. Definir para -1 para monitorizar todas as chamadas (ilimitadas) do Ajax na página. |
| desativarDataLossAnalysis | true | Se falso, os amortecedores internos de telemetria serão verificados no arranque de itens ainda não enviados. |
| desativar Os Cabeçalhos deCorrelation | false | Se for falso, o SDK adicionará dois cabeçalhos ('Request-Id' e 'Request-Context') a todos os pedidos de dependência para os correlacionar com os pedidos correspondentes do lado do servidor. A predefinição é falso. |
| correlationHeaderExcludedDomains |  | Desativar cabeçalhos de correlação para domínios específicos |
| correlationHeaderDomains |  | Permitir cabeçalhos de correlação para domínios específicos |
| desativarFlushOnBeforeUnload | false | Falso padrão. Se for verdade, o método de descarga não será chamado quando emBeforeUnload evento dispara |
| enableSessionStorageBuffer | true | Padrão verdadeiro. Se for verdade, o tampão com toda a telemetria não solicitada é armazenado no armazenamento da sessão. O tampão é restaurado na carga da página |
| isCookieUseDisabled | false | Falso padrão. Se for verdade, o SDK não armazenará nem lerá quaisquer dados a partir de cookies.|
| cookieDomain | nulo | Domínio de cookies personalizado. Isto é útil se você quiser compartilhar cookies Application Insights em subdomínios. |
| isRetryDisabled | false | Falso padrão. Se for falso, reda o ano de 206 (sucesso parcial), 408 (tempo limite), 429 (pedidos a mais), 500 (erro do servidor interno), 503 (serviço indisponível) e 0 (offline, apenas se for detetado) |
| isStorageUseDisabled | false | Se for verdade, o SDK não armazenará nem lerá quaisquer dados do armazenamento local e da sessão. A predefinição é falso. |
| isBeaconApiDisabled | true | Se for falso, o SDK enviará toda a telemetria usando a [API do Farol](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | Falso padrão. quando o separador estiver fechado, o SDK enviará toda a telemetria restante usando a [API do Farol](https://www.w3.org/TR/beacon) |
| sdkExtension | nulo | Define o nome da extensão do SDK. Só são permitidos caracteres alfabéticos. O nome da extensão é adicionado como prefixo à etiqueta 'ai.internal.sdkVersion' (por exemplo, 'ext_javascript:2.0.0'). O incumprimento é nulo. |
| isBrowserLinkTrackingEnabled | false | A predefinição é falso. Se for verdade, o SDK rastreará todos os pedidos [do Browser Link.](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) |
| appId | nulo | O AppId é utilizado para a correlação entre as dependências do AJAX que acontecem do lado do cliente com os pedidos do lado do servidor. Quando a API do Farol está ativada, não pode ser utilizada automaticamente, mas pode ser definida manualmente na configuração. O padrão é nulo |
| ativarcorrelação | false | Se for verdade, o SDK adicionará dois cabeçalhos ('Request-Id' e 'Request-Context') a todos os pedidos do CORS para correlacionar as dependências do AJAX cessantes com os pedidos correspondentes no lado do servidor. O padrão é falso |
| nomePrefixo | indefinido | Um valor opcional que será usado como postfix de nome para localStorage e nome de cookies.
| enableAutoRouteTracking | false | Acompanhe automaticamente as alterações de rota em Aplicações de Página Única (SPA). Se for verdade, cada mudança de rota enviará um novo Pageview para Insights de Aplicação. As alterações na rota do `example.com/foo#bar` haxixe também são registadas como novas vistas de página.
| permitir RequestHeaderTracking | false | Se for verdade, os cabeçalhos de pedido do AJAX & Fetch são rastreados, o padrão é falso.
| ativarResponseHeaderTracking | false | Se for verdade, os cabeçalhos de resposta do pedido do AJAX & Fetch são rastreados, o padrão é falso.
| DistributedTracingMode | `DistributedTracingModes.AI` | Define o modo de rastreio distribuído. Se AI_AND_W3C modo ou modo W3C estiver definido, os cabeçalhos de contexto de vestígios W3C (traceparent/tracestate) serão gerados e incluídos em todos os pedidos de saída. AI_AND_W3C está prevista para retrocompatibilidade com quaisquer serviços instrumentados da Aplicação Insights. Veja o exemplo [aqui.](https://docs.microsoft.com/azure/azure-monitor/app/correlation#enable-w3c-distributed-tracing-support-for-web-apps)

## <a name="single-page-applications"></a>Aplicações de página única

Por predefinição, este SDK **não** lidará com a mudança de rota baseada no estado que ocorre em aplicações de página única. Para ativar o rastreio automático de alterações de rota para a sua aplicação de página única, pode adicionar `enableAutoRouteTracking: true` à sua configuração de configuração.

Atualmente, oferecemos um [plugin React](#react-extensions)separado, que pode inicializar com este SDK. Também irá realizar o rastreio de mudança de rota para si, bem como recolher [outras telemetrias específicas react.](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)

> [!NOTE]
> `enableAutoRouteTracking: true`Utilize apenas se **não** estiver a utilizar o plugin React. Ambos são capazes de enviar novos PageViews quando a rota muda. Se ambos estiverem ativados, podem ser enviadas versões de página duplicadas.

## <a name="configuration-autotrackpagevisittime"></a>Configuração: autoTrackPageVisitTime

Ao `autoTrackPageVisitTime: true` configurar, o tempo que um utilizador passa em cada página é rastreado. Em cada novo PageView, a duração que o utilizador gastou na página *anterior* é enviada como uma [métrica personalizada](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview) chamada `PageVisitTime` . Esta métrica personalizada é visível no [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) como uma "métrica baseada em log".

## <a name="react-extensions"></a>Reaja extensões

| Extensões |
|---------------|
| [Reagir](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Reagir Nativo](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Explore dados do navegador/cliente

Os dados do navegador/cliente podem ser vistos indo a **Metrics** e adicionando métricas individuais que lhe interessam:

![](./media/javascript/page-view-load-time.png)

Também pode ver os seus dados a partir do SDK JavaScript através da experiência Do Navegador no portal.

Selecione **Browser** e, em seguida, escolha **Falhas** ou **Performance**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Desempenho

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Dependências

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Análise

Para consultar a sua telemetria recolhida pelo JavaScript SDK, selecione o botão **Ver em Registos (Analytics).** Ao adicionar uma `where` declaração de `client_Type == "Browser"` , apenas verá dados do JavaScript SDK e qualquer telemetria do lado do servidor recolhida por outros SDKs será excluída.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Suporte ao mapa de origem

O saque minificado da sua telemetria de exceção pode não ser administrado no portal Azure. Todas as integrações existentes no painel Detalhes de Exceção funcionarão com o novo saque não qualificado.

#### <a name="link-to-blob-storage-account"></a>Link para a conta de armazenamento Blob

Pode ligar o seu recurso Application Insights ao seu próprio recipiente de armazenamento Azure Blob para desminar automaticamente as pilhas de chamadas. Para começar, consulte o [suporte automático do mapa de origem.](./source-map-support.md)

### <a name="drag-and-drop"></a>Arrastar e largar

1. Selecione um item de telemetria de exceção no portal Azure para ver os seus "detalhes de transações de ponta a ponta"
2. Identifique quais mapas de origem correspondem a esta pilha de chamadas. O mapa de origem deve corresponder ao ficheiro de origem de uma pilha, mas sufixado com`.map`
3. Arraste e deixe cair os mapas de origem na pilha de chamadas no portal Azure![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Insights de Aplicação Web Basic

Para uma experiência leve, pode instalar a versão básica do Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Esta versão vem com o número mínimo de funcionalidades e funcionalidades e conta consigo para a construir como entender. Por exemplo, não realiza autocolecção (exceções não conseguiu, AJAX, etc.). As APIs para enviar certos tipos de telemetria, `trackTrace` `trackException` como, etc., não estão incluídas nesta versão, pelo que terá de fornecer o seu próprio invólucro. A única API disponível `track` é. Uma [amostra](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) está localizada aqui.

## <a name="examples"></a>Exemplos

Para exemplos runnáveis, consulte [Informações de Aplicações JavaScript Amostras SDK](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Upgrade a partir da versão antiga da Application Insights

Quebrando alterações na versão SDK V2:
- Para permitir melhores assinaturas de API, algumas das chamadas API, tais como trackPageView e trackException, foram atualizadas. A execução no Internet Explorer 8 e versões anteriores do navegador não são suportadas.
- O envelope de telemetria tem nome de campo e alterações de estrutura devido a atualizações de esquemas de dados.
- Mudou-se `context.operation` `context.telemetryTrace` para. Alguns campos também foram `operation.id` alterados .  -->  `telemetryTrace.traceID`
  - Para atualizar manualmente o ID do visão de página atual (por exemplo, em aplicações DE SPA), utilize `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()` .
    > [!NOTE]
    > Para manter o vestígio único, onde já `Util.newId()` utilizou, utilize agora `Util.generateW3CId()` . Ambos acabam por ser a identificação da operação.

Se estiver a utilizar os atuais insights de aplicação PRODUCTION SDK (1.0.20) e quiser ver se o novo SDK funciona em tempo de execução, atualize o URL dependendo do seu cenário de carregamento SDK atual.

- Descarregue através do cenário CDN: Atualize o corte de código que utiliza atualmente para apontar para o seguinte URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm cenário: Chamada `downloadAndSetup` para descarregar o script completo applicationInsights da CDN e inicializá-lo com chave de instrumentação:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Teste em ambiente interno para verificar se a telemetria de monitorização está a funcionar como esperado. Se tudo funcionar, atualize as suas assinaturas API adequadamente para a versão SDK V2 e implemente nos seus ambientes de produção.

## <a name="sdk-performanceoverhead"></a>Desempenho/sobrecarga da SDK

Com apenas 25 KB gzipped, e levando apenas ~15 ms para inicializar, Application Insights adiciona uma quantidade insignificante de tempo de carga ao seu website. Ao utilizar o corte, os componentes mínimos da biblioteca são rapidamente carregados. Entretanto, o script completo é descarregado em segundo plano.

Enquanto o script está a ser descarregado a partir do CDN, todo o rastreio da sua página está na fila. Uma vez que o script descarregado termina assiná-lo assíncronos, todos os eventos que foram em fila são rastreados. Como resultado, não perderá nenhuma telemetria durante todo o ciclo de vida da sua página. Este processo de configuração fornece à sua página um sistema de análise sem emenda, invisível para os seus utilizadores.

> Resumo:
> - **25 KB** gzipped
> - **15 ms** tempo de inicialização geral
> - **Rastreio zero** perdido durante o ciclo de vida da página

## <a name="browser-support"></a>Browser support (Suporte do browser)

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Ópera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Últimas ✔ do Chrome |  ✔ mais recentes do Firefox | IE 9+ & Edge ✔ | Última ✔ de Ópera | Últimas ✔ do Safari |

## <a name="open-source-sdk"></a>SDK de código aberto

O App Insights JavaScript SDK é de código aberto para visualizar o código fonte ou para contribuir para o projeto visitar o [repositório oficial](https://github.com/Microsoft/ApplicationInsights-JS)do GitHub .

## <a name="next-steps"></a><a name="next"></a>Próximos passos
* [Controlar a utilização](usage-overview.md)
* [Métricas e eventos personalizados](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
