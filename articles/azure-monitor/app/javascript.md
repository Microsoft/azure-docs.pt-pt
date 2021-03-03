---
title: Azure Application Insights para aplicações web JavaScript
description: Obtenha a visualização da página e as contagens de sessão, dados do cliente web, aplicações de página única (SPA) e rastreiem padrões de utilização. Detete exceções e problemas de desempenho em páginas Web de JavaScript.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 58f17deb20a43a76a9b7813e25b16d0ee3770c71
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708429"
---
# <a name="application-insights-for-web-pages"></a>Application Insights para páginas Web

Saiba mais sobre o desempenho e a utilização da sua aplicação ou página Web. Se adicionar [Insights de Aplicação](app-insights-overview.md) ao seu script de página, obtém os timings das cargas de página e chamadas, contagens e detalhes das exceções do navegador e falhas do AJAX, bem como utilizadores e contagem de sessão. Todas estas podem ser segmentadas por página, SO de cliente e versão do browser, geolocalização e outras dimensões. Pode definir alertas em contagens de falhas ou carregamento lento de página. E, ao inserir chamadas de rastreio no seu código JavaScript, pode controlar a utilização das diferentes funcionalidades da sua aplicação da página Web.

O Application Insights pode ser utilizado com quaisquer páginas Web - basta adicionar um pequeno pedaço de JavaScript. Se o seu serviço web for [Java](java-get-started.md) ou [ASP.NET,](asp-net.md)pode utilizar os SDKs do lado do servidor em conjunto com o JavaScript SDK do lado do cliente para obter uma compreensão final do desempenho da sua aplicação.

## <a name="adding-the-javascript-sdk"></a>Adicionar o JavaScript SDK

> [!IMPORTANT]
> As novas regiões de Azure **requerem** a utilização de cordas de ligação em vez de teclas de instrumentação. [A cadeia de ligação](./sdk-connection-string.md?tabs=js) identifica o recurso com o que pretende associar os seus dados de telemetria. Também permite modificar os pontos finais que o seu recurso utilizará como destino para a sua telemetria. Terá de copiar o fio de ligação e adicioná-lo ao código da sua aplicação ou a uma variável ambiental.

1. Primeiro precisa de um recurso Application Insights. Se ainda não tiver uma chave de recursos e instrumentação, siga a [criação de novas instruções de recurso](create-new-resource.md).
2. Copie a _tecla de instrumentação_ (também conhecida como "iKey") ou [a cadeia de ligação](#connection-string-setup) para o recurso onde pretende que a sua telemetria JavaScript seja enviada (a partir do passo 1.) Irá adicioná-lo à `instrumentationKey` ou `connectionString` configuração do SDK JavaScript De Aplicações.
3. Adicione a Aplicação Insights JavaScript SDK à sua página web ou app através de uma das duas opções seguintes:
    * [npm Configuração](#npm-based-setup)
    * [JavaScript Snippet](#snippet-based-setup)

> [!IMPORTANT]
> Utilize apenas um método para adicionar o SDK JavaScript à sua aplicação. Se utilizar a Configuração NPM, não utilize o Snippet e vice-versa.

> [!NOTE]
> A Configuração NPM instala o JavaScript SDK como uma dependência do seu projeto, permitindo ao IntelliSense, enquanto o Snippet vai buscar o SDK em tempo de execução. Ambos suportam as mesmas funcionalidades. No entanto, os desenvolvedores que desejam eventos e configurações mais personalizados geralmente optam pela Configuração NPM, enquanto os utilizadores que procuram uma ativação rápida de análises web fora da caixa optam pelo Snippet.

### <a name="npm-based-setup"></a>configuração baseada em npm

Instalar via NPM.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **As dactilografias estão incluídas neste pacote,** pelo que **não** é necessário instalar um pacote de dactilografias separados.
    
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

Se a sua aplicação não utilizar o npm, pode instrumentar diretamente as suas páginas web com Insights de Aplicação, colando este corte no topo de cada uma das suas páginas. De preferência, deve ser o primeiro script na sua `<head>` secção para que possa monitorizar quaisquer problemas potenciais com todas as suas dependências e opcionalmente quaisquer erros javaScript. Se estiver a utilizar a Aplicação do Servidor Blazor, adicione o corte na parte superior do ficheiro `_Host.cshtml` na `<head>` secção.

Para ajudar no rastreio de qual versão do snippet a sua aplicação está a usar, a partir da versão 2.5.5 o evento de visualização da página incluirá uma nova tag "ai.internal.snippet" que conterá a versão de snippet identificada.

O snippet atual (listado abaixo) é a versão "5", a versão está codificada no snippet como sv:"#" e a [versão atual também está disponível no GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{nConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> Para a legibilidade e para reduzir possíveis erros javaScript, todas as opções de configuração possíveis estão listadas numa nova linha no código de snippet acima, se não quiser alterar o valor de uma linha comentada, pode ser removida.


#### <a name="reporting-script-load-failures"></a>Relatório de falhas de carga do script

Esta versão do snippet deteta e reporta falhas ao carregar o SDK do CDN como uma exceção ao portal Azure Monitor (sob o &gt; navegador de exceções de &gt; falhas), esta exceção proporciona visibilidade a falhas deste tipo para que esteja ciente de que a sua aplicação não está a reportar telemetria (ou outras exceções) como esperado. Este sinal é uma medida importante na compreensão de que perdeu a telemetria porque o SDK não carregou ou iniiva que pode levar a:
- Subnotificação de como os utilizadores estão a usar (ou a tentar utilizar) o seu site;
- Falta de telemetria sobre a forma como os seus utilizadores finais estão a utilizar o seu site;
- Erros javaScript em falta que podem estar a bloquear os seus utilizadores finais de utilizarem com sucesso o seu site.

Para obter detalhes sobre esta exceção consulte a página de resolução de problemas de falha de [carga SDK.](javascript-sdk-load-failure.md)

A comunicação desta falha como uma exceção ao portal não utiliza a opção de configuração ```disableExceptionTracking``` a partir da configuração de insights de aplicação e, portanto, se esta falha ocorrer, será sempre reportada pelo corte, mesmo quando o suporte de janela.onerror está desativado.

A comunicação de falhas de carga SDK não é suportada especificamente no IE 8 (ou menos). Isto ajuda na redução do tamanho minizado do corte, assumindo que a maioria dos ambientes não são exclusivamente IE 8 ou menos. Se tiver este requisito e desejar receber estas exceções, terá de incluir um preenchimento de poli ou criar a sua própria versão de snippet que utiliza ```XDomainRequest``` em vez de , ```XMLHttpRequest``` recomenda-se que utilize o [código fonte de corte fornecido](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) como ponto de partida.

> [!NOTE]
> Se estiver a utilizar uma versão anterior do snippet, é altamente recomendável que atualize a versão mais recente para que receba estes problemas anteriormente não reportados.

#### <a name="snippet-configuration-options"></a>Opções de configuração de snippet

Todas as opções de configuração foram agora movendo-se para o final do script para ajudar a evitar a introdução acidental de erros JavaScript que não só fariam com que o SDK não fosse carregado, mas também desativaria o relato da falha.

Cada opção de configuração é mostrada acima numa nova linha, se não pretender anular o valor predefinido de um item listado como [opcional] pode remover essa linha para minimizar o tamanho resultante da sua página devolvida.

As opções de configuração disponíveis são

| Nome | Tipo | Descrição
|------|------|----------------
| src | corda **[necessária]** | O URL completo para onde carregar o SDK. Este valor é usado para o atributo "src" de um script/tag dinamicamente &lt; &gt; adicionado. Você pode usar a localização pública da CDN ou a sua própria hospedada privada.
| name | corda *[opcional]* | O nome global para o SDK inicializado, predefine para `appInsights` . Assim ```window.appInsights``` será uma referência à instância inicializada. Nota: se fornecer um valor de nome ou uma instância anterior parece ser atribuído (através da app de nome globalInsightsSDK) então este valor de nome também será definido no espaço de nome global, pois ```window.appInsightsSDK=<name value>``` , isto é exigido pelo código de inicialização SDK para garantir que está a inicializar e atualizar os métodos corretos de esqueleto e procuração.
| ld | número em ms *[opcional]* | Define o atraso de carga para esperar antes de tentar carregar o SDK. O valor predefinido é de 0ms e qualquer valor negativo adicionará imediatamente uma etiqueta de script à &lt; &gt; região principal da página, que bloqueará o evento de carga de página até que o script seja carregado (ou falhe).
| useXhr | boolean *[opcional]* | Esta definição é utilizada apenas para reportar falhas de carga SDK. A reportagem tentará primeiro utilizar o fetch() se disponível e, em seguida, recue para XHR, definindo este valor para verdadeiro apenas ignora a verificação de procura. A utilização deste valor só é necessária se a sua aplicação estiver a ser utilizada num ambiente onde a procura não enviaria os eventos de falha.
| crossOrigin | corda *[opcional]* | Ao incluir esta definição, a etiqueta de script adicionada para descarregar o SDK incluirá o atributo crossOrigin com este valor de cadeia. Quando não definido (o padrão) não é adicionado nenhum atributo crossOrigin. Os valores recomendados não são definidos (o padrão); ""; ou "anónimo" (Para todos os valores válidos ver [atributo HTML: `crossorigin` ](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) documentação)
| cfg | objeto **[necessário]** | A configuração passou para o Application Insights SDK durante a inicialização.

### <a name="connection-string-setup"></a>Configuração de cadeia de ligação

Para a configuração NPM ou Snippet, também pode configurar a sua instância de Application Insights utilizando uma cadeia de ligação. Basta substituir o `instrumentationKey` campo pelo `connectionString` campo.
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
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
| instrumentaçãoKey | nulo | **Obrigatório**<br>Chave de instrumentação que obteve do portal Azure. |
| accountId | nulo | Um ID de conta opcional, se a sua aplicação agru tiver em conta os utilizadores. Sem espaços, vírgulas, semi-acolchoados, iguais ou barras verticais |
| sessionRenewalMs | 1800000 | Uma sessão é registada se o utilizador estiver inativo durante este período de tempo em milissegundos. O padrão é de 30 minutos |
| sessionExpirationMs | 86400000 | Uma sessão é registada se tiver continuado por este tempo em milissegundos. O padrão é de 24 horas |
| maxBatchSizeInBytes | 10000 | Tamanho máximo do lote de telemetria. Se um lote exceder este limite, é imediatamente enviado e um novo lote é iniciado |
| maxBatchInterval | 15 000 | Quanto tempo para a telemetria de lote antes de enviar (milissegundos) |
| desativarExcepçãoTracking | false | Se for verdade, as exceções não são automaticamente recolhidas. A predefinição é falso. |
| desativar atelemetria | false | Se for verdade, a telemetria não é recolhida ou enviada. A predefinição é falso. |
| enableDebug | false | Se forem verdadeiros, os dados de depuragem **internos** são lançados como uma exceção **em vez** de serem registados, independentemente das definições de registo SDK. A predefinição é falso. <br>**_Nota:_** Ativar esta definição resultará numa telemetria abandonada sempre que ocorrer um erro interno. Isto pode ser útil para identificar rapidamente problemas com a sua configuração ou utilização do SDK. Se não quiser perder a telemetria durante a depuragem, considere utilizar `consoleLoggingLevel` ou em vez de `telemetryLoggingLevel` `enableDebug` . |
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
| isCookieUseDisabled | false | Falso padrão. Se for verdade, o SDK não armazenará nem lerá quaisquer dados a partir de cookies. Note que isto desativa os cookies de Utilizador e Sessão e torna inúteis as lâminas de utilização e as experiências. |
| cookieDomain | nulo | Domínio de cookies personalizado. Isto é útil se você quiser compartilhar cookies Application Insights em subdomínios. |
| isRetryDisabled | false | Falso padrão. Se for falso, reda o ano de 206 (sucesso parcial), 408 (tempo limite), 429 (pedidos a mais), 500 (erro do servidor interno), 503 (serviço indisponível) e 0 (offline, apenas se for detetado) |
| isStorageUseDisabled | false | Se for verdade, o SDK não armazenará nem lerá quaisquer dados do armazenamento local e da sessão. A predefinição é falso. |
| isBeaconApiDisabled | true | Se for falso, o SDK enviará toda a telemetria usando a [API do Farol](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | Falso padrão. quando o separador estiver fechado, o SDK enviará toda a telemetria restante usando a [API do Farol](https://www.w3.org/TR/beacon) |
| sdkExtension | nulo | Define o nome da extensão do SDK. Só são permitidos caracteres alfabéticos. O nome da extensão é adicionado como prefixo à etiqueta 'ai.internal.sdkVersion' (por exemplo, 'ext_javascript:2.0.0'). O incumprimento é nulo. |
| isBrowserLinkTrackingEnabled | false | A predefinição é falso. Se for verdade, o SDK rastreará todos os pedidos [do Browser Link.](/aspnet/core/client-side/using-browserlink) |
| appId | nulo | O AppId é utilizado para a correlação entre as dependências do AJAX que acontecem do lado do cliente com os pedidos do lado do servidor. Quando a API do Farol está ativada, não pode ser utilizada automaticamente, mas pode ser definida manualmente na configuração. O padrão é nulo |
| ativarcorrelação | false | Se for verdade, o SDK adicionará dois cabeçalhos ('Request-Id' e 'Request-Context') a todos os pedidos do CORS para correlacionar as dependências do AJAX cessantes com os pedidos correspondentes no lado do servidor. O padrão é falso |
| nomePrefixo | indefinido | Um valor opcional que será usado como postfix de nome para localStorage e nome de cookies.
| enableAutoRouteTracking | false | Acompanhe automaticamente as alterações de rota em Aplicações de Página Única (SPA). Se for verdade, cada mudança de rota enviará um novo Pageview para Insights de Aplicação. As alterações na rota do `example.com/foo#bar` haxixe também são registadas como novas vistas de página.
| permitir RequestHeaderTracking | false | Se for verdade, os cabeçalhos de pedido do AJAX & Fetch são rastreados, o padrão é falso.
| ativarResponseHeaderTracking | false | Se for verdade, os cabeçalhos de resposta do pedido do AJAX & Fetch são rastreados, o padrão é falso.
| DistributedTracingMode | `DistributedTracingModes.AI` | Define o modo de rastreio distribuído. Se AI_AND_W3C modo ou modo W3C estiver definido, os cabeçalhos de contexto de vestígios W3C (traceparent/tracestate) serão gerados e incluídos em todos os pedidos de saída. AI_AND_W3C está prevista para retrocompatibilidade com quaisquer serviços instrumentados da Aplicação Insights. Veja o exemplo [aqui.](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)
| enableAjaxErrorStatusText | false | Falso padrão. Se for verdade, inclua texto de dados de erro de resposta em evento de dependência em pedidos de AJAX falhados.
| enableAjaxPerfTracking | false | Falso padrão. Bandeira para permitir olhar para cima e incluir tempos adicionais de janela de navegador.performance nas `ajax` métricas reportadas (XHR e fetch).
| maxAjaxPerfLookupAttempts | 3 | Incumprimentos para 3. O número máximo de vezes para procurar os timings de desempenho da janela (se disponível), isto é necessário, uma vez que nem todos os navegadores povoam a janela.desempenho antes de reportar o fim do pedido de XHR e para pedidos de busca este é adicionado após a sua conclusão.
| ajaxPerfLookupDelay | 25 | Incumprimentos a 25 ms. A quantidade de tempo para esperar antes de tentar encontrar os timings de desempenho do windows.performance para um `ajax` pedido, o tempo é de milissegundos e é passado diretamente para definirTimeout().
| enableUnledPromiseRejectionTracking | false | Se forem verdadeiras e não manipuladas, as rejeições de promessas serão automaticamente recolhidas e reportadas como um erro javaScript. Quando desativarExceptionTracking é verdadeiro (não rastreia exceções), o valor config será ignorado e as rejeições de promessas não manipuladas não serão reportadas.

## <a name="enable-time-on-page-tracking"></a>Ativar o rastreio de tempo na página

Ao `autoTrackPageVisitTime: true` configurar, o tempo que um utilizador passa em cada página é rastreado. Em cada novo PageView, a duração que o utilizador gastou na página *anterior* é enviada como uma [métrica personalizada](../essentials/metrics-custom-overview.md) chamada `PageVisitTime` . Esta métrica personalizada é visível no [Metrics Explorer](../essentials/metrics-getting-started.md) como uma "métrica baseada em log".

## <a name="enable-correlation"></a>Ativar a correlação

A correlação gera e envia dados que permitem o rastreio distribuído e alimenta o mapa de [aplicações,](../app/app-map.md) [a vista de transações de ponta a ponta](../app/app-map.md#go-to-details)e outras ferramentas de diagnóstico.

O exemplo a seguir mostra todas as configurações possíveis necessárias para permitir a correlação, com notas específicas do cenário abaixo:

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

Se algum dos seus servidores de terceiros com os quais o cliente comunica não puder aceitar os `Request-Id` `Request-Context` cabeçalhos e os cabeçalhos, e não conseguir atualizar a sua configuração, então terá de os colocar numa lista de exclusão através da `correlationHeaderExcludeDomains` propriedade de configuração. Esta propriedade suporta wildcards.

O lado do servidor tem de ser capaz de aceitar ligações com os cabeçalhos presentes. Dependendo da `Access-Control-Allow-Headers` configuração do lado do servidor, é frequentemente necessário alargar a lista do lado do servidor adicionando manualmente `Request-Id` e `Request-Context` .

Acesso-Controlo-Permitir-Cabeçalhos: `Request-Id` `Request-Context` , `<your header>`

> [!NOTE]
> Se estiver a utilizar SDKs OpenTelemtry ou Application Insights lançados em 2020 ou posteriormente, recomendamos a utilização [do WC3 TraceContext](https://www.w3.org/TR/trace-context/). Consulte a orientação de configuração [aqui](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).

## <a name="single-page-applications"></a>Aplicações de página única

Por predefinição, este SDK **não** lidará com a mudança de rota baseada no estado que ocorre em aplicações de página única. Para ativar o rastreio automático de alterações de rota para a sua aplicação de página única, pode adicionar `enableAutoRouteTracking: true` à sua configuração de configuração.

Atualmente, oferecemos um [plugin React](javascript-react-plugin.md)separado, que pode inicializar com este SDK. Também irá realizar o rastreio de mudança de rota para si, bem como recolher outras telemetrias específicas da React.
> [!NOTE]
> `enableAutoRouteTracking: true`Utilize apenas se **não** estiver a utilizar o plugin React. Ambos são capazes de enviar novos PageViews quando a rota muda. Se ambos estiverem ativados, podem ser enviadas versões de página duplicadas.

## <a name="extensions"></a>Extensões

| Extensões |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [Clique na coleção automática de Analytics](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>Explore dados do navegador/cliente

Os dados do navegador/cliente podem ser vistos indo a **Metrics** e adicionando métricas individuais que lhe interessam:

![Screenshot da página Métricas em Application Insights mostrando exibições gráficas de dados de métricas para uma aplicação web.](./media/javascript/page-view-load-time.png)

Também pode ver os seus dados a partir do SDK JavaScript através da experiência Do Navegador no portal.

Selecione **Browser** e, em seguida, escolha **Falhas** ou **Performance**.

![Screenshot da página do Navegador em Application Insights mostrando como adicionar Falhas do Navegador ou Desempenho do Navegador às métricas que você pode ver para a sua aplicação web.](./media/javascript/browser.png)

### <a name="performance"></a>Desempenho

![Screenshot da página de Desempenho em Application Insights mostrando exibições gráficas de métricas de Operações para uma aplicação web.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Dependências

![Screenshot da página de Desempenho em Application Insights mostrando exibições gráficas de métricas de dependência para uma aplicação web.](./media/javascript/performance-dependencies.png)

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
2. Identifique quais mapas de origem correspondem a esta pilha de chamadas. O mapa de origem deve corresponder ao ficheiro de origem de uma pilha, mas sufixado com `.map`
3. Arraste e deixe cair os mapas de origem na pilha de chamadas no portal Azure ![ Uma imagem animada mostrando como arrastar e largar ficheiros de mapas de origem de uma pasta de construção na janela Call Stack no portal Azure.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Insights de Aplicação Web Basic

Para uma experiência leve, pode instalar a versão básica do Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Esta versão vem com o número mínimo de funcionalidades e funcionalidades e conta consigo para a construir como entender. Por exemplo, não realiza autocolecção (exceções não conseguiu, AJAX, etc.). As APIs para enviar certos tipos de telemetria, `trackTrace` `trackException` como, etc., não estão incluídas nesta versão, pelo que terá de fornecer o seu próprio invólucro. A única API disponível `track` é. Uma [amostra](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) está localizada aqui.

## <a name="examples"></a>Exemplos

Para exemplos runnáveis, consulte [As amostras javaScript SDK de Informações de aplicações.](https://github.com/Azure-Samples?q=applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Upgrade a partir da versão antiga da Application Insights

Quebrando alterações na versão SDK V2:
- Para permitir melhores assinaturas de API, algumas das chamadas API, tais como trackPageView e trackException, foram atualizadas. A execução no Internet Explorer 8 e versões anteriores do navegador não são suportadas.
- O envelope de telemetria tem nome de campo e alterações de estrutura devido a atualizações de esquemas de dados.
- Mudou-se `context.operation` `context.telemetryTrace` para. Alguns campos também foram `operation.id` alterados .  -->  `telemetryTrace.traceID`
  - Para atualizar manualmente o ID do visão de página atual (por exemplo, em aplicações DE SPA), utilize `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` .
    > [!NOTE]
    > Para manter o vestígio único, onde já `Util.newId()` utilizou, utilize agora `Util.generateW3CId()` . Ambos acabam por ser a identificação da operação.

Se estiver a utilizar os atuais insights de aplicação PRODUCTION SDK (1.0.20) e quiser ver se o novo SDK funciona em tempo de execução, atualize o URL dependendo do seu cenário de carregamento SDK atual.

- Descarregue através do cenário CDN: Atualize o corte de código que utiliza atualmente para apontar para o seguinte URL:
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- npm cenário: Chamada `downloadAndSetup` para descarregar o script completo applicationInsights da CDN e inicializá-lo com chave de instrumentação:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

Teste em ambiente interno para verificar se a telemetria de monitorização está a funcionar como esperado. Se tudo funcionar, atualize as suas assinaturas API adequadamente para a versão SDK V2 e implemente nos seus ambientes de produção.

## <a name="sdk-performanceoverhead"></a>Desempenho/sobrecarga da SDK

Com apenas 36 KB gzipped, e levando apenas ~15 ms para inicializar, Application Insights adiciona uma quantidade insignificante de tempo de carga ao seu website. Ao utilizar o corte, os componentes mínimos da biblioteca são rapidamente carregados. Entretanto, o script completo é descarregado em segundo plano.

Enquanto o script está a ser descarregado a partir do CDN, todo o rastreio da sua página está na fila. Uma vez que o script descarregado termina assiná-lo assíncronos, todos os eventos que foram em fila são rastreados. Como resultado, não perderá nenhuma telemetria durante todo o ciclo de vida da sua página. Este processo de configuração fornece à sua página um sistema de análise sem emenda, invisível para os seus utilizadores.

> Resumo:
> - ![versão npm](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![tamanho comprimido gzip](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 ms** tempo de inicialização geral
> - **Rastreio zero** perdido durante o ciclo de vida da página

## <a name="browser-support"></a>Browser support (Suporte do browser)

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Ópera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Últimas ✔ do Chrome |  ✔ mais recentes do Firefox | IE 9+ & Edge ✔<br>IE 8- Compatível | Última ✔ de Ópera | Últimas ✔ do Safari |

## <a name="es3ie8-compatibility"></a>Compatibilidade ES3/iE8

Como SDK existem inúmeros utilizadores que não conseguem controlar os navegadores que os seus clientes utilizam. Como tal, precisamos de garantir que este SDK continua a "trabalhar" e não quebra a execução do JS quando carregado por um navegador mais antigo. Embora seja ideal não suportar navegadores IE8 e de geração mais velha (ES3), existem inúmeros grandes clientes/utilizadores que continuam a exigir que as páginas "funcionem" e, como notado, podem ou não controlar que navegador que os seus utilizadores finais escolhem utilizar.

Isto NÃO significa que apenas suportaremos o conjunto comum mais baixo de funcionalidades, apenas que precisamos de manter a compatibilidade do código ES3 e ao adicionar novas funcionalidades, elas terão de ser adicionadas de uma forma que não quebraria a análise do ES3 JavaScript e adicionada como uma funcionalidade opcional.

[Consulte o GitHub para obter todos os detalhes sobre o suporte do IE8](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>SDK de código aberto

O App Insights JavaScript SDK é de código aberto para visualizar o código fonte ou para contribuir para o projeto visitar o [repositório oficial](https://github.com/Microsoft/ApplicationInsights-JS)do GitHub . 

Para obter as últimas atualizações e correções de erro [consulte as notas de lançamento](./release-notes.md).

## <a name="next-steps"></a><a name="next"></a> Próximos passos
* [Controlar a utilização](usage-overview.md)
* [Métricas e eventos personalizados](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
* [Falha de carga SDK de resolução de problemas](javascript-sdk-load-failure.md)
