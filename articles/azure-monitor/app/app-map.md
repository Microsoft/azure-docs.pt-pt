---
title: Mapa da aplicação no Application Insights do Azure | Documentos da Microsoft
description: Monitorizar topologias de aplicativo complexo com o mapa da aplicação
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 777bf38fbc8a90335af79bbd0fb2ce74310bd991
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006792"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa da aplicação: Faça a triagem de aplicações distribuídas

Mapa da aplicação ajuda-o a afunilamentos de desempenho spot ou hotspots de falha em todos os componentes da aplicação distribuída. Cada nó no mapa representa um componente da aplicação ou as respetivas dependências; e tem o estado de funcionamento KPI e estado de alerta. Pode clicar a partir de qualquer componente para diagnósticos mais detalhados, como eventos do Application Insights. Se a sua aplicação utilizar serviços do Azure, também pode clicar sucessivamente para diagnóstico do Azure, tais como as recomendações do Assistente de base de dados SQL.

## <a name="what-is-a-component"></a>O que é um componente?

Os componentes são partes implementáveis independentemente do aplicativo/microsserviços distribuídos. As equipes de operações e os desenvolvedores têm visibilidade de nível de código ou de acesso à telemetria gerado por estes componentes da aplicação. 

* Componentes são diferentes de "observadas" dependências externas, como SQL, etc. de EventHub que sua equipe/organização não pode ter acesso ao (código ou telemetria).
* Componentes são executados em qualquer número de instâncias de função/servidor/contentor.
* Componentes podem ser separadas chaves de instrumentação do Application Insights (mesmo que as subscrições são diferentes) ou de diferentes funções de relatório para uma única chave de instrumentação do Application Insights. A experiência de mapa de pré-visualização mostra os componentes independentemente da forma como eles são configurados.

## <a name="composite-application-map"></a>Mapa de aplicativo composto

Pode ver a topologia de completo da aplicação através de vários níveis dos componentes de aplicativos relacionados. Componentes podem ser diferentes recursos do Application Insights, ou funções diferentes num único recurso. O mapa da aplicação localiza os componentes seguintes chamadas de dependência HTTP feitas entre servidores com o Application Insights SDK instalado. 

Esta experiência é iniciado com a deteção de progressiva dos componentes. Quando carrega o mapa de aplicativo em primeiro lugar, um conjunto de consultas são acionados para detetar os componentes relacionados com este componente. Um botão no canto superior esquerdo será atualizado com o número de componentes na sua aplicação à medida que são detetados. 

Ao clicar em "Componentes de mapa de atualização", o mapa é atualizado com todos os componentes detetados até que ponto. Dependendo da complexidade da sua aplicação, isto pode demorar um minuto para carregar.

Se todos os componentes são funções dentro de um único recurso do Application Insights, em seguida, este passo de deteção não é necessário. O carregamento inicial para esse aplicativo terá todos os seus componentes.

![Captura de ecrã de mapa de aplicativo](media/app-map/app-map-001.png)

Um dos principais objetivos com esta experiência é conseguir visualizar topologias complexas com centenas de componentes.

Clique em qualquer componente para ver informações relacionadas e vá para o desempenho e a experiência de triagem de falha desse componente.

![Lista de opções](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Investigar falhas

Selecione **investigar falhas** para iniciar o painel de falhas.

![Captura de ecrã do botão de falhas de investigar](media/app-map/investigate-failures.png)

![Captura de ecrã da experiência de falhas](media/app-map/failures.png)

### <a name="investigate-performance"></a>Investigar o desempenho

Para resolver problemas de desempenho, selecione **investigar desempenho**.

![Captura de ecrã do botão de desempenho de investigar](media/app-map/investigate-performance.png)

![Captura de ecrã da experiência de desempenho](media/app-map/performance.png)

### <a name="go-to-details"></a>Ir para os detalhes

Selecione **vá para detalhes** para explorar a experiência de transação de ponto-a-ponto, o que pode oferecer exibições feitas para o nível de pilha de chamada.

![Captura de ecrã do botão go-detalhes](media/app-map/go-to-details.png)

![Captura de ecrã de detalhes da transação de ponta a ponta](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Vista no Analytics

Para consultar e investigar ainda mais os seus dados de aplicações, clique em **ver na análise**.

![Captura de ecrã da vista no botão análise](media/app-map/view-in-analytics.png)

![Captura de ecrã da experiência de análise](media/app-map/analytics.png)

### <a name="alerts"></a>Alertas

Para ver alertas ativos e as regras subjacentes que fazem com que os alertas ser acionado, selecione **alertas**.

![Captura de ecrã do botão de alertas](media/app-map/alerts.png)

![Captura de ecrã da experiência de análise](media/app-map/alerts-view.png)

## <a name="set-cloudrolename"></a>Set cloud_RoleName

Mapa da aplicação utiliza o `cloud_RoleName` propriedade para identificar os componentes no mapa. O SDK do Application Insights adiciona automaticamente o `cloud_RoleName` propriedade para a telemetria emitida por componentes. Por exemplo, o SDK irá adicionar um nome do web site ou um nome de função de serviço para o `cloud_RoleName` propriedade. No entanto, há casos em que poderá pretender substituir o valor predefinido. Para substituir cloud_RoleName e alterar o que é apresentado no mapa de aplicativo:

### <a name="net"></a>.NET

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
                telemetry.Context.Cloud.RoleName = "RoleName";
            }
        }
    }
}
```

**Carregar seu inicializador**

In ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

É um método alternativo instanciar o inicializador no código, por exemplo, no Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
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

Se utilizar o Spring Boot com o arranque de Spring Boot do Application Insights, a única alteração necessária é definir seu nome personalizado para o aplicativo no arquivo Application.

`spring.application.name=<name-of-app>`

O starter Spring Boot atribuirá automaticamente para o valor introduzido para a propriedade spring.application.name cloudRoleName.

Para obter mais informações sobre a correlação de Java e como configurar cloudRoleName para Check-out de aplicativos de não-SpringBoot [secção](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) no correlação.

### <a name="clientbrowser-side-javascript"></a>JavaScript do lado do cliente/browser

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

Para obter mais informações sobre como substituir a propriedade cloud_RoleName com inicializadores de telemetria, consulte [adicionar propriedades: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Resolução de problemas

Se estiver a ter problemas ao mapa da aplicação a funcionar conforme esperado, experimente estes passos:

1. Confirme que está a utilizar um SDK suportado oficialmente. Os SDKs não suportados/da comunidade poderão não suportar a correlação.

    Consulte este [artigo](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) para obter uma lista dos SDKs suportados.

2. Atualize todos os componentes para a versão mais recente do SDK.

3. Se estiver a utilizar as funções do Azure com o C#, atualize para o [V2 funções](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Confirme [cloud_RoleName](app-map.md#set-cloudrolename) está configurado corretamente.

5. Se tiver uma dependência em falta, certifique-se de que a mesma está na lista de [dependências recolhidas automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se não, pode acompanhá-la manualmente com uma [chamada de dependência de acompanhamento](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="portal-feedback"></a>Comentários do portal
Para enviar comentários, utilize a opção de comentários.

![Imagem de MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Passos Seguintes

* [Correlação de compreensão](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
