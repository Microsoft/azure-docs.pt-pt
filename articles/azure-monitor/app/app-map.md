---
title: Mapa de aplicativos no Aplicativo Azure insights | Microsoft Docs
description: Monitorar topologias de aplicativo complexas com o mapa do aplicativo
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 73cf6fd1c20f2e4208d1f7c28a756f28a2fad839
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302579"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa do aplicativo: Triagem de aplicativos distribuídos

O mapa do aplicativo ajuda a identificar gargalos de desempenho ou pontos de interativações de falha em todos os componentes do seu aplicativo distribuído. Cada nó no mapa representa um componente de aplicativo ou suas dependências; e tem KPI de integridade e status de alertas. Você pode clicar em qualquer componente para diagnóstico mais detalhado, como Application Insights eventos. Se seu aplicativo usa os serviços do Azure, você também pode clicar no diagnóstico do Azure, como Assistente do Banco de Dados SQL recomendações.

## <a name="what-is-a-component"></a>O que é um componente?

Os componentes são partes implantáveis independentemente do seu aplicativo distribuído/de microserviços. Os desenvolvedores e as equipes de operações têm visibilidade de nível de código ou acesso à telemetria gerada por esses componentes de aplicativos. 

* Os componentes são diferentes das dependências externas "observadas", como SQL, EventHub, etc. às quais sua equipe/organização pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de servidor/função/contêiner.
* Os componentes podem ser separados Application Insights chaves de instrumentação (mesmo se as assinaturas forem diferentes) ou diferentes funções subordinadas a uma única chave de instrumentação de Application Insights. A experiência do mapa de visualização mostra os componentes independentemente de como eles são configurados.

## <a name="composite-application-map"></a>Mapa do aplicativo composto

Você pode ver a topologia de aplicativo completa em vários níveis de componentes de aplicativos relacionados. Os componentes podem ser diferentes Application Insights recursos ou funções diferentes em um único recurso. O mapa do aplicativo localiza componentes seguindo as chamadas de dependência HTTP feitas entre servidores com o SDK do Application Insights instalado. 

Essa experiência começa com a descoberta progressiva dos componentes. Quando você carrega o mapa do aplicativo pela primeira vez, um conjunto de consultas é disparado para descobrir os componentes relacionados a esse componente. Um botão no canto superior esquerdo será atualizado com o número de componentes em seu aplicativo à medida que eles forem descobertos. 

Ao clicar em "atualizar componentes do mapa", o mapa é atualizado com todos os componentes descobertos até esse ponto. Dependendo da complexidade do seu aplicativo, isso pode levar um minuto para ser carregado.

Se todos os componentes forem funções dentro de um único recurso de Application Insights, essa etapa de descoberta não será necessária. A carga inicial para esse aplicativo terá todos os seus componentes.

![Captura de tela do mapa do aplicativo](media/app-map/app-map-001.png)

Um dos principais objetivos dessa experiência é poder visualizar topologias complexas com centenas de componentes.

Clique em qualquer componente para ver as informações relacionadas e vá para a experiência de triagem de desempenho e falha para esse componente.

![Menu](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Investigar falhas

Selecione **investigar falhas** para iniciar o painel falhas.

![Captura de tela do botão investigar falhas](media/app-map/investigate-failures.png)

![Captura de tela da experiência de falhas](media/app-map/failures.png)

### <a name="investigate-performance"></a>Investigar o desempenho

Para solucionar problemas de desempenho, selecione **investigar desempenho**.

![Captura de tela do botão investigar desempenho](media/app-map/investigate-performance.png)

![Captura de tela da experiência de desempenho](media/app-map/performance.png)

### <a name="go-to-details"></a>Ir para detalhes

Selecione **ir para detalhes** para explorar a experiência de transação de ponta a ponta, que pode oferecer exibições até o nível da pilha de chamadas.

![Captura de tela do botão ir para detalhes](media/app-map/go-to-details.png)

![Captura de tela de detalhes de transação de ponta a ponta](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Exibir no Analytics

Para consultar e investigar ainda mais os dados de aplicativos, clique em **Exibir na análise**.

![Captura de tela da exibição no botão de análise](media/app-map/view-in-analytics.png)

![Captura de tela da experiência de análise](media/app-map/analytics.png)

### <a name="alerts"></a>Alertas

Para exibir alertas ativos e as regras subjacentes que fazem com que os alertas sejam disparados, selecione **alertas**.

![Captura de tela do botão alertas](media/app-map/alerts.png)

![Captura de tela da experiência de análise](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Definir nome da função de nuvem

O mapa do aplicativo usa a propriedade **nome da função de nuvem** para identificar os componentes no mapa. O SDK do Application Insights adiciona automaticamente a propriedade nome da função de nuvem à telemetria emitida pelos componentes. Por exemplo, o SDK adicionará um nome de site ou nome de função de serviço à propriedade de nome da função de nuvem. No entanto, há casos em que você talvez queira substituir o valor padrão. Para substituir o nome da função de nuvem e alterar o que é exibido no mapa do aplicativo:

### <a name="netnet-core"></a>.NET/.NET Core

**Escreva personalizada telemetryinitializer personalizado como abaixo.**

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

**Aplicativos ASP.NET: Carregar inicializador para o TelemetryConfiguration ativo**

Em ApplicationInsights. config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Um método alternativo para aplicativos Web ASP.NET é instanciar o inicializador no código, por exemplo, em Global.aspx.cs:

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
> Adicionar inicializador `ApplicationInsights.config` usando ou `TelemetryConfiguration.Active` usando não é válido para aplicativos ASP.NET Core. 

**ASP.NET Core aplicativos: Carregar inicializador para o TelemetryConfiguration**

Para aplicativos [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) , adicionar um novo `TelemetryInitializer` é feito adicionando-o ao contêiner de injeção de dependência, conforme mostrado abaixo. Isso é feito no `ConfigureServices` método da sua `Startup.cs` classe.

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

### <a name="alternate-method-for-nodejs"></a>Método alternativo para node. js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Se você usar o Spring boot com o iniciador do Spring boot Application Insights, a única alteração necessária será definir seu nome personalizado para o aplicativo no arquivo Application. Properties.

`spring.application.name=<name-of-app>`

O iniciador do Spring boot atribuirá automaticamente o nome da função de nuvem ao valor inserido para a propriedade spring.application.name.

Para obter mais informações sobre a correlação de Java e como configurar o nome da função de nuvem para aplicativos não SpringBoot, faça check-in desta [seção](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) na correlação.

### <a name="clientbrowser-side-javascript"></a>JavaScript do lado do cliente/navegador

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Noções básicas sobre o nome da função de nuvem no contexto do mapa do aplicativo

No que diz respeito a como pensar sobre o **nome da função de nuvem**, pode ser útil examinar um mapa de aplicativo que tem vários nomes de função de nuvem presentes:

![Captura de tela do mapa do aplicativo](media/app-map/cloud-rolename.png)

No mapa do aplicativo acima, cada um dos nomes em caixas verdes são valores de nome de função de nuvem para diferentes aspectos desse aplicativo distribuído específico. Portanto, para esse aplicativo, suas funções consistem `acmefrontend`em `Inventory Management`: `Authentication`, `Payment Processing Worker Role`,, a. 

No caso desse aplicativo, cada um desses nomes de função de nuvem também representa um recurso de Application Insights exclusivo diferente com suas próprias chaves de instrumentação. Como o proprietário deste aplicativo tem acesso a cada um desses quatro recursos de Application Insights diferentes, o mapa de aplicativos é capaz de reunir um mapa das relações subjacentes.

Para as [definições oficiais](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Como alternativa, a **instância de função de nuvem** pode ser útil para cenários em que o nome da função de **nuvem** informa que o problema está em algum lugar no front-end da Web, mas você pode estar executando seu front-end da Web em vários servidores com balanceamento de carga, para que possa Aprofunde-se em uma camada mais profunda por meio de consultas de Kusto e saiba se o problema está afetando todos os servidores/instâncias de front-end da Web ou se apenas um pode ser extremamente importante.

Um cenário em que você talvez queira substituir o valor para a instância de função de nuvem poderia ser se seu aplicativo estiver sendo executado em um ambiente em contêineres, onde apenas saber se o servidor individual pode não ser informações suficientes para localizar um determinado problema.

Para obter mais informações sobre como substituir a propriedade de nome da função de nuvem por inicializadores [de telemetria, consulte Adicionar propriedades: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Resolução de problemas

Se você estiver tendo problemas para obter o mapa do aplicativo funcionar conforme o esperado, tente estas etapas:

### <a name="general"></a>Geral

1. Confirme que está a utilizar um SDK suportado oficialmente. Os SDKs não suportados/da comunidade poderão não suportar a correlação.

    Consulte este [artigo](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) para obter uma lista de SDKs com suporte.

2. Atualize todos os componentes para a versão mais recente do SDK.

3. Se você estiver usando Azure Functions com C#, atualize para o [Functions v2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Confirme se o [nome da função de nuvem](#set-cloud-role-name) está configurado corretamente.

5. Se tiver uma dependência em falta, certifique-se de que a mesma está na lista de [dependências recolhidas automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se não, pode acompanhá-la manualmente com uma [chamada de dependência de acompanhamento](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Muitos nós no mapa

O mapa de aplicativos constrói um nó de aplicativo para cada nome de função de nuvem exclusivo presente na telemetria de solicitação e um nó de dependência para cada combinação exclusiva de tipo, destino e nome da função de nuvem em sua telemetria de dependência. Se houver mais de 10.000 nós em sua telemetria, o mapa do aplicativo não poderá buscar todos os nós e links, de modo que o mapa estará incompleto. Se isso acontecer, uma mensagem de aviso será exibida ao exibir o mapa.

Além disso, o mapa de aplicativos dá suporte apenas a até 1000 nós desagrupados separados ao mesmo tempo. O mapa do aplicativo reduz a complexidade visual agrupando dependências que têm o mesmo tipo e chamadores, mas se a telemetria tiver muitos nomes de função de nuvem exclusivos ou um número excessivo de tipos de dependência, esse agrupamento será insuficiente e o mapa não poderá aplicar.

Para corrigir isso, você precisará alterar sua instrumentação para definir corretamente o nome da função de nuvem, o tipo de dependência e os campos de destino de dependência.

* O destino de dependência deve representar o nome lógico de uma dependência. Em muitos casos, é equivalente ao nome do servidor ou do recurso da dependência. Por exemplo, no caso de dependências HTTP, ele é definido como o nome do host. Ele não deve conter IDs ou parâmetros exclusivos que se alteram de uma solicitação para outra.

* O tipo de dependência deve representar o tipo lógico de uma dependência. Por exemplo, HTTP, SQL ou BLOB do Azure são tipos de dependência típicos. Ele não deve conter IDs exclusivas.

* A finalidade do nome da função de nuvem é descrita na [seção acima](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Comentários do portal

Para fornecer comentários, use a opção comentários.

![MapLink-1 imagem](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Passos seguintes

* [Entendendo a correlação](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
