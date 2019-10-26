---
title: Separando a telemetria do desenvolvimento, teste e lançamento no Aplicativo Azure insights | Microsoft Docs
description: Telemetria direta para recursos diferentes para carimbos de desenvolvimento, teste e produção.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/15/2017
ms.openlocfilehash: bcf741e82e247a5b79a478ef1015a70cccb4d274
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899914"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Separação de telemetria de desenvolvimento, teste e produção

Ao desenvolver a próxima versão de um aplicativo Web, você não deseja misturar a telemetria de [Application insights](../../azure-monitor/app/app-insights-overview.md) da nova versão e da versão já lançada. Para evitar confusão, envie a telemetria de diferentes estágios de desenvolvimento para separar Application Insights recursos, com chaves de instrumentação separadas (ikeys). Para facilitar a alteração da chave de instrumentação à medida que uma versão é movida de um estágio para outro, pode ser útil definir o iKey no código em vez de no arquivo de configuração. 

(Se o seu sistema for um serviço de nuvem do Azure, há [outro método de configuração de ikeys separados](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sobre recursos e chaves de instrumentação

Ao configurar o monitoramento de Application Insights para seu aplicativo Web, você cria um *recurso* de Application Insights no Microsoft Azure. Você abre esse recurso no portal do Azure para ver e analisar a telemetria coletada do seu aplicativo. O recurso é identificado por uma *chave de instrumentação* (iKey). Ao instalar o pacote de Application Insights para monitorar seu aplicativo, você o configura com a chave de instrumentação, para que ele saiba onde enviar a telemetria.

Normalmente, você opta por usar recursos separados ou um único recurso compartilhado em diferentes cenários:

* Aplicativos diferentes e independentes – usam um recurso separado e iKey para cada aplicativo.
* Vários componentes ou funções de um aplicativo de negócios – use um [único recurso compartilhado](../../azure-monitor/app/app-map.md) para todos os aplicativos de componente. A telemetria pode ser filtrada ou segmentada pela propriedade cloud_RoleName.
* Desenvolvimento, teste e versão – use um recurso separado e iKey para versões do sistema em ' Stamp ' ou no estágio de produção.
* A | B teste-use um único recurso. Crie um Personalizada telemetryinitializer para adicionar uma propriedade à telemetria que identifica as variantes.


## <a name="dynamic-ikey"></a>Chave de instrumentação dinâmica

Para facilitar a alteração do iKey à medida que o código se move entre os estágios de produção, defina-o no código, em vez de no arquivo de configuração.

Defina a chave em um método de inicialização, como global.aspx.cs em um serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Neste exemplo, o ikeys para os diferentes recursos é colocado em versões diferentes do arquivo de configuração da Web. Alternando o arquivo de configuração da Web – o que você pode fazer como parte do script de versão – alternará o recurso de destino.

### <a name="web-pages"></a>Páginas Web
O iKey também é usado nas páginas da Web do seu aplicativo, no [script que você obteve da folha início rápido](../../azure-monitor/app/javascript.md). Em vez de codificá-lo literalmente no script, gere-o a partir do estado do servidor. Por exemplo, em um aplicativo ASP.NET:

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


## <a name="create-additional-application-insights-resources"></a>Criar recursos de Application Insights adicionais
Para separar a telemetria para componentes de aplicativos diferentes, ou para diferentes carimbos (desenvolvimento/teste/produção) do mesmo componente, você precisará criar um novo recurso de Application Insights.

No [Portal.Azure.com](https://portal.azure.com), adicione um recurso de Application insights:

![Clicar em Novo, Application Insights](./media/separate-resources/01-new.png)

* O **tipo de aplicativo** afeta o que você vê na folha visão geral e as propriedades disponíveis no Gerenciador de [métricas](../../azure-monitor/app/metrics-explorer.md). Se você não vir seu tipo de aplicativo, escolha um dos tipos de Web para páginas da Web.
* O **grupo de recursos** é uma conveniência para gerenciar propriedades como o controle de [acesso](../../azure-monitor/app/resources-roles-access-control.md). Você pode usar grupos de recursos separados para desenvolvimento, teste e produção.
* A **assinatura** é sua conta de pagamento no Azure.
* **Local** é onde mantemos seus dados. Atualmente, ele não pode ser alterado. 
* **Adicionar ao painel** coloca um bloco de acesso rápido para seu recurso em sua home page do Azure. 

A criação do recurso leva alguns segundos. Você verá um alerta quando terminar.

(Você pode escrever um [script do PowerShell](../../azure-monitor/app/powershell-script-create-resource.md) para criar um recurso automaticamente.)

### <a name="getting-the-instrumentation-key"></a>Obtendo a chave de instrumentação
A chave de instrumentação identifica o recurso que você criou. 

![Clique em Essentials, clique na chave de instrumentação, CTRL + C](./media/separate-resources/02-props.png)

Você precisará das chaves de instrumentação de todos os recursos para os quais seu aplicativo enviará dados.

## <a name="filter-on-build-number"></a>Filtrar no número da versão
Ao publicar uma nova versão do seu aplicativo, você desejará poder separar a telemetria de compilações diferentes.

Você pode definir a propriedade de versão do aplicativo para que você possa filtrar os resultados de [pesquisa](../../azure-monitor/app/diagnostic-search.md) e do [Gerenciador de métricas](../../azure-monitor/app/metrics-explorer.md) .

![Filtragem em uma propriedade](./media/separate-resources/050-filter.png)

Há vários métodos diferentes de definir a propriedade de versão do aplicativo.

* Definir diretamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Coloque essa linha em um [inicializador de telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para garantir que todas as instâncias de TelemetryClient sejam definidas de forma consistente.
* [ASP.NET] Defina a versão em `BuildInfo.config`. O módulo da Web irá pegar a versão do nó BuildLabel. Inclua esse arquivo em seu projeto e lembre-se de definir a propriedade copiar sempre em Gerenciador de Soluções.

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
* [ASP.NET] Gere BuildInfo. config automaticamente no MSBuild. Para fazer isso, adicione algumas linhas ao arquivo de `.csproj`:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Isso gera um arquivo chamado *yourProjectName*. BuildInfo. config. o processo de publicação o renomeia para BuildInfo. config.

    O rótulo de compilação contém um espaço reservado (AutoGen_...) quando você compila com o Visual Studio. Mas, quando compilado com o MSBuild, ele é populado com o número de versão correto.

    Para permitir que o MSBuild gere números de versão, defina a versão como `1.0.*` em AssemblyReference.cs

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
Se você usar o Azure DevOps, poderá [obter um marcador de anotação](../../azure-monitor/app/annotations.md) adicionado aos seus gráficos sempre que liberar uma nova versão. A imagem seguinte mostra como este marcador é apresentado.

![Captura de ecrã de um exemplo de anotação de versão num gráfico](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Passos seguintes

* [Recursos compartilhados para várias funções](../../azure-monitor/app/app-map.md)
* [Criar um inicializador de telemetria para distinguir um | Variantes B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
