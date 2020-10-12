---
title: Mapa de aplicações em Insights de Aplicação Azure / Microsoft Docs
description: Monitorize topologias complexas de aplicações com o mapa de aplicações
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: 642a382f2ec5da7821c9d709bd27d592ced31c8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90974031"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa de aplicações: Aplicações distribuídas por triagem

O Mapa de Aplicações ajuda-o a detetar estrangulamentos de desempenho ou falhas em hotspots em todos os componentes da aplicação distribuída. Cada nó no mapa representa um componente de aplicação ou as suas dependências; e tem KPI de saúde e estado de alerta. Pode clicar em qualquer componente para diagnósticos mais detalhados, como eventos Application Insights. Se a sua aplicação utilizar serviços Azure, também pode clicar nos diagnósticos Azure, como recomendações do SQL Database Advisor.

## <a name="what-is-a-component"></a>O que é um Componente?

Os componentes são peças desdobráveis independentemente da sua aplicação distribuída/microserviços. Os desenvolvedores e equipas de operações têm visibilidade de nível de código ou acesso à telemetria gerada por estes componentes de aplicação. 

* Os componentes são diferentes das dependências externas "observadas", tais como SQL, EventHub, etc. às quais a sua equipa/organização pode não ter acesso (código ou telemetria).
* Os componentes funcionam em qualquer número de instâncias do servidor/função/contentor.
* Os componentes podem ser teclas de instrumentação de Insights de Aplicação separadas (mesmo que as subscrições sejam diferentes) ou diferentes funções reportando a uma única chave de instrumentação application insights. A experiência do mapa de pré-visualização mostra os componentes independentemente da forma como são configurados.

## <a name="composite-application-map"></a>Mapa de aplicação compósita

Pode ver a topologia completa da aplicação em vários níveis de componentes de aplicação relacionados. Os componentes podem ser diferentes recursos de Insights de Aplicação, ou diferentes funções num único recurso. O mapa da aplicação encontra componentes seguindo chamadas de dependência HTTP feitas entre servidores com o SDK de Insights de Aplicação instalado. 

Esta experiência começa com a descoberta progressiva dos componentes. Quando carrega pela primeira vez o mapa de aplicações, é desencadeado um conjunto de consultas para descobrir os componentes relacionados com este componente. Um botão no canto superior esquerdo irá atualizar-se com o número de componentes da sua aplicação à medida que forem descobertos. 

Ao clicar em "Atualizar componentes do mapa", o mapa é atualizado com todos os componentes descobertos até esse ponto. Dependendo da complexidade da sua aplicação, isto pode demorar um minuto a carregar.

Se todos os componentes forem funções dentro de um único recurso Application Insights, então este passo de descoberta não é necessário. A carga inicial para tal aplicação terá todos os seus componentes.

![A screenshot mostra um exemplo de um mapa de aplicações.](media/app-map/app-map-001.png)

Um dos objetivos principais com esta experiência é ser capaz de visualizar topologias complexas com centenas de componentes.

Clique em qualquer componente para ver insights relacionados e vá para a experiência de triagem de desempenho e falha para esse componente.

![Flyout](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Investigar falhas

Selecione **investigar falhas** para lançar o painel de falhas.

![Screenshot do botão de falhas de investigação](media/app-map/investigate-failures.png)

![Screenshot da experiência de falhas](media/app-map/failures.png)

### <a name="investigate-performance"></a>Investigar desempenho

Para resolver problemas de desempenho, selecione **investigar o desempenho**.

![Screenshot do botão de desempenho de investigação](media/app-map/investigate-performance.png)

![Screenshot da experiência de desempenho](media/app-map/performance.png)

### <a name="go-to-details"></a>Ir para os detalhes

Selecione **ir a detalhes** para explorar a experiência de transação de ponta a ponta, que pode oferecer vistas até ao nível da pilha de chamadas.

![Screenshot do botão ir-aos-detalhes](media/app-map/go-to-details.png)

![Screenshot dos detalhes das transações de ponta a ponta](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Ver Registos (Analytics)

Para consultar e investigar mais os dados das suas aplicações, clique **em visualização em Logs (Analytics)**.

![Screenshot de vista no botão de análise](media/app-map/view-logs.png)

![Screenshot da experiência de análise. Gráfico de linha que resume a duração média de resposta de um pedido nas últimas 12 horas.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Alertas

Para visualizar alertas ativos e as regras subjacentes que fazem com que os alertas sejam acionados, selecione **alertas**.

![Screenshot do botão alertas](media/app-map/alerts.png)

![Screenshot da experiência de análise](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>Definir ou sobrepor nome de papel de nuvem

O Mapa de Aplicações utiliza a propriedade **do nome da função da nuvem** para identificar os componentes no mapa. Para definir ou substituir manualmente o nome da função da nuvem e alterar o que é apresentado no Mapa de Aplicações:

> [!NOTE]
> O Application Insights SDK ou Agente adiciona automaticamente a propriedade do nome da função em nuvem à telemetria emitida por componentes num ambiente de Serviço de Aplicações Azure.

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**Escreva telemetriaInitializador personalizado como abaixo.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET apps: Carregue o inicializador para a Configuração de Telemetria ativa**

Em ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Um método alternativo para ASP.NET aplicações Web é instantaneamente o inicializador em código, por exemplo em Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Adicionar inicializador usando `ApplicationInsights.config` ou usando `TelemetryConfiguration.Active` não é válido para aplicações core ASP.NET. 

**ASP.NET Aplicações Core: Inicializador de carga para a Configuração de Telemetria**

Para ASP.NET aplicações [Core,](asp-net-core.md#adding-telemetryinitializers) a adição de um novo `TelemetryInitializer` é feita adicionando-a ao recipiente de Injeção de Dependência, como mostrado abaixo. Isto é feito no `ConfigureServices` método da sua `Startup.cs` classe.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Agente java**

Para [o agente Java 3.0,](./java-in-process-agent.md) o nome da função em nuvem é definido da seguinte forma:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

Também pode definir o nome da função de nuvem usando a variável ```APPLICATIONINSIGHTS_ROLE_NAME``` ambiente.

**SDK Java**

Se estiver a utilizar o SDK, a começar pelo Application Insights Java SDK 2.5.0, pode especificar o nome da função da nuvem adicionando `<RoleName>` ao seu `ApplicationInsights.xml` ficheiro, por exemplo.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Se utilizar o Boot de primavera com o arranque do Boot De primavera do Application Insights, a única alteração necessária é definir o seu nome personalizado para a aplicação no ficheiro application.properties.

`spring.application.name=<name-of-app>`

O arranque do Boot de mola atribuirá automaticamente o nome da função de nuvem ao valor que introduz para a propriedade spring.application.name.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Método alternativo para Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Compreender o nome do papel na nuvem no contexto do Mapa de Aplicações

No que diz respeito a como pensar sobre **o nome do papel na nuvem,** pode ser útil olhar para um Mapa de Aplicações que tem vários nomes de papéis em nuvem presentes:

![Screenshot do mapa de aplicações](media/app-map/cloud-rolename.png)

No Mapa de Aplicações acima de cada um dos nomes em caixas verdes estão valores de nome de função em nuvem para diferentes aspetos desta aplicação distribuída em particular. Assim, para esta aplicação, as suas funções consistem em, `Authentication` `acmefrontend` , , a `Inventory Management` `Payment Processing Worker Role` . 

No caso desta aplicação, cada um desses nomes de papel em nuvem também representa um recurso exclusivo app Insights com as suas próprias teclas de instrumentação. Uma vez que o proprietário desta aplicação tem acesso a cada um desses quatro recursos de Insights de Aplicação, o Application Map é capaz de coser um mapa das relações subjacentes.

Para as [definições oficiais:](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternativamente, **a instância de papel** em nuvem pode ser útil para cenários onde o nome da **função da nuvem** diz que o problema está algures na sua frente web, mas você pode estar executando a sua web front-end através de vários servidores equilibrados de carga para que ser capaz de perfurar em uma camada mais profunda através de consultas kusto e saber se o problema está afetando todos os servidores/instâncias frontais web ou apenas um pode ser extremamente importante.

Um cenário em que poderá querer sobrepor-se ao valor para a aplicação em nuvem pode ser se a sua aplicação estiver a funcionar num ambiente contentorizado onde apenas conhecer o servidor individual pode não ser informação suficiente para localizar um determinado problema.

Para obter mais informações sobre como sobrepor a propriedade do nome de papel em nuvem com inicializadores de telemetria, consulte [adicionar propriedades: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Resolução de problemas

Se está com dificuldades em fazer com que o Mapa da Aplicação funcione como esperado, experimente estes passos:

### <a name="general"></a>Geral

1. Confirme que está a utilizar um SDK suportado oficialmente. Os SDKs não suportados/da comunidade poderão não suportar a correlação.

    Veja uma lista dos SDKs suportados neste [artigo](./platforms.md).

2. Atualize todos os componentes para a versão SDK mais recente.

3. Se estiver a utilizar funções Azure com C#, atualize para [funções V2](../../azure-functions/functions-versions.md).

4. Confirme que [o nome da função da nuvem](#set-or-override-cloud-role-name) está corretamente configurado.

5. Se tiver uma dependência em falta, certifique-se de que a mesma está na lista de [dependências recolhidas automaticamente](./auto-collect-dependencies.md). Se não, pode acompanhá-la manualmente com uma [chamada de dependência de acompanhamento](./api-custom-events-metrics.md#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Demasiados nós no mapa

O Application Map constrói um nó de aplicação para cada nome de função de nuvem único presente na telemetria de pedido e um nó de dependência para cada combinação única de tipo, destino e nome de papel em nuvem na sua telemetria de dependência. Se houver mais de 10.000 nós na sua telemetria, o Mapa de Aplicações não será capaz de recolher todos os nós e links, pelo que o seu mapa estará incompleto. Se isto acontecer, aparecerá uma mensagem de aviso ao visualizar o mapa.

Além disso, o Mapa de Aplicações suporta apenas até 1000 nós não agrupados separados, renderizados de uma só vez. O Application Map reduz a complexidade visual através do agrupamento de dependências que têm o mesmo tipo e chamadas, mas se a sua telemetria tiver demasiados nomes de papel na nuvem ou demasiados tipos de dependência, esse agrupamento será insuficiente e o mapa será incapaz de renderizar.

Para corrigir isto, terá de alterar a sua instrumentação para definir corretamente o nome da função de nuvem, o tipo de dependência e os campos-alvo de dependência.

* O alvo da dependência deve representar o nome lógico de uma dependência. Em muitos casos, é equivalente ao nome do servidor ou recurso da dependência. Por exemplo, no caso das dependências HTTP é definido para o nome de hospedeiro. Não deve conter IDs ou parâmetros únicos que mudam de um pedido para outro.

* O tipo de dependência deve representar o tipo lógico de dependência. Por exemplo, HTTP, SQL ou Azure Blob são tipos típicos de dependência. Não deve conter identificações únicas.

* O objetivo do nome da função em nuvem é descrito na [secção acima](#set-or-override-cloud-role-name).

## <a name="portal-feedback"></a>Feedback do portal

Para fornecer feedback, utilize a opção de feedback.

![Imagem mapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como funciona a correlação no Application Insights consulte o [artigo de correlação de telemetria](correlation.md).
* A [experiência de diagnóstico de transações de ponta a ponta](transaction-diagnostics.md) correlaciona a telemetria do lado do servidor de todos os seus componentes monitores application insights numa única visão.
* Para cenários de correlação avançada em ASP.NET Core e ASP.NET consulte o artigo [de operações personalizadas](custom-operations-tracking.md) de faixa.