---
title: Separar a telemetria de desenvolvimento, teste e no lançamento no Azure Application Insights | Documentos da Microsoft
description: Obter telemetria direta a recursos diferentes para os carimbos de desenvolvimento, teste e produção.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 2e9c599c12ed10327d352baee02500d2284d98d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60713471"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>A separação de telemetria de desenvolvimento, teste e produção

Quando estiver desenvolvendo a próxima versão de um aplicativo web, não quer misturar os [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetria a partir da nova versão e a versão de lançamento já. Para evitar confusão, envie a telemetria a partir de fases de desenvolvimento diferentes para separar recursos do Application Insights, com chaves de instrumentação separados (as ikeys). Para tornar mais fácil alterar a chave de instrumentação, à medida que passa de uma versão de uma fase para outra, pode ser útil definir a ikey no código, em vez de no ficheiro de configuração. 

(Se o seu sistema é um serviço de nuvem do Azure, não há [outro método de configuração de outras ikeys separadas](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Informações sobre recursos e chaves de instrumentação

Quando configurar a monitorização do Application Insights para a sua aplicação web, criar um Application Insights *recursos* no Microsoft Azure. Abrir este recurso no portal do Azure para ver e analisar a telemetria recolhida da sua aplicação. O recurso é identificado por uma *chave de instrumentação* (ikey). Quando instala o pacote do Application Insights para monitorizar a aplicação, configurá-lo com a chave de instrumentação, para que ela saiba para onde enviar a telemetria.

Normalmente, optar por utilizar recursos separados ou um único recurso compartilhado em cenários diferentes:

* Diferentes, independentes de aplicações - utilizar um recurso separado e a ikey para cada aplicação.
* Vários componentes ou funções de aplicação de um negócio - utilizar uma [único recurso partilhado](../../azure-monitor/app/app-map.md) para todas as aplicações de componente. Telemetria pode ser filtrada ou segmentada pela propriedade cloud_RoleName.
* Desenvolvimento, teste e lançamento - utilizam um recurso separado e a ikey para versões do sistema em 'Carimbo' ou a fase de produção.
* R | Teste de B - utilizar um único recurso. Crie um TelemetryInitializer para adicionar uma propriedade para a telemetria que identifica as variantes.


## <a name="dynamic-ikey"></a> Chave de instrumentação dinâmico

Para tornar mais fácil alterar a ikey, à medida que o código passa entre fases de produção, defina-o no código, em vez de no ficheiro de configuração.

Defina a chave num método de inicialização, como global.aspx.cs num serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Neste exemplo, as outras ikeys para diferentes recursos são colocadas em diferentes versões do ficheiro de configuração web. A troca de ficheiro de configuração web - que pode ser feito como parte do script de versão - irá alternar o recurso de destino.

### <a name="web-pages"></a>Páginas Web
A iKey também é utilizada em páginas da web da sua aplicação, no [script que obteve a partir do painel de início rápido](../../azure-monitor/app/javascript.md). Em vez de codificá-lo, literalmente, para o script, gerá-lo a partir do Estado do servidor. Por exemplo, num aplicativo do ASP.NET:

*JavaScript no Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Criar recursos adicionais do Application Insights
Para separar a telemetria de componentes da aplicação diferentes ou para carimbos diferentes (dev/teste/produção) do mesmo componente, em seguida, terá de criar um novo recurso do Application Insights.

Na [portal.azure.com](https://portal.azure.com), adicionar um recurso do Application Insights:

![Clicar em Novo, Application Insights](./media/separate-resources/01-new.png)

* **Tipo de aplicação** afeta o que vê no painel de descrição geral e as propriedades disponíveis no [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md). Se não vir o seu tipo de aplicação, escolha um dos tipos de web para páginas da web.
* **Grupo de recursos** é uma conveniência para gerir as propriedades como [controlo de acesso](../../azure-monitor/app/resources-roles-access-control.md). Poderia usar grupos de recursos separados para desenvolvimento, teste e produção.
* **Subscrição** é a sua conta de pagamento no Azure.
* **Localização** é onde manter os seus dados. Atualmente não pode ser alterado. 
* **Adicionar ao dashboard** coloca um mosaico de acesso rápido para o seu recurso na sua página inicial do Azure. 

Criar o recurso demora alguns segundos. Verá um alerta quando tiver terminado.

(Pode escrever um [script do PowerShell](../../azure-monitor/app/powershell-script-create-resource.md) para criar um recurso automaticamente.)

### <a name="getting-the-instrumentation-key"></a>Obtendo a chave de instrumentação
A chave de instrumentação identifica o recurso que criou. 

![Clique em Essentials, clique na chave de instrumentação, CTRL + C](./media/separate-resources/02-props.png)

Terá das chaves de instrumentação de todos os recursos para o qual irá enviar dados.

## <a name="filter-on-build-number"></a>Filtre por número de compilação
Quando publica uma nova versão da sua aplicação, vai querer ser capaz de separar a telemetria de compilações diferentes.

Pode definir a propriedade de versão da aplicação para que pode filtrar [pesquisa](../../azure-monitor/app/diagnostic-search.md) e [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) resultados.

![Filtrar numa propriedade](./media/separate-resources/050-filter.png)

Existem vários métodos diferentes de definir a propriedade de versão da aplicação.

* Defina diretamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Encapsular essa linha num [inicializador de telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para garantir que todas as instâncias de TelemetryClient estão definidas de forma consistente.
* [ASP.NET] Definir a versão `BuildInfo.config`. A versão do nó BuildLabel recolhem o módulo da web. Inclua esse arquivo em seu projeto e não se esqueça de definir a propriedade Copy Always no Explorador de soluções.

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
* [ASP.NET] Gere automaticamente BuildInfo.config nas MSBuild. Para tal, adicione algumas linhas para sua `.csproj` ficheiro:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Isso gera um arquivo chamado *Nomedoprojeto*. BuildInfo.config. O processo de publicação lhe mude o nome para BuildInfo.config.

    A etiqueta de compilação contém um marcador de posição (AutoGen _), quando criar com o Visual Studio. Mas quando criada com o MSBuild, ele é preenchido com o número de versão correta.

    Para permitir que o MSBuild gerar números de versão, definir a versão como `1.0.*` no AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versão e controlo de versão
Para controlar a versão da aplicação, certifique-se de que `buildinfo.config` é gerado pelo processo do Microsoft Build Engine. No ficheiro .csproj, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Quando possui informações de compilação, o módulo Web do Application Insights adiciona automaticamente a **Versão da aplicação** como uma propriedade a todos os itens de telemetria. Desta forma, poderá filtrar por versão quando executar [pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md) ou [explorar métricas](../../azure-monitor/app/metrics-explorer.md).

No entanto, tenha em atenção que o número de versão da compilação é gerado apenas pelo Microsoft Build Engine, não pela compilação do programador no Visual Studio.

### <a name="release-annotations"></a>Anotações da versão
Se utilizar o Azure DevOps, pode [obter um marcador de anotação](../../azure-monitor/app/annotations.md) adicionado aos seus gráficos sempre que lançar uma nova versão. A imagem seguinte mostra como este marcador é apresentado.

![Captura de ecrã de um exemplo de anotação de versão num gráfico](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Passos Seguintes

* [Recursos partilhados para várias funções](../../azure-monitor/app/app-map.md)
* [Criar um inicializador de telemetria para distinguir um | Variantes de B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
