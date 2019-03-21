---
title: Monitorizar serviços Node.js com o Azure Application Insights | Microsoft Docs
description: Monitorize o desempenho e diagnostique problemas em serviços Node.js com o Application Insights.
services: application-insights
documentationcenter: nodejs
author: mrbullwinkle
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 13379111706eaa816a8fa16cfe72711b7bf4d739
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013290"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Monitorizar os seus serviços e aplicações Node.js com o Application Insights

O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitoriza os seus serviços e componentes de back-end após a implementação para ajudar a [detetar e diagnosticar rapidamente problemas de desempenho, entre outros](../../azure-monitor/app/detect-triage-diagnose.md). Pode utilizar o Application Insights para os serviços Node.js que estão alojados no seu datacenter, em VMs do Azure, em aplicações Web e inclusivamente noutras clouds privadas.

Para receber, armazenar e explorar os seus dados de monitorização, inclua o SDK no seu código e configure um recurso do Application Insights correspondente no Azure. O SDK envia dados para esse recurso para análise e exploração adicionais.

O SDK para Node.js pode monitorizar automaticamente pedidos HTTP de entrada e saída, exceções e algumas métricas do sistema. A partir da versão 0.20, o SDK também pode monitorizar alguns pacotes de terceiros comuns, como MongoDB, MySQL e Redis. Todos os eventos relacionados com pedidos HTTP de entrada são correlacionados para resolução de problemas mais rápida.

Pode utilizar a API TelemetryClient para instrumentalizar e monitorizar manualmente aspetos adicionais da sua aplicação e do seu sistema. A API TelemetryClient está descrita em mais detalhe posteriormente neste artigo.

## <a name="get-started"></a>Introdução

Conclua as tarefas seguintes para configurar a monitorização para uma aplicação ou serviço.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que tem uma subscrição do Azure ou [obtenha uma nova gratuitamente][azure-free-offer]. Se a sua organização já tiver uma subscrição do Azure, um administrador pode seguir [estas instruções][add-aad-user] para o adicionar à mesma.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a> Configurar um recurso do Application Insights


1. Inicie sessão no [Portal do Azure][portal].
2. Selecione **Criar um recurso** > **Ferramentas de programador** > **Application Insights**. O recurso inclui um ponto final para receber dados de telemetria, armazenamento para esses dados, relatórios guardados e configuração de dashboards, regras e alertas, entre outras coisas.

3. Na página de criação do recurso, na caixa **Tipo de Aplicação**, selecione **Aplicação Node.js**. O tipo de aplicação determina os dashboards e relatórios predefinidos que são criados por si. (Os recursos do Application Insights podem recolher dados de qualquer linguagem e plataforma.)

### <a name="sdk"></a> Configurar o SDK para Node.js

Inclua o SDK na sua aplicação, para que esta possa recolher dados. 

1. Copie a Chave de Instrumentação do recurso (também designada como *ikey*) a partir do portal do Azure. O Application Insights utiliza a ikey para mapear dados para o seu recurso do Azure. Antes de o SDK pode utilizar a ikey, tem de especificar a ikey numa variável de ambiente ou no seu código.  

   ![Copiar a chave de instrumentação](./media/nodejs/instrumentation-key-001.png)

2. Adicione a biblioteca do SDK para Node.js às dependências da aplicação, através de package.json. Na pasta raiz da aplicação, execute:

   ```bash
   npm install applicationinsights --save
   ```

3. Carregue explicitamente a biblioteca para o seu código. Uma vez que o SDK injeta instrumentação em muitas outras bibliotecas, carregue a biblioteca o mais cedo possível, até antes de outras declarações `require`. 

   Na parte superior do seu primeiro ficheiro .js, adicione o código abaixo. O método `setup` configura a ikey (e, por conseguinte, o recurso do Azure) que vai ser utilizada por predefinição para todos os itens monitorizados.

   ```javascript
   const appInsights = require("applicationinsights");
   appInsights.setup("<instrumentation_key>");
   appInsights.start();
   ```
   
   Também pode indicar uma ikey através da variável de ambiente APPINSIGHTS\_INSTRUMENTATIONKEY, em vez de a transmitir manualmente para `setup()` ou `new appInsights.TelemetryClient()`. Esta prática permite-lhe manter as ikeys fora do código de origem consolidado e especificar outras ikeys para diferentes ambientes.

   Para opções de configuração adicionais, veja as secções seguintes.

   Pode definir `appInsights.defaultClient.config.disableAppInsights = true` para experimentar o SDK sem enviar telemetria.

### <a name="monitor"></a> Monitorizar a sua aplicação

O SDK recolhe automaticamente telemetria sobre o runtime de Node.js e sobre alguns módulos de terceiros comuns. Utilize a sua aplicação para gerar alguns destes dados.

Em seguida, no [portal do Azure][portal], aceda ao recurso do Application Insights que criou anteriormente. Na **Linha cronológica geral**, procure os seus primeiros pontos de dados. Para ver dados mais detalhadas, selecione diferentes componentes nos gráficos.

Para ver a topologia que é detetada para a sua aplicação, selecione o botão **Mapa da aplicação**. Selecione os componentes no mapa para ver mais detalhes.

![Mapa de aplicação individual](./media/nodejs/application-map-002.png)

Para saber mais sobre a sua aplicação e resolver problemas, selecione as restantes vistas disponíveis na secção **INVESTIGAR**.

![Secção Investigar](./media/nodejs/007-investigate-pane.png)

#### <a name="no-data"></a>Não existem dados?

Visto que o SDK põe os dados em lotes para a submissão, poderá haver um atraso antes de os itens aparecerem no portal. Se não vir dados no seu recurso, experimente algumas das correções seguintes:

* Continue a utilizar a aplicação. Realize mais ações para gerar mais telemetria.
* Clique em **Atualizar** na vista de recursos do portal. Os gráficos atualizam-se periodicamente por si próprios, mas atualizá-los manualmente força-os a fazer a atualização de imediato.
* Confirme que as [portas de saída necessárias](../../azure-monitor/app/ip-addresses.md) estão abertas.
* Utilize a [Pesquisa](../../azure-monitor/app/diagnostic-search.md) para procurar eventos específicos.
* Veja as [FAQ][FAQ].


## <a name="sdk-configuration"></a>Configuração do SDK

Os métodos de configuração do SDK e os valores predefinidos estão listados no código de exemplo seguinte.

Para correlacionar totalmente os eventos num serviço, confirme que define `.setAutoDependencyCorrelation(true)`. Com esta opção definida, o SDK pode monitorizar o contexto em chamadas de retorno assíncronas em Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>API TelemetryClient

Para obter uma descrição completa da API TelemetryClient, veja [Application Insights API for custom events and metrics](../../azure-monitor/app/api-custom-events-metrics.md) (API do Application Insights para eventos e métricas personalizadas).

Pode controlar qualquer pedido, evento, métrica ou exceção com o SDK para Node.js do Application Insights. O exemplo de código seguinte demonstra algumas das APIs que pode utilizar:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
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
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Adicionar propriedades personalizadas a todos os eventos

Utilize o código abaixo para adicionar uma propriedade personalizada a todos os eventos:

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Monitorizar pedidos HTTP GET

Utilize o código abaixo para fazer o acompanhamento dos pedidos GET HTTP:

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
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

## <a name="next-steps"></a>Passos Seguintes

* [Monitorizar a telemetria no portal](../../azure-monitor/app/app-insights-dashboards.md)
* [Escrever consultas de análise sobre a telemetria](../../azure-monitor/log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../../azure-monitor/app/troubleshoot-faq.md

