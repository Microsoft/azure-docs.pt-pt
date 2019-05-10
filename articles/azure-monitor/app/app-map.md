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
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 618453ec9857254fe14608df8091bb79bd3815b7
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509978"
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

Esta experiência é iniciado com a deteção de progressiva dos componentes. Quando carrega o mapa de aplicativo em primeiro lugar, um conjunto de consultas é acionado para detetar os componentes relacionados com este componente. Um botão no canto superior esquerdo será atualizado com o número de componentes na sua aplicação à medida que são detetados. 

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

Selecione **vá para detalhes** para explorar a experiência de transação de ponto-a-ponto, o que pode oferecer exibições até o nível de pilha de chamada.

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

## <a name="set-cloud-role-name"></a>Nome de função do conjunto de cloud

Mapa da aplicação utiliza a **nome da função na nuvem** propriedade para identificar os componentes no mapa. O SDK do Application Insights adiciona automaticamente a propriedade de nome de função na nuvem para a telemetria emitida por componentes. Por exemplo, o SDK irá adicionar um nome do web site ou o nome do serviço de função para a propriedade de nome de função na nuvem. No entanto, há casos em que poderá pretender substituir o valor predefinido. Para substituir o nome da função na nuvem e alterar o que é apresentado no mapa de aplicativo:

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
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance"
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

O starter Spring Boot atribuirá automaticamente o nome da função na nuvem para o valor introduzido para a propriedade spring.application.name.

Para obter mais informações sobre Java correlação e como configurar a função na nuvem nome para Check-out de aplicativos de não-SpringBoot isso [secção](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) no correlação.

### <a name="clientbrowser-side-javascript"></a>JavaScript do lado do cliente/browser

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Nome de função de cloud de compreensão dentro do contexto do mapa da aplicação

Com relação às General como pensar sobre **nome da função na nuvem**, pode ser útil ver um mapa da aplicação que tenha vários nomes de funções de cloud presentes:

![Captura de ecrã de mapa de aplicativo](media/app-map/cloud-rolename.png)

No mapa de aplicativo, acima de cada um dos nomes na caixas verdes é, por exemplo, valores de nome de função para diferentes aspetos desta aplicação distribuída específico na cloud. Portanto, para esta aplicação respetivas funções consistem: `Authentication`, `acmefrontend`, `Inventory Management`, um `Payment Processing Worker Role`. 

No caso desta aplicação cada um desses nomes de função na nuvem também representa um recurso do Application Insights exclusivo diferente com suas próprias chaves de instrumentação. Uma vez que o proprietário desta aplicação tem acesso a cada um desses quatro recursos diferentes do Application Insights, o mapa da aplicação é capaz de reunir um mapa das relações subjacentes.

Para o [oficiais definições](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Em alternativa, **instância de função na nuvem** pode ser útil para cenários em que **nome da função na nuvem** informa o problema está algures na web front-end, mas poderá estar a executar a web front-end em vários com balanceamento de carga servidores então, ser capaz de fazer uma busca numa camada mais aprofundada através de consultas de Kusto e saber se o problema está a afetar a todas as web front-end servidores/instâncias, ou apenas um pode ser extremamente importante.

Um cenário em que escolherá para substituir o valor para a instância de função na nuvem é possível se a aplicação é executada num ambiente em contentores onde saber apenas o servidor individual pode não ser informações suficientes para localizar um problema específico.

Para obter mais informações sobre como substituir a propriedade de nome de função na nuvem com inicializadores de telemetria, consulte [adicionar propriedades: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Resolução de problemas

Se estiver a ter problemas ao mapa da aplicação a funcionar conforme esperado, experimente estes passos:

### <a name="general"></a>Geral

1. Confirme que está a utilizar um SDK suportado oficialmente. Os SDKs não suportados/da comunidade poderão não suportar a correlação.

    Consulte este [artigo](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) para obter uma lista dos SDKs suportados.

2. Atualize todos os componentes para a versão mais recente do SDK.

3. Se estiver a utilizar as funções do Azure com o C#, atualize para o [V2 funções](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Confirme [nome da função na nuvem](#set-cloud-role-name) está configurado corretamente.

5. Se tiver uma dependência em falta, certifique-se de que a mesma está na lista de [dependências recolhidas automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se não, pode acompanhá-la manualmente com uma [chamada de dependência de acompanhamento](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Muitos de nós no mapa

Mapa da aplicação constrói um nó de aplicação para cada nome de função de cloud única presente na sua telemetria de pedido e um nó de dependência para cada combinação exclusiva de tipo, o destino e o nome da função na nuvem na sua telemetria de dependência. Se existirem mais de 10.000 nós na sua telemetria, o mapa da aplicação não poderá obter todos os nós e ligações, por isso, o mapa será incompleto. Se isto acontecer, será apresentada uma mensagem de aviso quando visualizar o mapa.

Além disso, o mapa da aplicação só suporta até 1000 nós sem grupo separados compostos de uma só vez. Mapa da aplicação reduz a complexidade visual ao agrupar as dependências que têm o mesmo tipo e os autores de chamadas, mas se sua telemetria tem demasiados nomes de função de cloud única ou muitos tipos de dependência, esse agrupamento insuficiente, e o mapa será possível composição.

Para corrigir este problema, terá de alterar a instrumentação para configurar corretamente o nome da função na nuvem, tipo de dependência e os campos de destino de dependência.

* Destino de dependência deve representar o nome lógico de uma dependência. Em muitos casos, é equivalente ao servidor ou o nome de recurso da dependência. Por exemplo, no caso de dependências HTTP está definido para o nome do anfitrião. Não deve conter IDs exclusivos ou parâmetros que alterar-se de uma solicitação para outra.

* Tipo de dependência deve representar o tipo de lógico de uma dependência. Por exemplo, HTTP, de SQL ou de Blobs do Azure são tipos de dependência típico. Não deve conter IDs exclusivos.

* A finalidade do nome da função na nuvem está descrita com o [acima secção](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Comentários do portal

Para enviar comentários, utilize a opção de comentários.

![Imagem de MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Passos Seguintes

* [Correlação de compreensão](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
