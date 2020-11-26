---
title: Monitorizar serviços Node.js com o Azure Application Insights | Microsoft Docs
description: Monitorize o desempenho e diagnostique problemas em serviços Node.js com o Application Insights.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 4c350cbfdf92d19a084940941351cf1f028c93d2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186284"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Monitorizar os seus serviços e aplicações Node.js com o Application Insights

[O Application Insights](./app-insights-overview.md) monitoriza os seus serviços e componentes de backend após a implementação, para ajudá-lo a descobrir e diagnosticar rapidamente o desempenho e outros problemas. Pode utilizar o Application Insights para Node.js serviços que estão hospedados no seu datacenter, VMs Azure e aplicações web, e até mesmo em outras nuvens públicas.

Para receber, armazenar e explorar os seus dados de monitorização, inclua o SDK no seu código e configure um recurso do Application Insights correspondente no Azure. O SDK envia dados para esse recurso para análise e exploração adicionais.

O SDK para Node.js pode monitorizar automaticamente pedidos HTTP de entrada e saída, exceções e algumas métricas do sistema. A partir da versão 0.20, o SDK também pode monitorizar [alguns pacotes comuns de terceiros](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules), como MongoDB, MySQL e Redis. Todos os eventos relacionados com pedidos HTTP de entrada são correlacionados para resolução de problemas mais rápida.

Pode utilizar a API TelemetryClient para instrumentalizar e monitorizar manualmente aspetos adicionais da sua aplicação e do seu sistema. A API TelemetryClient está descrita em mais detalhe posteriormente neste artigo.

## <a name="get-started"></a>Introdução

Conclua as tarefas seguintes para configurar a monitorização para uma aplicação ou serviço.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que tem uma subscrição do Azure ou [obtenha uma nova gratuitamente][azure-free-offer]. Se a sua organização já tiver uma subscrição do Azure, um administrador pode seguir [estas instruções][add-aad-user] para o adicionar à mesma.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Configurar um recurso do Application Insights

1. Inicie sessão no [portal do Azure][portal].
2. [Criar um recurso do Application Insights](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> Configurar o SDK para Node.js

Inclua o SDK na sua aplicação, para que esta possa recolher dados.

1. Copie a chave de instrumentação do seu recurso (também chamada *de ikey)* a partir do seu recurso recém-criado. O Application Insights utiliza a ikey para mapear dados para o seu recurso do Azure. Antes de o SDK pode utilizar a ikey, tem de especificar a ikey numa variável de ambiente ou no seu código.  

   ![Copiar a chave de instrumentação](./media/nodejs/instrumentation-key-001.png)

2. Adicione a biblioteca do SDK para Node.js às dependências da aplicação, através de package.json. Na pasta raiz da aplicação, execute:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > Se estiver a utilizar o TypeScript, não instale pacotes separados de "dactilografias". Este pacote NPM contém digitações internas.

3. Carregue explicitamente a biblioteca para o seu código. Uma vez que o SDK injeta instrumentação em muitas outras bibliotecas, carregue a biblioteca o mais cedo possível, até antes de outras declarações `require`.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  Também pode fornecer um ikey através da variável `APPINSIGHTS_INSTRUMENTATIONKEY` ambiental, em vez de passá-lo manualmente para  `setup()` ou `new appInsights.TelemetryClient()` . Esta prática permite-lhe manter as ikeys fora do código de origem consolidado e especificar outras ikeys para diferentes ambientes. Para configurar manualmente, `appInsights.setup('[your ikey]');` ligue.

    Para opções de configuração adicionais, veja as secções seguintes.

    Pode definir `appInsights.defaultClient.config.disableAppInsights = true` para experimentar o SDK sem enviar telemetria.

5. Comece a recolher e enviar automaticamente dados `appInsights.start();` ligando.

### <a name="monitor-your-app"></a><a name="monitor"></a> Monitorize a sua aplicação

O SDK reúne automaticamente telemetria sobre o tempo de execução Node.js e alguns módulos de terceiros comuns. Utilize a sua aplicação para gerar alguns destes dados.

Em seguida, no [portal do Azure][portal], aceda ao recurso do Application Insights que criou anteriormente. Na **Linha cronológica geral**, procure os seus primeiros pontos de dados. Para ver dados mais detalhadas, selecione diferentes componentes nos gráficos.

Para ver a topologia que é descoberta para a sua aplicação, pode utilizar [o mapa da Aplicação.](app-map.md)

#### <a name="no-data"></a>Sem dados

Visto que o SDK põe os dados em lotes para a submissão, poderá haver um atraso antes de os itens aparecerem no portal. Se não vir dados no seu recurso, experimente algumas das correções seguintes:

* Continue a utilizar a aplicação. Realize mais ações para gerar mais telemetria.
* Clique em **Atualizar** na vista de recursos do portal. Os gráficos atualizam-se periodicamente por si próprios, mas atualizá-los manualmente força-os a fazer a atualização de imediato.
* Confirme que as [portas de saída necessárias](./ip-addresses.md) estão abertas.
* Utilize a [Pesquisa](./diagnostic-search.md) para procurar eventos específicos.
* Verifique as [FAQ.][FAQ]

## <a name="basic-usage"></a>Utilização básica

Para a recolha fora da caixa de pedidos HTTP, eventos populares de biblioteca de terceiros, exceções não manipuladas e métricas do sistema:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Se a chave de instrumentação for definida na variável `APPINSIGHTS_INSTRUMENTATIONKEY` ambiente, `.setup()` pode ser chamada sem argumentos. Isto facilita a utilização de ikeys diferentes para diferentes ambientes.

Carregue a biblioteca Application Insights , `require("applicationinsights")` o mais cedo possível nos seus scripts antes de carregar outros pacotes. Isto é necessário para que a biblioteca Application Insights possa preparar pacotes posteriores para rastreio. Se encontrar conflitos com outras bibliotecas que façam uma preparação semelhante, tente carregar a biblioteca Application Insights depois delas.

Devido à forma como o JavaScript lida com as chamadas, é necessário um trabalho adicional para rastrear um pedido através de dependências externas e posteriores chamadas. Por predefinição, este rastreio adicional está ativado; desativá-lo, chamando `setAutoDependencyCorrelation(false)` como descrito na secção de [configuração](#sdk-configuration) abaixo.

## <a name="migrating-from-versions-prior-to-022"></a>Migrar de versões antes de 0.22

Há alterações entre os lançamentos antes da versão 0.22 e depois. Estas alterações destinam-se a trazer consistência com outros SDKs application insights e permitir a extensibilidade futura.

Em geral, pode migrar com o seguinte:

- Substitua as referências a `appInsights.client` `appInsights.defaultClient` .
- Substituir referências `appInsights.getClient()` a `new appInsights.TelemetryClient()`
- Substitua todos os argumentos para os métodos client.track* por um único objeto que contenha propriedades nomeadas como argumentos. Consulte as indicações do tipo incorporado do IDE ou os Tipos de [Telemetria](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) para o objeto com exceção para cada tipo de telemetria.

Se aceder às funções de configuração SDK sem acorrentá-las, `appInsights.setup()` pode agora encontrar estas funções em `appInsights.Configurations` (por exemplo, `appInsights.Configuration.setAutoCollectDependencies(true)` ). Reveja as alterações à configuração predefinida na secção seguinte.

## <a name="sdk-configuration"></a>Configuração do SDK

O `appInsights` objeto fornece uma série de métodos de configuração. Estão listados no seguinte corte com os seus valores predefinidos.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Para correlacionar totalmente os eventos num serviço, confirme que define `.setAutoDependencyCorrelation(true)`. Com esta opção definida, o SDK pode monitorizar o contexto em chamadas de retorno assíncronas em Node.js.

Reveja as suas descrições no tipo incorporado do seu IDE, ou [applicationinsights.ts](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) para obter informações detalhadas sobre o que estes controlam e argumentos secundários opcionais.

> [!NOTE]
>  Por `setAutoCollectConsole` predefinição está configurado para *excluir* chamadas para `console.log` (e outros métodos de consola). Apenas serão recolhidas chamadas para madeireiros de terceiros apoiados (por exemplo, winston e bunyan). Pode alterar este comportamento para incluir chamadas para `console` métodos utilizando `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>Amostragem

Por predefinição, o SDK enviará todos os dados recolhidos para o serviço Desemis. Se recolher muitos dados, é melhor permitir que a amostragem reduza a quantidade de dados enviados. Coloque o `samplingPercentage` campo no objeto de um cliente para realizar `config` isto. A definição `samplingPercentage` para 100 (o padrão) significa que todos os dados serão enviados e 0 significa que nada será enviado.

Se estiver a utilizar a correlação automática, todos os dados associados a um único pedido serão incluídos ou excluídos como unidade.

Adicione código, como o seguinte, para permitir a amostragem:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Múltiplas funções para aplicações multicomponentes

Se a sua aplicação for constituída por múltiplos componentes que deseja instrumentar todos com a mesma chave de instrumentação e ainda vir estes componentes como unidades separadas no portal, como se estivessem a usar teclas de instrumentação separadas (por exemplo, como nós separados no Mapa de Aplicações), poderá ser necessário configurar manualmente o campo RoleName para distinguir a telemetria de um componente de outros componentes que enviam para o seu recurso Application Insights.

Utilize o seguinte para definir o campo RoleName:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Instrumentação automática de terceiros

A fim de rastrear o contexto através de chamadas assíncronos, são necessárias algumas alterações em bibliotecas de terceiros, como o MongoDB e o Redis. Por padrão, a Application Insights usará [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) para remendar algumas destas bibliotecas. Isto pode ser desativado definindo a `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` variável ambiente.

> [!NOTE]
> Ao definir esta variável ambiental, os eventos podem deixar de estar corretamente associados à operação correta.

 As manchas individuais de macaco podem ser desativadas, definindo a `APPLICATION_INSIGHTS_NO_PATCH_MODULES` variável ambiente para uma lista separada de vírgulas de pacotes para desativar (por exemplo, `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` ) para evitar remendar as `console` embalagens e as `redis` embalagens.

Atualmente existem nove pacotes que são instrumentados: `bunyan` , , , , , , , , , `console` e `mongodb` `mongodb-core` `mysql` `redis` `winston` `pg` `pg-pool` . Visite a [README dos editores de canais de diagnóstico](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) para obter informações sobre a versão exata destes pacotes.

Os `bunyan` , e patches `winston` `console` gerarão insights de aplicação trace eventos baseados em se `setAutoCollectConsole` está ativado. O resto gerará eventos de dependência de Insights de Aplicação com base no facto `setAutoCollectDependencies` de estar ativado.

### <a name="live-metrics"></a>Live Metrics

Para ativar o envio de Métricas Ao Vivo da sua aplicação para a Azure, `setSendLiveMetrics(true)` utilize. A filtragem das métricas vivas no portal não é suportada atualmente.

### <a name="extended-metrics"></a>Métricas estendidas

> [!NOTE]
> A capacidade de enviar métricas nativas estendidas foi adicionada na versão 1.4.0

Para permitir o envio de métricas nativas estendidas da sua app para a Azure, instale o pacote de métricas nativas separadas. O SDK carregará automaticamente quando estiver instalado e começará a recolher Node.js métricas nativas.

```bash
npm install applicationinsights-native-metrics
```

Atualmente, o pacote de métricas nativas realiza autocolecção do tempo de cpu de recolha de lixo, tiques de loop de eventos e uso de pilhas:

- **Recolha de lixo**: A quantidade de tempo de CPU gasto em cada tipo de recolha de lixo, e quantas ocorrências de cada tipo.
- **Loop de evento**: Quantas carraças ocorreram e quanto tempo de CPU foi gasto no total.
- **Heap vs não-heap**: Quanto do uso da memória da sua aplicação está na pilha ou não-pilha.

### <a name="distributed-tracing-modes"></a>Modos de rastreio distribuídos

Por predefinição, o SDK enviará cabeçalhos compreendidos por outras aplicações/serviços instrumentados com um SDK de Insights de Aplicação. Pode opcionalmente ativar o envio/receção dos cabeçalhos [W3C Trace Context](https://github.com/w3c/trace-context) para além dos cabeçalhos de IA existentes, para que não quebre a correlação com nenhum dos seus serviços legados existentes. Permitir cabeçalhos W3C permitirá que a sua app se correlacione com outros serviços não instrumentados com a Application Insights, mas adote esta norma W3C.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>API TelemetryClient

Para obter uma descrição completa da API TelemetryClient, veja [Application Insights API for custom events and metrics](./api-custom-events-metrics.md) (API do Application Insights para eventos e métricas personalizadas).

Pode controlar qualquer pedido, evento, métrica ou exceção com o SDK para Node.js do Application Insights. O exemplo de código seguinte demonstra algumas das APIs que pode utilizar:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Monitorizar as dependências

Utilize o código abaixo para fazer o acompanhamento das suas dependências:

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

Um utilitário de exemplo que usa `trackMetric` para medir o tempo que o agendamento do ciclo de eventos demora:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Adicionar propriedades personalizadas a todos os eventos

Utilize o código abaixo para adicionar uma propriedade personalizada a todos os eventos:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Monitorizar pedidos HTTP GET

Utilize o seguinte código para rastrear manualmente pedidos HTTP GET:

> [!NOTE]
> Todos os pedidos são rastreados por defeito. Para desativar a recolha automática, ligue para .setAutoCollectRequests (falso) antes de iniciar a chamada().

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

Em alternativa, pode rastrear pedidos utilizando `trackNodeHttpRequest` o método:

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>Monitorizar o tempo de arranque do servidor

Utilize o código abaixo para fazer o acompanhamento do tempo de arranque do servidor:

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="preprocess-data-with-telemetry-processors"></a>Dados de pré-processamento com processadores de telemetria

Pode processar e filtrar os dados recolhidos antes de serem enviados para retenção utilizando *processadores de telemetria.* Os processadores de telemetria são chamados um a um na ordem que foram adicionados antes do item da telemetria ser enviado para a nuvem.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Se um processador de telemetria voltar falso, esse item de telemetria não será enviado.

Todos os processadores de telemetria recebem os dados da telemetria e o seu envelope para inspecionar e modificar. Também recebem um objeto de contexto. O conteúdo deste objeto é definido pelo parâmetro ao chamar um método de `contextObjects` pista para telemetria rastreada manualmente. Para telemetria recolhida automaticamente, este objeto é preenchido com informações de pedido disponíveis e o conteúdo de pedido persistente fornecido por `appInsights.getCorrelationContext()` (se a correlação de dependência automática estiver ativada).

O tipo TypeScript para um processador de telemetria é:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Por exemplo, um processador que remove pilhas de traços de dados de exceções pode ser escrito e adicionado da seguinte forma:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Use várias teclas de instrumentação

Pode criar vários recursos application insights e enviar diferentes dados para cada um, utilizando as respetivas teclas de instrumentação ("ikey").

 Por exemplo:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Opções de configuração avançadas

O objeto cliente contém uma `config` propriedade com muitas configurações opcionais para cenários avançados. Estes podem ser definidos da seguinte forma:

```javascript
client.config.PROPERTYNAME = VALUE;
```

Estas propriedades são específicas do cliente, para que possa configurar `appInsights.defaultClient` separadamente dos clientes criados com `new appInsights.TelemetryClient()` .

| Propriedade                        | Descrição                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentaçãoKey              | Um identificador para o seu recurso Application Insights.                                                      |
| endpointUrl                     | O ponto final de ingestão para enviar cargas de telemetria para.                                                      |
| QuickPulseHost                  | O anfitrião Live Metrics Stream para enviar telemetria ao vivo para.                                            |
| proxyHttpUrl                    | Um servidor proxy para tráfego SDK HTTP (Opcional, Padrão retirado da `http_proxy` variável ambiente).     |
| proxyHttpsUrl                   | Um servidor proxy para tráfego SDK HTTPS (Opcional, Padrão retirado da `https_proxy` variável ambiente).   |
| httpAgent                       | Um http. Agente a utilizar para tráfego SDK HTTP (Opcional, Padrão indefinido).                                   |
| httpsAgent                      | Um https. Agente a utilizar para o tráfego SDK HTTPS (Opcional, Padrão indefinido).                                 |
| maxBatchSize                    | O número máximo de itens de telemetria a incluir numa carga útil para o ponto final de ingestão `250` (Padrão).   |
| maxBatchIntervalMs              | O tempo máximo para esperar que uma carga útil atinja o máximo de Tamanho `15000` (Padrão).               |
| desativar As Inesceptas              | Uma bandeira que indique se a transmissão de telemetria é desativada (Predefinição). `false`                                 |
| amostragemPercentage              | A percentagem de itens de telemetria rastreados que devem ser transmitidos `100` (Padrão).                      |
| correlaidretryIntervalms    | O tempo para esperar antes de voltar a tentar recuperar o ID para a correlação entre componentes `30000` (Padrão).     |
| correlationHeaderExcludedDomains| Uma lista de domínios a excluir da injeção de cabeçalho de correlação de componentes [cruzados (Config.ts Config.ts).](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)|

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar a telemetria no portal](./overview-dashboard.md)
* [Escrever consultas de análise sobre a telemetria](../log-query/log-analytics-tutorial.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md