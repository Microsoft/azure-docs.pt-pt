---
title: Mapa de candidaturas em Insights de Aplicação Azure  Microsoft Docs
description: Monitorize topoologias de aplicações complexas com o mapa de aplicações
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: cc7d58f0557e620c273043af8ffb2e8ec4d8ec87
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485153"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa de aplicações: Aplicações distribuídas da triagem

O Mapa de Aplicações ajuda-o a detetar estrangulamentos de desempenho ou pontos de acesso em todos os componentes da sua aplicação distribuída. Cada nó no mapa representa um componente de aplicação ou as suas dependências; e tem KPI de saúde e alerta estado. Pode clicar em qualquer componente para diagnósticos mais detalhados, como eventos de Insights de Aplicação. Se a sua aplicação utilizar os serviços Azure, também pode clicar em diagnósticos Azure, como recomendações do SQL Database Advisor.

## <a name="what-is-a-component"></a>O que é um Componente?

Os componentes são partes desdobráveis independentemente da sua aplicação distribuída/microserviços. Os desenvolvedores e equipas de operações têm visibilidade de nível de código ou acesso à telemetria gerado por estes componentes de aplicação. 

* Os componentes são diferentes das dependências externas "observadas", tais como SQL, EventHub, etc. a que a sua equipa/organização pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de servidor/role/contentor.
* Os componentes podem ser teclas de instrumentação de informação de aplicação separadas (mesmo que as subscrições sejam diferentes) ou funções diferentes que reportem a uma única chave de instrumentação de Insights de Aplicação. A experiência do mapa de pré-visualização mostra os componentes independentemente da forma como são configurados.

## <a name="composite-application-map"></a>Mapa de aplicações compostas

Pode ver a topologia completa da aplicação em vários níveis de componentes de aplicação relacionados. Os componentes podem ser diferentes recursos de Insights de Aplicação, ou diferentes funções num único recurso. O mapa da aplicação encontra componentes seguindo as chamadas de dependência http feitas entre servidores com o SDK de Aplicação Insights instalado. 

Esta experiência começa com a descoberta progressiva dos componentes. Quando carrega o mapa de aplicação pela primeira vez, é desencadeado um conjunto de consultas para descobrir os componentes relacionados com este componente. Um botão no canto superior esquerdo irá atualizar-se com o número de componentes da sua aplicação à medida que forem descobertos. 

Ao clicar em "Atualizar componentes de mapas", o mapa é atualizado com todos os componentes descobertos até esse ponto. Dependendo da complexidade da sua aplicação, este pode demorar um minuto a carregar.

Se todos os componentes forem funções dentro de um único recurso de Application Insights, então este passo de descoberta não é necessário. A carga inicial para tal aplicação terá todos os seus componentes.

![Screenshot do mapa da aplicação](media/app-map/app-map-001.png)

Um dos principais objetivos com esta experiência é ser capaz de visualizar topologas complexas com centenas de componentes.

Clique em qualquer componente para ver insights relacionados e ir à experiência de triagem de desempenho e falha para esse componente.

![Flyout](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Investigar falhas

**Selecione investigar falhas** no lançamento do painel de falhas.

![Screenshot do botão de falhas de investigação](media/app-map/investigate-failures.png)

![Screenshot da experiência de falhas](media/app-map/failures.png)

### <a name="investigate-performance"></a>Investigar desempenho

Para resolver problemas de desempenho, selecione investigar o **desempenho.**

![Screenshot do botão de desempenho investigar](media/app-map/investigate-performance.png)

![Screenshot da experiência de desempenho](media/app-map/performance.png)

### <a name="go-to-details"></a>Vá aos detalhes

Selecione **ir a detalhes** para explorar a experiência de transação de ponta a ponta, que pode oferecer vistas para baixo para o nível de pilha de chamadas.

![Screenshot do botão ir-a-detalhes](media/app-map/go-to-details.png)

![Screenshot dos detalhes da transação de ponta a ponta](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Ver Registos (Analytics)

Para consultar e investigar mais aprofundadamente os dados das suas aplicações, clique **na visualização em Registos (Analytics)** .

![Screenshot da vista no botão de análise](media/app-map/view-logs.png)

![Screenshot da experiência de análise. Gráfico de linha resumindo a duração média da resposta de um pedido nas últimas 12 horas.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Alertas

Para ver alertas ativos e as regras subjacentes que fazem com que os alertas sejam desencadeados, selecione **alertas**.

![Screenshot do botão alertas](media/app-map/alerts.png)

![Screenshot da experiência de análise](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Definir nome de papel de nuvem

O Mapa de Aplicações utiliza a propriedade do nome da **nuvem** para identificar os componentes no mapa. O Application Insights SDK adiciona automaticamente a propriedade de nome de papel em nuvem à telemetria emitida por componentes. Por exemplo, o SDK adicionará um nome de site ou nome de serviço à propriedade de nome de papel na nuvem. No entanto, existem casos em que pode querer anular o valor predefinido. Para anular o nome do papel da nuvem e alterar o que é exibido no Mapa de Aplicações:

### <a name="netnet-core"></a>Núcleo .NET/.NET

**Escreva TelemettryInitializer personalizado como abaixo.**

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

**ASP.NET aplicações: Carregar inicializador para a Configuração Telemetria ativa**

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

Um método alternativo para ASP.NET aplicações Web é instantaneamente o inicializador em código, por exemplo, em Global.aspx.cs:

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
> Adicionar inicializador utilizando `ApplicationInsights.config` ou utilizar `TelemetryConfiguration.Active` não é válido para aplicações ASP.NET Core. 

**ASP.NET Aplicações Core: Carregar inicializador para a Configuração telemetria**

Para ASP.NET aplicações [Core,](asp-net-core.md#adding-telemetryinitializers) a adição de um novo `TelemetryInitializer` é feita adicionando-a ao recipiente de Injeção de Dependência, como mostrado abaixo. Isto é feito em `ConfigureServices` método da sua aula de `Startup.cs`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Método alternativo para Nó.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Começando com insights de aplicação Java SDK 2.5.0, pode especificar o nome da função cloud adicionando `<RoleName>` ao seu ficheiro `ApplicationInsights.xml`, por exemplo.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Se utilizar a Bota de Mola com o arranque da Bota de Mola Insights de Aplicação, a única alteração necessária é definir o seu nome personalizado para a aplicação no ficheiro application.properties.

`spring.application.name=<name-of-app>`

O arranque da Bota de primavera atribuirá automaticamente o nome do papel da nuvem ao valor que introduzir para a propriedade spring.application.name.

### <a name="clientbrowser-side-javascript"></a>JavaScript lado cliente/navegador

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Compreender o nome do papel da nuvem no contexto do Mapa de Aplicações

Quanto à forma de pensar sobre o nome do **papel na nuvem,** pode ser útil olhar para um Mapa de Aplicação que tem vários nomes de papéis em nuvem presentes:

![Screenshot do mapa da aplicação](media/app-map/cloud-rolename.png)

No Mapa de Aplicações acima de cada um dos nomes em caixas verdes estão valores de nome em nuvem para diferentes aspetos desta aplicação distribuída em particular. Assim, para esta aplicação as suas funções consistem em: `Authentication`, `acmefrontend`, `Inventory Management`, um `Payment Processing Worker Role`. 

No caso desta aplicação, cada um desses nomes de papéis na nuvem também representa um recurso exclusivo de Application Insights com as suas próprias chaves de instrumentação. Uma vez que o proprietário desta aplicação tem acesso a cada um desses quatro recursos de Aplicação Insights, o Mapa de Aplicações é capaz de coser um mapa das relações subjacentes.

Para as [definições oficiais:](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternativamente, a instância de **papel na nuvem** pode ser útil para cenários onde o nome de papel na **nuvem** diz que o problema está algures na sua frente frontal web, mas você pode estar executando a sua frente web através de vários servidores equilibrados em carga, por isso ser capaz de perfurar em uma camada mais profunda através de consultas Kusto e saber se o problema está afetando todos os servidores/instâncias front-end web ou apenas um pode ser extremamente importante.

Um cenário em que você possa querer sobrepor o valor para a instância de papel na nuvem pode ser se a sua aplicação estiver funcionando em um ambiente contentorizado onde apenas saber que o servidor individual pode não ser informação suficiente para localizar um determinado problema.

Para obter mais informações sobre como sobrepor a propriedade de nome de papel em nuvem com iniciais de telemetria, consulte [Adicionar propriedades: ITelemettryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Resolução de problemas

Se está com dificuldades em pôr o Mapa de Aplicações a funcionar como esperado, experimente estes passos:

### <a name="general"></a>Geral

1. Confirme que está a utilizar um SDK suportado oficialmente. Os SDKs não suportados/da comunidade poderão não suportar a correlação.

    Consulte este [artigo](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) para uma lista de SDKs suportados.

2. Atualize todos os componentes para a versão Mais recente do SDK.

3. Se estiver a utilizar funções C#Azure com , atualize para [funções V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Confirme que o nome da [função cloud](#set-cloud-role-name) está corretamente configurado.

5. Se tiver uma dependência em falta, certifique-se de que a mesma está na lista de [dependências recolhidas automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se não, pode acompanhá-la manualmente com uma [chamada de dependência de acompanhamento](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Muitos nódosos no mapa

O Mapa de Aplicações constrói um nó de aplicação para cada nome de papel de nuvem único presente na sua telemetria de pedido e um nó de dependência para cada combinação única de tipo, alvo e nome de papel em nuvem na sua telemetria de dependência. Se houver mais de 10.000 nós na sua telemetria, o Mapa de Aplicações não será capaz de pegar todos os nós e links, por isso o seu mapa estará incompleto. Se isso acontecer, aparecerá uma mensagem de aviso ao visualizar o mapa.

Além disso, o Mapa de Aplicações apenas suporta até 1000 nós separados não agrupados prestados ao mesmo tempo. O Mapa de Aplicações reduz a complexidade visual ao agrupar dependências que têm o mesmo tipo e chamadas, mas se a sua telemetria tiver demasiados nomes de papéis de nuvem únicos ou demasiados tipos de dependência, esse agrupamento será insuficiente, e o mapa será incapaz de render.

Para corrigir isto, terá de alterar a sua instrumentação para definir corretamente o nome da nuvem, o tipo de dependência e os campos-alvo da dependência.

* O alvo da dependência deve representar o nome lógico de uma dependência. Em muitos casos, é equivalente ao nome do servidor ou recurso da dependência. Por exemplo, no caso das dependências http é definido para o nome de anfitrião. Não deve conter iDs ou parâmetros únicos que mudam de um pedido para outro.

* O tipo de dependência deve representar o tipo lógico de dependência. Por exemplo, HTTP, SQL ou Azure Blob são tipos típicos de dependência. Não deve conter identificações únicas.

* O objetivo do nome da nuvem é descrito na [secção acima](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Feedback do portal

Para fornecer feedback, utilize a opção de feedback.

![Imagem MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como funciona a correlação em Application Insights consulte o artigo de correlação de [telemetria.](correlation.md)
* A experiência de diagnóstico de [transações](transaction-diagnostics.md) de ponta a ponta correlaciona a telemetria do lado do servidor de todos os seus componentes monitorizados application Insights numa única vista.
* Para cenários avançados de correlação em ASP.NET Core e ASP.NET consultar o artigo de [operações personalizadas](custom-operations-tracking.md) da pista.
