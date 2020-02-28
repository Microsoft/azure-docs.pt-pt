---
title: Separando a telemetria em Insights de Aplicação Azure
description: Telemetria direta para diferentes recursos para o desenvolvimento, teste e selos de produção.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 3580d162f4b3955a04ffcd0f13933221bfef3b65
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671465"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Separando a telemetria do desenvolvimento, teste e produção

Quando está a desenvolver a próxima versão de uma aplicação web, não pretende misturar a telemetria [Application Insights](../../azure-monitor/app/app-insights-overview.md) da nova versão e a versão já lançada. Para evitar confusões, envie a telemetria de diferentes fases de desenvolvimento para separar os recursos da Application Insights, com chaves de instrumentação separadas (ikeys). Para facilitar a alteração da chave de instrumentação à medida que uma versão se move de um estágio para outro, pode ser útil definir a chave em código em vez de no ficheiro de configuração. 

(Se o seu sistema for um Serviço azure cloud, há [outro método de configuração de ikeys separados](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sobre recursos e chaves de instrumentação

Quando configura a monitorização de Application Insights para a sua aplicação web, cria um *recurso* Desinformação de aplicações no Microsoft Azure. Abre este recurso no portal Azure para ver e analisar a telemetria recolhida na sua app. O recurso é identificado por uma chave de *instrumentação* (ikey). Quando instala o pacote Application Insights para monitorizar a sua aplicação, configurá-lo com a chave de instrumentação, para que saiba onde enviar a telemetria.

Normalmente opta por utilizar recursos separados ou um único recurso partilhado em diferentes cenários:

* Aplicações diferentes e independentes - Utilize um recurso separado e ikey para cada aplicação.
* Múltiplos componentes ou funções de uma aplicação de negócio - Use um [único recurso partilhado](../../azure-monitor/app/app-map.md) para todas as aplicações componentes. A telemetria pode ser filtrada ou segmentada pela propriedade cloud_RoleName.
* Desenvolvimento, Teste e Lançamento - Utilize um recurso separado e chave para versões do sistema em 'carimbo' ou fase de produção.
* A B teste - Utilize um único recurso. Crie um TelemettryInitializer para adicionar uma propriedade à telemetria que identifica as variantes.


## <a name="dynamic-ikey"></a>Chave de instrumentação dinâmica

Para facilitar a alteração da tecla à medida que o código se move entre as fases de produção, detetete-o em código em vez de no ficheiro de configuração.

Desloque a chave num método de inicialização, como global.aspx.cs num serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Neste exemplo, os ikeys para os diferentes recursos são colocados em diferentes versões do ficheiro de configuração web. Trocar o ficheiro de configuração web - o que pode fazer como parte do script de lançamento - irá trocar o recurso-alvo.

### <a name="web-pages"></a>Páginas Web
O iKey também é usado nas páginas web da sua aplicação, no [script que obteve da lâmina de arranque rápido](../../azure-monitor/app/javascript.md). Em vez de codificar literalmente o script, gere-o a partir do estado do servidor. Por exemplo, numa aplicação ASP.NET:

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
Para separar a telemetria para diferentes componentes de aplicação, ou para diferentes selos (dev/teste/produção) do mesmo componente, terá de criar um novo recurso Application Insights.

No [portal.azure.com,](https://portal.azure.com)adicione um recurso Deinsights de Aplicação:

![Clicar em Novo, Application Insights](./media/separate-resources/01-new.png)

* **O tipo de aplicação** afeta o que se vê na lâmina de visão geral e as propriedades disponíveis no [explorador métrico.](../../azure-monitor/app/metrics-explorer.md) Se não vir o seu tipo de aplicação, escolha um dos tipos web para páginas web.
* **O grupo de recursos** é uma conveniência para gerir propriedades como o controlo de [acesso.](../../azure-monitor/app/resources-roles-access-control.md) Pode utilizar grupos de recursos separados para desenvolvimento, teste e produção.
* **A subscrição** é a sua conta de pagamento no Azure.
* **A localização** é onde guardamos os seus dados. Atualmente não pode ser mudado. 
* **Adicione ao dashboard** coloca um azulejo de acesso rápido para o seu recurso na sua página Azure Home. 

Criar o recurso leva alguns segundos. Verá um alerta quando estiver feito.

(Pode escrever um [script PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) para criar um recurso automaticamente.)

### <a name="getting-the-instrumentation-key"></a>Obter a chave de instrumentação
A chave de instrumentação identifica o recurso que criou. 

![Clique no Essencial, clique na chave de instrumentação, CTRL+C](./media/separate-resources/02-props.png)

Precisa das chaves de instrumentação de todos os recursos para os quais a sua aplicação enviará dados.

## <a name="filter-on-build-number"></a>Filtro no número de construção
Quando publicar uma nova versão da sua aplicação, vai querer ser capaz de separar a telemetria de diferentes construções.

Pode definir a propriedade versão de aplicação para que possa filtrar a [pesquisa](../../azure-monitor/app/diagnostic-search.md) e os resultados do [explorador métrico.](../../azure-monitor/app/metrics-explorer.md)

![Filtragem em uma propriedade](./media/separate-resources/050-filter.png)

Existem vários métodos diferentes para definir a propriedade versão de aplicação.

* Definir diretamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Envolva essa linha num inicializador de [telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para garantir que todas as instâncias do TelemettryClient sejam definidas de forma consistente.
* [ASP.NET] Desloque a versão em `BuildInfo.config`. O módulo web irá captar a versão do nó BuildLabel. Inclua este ficheiro no seu projeto e lembre-se de definir a propriedade Copy Always no Solution Explorer.

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
* [ASP.NET] Gere buildInfo.config automaticamente na MSBuild. Para isso, adicione algumas linhas ao seu ficheiro `.csproj`:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Isto gera um ficheiro chamado *"ProjectName*". BuildInfo.config. O processo Publish renomea-o para BuildInfo.config.

    A etiqueta de construção contém um espaço reservado (AutoGen_...) quando se constrói com o Visual Studio. Mas quando construído com MSBuild, é povoado com o número de versão correta.

    Para permitir que o MSBuild gere números de versão, defina a versão como `1.0.*` em AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versão e controlo de versão
Para controlar a versão da aplicação, certifique-se de que `buildinfo.config` é gerado pelo processo do Microsoft Build Engine. No seu ficheiro `.csproj`, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Quando possui informações de compilação, o módulo Web do Application Insights adiciona automaticamente a **Versão da aplicação** como uma propriedade a todos os itens de telemetria. Desta forma, poderá filtrar por versão quando executar [pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md) ou [explorar métricas](../../azure-monitor/app/metrics-explorer.md).

No entanto, note que o número da versão build é gerado apenas pelo Microsoft Build Engine, e não pela construção do desenvolvedor a partir do Visual Studio.

### <a name="release-annotations"></a>Notas da versão
Se utilizar o Azure DevOps, pode obter um marcador de [anotação](../../azure-monitor/app/annotations.md) adicionado às suas tabelas sempre que lançar uma nova versão. A imagem seguinte mostra como este marcador é apresentado.

![Captura de ecrã de um exemplo de anotação de versão num gráfico](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Passos seguintes

* [Recursos partilhados para múltiplos papéis](../../azure-monitor/app/app-map.md)
* [Criar um Inicializador de Telemetria para distinguir A. Variantes B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
