---
title: Como conceber a sua implementação de Application Insights - Um vs muitos recursos?
description: Telemetria direta a diferentes recursos para desenvolvimento, teste e carimbos de produção.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 3964cddcf27a4b2c7397b508ccb3cc8928bd04ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589533"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Quantos recursos de Insights de Aplicação devo implementar

Quando está a desenvolver a próxima versão de uma aplicação web, não pretende misturar a telemetria [Application Insights](../../azure-monitor/app/app-insights-overview.md) a partir da nova versão e da versão já lançada. Para evitar confusões, envie a telemetria de diferentes fases de desenvolvimento para separar os recursos de Application Insights, com teclas de instrumentação separadas (ikeys). Para facilitar a alteração da tecla de instrumentação à medida que uma versão se move de um estágio para outro, pode ser útil definir o ikey em código em vez de no ficheiro de configuração.

(Se o seu sistema é um Serviço de Nuvem Azure, existe [outro método de configuração de ikeys separados](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sobre recursos e chaves de instrumentação

Quando configura a monitorização do Application Insights para a sua aplicação web, cria um *recurso* Application Insights no Microsoft Azure. Abre este recurso no portal Azure para ver e analisar a telemetria recolhida na sua app. O recurso é identificado por uma *chave de instrumentação* (ikey). Quando instala o pacote Application Insights para monitorizar a sua aplicação, configura-a com a chave de instrumentação, para que saiba para onde enviar a telemetria.

Cada recurso De Insights de Aplicação vem com métricas que estão disponíveis fora da caixa. Se componentes completamente separados reportarem ao mesmo recurso Application Insights, estas métricas podem não fazer sentido para o dashboard/alerta.

### <a name="when-to-use-a-single-application-insights-resource"></a>Quando utilizar um único recurso Application Insights

-   Para os componentes de aplicação que são implantados em conjunto. Normalmente desenvolvido por uma única equipa, gerido pelo mesmo conjunto de utilizadores de DevOps/ITOps.
-   Se fizer sentido agregar indicadores-chave de desempenho (KPI's) tais como durações de resposta, taxas de falha no painel de instrumentos, etc., em todos eles por padrão (pode optar por segmentar por nome de função na experiência Metrics Explorer).
-   Se não houver necessidade de gerir o controlo de acesso baseado em funções (Azure RBAC) de forma diferente entre os componentes da aplicação.
-   Se não precisar de critérios de alerta de métricas que sejam diferentes entre os componentes.
-   Se não for necessário gerir as exportações contínuas de forma diferente entre os componentes.
-   Se não precisar de gerir a faturação/quotas de forma diferente entre os componentes.
-   Se não houver problema em ter uma chave API tem o mesmo acesso aos dados de todos os componentes. E 10 chaves API são suficientes para as necessidades em todas elas.
-   Se não houver problema em ter as mesmas definições inteligentes de deteção e integração de artigos de trabalho em todas as funções.

### <a name="other-things-to-keep-in-mind"></a>Outras coisas a ter em mente

-   Pode ser necessário adicionar código personalizado para garantir que valores significativos são definidos no [atributo Cloud_RoleName.](./app-map.md?tabs=net#set-or-override-cloud-role-name) Sem valores significativos definidos para este atributo, *NENHUMA* das experiências do portal funcionará.
- Para aplicações de Tecido de Serviço e serviços clássicos de nuvem, o SDK lê automaticamente do Azure Role Environment e define-as. Para todos os outros tipos de aplicações, provavelmente terá de definir isto explicitamente.
-   A experiência Live Metrics não suporta a divisão pelo nome de papel.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Chave de instrumentação dinâmica

Para facilitar a alteração do ikey à medida que o código se move entre fases de produção, consulte a chave dinamicamente em código em vez de utilizar um valor codificado/estático.

Desa parte num método de inicialização, como global.aspx.cs num serviço ASP.NET:

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

Neste exemplo, os ikeys para os diferentes recursos são colocados em diferentes versões do ficheiro de configuração web. A troca do ficheiro de configuração web - que pode fazer como parte do script de lançamento - irá trocar o recurso-alvo.

### <a name="web-pages"></a>Páginas Web
O iKey também é usado nas páginas web da sua aplicação, no [script que obteve a partir do painel de arranque rápido.](../../azure-monitor/app/javascript.md) Em vez de codificar literalmente no script, gere-o a partir do estado do servidor. Por exemplo, numa aplicação ASP.NET:

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>Criar recursos adicionais de Insights de Aplicação

Para criar um recurso Applications Insights siga o [guia de criação de recursos.](./create-new-resource.md)

### <a name="getting-the-instrumentation-key"></a>Obtenção da chave de instrumentação
A chave de instrumentação identifica o recurso que criou.

Precisa das chaves de instrumentação de todos os recursos para os quais a sua aplicação enviará dados.

## <a name="filter-on-build-number"></a>Filtro no número de construção
Ao publicar uma nova versão da sua aplicação, vai querer separar a telemetria de diferentes construções.

Pode definir a propriedade Versão aplicação para que possa filtrar os resultados [da pesquisa](../../azure-monitor/app/diagnostic-search.md) e do [explorador métrico.](../../azure-monitor/essentials/metrics-charts.md)

Existem vários métodos diferentes de definir a propriedade Versão aplicação.

* Definir diretamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Embrulhe esta linha num [inicializador de telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para garantir que todas as instâncias de TelemetriaClient sejam definidas de forma consistente.
* [ASP.NET] Coloque a versão em `BuildInfo.config` . O módulo web irá recolher a versão a partir do nó BuildLabel. Inclua este ficheiro no seu projeto e lembre-se de definir a propriedade Copy Always no Solution Explorer.

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
* [ASP.NET] Gere BuildInfo.config automaticamente em MSBuild. Para isso, adicione algumas linhas ao seu `.csproj` arquivo:

    ```XML
    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Isto gera um ficheiro chamado *.BuildInfo.config de Nome Deprojecto.* O processo de Publicação renomea-o para BuildInfo.config.

    A etiqueta de construção contém um espaço reservado (AutoGen_...) quando se constrói com o Visual Studio. Mas quando construído com MSBuild, é povoado com o número de versão correta.

    Para permitir que o MSBuild gere números de versão, desave a versão como `1.0.*` em AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versão e controlo de versão
Para controlar a versão da aplicação, certifique-se de que `buildinfo.config` é gerado pelo processo do Microsoft Build Engine. No seu `.csproj` ficheiro, adicione:  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

Quando possui informações de compilação, o módulo Web do Application Insights adiciona automaticamente a **Versão da aplicação** como uma propriedade a todos os itens de telemetria. Desta forma, poderá filtrar por versão quando executar [pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md) ou [explorar métricas](../../azure-monitor/essentials/metrics-charts.md).

No entanto, note que o número da versão de construção é gerado apenas pelo Microsoft Build Engine, e não pelo desenvolvedor construído a partir de Visual Studio.

### <a name="release-annotations"></a>Anotações da versão
Se utilizar O Azure DevOps, pode [obter um marcador de anotação](../../azure-monitor/app/annotations.md) adicionado às suas tabelas sempre que lançar uma nova versão. 

## <a name="next-steps"></a>Passos seguintes

* [Recursos partilhados para múltiplos papéis](../../azure-monitor/app/app-map.md)
* [Crie um inicializador de telemetria para distinguir A| Variantes B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)