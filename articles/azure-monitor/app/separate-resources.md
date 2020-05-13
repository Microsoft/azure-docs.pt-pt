---
title: Como conceber a sua implementação de Insights de Aplicação - Um vs muitos recursos?
description: Telemetria direta para diferentes recursos para o desenvolvimento, teste e selos de produção.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 6df6622cbba251c221533c3307dc194f08e871fb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125694"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Quantos recursos de Aplicação Insights devo implementar

Quando está a desenvolver a próxima versão de uma aplicação web, não pretende misturar a telemetria [Application Insights](../../azure-monitor/app/app-insights-overview.md) da nova versão e a versão já lançada. Para evitar confusões, envie a telemetria de diferentes fases de desenvolvimento para separar os recursos da Application Insights, com chaves de instrumentação separadas (ikeys). Para facilitar a alteração da chave de instrumentação à medida que uma versão se move de um estágio para outro, pode ser útil definir a chave em código em vez de no ficheiro de configuração.

(Se o seu sistema for um Serviço azure cloud, há [outro método de configuração de ikeys separados](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sobre recursos e chaves de instrumentação

Quando configura a monitorização de Application Insights para a sua aplicação web, cria um *recurso* Desinformação de aplicações no Microsoft Azure. Abre este recurso no portal Azure para ver e analisar a telemetria recolhida na sua app. O recurso é identificado por uma chave de *instrumentação* (ikey). Quando instala o pacote Application Insights para monitorizar a sua aplicação, configurá-lo com a chave de instrumentação, para que saiba onde enviar a telemetria.

Cada recurso Application Insights vem com métricas que estão disponíveis fora da caixa. Se componentes completamente separados reportarem ao mesmo recurso Application Insights, estas métricas podem não fazer sentido para o dashboard/alerta.

### <a name="when-to-use-a-single-application-insights-resource"></a>Quando utilizar um único recurso de Insights de Aplicação

-   Para componentes de aplicação que são implantados em conjunto. Normalmente desenvolvido por uma única equipa, gerida pelo mesmo conjunto de utilizadores DevOps/ITOps.
-   Se fizer sentido agregar indicadores-chave de desempenho (KPIIs) tais como durações de resposta, taxas de falha no dashboard, etc., em todos eles por padrão (pode optar por segmentar por nome de papel na experiência Metrics Explorer).
-   Se não houver necessidade de gerir o Controlo de Acesso baseado em Funções (RBAC) de forma diferente entre os componentes da aplicação.
-   Se não precisar de critérios de alerta métricos diferentes entre os componentes.
-   Se não precisar de gerir as exportações contínuas de forma diferente entre os componentes.
-   Se não precisar de gerir a faturação/quotas de forma diferente entre os componentes.
-   Se não houver problema em ter uma chave API, terá o mesmo acesso aos dados de todos os componentes. E 10 chaves API são suficientes para as necessidades em todas elas.
-   Se não houver problema em ter as mesmas definições de integração de itens de deteção inteligente e de trabalho em todas as funções.

### <a name="other-things-to-keep-in-mind"></a>Outras coisas a ter em mente

-   Pode ser necessário adicionar código personalizado para garantir que valores significativos são definidos no [atributo Cloud_RoleName.](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net#set-cloud-role-name) Sem valores significativos definidos para este atributo, *nenhuma* das experiências do portal funcionará.
- Para aplicações de Tecido de Serviço e serviços clássicos na nuvem, o SDK lê automaticamente a partir do Ambiente de Papel Azure e define-os. Para todos os outros tipos de aplicações, provavelmente terá de o definir explicitamente.
-   A experiência Live Metrics não suporta a divisão pelo nome do papel.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Chave de instrumentação dinâmica

Para facilitar a alteração da tecla à medida que o código se move entre as fases de produção, detetete-o em código em vez de no ficheiro de configuração.

Desloque a chave num método de inicialização, como global.aspx.cs num serviço ASP.NET:

*C #*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Neste exemplo, os ikeys para os diferentes recursos são colocados em diferentes versões do ficheiro de configuração web. Trocar o ficheiro de configuração web - o que pode fazer como parte do script de lançamento - irá trocar o recurso-alvo.

### <a name="web-pages"></a>Páginas Web
O iKey também é usado nas páginas web da sua aplicação, no [script que obteve do painel de arranque rápido](../../azure-monitor/app/javascript.md). Em vez de codificar literalmente o script, gere-o a partir do estado do servidor. Por exemplo, numa aplicação ASP.NET:

*JavaScript em Navalha*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Criar recursos adicionais de Insights de Aplicação

Para criar um recurso Applications Insights siga o guia de criação de [recursos.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

### <a name="getting-the-instrumentation-key"></a>Obter a chave de instrumentação
A chave de instrumentação identifica o recurso que criou.

Precisa das chaves de instrumentação de todos os recursos para os quais a sua aplicação enviará dados.

## <a name="filter-on-build-number"></a>Filtro no número de construção
Quando publicar uma nova versão da sua aplicação, vai querer ser capaz de separar a telemetria de diferentes construções.

Pode definir a propriedade versão de aplicação para que possa filtrar a [pesquisa](../../azure-monitor/app/diagnostic-search.md) e os resultados do [explorador métrico.](../../azure-monitor/platform/metrics-charts.md)

Existem vários métodos diferentes para definir a propriedade versão de aplicação.

* Definir diretamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Envolva essa linha num inicializador de [telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para garantir que todas as instâncias do TelemettryClient sejam definidas de forma consistente.
* [ASP.NET] Desloque a versão `BuildInfo.config` em . O módulo web irá captar a versão do nó BuildLabel. Inclua este ficheiro no seu projeto e lembre-se de definir a propriedade Copy Always no Solution Explorer.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Gere buildInfo.config automaticamente na MSBuild. Para isso, adicione algumas linhas ao seu `.csproj` ficheiro:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Isto gera um ficheiro chamado *"ProjectName*". BuildInfo.config. O processo Publish renomea-o para BuildInfo.config.

    A etiqueta de construção contém um espaço reservado (AutoGen_...) quando se constrói com o Visual Studio. Mas quando construído com MSBuild, é povoado com o número de versão correta.

    Para permitir que o MSBuild gere números de versão, defina a versão como `1.0.*` em AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versão e controlo de versão
Para controlar a versão da aplicação, certifique-se de que `buildinfo.config` é gerado pelo processo do Microsoft Build Engine. No seu `.csproj` ficheiro, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Quando possui informações de compilação, o módulo Web do Application Insights adiciona automaticamente a **Versão da aplicação** como uma propriedade a todos os itens de telemetria. Desta forma, poderá filtrar por versão quando executar [pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md) ou [explorar métricas](../../azure-monitor/platform/metrics-charts.md).

No entanto, note que o número da versão build é gerado apenas pelo Microsoft Build Engine, e não pela construção do desenvolvedor a partir do Visual Studio.

### <a name="release-annotations"></a>Anotações da versão
Se utilizar o Azure DevOps, pode obter um marcador de [anotação](../../azure-monitor/app/annotations.md) adicionado às suas tabelas sempre que lançar uma nova versão. A imagem seguinte mostra como este marcador é apresentado.

## <a name="next-steps"></a>Passos seguintes

* [Recursos partilhados para múltiplos papéis](../../azure-monitor/app/app-map.md)
* [Criar um Inicializador de Telemetria para distinguir A. Variantes B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
