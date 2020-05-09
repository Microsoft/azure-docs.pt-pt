---
title: Monitor Azure app services performance [ Monitor Azure app services performance ] Microsoft Docs
description: Monitorização do desempenho da aplicação para serviços de aplicações Azure. Tempo de carga e resposta de gráficos, informações de dependência e alertas sobre o desempenho.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0f4d4dedab30839db56cb47ac7ac103413f2d4be
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733469"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorizar o desempenho do Serviço de Aplicações do Azure

Permitir a monitorização das suas aplicações web baseadas em ASP.NET e ASP.NET Core em funcionamento nos Serviços de [Aplicações Azure](https://docs.microsoft.com/azure/app-service/) é agora mais fácil do que nunca. Enquanto que anteriormente precisava de instalar manualmente uma extensão do site, a última extensão/agente está agora incorporada na imagem do serviço de aplicações por padrão. Este artigo irá acompanhá-lo através da monitorização de Insights de Aplicação, bem como fornecer orientações preliminares para automatizar o processo para implementações em larga escala.

> [!NOTE]
> A adição manual de uma extensão do site Application Insights através de**extensões** de **ferramentas** > de desenvolvimento é depreciada. Este método de instalação de extensão dependia de atualizações manuais para cada nova versão. O mais recente lançamento estável da extensão está agora [pré-instalado](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte da imagem do Serviço de Aplicações. Os ficheiros `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` estão localizados e são automaticamente atualizados com cada desbloqueio estável. Se seguir as instruções baseadas no agente para ativar a monitorização abaixo, removerá automaticamente a extensão depreciada para si.

## <a name="enable-application-insights"></a>Ativar o Application Insights

Existem duas formas de permitir a monitorização de aplicações para os Serviços de Aplicações Azure hospedados aplicações:

* **Monitorização de aplicações baseada no agente** (ApplicationInsightsAgent).  
    * Este método é o mais fácil de ativar, e não é necessária nenhuma configuração avançada. É frequentemente referido como monitorização "tempo de funcionamento". Para os Serviços de Aplicações Azure recomendamos, no mínimo, este nível de monitorização e, em seguida, com base no seu cenário específico, pode avaliar se é necessária uma monitorização mais avançada através da instrumentação manual.

* **Instrumentar manualmente a aplicação através** do código, instalando o SDK de Insights de Aplicação.

    * Esta abordagem é muito mais personalizável, mas requer a adição de [uma dependência dos pacotes DeSC NuGet](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)da Aplicação Insights . Este método também significa que você tem que gerir as atualizações para a versão mais recente dos pacotes por si mesmo.

    * Se precisar de fazer chamadas aPI personalizadas para rastrear eventos/dependências não capturadas por padrão com monitorização baseada em agentes, terá de utilizar este método. Confira a API para saber [eventos personalizados e artigo de métricas](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) para saber mais. Esta é também atualmente a única opção suportada para cargas de trabalho baseadas em Linux.

> [!NOTE]
> Se for detetada uma monitorização baseada no agente e uma instrumentação manual baseada em SDK, apenas as definições de instrumentação manual serão respeitadas. Isto é para evitar que os dados duplicados sejam enviados. Para saber mais sobre isso, confira a secção de resolução de [problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) abaixo.

## <a name="enable-agent-based-monitoring"></a>Ativar a monitorização baseada no agente

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> A combinação de APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression não é suportada. Para mais informações consulte a explicação na secção de resolução de [problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Selecione Application Insights** no painel de controlo Azure para o seu serviço de aplicações.

    ![Em Definições, escolha Insights de Aplicação](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que já tenha configurado um recurso Application Insights para esta aplicação. 

     > [!NOTE]
     > Quando clicar em **OK** para criar o novo recurso, será solicitado a **aplicar as definições de monitorização**. A seleção **do Continue** irá ligar o seu novo recurso Desinformação de aplicações ao seu serviço de aplicações, o que também irá desencadear **um reinício do seu serviço de aplicações**. 

     ![Instrumente a sua aplicação Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar qual o recurso a utilizar, pode escolher como pretende que os conhecimentos da aplicação recolham dados por plataforma para a sua aplicação. ASP.NET monitorização de aplicações é por defeito com dois níveis diferentes de recolha.

    ![Escolha opções por plataforma](./media/azure-web-apps/choose-options-new.png)
 
 Abaixo está um resumo dos dados recolhidos para cada rota:
        
|  | Coleção Básica .NET | .NET Coleção recomendada |
| --- | --- | --- |
| Adiciona tendências de utilização da CPU, memória e E/S |Sim |Sim |
| Recolhe tendências de utilização e permite a correlação dos resultados de disponibilidade para transações | Sim |Sim |
| Recolhe exceções sem processamento pelo processo anfitrião | Sim |Sim |
| Melhora a precisão das métricas de APM sob carga, quando é utilizada a amostragem | Sim |Sim |
| Correlaciona os microsserviços entre limites de pedidos/dependências | Não (apenas capacidades APM de instância única) |Sim |

3. Para configurar definições como a amostragem, que poderia controlar anteriormente através do ficheiro applicationinsights.config, pode agora interagir com essas mesmas definições através das definições de Aplicação com um prefixo correspondente. 

    * Por exemplo, para alterar a percentagem inicial de `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` amostragem, `100`pode criar uma definição de Aplicação de: e um valor de .

    * Para a lista de definições de processador de telemetria de amostragem adaptável suportada, pode consultar o [código](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) e [documentação associada.](https://docs.microsoft.com/azure/azure-monitor/app/sampling)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

As seguintes versões de .NET Core são suportadas: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0

Atualmente, as aplicações baseadas em direcionar a estrutura completa a partir de .NET Core, implementação autónoma e aplicações baseadas em Linux não são atualmente **suportadas** com monitorização baseada em agentes/extensões. (A[instrumentação manual](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) via código funcionará em todos os cenários anteriores.)

1. **Selecione Application Insights** no painel de controlo Azure para o seu serviço de aplicações.

    ![Em Definições, escolha Insights de Aplicação](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que já tenha configurado um recurso Application Insights para esta aplicação. 

     > [!NOTE]
     > Quando clicar em **OK** para criar o novo recurso, será solicitado a **aplicar as definições de monitorização**. A seleção **do Continue** irá ligar o seu novo recurso Desinformação de aplicações ao seu serviço de aplicações, o que também irá desencadear **um reinício do seu serviço de aplicações**. 

     ![Instrumente a sua aplicação Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar que recurso utilizar, pode escolher como pretende que os Insights de Aplicação recolham dados por plataforma para a sua aplicação. .NET Core oferece **recolha recomendada** ou **desativada** para .NET Core 2.0, 2.1, 2.2 e 3.0.

    ![Escolha opções por plataforma](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A partir da sua aplicação web do Serviço de Aplicações, em **Definições,** > **selecione Insights** > **De aplicação Ativação**. A monitorização baseada no agente Node.js está atualmente em pré-visualização.

# <a name="java"></a>[Java](#tab/java)

As aplicações web baseadas no Java App Service não suportam atualmente a monitorização automática baseada em agentes/extensões. Para permitir a monitorização da sua aplicação Java, é necessário [instrumentalizar manualmente](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)a sua aplicação .

# <a name="python"></a>[Python](#tab/python)

As aplicações web baseadas em Python App Service não suportam atualmente a monitorização automática baseada em agentes/extensões. Para permitir a monitorização da sua aplicação Python, é necessário [instrumentalizar manualmente](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)a sua aplicação .

---

## <a name="enable-client-side-monitoring"></a>Ativar a monitorização do lado do cliente

# <a name="net"></a>[.NET](#tab/net)

A monitorização do lado do cliente é opt-in para ASP.NET. Para permitir a monitorização do lado do cliente:

* Selecione **Definições** >** **Definições de aplicação****
   * De acordo com as definições da Aplicação, adicione um novo nome e **valor**de definição de **aplicações:**

     Nome:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor:`true`

   * **Guarde** as definições e **reinicie** a aplicação.

![Screenshot das definições de aplicação UI](./media/azure-web-apps/appinsights-javascript-enabled.png)

Para desativar a monitorização do lado do cliente, remova o par de valor-chave associado das definições de Aplicação ou detetetete o valor para falso.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

A monitorização do lado do cliente é **ativada por padrão** para aplicações .NET Core com **recolha Recomendada**, independentemente de a definição da aplicação 'APPINSIGHTS_JAVASCRIPT_ENABLED' estar presente.

Se por alguma razão quiser desativar a monitorização do lado do cliente:

* Selecione**definições de aplicação** de **definições** > 
   * De acordo com as definições da Aplicação, adicione um novo nome e **valor**de definição de **aplicações:**

     nome:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor:`false`

   * **Guarde** as definições e **reinicie** a aplicação.

![Screenshot das definições de aplicação UI](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Para ativar a monitorização do lado do cliente para a sua aplicação Node.js, precisa adicionar [manualmente o JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript)do lado do cliente à sua aplicação .

# <a name="java"></a>[Java](#tab/java)

Para ativar a monitorização do lado do cliente para a sua aplicação Java, precisa de [adicionar manualmente o JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript)do lado do cliente à sua aplicação .

# <a name="python"></a>[Python](#tab/python)

Para ativar a monitorização do lado do cliente para a sua aplicação Python, precisa de [adicionar manualmente o JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript)do lado do cliente à sua aplicação .

---

## <a name="automate-monitoring"></a>Monitorização automatizada

Para permitir a recolha de telemetria com Insights de Aplicação, apenas as definições de Aplicação devem ser definidas:

   ![Definições de aplicação de aplicação de serviço de aplicação com definições de Insights de aplicação disponíveis](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definições de definições de definições de aplicação

|Nome de definição de aplicativo |  Definição | Valor |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensão principal, que controla a monitorização do tempo de execução. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Apenas no modo predefinido, as funcionalidades essenciais são ativadas para garantir um desempenho ótimo. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla se o motor `InstrumentationEngine` binário de reescrita será ligado. Esta definição tem implicações de desempenho e impacta o tempo de arranque/arranque frio. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Os controlos se o texto da tabela SQL & Azure serão capturados juntamente com as chamadas de dependência. Aviso de desempenho: o tempo de arranque a frio da aplicação será afetado. Esta definição `InstrumentationEngine`requer o . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Definições de aplicação de aplicativos de serviço de aplicação com Gestor de Recursos Azure

As definições de aplicações para serviços de aplicações podem ser geridas e configuradas com modelos de [Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Este método pode ser utilizado na implementação de novos recursos do Serviço de Aplicações com automatização do Gestor de Recursos Do Azure, ou para modificar as definições dos recursos existentes.

A estrutura básica das definições de aplicação JSON para um serviço de aplicações é a seguinte:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Para um exemplo de um modelo de Gestor de Recursos Azure com definições de aplicação configuradas para Insights de Aplicação, este [modelo](https://github.com/Andrew-MSFT/BasicImageGallery) pode ser útil, especificamente a secção que começa na [linha 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizar a criação de um recurso Application Insights e ligar-se ao seu recém-criado Serviço de Aplicações.

Para criar um modelo de Gestor de Recursos Azure com todas as definições de Insights de Aplicação predefinidos configuradas, inicie o processo como se fosse criar uma nova Web App com Insights de Aplicação ativadas.

Selecione **opções de Automação**

   ![Menu de criação de aplicativos web do App Service](./media/azure-web-apps/create-web-app.png)

Esta opção gera o mais recente modelo do Gestor de Recursos Azure com todas as configurações necessárias configuradas.

  ![Modelo de aplicativo web do serviço de aplicação do serviço de aplicação](./media/azure-web-apps/arm-template.png)

Abaixo está uma amostra, substitua todas as instâncias com o nome do `AppMonitoredSite` seu site:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Habilitar através da PowerShell

Para permitir a monitorização da aplicação através do PowerShell, apenas as definições de aplicação subjacentes devem ser alteradas. Abaixo está uma amostra, que permite a monitorização de aplicações para um website chamado "AppMonitoredSite" no grupo de recursos "AppMonitoredRG", e configura os dados a serem enviados para a chave de instrumentação "012345678-abcd-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Ampliação de extensão/agente de monitorização

### <a name="upgrading-from-versions-289-and-up"></a>Upgrade a partir das versões 2.8.9 e acima

A atualização a partir da versão 2.8.9 acontece automaticamente, sem quaisquer ações adicionais. As novas peças de monitorização são entregues em segundo plano para o serviço de aplicações alvo, e no reinício da aplicação serão recolhidos.

Para verificar qual a versão da extensão que está a executar visita`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Screenshot do caminho da urlhttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Upgrade a partir das versões 1.0.0 - 2.6.5

A partir da versão 2.8.9 é utilizada a extensão do local pré-instalada. Se for uma versão anterior, pode atualizar através de uma de duas formas:

* [Atualize via portal](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Mesmo que tenha a extensão De insights de aplicação instalada para o Serviço de Aplicações Azure, o UI mostra apenas o botão **Ativa.** Nos bastidores, a antiga extensão do site privado será removida.)

* [Upgrade através da PowerShell:](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)

    1. Detete as definições de aplicação para ativar a extensão do site pré-instalada ApplicationInsightsAgent. Ver [Habilitar através da powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Remova manualmente a extensão do site privado chamada extensão de Insights de Aplicação para o Serviço de Aplicações Azure.

Se a atualização for feita a partir de uma versão anterior a 2.5.1, verifique se as dlls ApplicationInsigths são removidas da pasta do recipiente de aplicação [ver passos](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)de resolução de problemas .

## <a name="troubleshooting"></a>Resolução de problemas

Abaixo está o nosso guia passo a passo para monitorização baseada em extensão/agente para aplicações baseadas em .NET e .NET Core em execução em Serviços de Aplicações Azure.

> [!NOTE]
> As aplicações Java são suportadas apenas nos Serviços de Aplicações Azure através de instrumentação manual baseada em SDK e, portanto, os passos abaixo não se aplicam a estes cenários.

1. Verifique se a aplicação `ApplicationInsightsAgent`é monitorizada via .
    * Verifique `ApplicationInsightsAgent_EXTENSION_VERSION` se a definição da aplicação está definida como um valor de "~2".
2. Certifique-se de que a aplicação satisfaz os requisitos a monitorizar.
    * Navegue para`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Screenshot https://yoursitename.scm.azurewebsites/applicationinsights da página de resultados](./media/azure-web-apps/app-insights-sdk-status.png)

    * Confirme `Application Insights Extension Status` que o é`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Se não estiver a funcionar, siga as instruções de monitorização de informações de [aplicação ativas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Confirme que a fonte de estado existe e se parece:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se não houver um valor semelhante, significa que a aplicação não está atualmente em execução ou não é suportada. Para garantir que a aplicação está em execução, tente visitar manualmente o url/pontos finais da aplicação, o que permitirá que a informação sobre o tempo de execução fique disponível.

    * Confirme `IKeyExists` que é`true`
        * Se for, `false` `APPINSIGHTS_INSTRUMENTATIONKEY` adicione `APPLICATIONINSIGHTS_CONNECTION_STRING` e com o seu guia chave para as definições de aplicação.

    * Confirme que não `AppAlreadyInstrumented`há `AppContainsDiagnosticSourceAssembly`inscrições para, e `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Se alguma destas entradas existir, retire as `Microsoft.ApplicationInsights`seguintes embalagens da sua aplicação: , `System.Diagnostics.DiagnosticSource`e `Microsoft.AspNet.TelemetryCorrelation`.

O quadro abaixo fornece uma explicação mais detalhada do que estes valores significam, as suas causas subjacentes, e correções recomendadas:

|Valor problemático|Explicação|Correção
|---- |----|---|
| `AppAlreadyInstrumented:true` | Este valor indica que a extensão detetou que algum aspeto do SDK já está presente na Aplicação, e irá recuar. Pode ser devido a `System.Diagnostics.DiagnosticSource`uma `Microsoft.AspNet.TelemetryCorrelation`referência a, ou`Microsoft.ApplicationInsights`  | Retire as referências. Algumas destas referências são adicionadas por padrão a partir de certos modelos `Microsoft.ApplicationInsights`do Estúdio Visual, e versões mais antigas do Visual Studio podem adicionar referências a .
|`AppAlreadyInstrumented:true` | Se a aplicação estiver direcionada para .NET Core 2.1 ou 2.2, e se referir a [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-package, então traz insights de aplicação, e a extensão irá recuar. | Os clientes do .NET Core 2.1,2.2 são [recomendados](https://github.com/aspnet/Announcements/issues/287) para usar o microsoft.AspNetCore.App meta-package.|
|`AppAlreadyInstrumented:true` | Este valor também pode ser causado pela presença dos dlls acima referidos na pasta da aplicação a partir de uma implementação anterior. | Limpe a pasta da aplicação para garantir que estes dlls sejam removidos. Consulte o diretório de bin da sua aplicação local e o diretório wwwroot no Serviço de Aplicações. (Para verificar o diretório wwwroot da sua aplicação web App Service: Advanced Tools (Kudu) > Consola Debug > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Este valor indica que a `Microsoft.AspNet.TelemetryCorrelation` extensão detetou referências na aplicação e irá recuar. | Retire a referência.
|`AppContainsDiagnosticSourceAssembly**:true`|Este valor indica que a `System.Diagnostics.DiagnosticSource` extensão detetou referências na aplicação e irá recuar.| Retire a referência.
|`IKeyExists:false`|Este valor indica que a chave de instrumentação `APPINSIGHTS_INSTRUMENTATIONKEY`não está presente no AppSetting, . Possíveis causas: Os valores podem ter sido removidos acidentalmente, esquecidos de definir os valores no script de automação, etc. | Certifique-se de que a definição está presente nas definições da aplicação do Serviço de Aplicações.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression não é suportado

Se utilizar APPINSIGHTS_JAVASCRIPT_ENABLED=verdadeiro nos casos em que o conteúdo é codificado, poderá obter erros como: 

- Erro de reescrita de URL de 500
- 500.53 Erro de reescrita de URL com regras de reescrita de saída de mensagem Não pode ser aplicado quando o conteúdo da resposta HTTP estiver codificado ('gzip'). 

Isto deve-se à definição de aplicação APPINSIGHTS_JAVASCRIPT_ENABLED que está a ser definida para a verdadeira e codificação de conteúdos estar presente ao mesmo tempo. Este cenário ainda não é apoiado. A suver é remover APPINSIGHTS_JAVASCRIPT_ENABLED das definições de aplicação. Infelizmente isto significa que se a instrumentação JavaScript do lado do cliente/navegador ainda for necessária, são necessárias referências SDK manuais para as suas páginas web. Siga as [instruções](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) de instrumentação manual com o JavaScript SDK.

Para obter as informações mais recentes sobre o agente/extensão de Informações de Aplicação, consulte as notas de [lançamento](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="php-and-wordpress-are-not-supported"></a>PHP e WordPress não são suportados

Os sites PHP e WordPress não são suportados. Não existe atualmente um SDK/agente oficialmente suportado para monitorização do lado do servidor destas cargas de trabalho. No entanto, a instrumentação manual de transações do lado do cliente num site PHP ou WordPress, adicionando o javascript do lado do cliente às suas páginas web, pode ser realizada utilizando o [JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

### <a name="connection-string-and-instrumentation-key"></a>Chave de corda de ligação e instrumentação

Quando se está a utilizar uma monitorização sem código, só é necessária a cadeia de ligação. No entanto, recomendamos ainda a definição da chave de instrumentação para preservar a compatibilidade para trás com versões mais antigas do SDK quando a instrumentação manual está a ser executada.

## <a name="next-steps"></a>Passos seguintes
* [Run the profiler on your live app](../app/profiler.md) (Executar o gerador de perfis na sua aplicação publicada).
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample) - monitorize Funções do Azure com o Application Insights
* [Ativar os diagnósticos do Azure](../platform/diagnostics-extension-to-application-insights.md) para serem enviados para o Application Insights.
* [Monitorizar as métricas de estado de funcionamento de serviço](../platform/data-platform.md) para se certificar de que o serviço está disponível e a responder.
* [Receber notificações de alertas](../platform/alerts-overview.md) sempre que ocorrem eventos operacionais ou quando as métricas ultrapassam um determinado limiar.
* Utilizar o [Application Insights para aplicações JavaScript e páginas Web](javascript.md) para obter telemetria de clientes a partir dos browsers que visitam as páginas Web.
* [Configurar os Testes Web de disponibilidade](monitor-web-app-availability.md) para ser alertado se o seu site estiver em baixo.
