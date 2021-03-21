---
title: Monitor Azure app services performance | Microsoft Docs
description: Monitorização do desempenho da aplicação para serviços de aplicações Azure. Cartografe o tempo de carga e resposta, informações de dependência e definir alertas sobre o desempenho.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 7661066bc2666070c8b3ed9263b1223c09d6c720
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734728"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorizar o desempenho do Serviço de Aplicações do Azure

Ativar a monitorização do seu ASP.NET, ASP.NET Core e aplicações web baseadas em Node.js em execução nos [Serviços de Aplicações Azure](../../app-service/index.yml) é agora mais fácil do que nunca. Enquanto que anteriormente era necessário instalar manualmente uma extensão do site, a mais recente extensão/agente está agora incorporada na imagem do serviço de aplicações por padrão. Este artigo irá acompanhá-lo através da monitorização de Insights de Aplicação, bem como fornecer orientações preliminares para automatizar o processo para implementações em larga escala.

> [!NOTE]
> A adição manual de uma extensão do site Application Insights através de extensões **de**  >  **ferramentas** de desenvolvimento é depreciada. Este método de instalação de extensão dependia de atualizações manuais para cada nova versão. A mais recente versão estável da extensão  [encontra-se agora pré-instalada](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte da imagem do Serviço de Aplicações. Os ficheiros `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` estão localizados e são automaticamente atualizados a cada libertação estável. Se seguir as instruções baseadas no agente para ativar a monitorização abaixo, removerá automaticamente a extensão prectada para si.

## <a name="enable-application-insights"></a>Ativar o Application Insights

Existem duas formas de permitir a monitorização de aplicações para os Serviços de Aplicações Azure hospedados:

* **Monitorização de aplicações baseada em agentes** (ApplicationInsightsAgent).  
    * Este método é o mais fácil de ativar, e não é necessária nenhuma configuração avançada. É frequentemente referido como monitorização do "tempo de execução". Para os Serviços de Aplicações Azure recomendamos, no mínimo, que este nível de monitorização seja possível e, em seguida, com base no seu cenário específico, pode avaliar se é necessária uma monitorização mais avançada através de instrumentação manual.

* **Instrumentando manualmente a aplicação através de código** instalando o Application Insights SDK.

    * Esta abordagem é muito mais personalizável, mas requer [a adição de uma dependência dos pacotes SDK NuGet da Aplicação Insights.](./asp-net.md) Este método também significa que você tem que gerir as atualizações para a versão mais recente dos pacotes por si mesmo.

    * Se precisar de fazer chamadas de API personalizadas para rastrear eventos/dependências não capturados por padrão com monitorização baseada em agentes, terá de utilizar este método. Confira a [API para obter eventos personalizados e artigo de métrica](./api-custom-events-metrics.md) para saber mais. Esta é também a única opção suportada para cargas de trabalho baseadas em Linux.

> [!NOTE]
> Se for detetada uma monitorização baseada em agentes e uma instrumentação manual baseada em SDK, apenas serão respeitadas as definições de instrumentação manual. Isto é para evitar que os dados duplicados sejam enviados. Para saber mais sobre isto, consulte a [secção de resolução de problemas abaixo.](#troubleshooting)

## <a name="enable-agent-based-monitoring"></a>Permitir a monitorização baseada em agentes

# <a name="aspnet"></a>[ASP.NET](#tab/net)

> [!NOTE]
> A combinação de APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression não é suportada. Para mais informações consulte a explicação na [secção de resolução de problemas.](#troubleshooting)


1. **Selecione Informações de aplicações** no painel de controlo Azure para o seu serviço de aplicações.

    ![Em Definições, escolha Insights de Aplicação](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a não ser que já tenha criado um recurso Application Insights para esta aplicação. 

     > [!NOTE]
     > Quando clicar **em OK** para criar o novo recurso, será solicitado para aplicar as **definições de monitorização**. A seleção **continue** ligará o seu novo recurso Application Insights ao seu serviço de aplicações, o que também **irá desencadear um reinício do seu serviço de aplicações.** 

     ![Instrumente a sua aplicação Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar que recurso utilizar, pode escolher como pretende que os insights da aplicação recolham dados por plataforma para a sua aplicação. ASP.NET monitorização de aplicações está on-by-default com dois níveis diferentes de recolha.

    ![O Screenshot mostra a página de extensões do site Application Insights com criar novos recursos selecionados.](./media/azure-web-apps/choose-options-new.png)
 
 Abaixo está um resumo dos dados recolhidos para cada rota:
        
| Dados | Coleção Básica ASP.NET | coleção recomendada ASP.NET |
| --- | --- | --- |
| Adiciona tendências de utilização da CPU, memória e E/S |Yes |Yes |
| Recolhe tendências de utilização e permite a correlação dos resultados de disponibilidade para transações | Yes |Yes |
| Recolhe exceções sem processamento pelo processo anfitrião | Yes |Yes |
| Melhora a precisão das métricas de APM sob carga, quando é utilizada a amostragem | Yes |Yes |
| Correlaciona os microsserviços entre limites de pedidos/dependências | Não (apenas capacidades APM de instância única) |Yes |

3. Para configurar configurações como amostragem, que poderia controlar previamente através do ficheiro applicationinsights.config pode agora interagir com essas mesmas definições através de definições de Aplicação com um prefixo correspondente. 

    * Por exemplo, para alterar a percentagem inicial de amostragem, pode criar uma definição de Aplicação de: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` e um valor de `100` .

    * Para a lista de definições de processador de telemetria adaptativa suportada, pode consultar o [código](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) e [a documentação associada](./sampling.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

> [!IMPORTANT]
> As seguintes versões de ASP.NET Core são suportadas: ASP.NET Core 2.1 e 3.1. As versões 2.0, 2.2 e 3.0 já não foram apoiadas. Por favor, atualize para uma [versão suportada](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) de .NET Core para que a auto-instrumentação funcione.

O quadro completo de ASP.NET, as aplicações baseadas em Base e Linux não são atualmente **suportadas** com monitorização baseada em agentes/extensões. ([A instrumentação manual](./asp-net-core.md) via código funcionará em todos os cenários anteriores.)

1. **Selecione Informações de aplicações** no painel de controlo Azure para o seu serviço de aplicações.

    ![Em Definições, escolha Insights de Aplicação](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a não ser que já tenha criado um recurso Application Insights para esta aplicação. 

     > [!NOTE]
     > Quando clicar **em OK** para criar o novo recurso, será solicitado para aplicar as **definições de monitorização**. A seleção **continue** ligará o seu novo recurso Application Insights ao seu serviço de aplicações, o que também **irá desencadear um reinício do seu serviço de aplicações.** 

     ![Instrumente a sua aplicação Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar que recurso utilizar, pode escolher como pretende que o Application Insights recolha dados por plataforma para a sua aplicação. ASP.NET Core oferece **coleção recomendada** ou **desativada** para ASP.NET Core 2.1 e 3.1.

    ![Escolha opções por plataforma](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A monitorização baseada em agentes do Windows não é suportada, para permitir que o Linux visite a [ documentação do Serviço de AplicaçõesNode.js](../../app-service/configure-language-nodejs.md?pivots=platform-linux#monitor-with-application-insights).

# <a name="java"></a>[Java](#tab/java)

Siga as diretrizes para o [agente Application Insights Java 3.0](./java-in-process-agent.md) para permitir a auto-instrumentação para as suas aplicações Java sem alterar o seu código.
A integração automática ainda não está disponível para o Serviço de Aplicações.

# <a name="python"></a>[Python](#tab/python)

As aplicações web baseadas no Python App Service não suportam atualmente a monitorização automática baseada em agente/extensão. Para ativar a monitorização da sua aplicação Python, é necessário [instrumentar manualmente a sua aplicação](./opencensus-python.md).

---

## <a name="enable-client-side-monitoring"></a>Ativar a monitorização do lado do cliente

# <a name="aspnet"></a>[ASP.NET](#tab/net)

A monitorização do lado do cliente é opt-in para ASP.NET. Para permitir a monitorização do lado do cliente:

* **Configurações** **>** **Configuração**
   * Nas definições de Aplicação, crie uma **nova definição de aplicação:**

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `true`

   * **Guarde** as definições e **reinicie** a aplicação.

Para desativar a monitorização do lado do cliente, remova o par de valor da chave associado das definições de Aplicação ou defina o valor em falso.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

A monitorização do lado do cliente é **ativada por padrão** para ASP.NET aplicações Core com **recolha Recomendada**, independentemente de a definição da aplicação APPINSIGHTS_JAVASCRIPT_ENABLED estar presente .

Se por alguma razão pretender desativar a monitorização do lado do cliente:

* **Configurações** **>** **Configuração**
   * Nas definições de Aplicação, crie uma **nova definição de aplicação:**

     nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `false`

   * **Guarde** as definições e **reinicie** a aplicação.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Para ativar a monitorização do lado do cliente para a sua aplicação Node.js, é necessário [adicionar manualmente o JavaScript SDK do lado do cliente à sua aplicação.](./javascript.md)

# <a name="java"></a>[Java](#tab/java)

Para ativar a monitorização do lado do cliente para a sua aplicação Java, é necessário [adicionar manualmente o JavaScript SDK do lado do cliente à sua aplicação.](./javascript.md)

# <a name="python"></a>[Python](#tab/python)

Para ativar a monitorização do lado do cliente para a sua aplicação Python, é necessário [adicionar manualmente o JavaScript SDK do lado do cliente à sua aplicação.](./javascript.md)

---

## <a name="automate-monitoring"></a>Automatizar a monitorização

Para permitir a recolha de telemetria com Insights de Aplicação, apenas as definições de Aplicação devem ser definidas:

   ![Definições de aplicações de serviço de aplicações de aplicativos com definições de Insights de aplicações disponíveis](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definições de definições de aplicações

|Nome de definição de aplicativo |  Definição | Valor |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensão principal, que controla a monitorização do tempo de funcionamento. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Apenas no modo predefinido, as funcionalidades essenciais são ativadas de forma a garantir um desempenho ótimo. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla se o motor de reescrita binária `InstrumentationEngine` for ligado. Esta definição tem implicações de desempenho e impacta o tempo de arranque/arranque a frio. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controla se o SQL & texto da tabela Azure será capturado juntamente com as chamadas de dependência. Aviso de desempenho: o tempo de arranque a frio da aplicação será afetado. Esta definição requer a `InstrumentationEngine` . | `~1` |
|XDT_MicrosoftApplicationInsights_PreemptSdk | Apenas para aplicações core ASP.NET. Permite o Interop (interoperação) com a Application Insights SDK. Carrega a extensão lado a lado com o SDK e utiliza-a para enviar telemetria (desativa o SDK application Insights). |`1`|

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Configurações de aplicações de serviço de aplicativo com gestor de recursos Azure

As definições de aplicações para Serviços de Aplicações podem ser geridas e configuradas com [modelos do Gestor de Recursos Azure.](../../azure-resource-manager/templates/template-syntax.md) Este método pode ser utilizado ao implementar novos recursos do Serviço de Aplicações com automação do Azure Resource Manager ou para modificar as definições dos recursos existentes.

A estrutura básica das definições de aplicação JSON para um serviço de aplicações é abaixo:

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

Para um exemplo de um modelo de Gestor de Recursos Azure com configurações de aplicação configuradas para Insights de Aplicação, este [modelo](https://github.com/Andrew-MSFT/BasicImageGallery) pode ser útil, especificamente a secção que começa na [linha 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatize a criação de um recurso Application Insights e ligue-se ao seu recém-criado Serviço de Aplicações.

Para criar um modelo de Gestor de Recursos Azure com todas as definições de Insights de Aplicação padrão configuradas, inicie o processo como se fosse criar uma nova App Web com Insights de Aplicação ativado.

Selecione **opções de automatização**

   ![Menu de criação de aplicativos web do Serviço de Aplicações de Aplicação](./media/azure-web-apps/create-web-app.png)

Esta opção gera o mais recente modelo do Gestor de Recursos Azure com todas as definições necessárias configuradas.

  ![Modelo de aplicativo web do serviço de aplicações de aplicativos de aplicativos de aplicativo](./media/azure-web-apps/arm-template.png)

Abaixo está uma amostra, substitua todas as instâncias com o nome do  `AppMonitoredSite` seu site:

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

### <a name="enabling-through-powershell"></a>Habilitação através do PowerShell

Para permitir a monitorização da aplicação através do PowerShell, apenas as definições de aplicação subjacentes precisam de ser alteradas. Abaixo está uma amostra, que permite a monitorização de aplicações para um website chamado "AppMonitoredSite" no grupo de recursos "AppMonitoredRG", e configura os dados a serem enviados para a chave de instrumentação "012345678-abcd-ef01-2345-6789abcd" da tecla de instrumentação.

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

## <a name="upgrade-monitoring-extensionagent"></a>Atualizar extensão/agente de monitorização

### <a name="upgrading-from-versions-289-and-up"></a>Upgrade a partir das versões 2.8.9 ou acima

O upgrade da versão 2.8.9 acontece automaticamente, sem quaisquer ações adicionais. As novas bits de monitorização são entregues em segundo plano ao serviço de aplicações-alvo, e no reinício da aplicação serão recolhidos.

Para verificar qual a versão da extensão que está a executar visita `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Screenshot do caminho url http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Atualização das versões 1.0.0 - 2.6.5

A partir da versão 2.8.9 é utilizada a extensão do local pré-instalada. Se for uma versão anterior, pode atualizar através de uma de duas formas:

* [Upgrade ativando através do portal](#enable-application-insights). (Mesmo que tenha a extensão De Insights de Aplicação para O Serviço de Aplicações Azure instalada, o UI mostra apenas o botão **Ativar.** Nos bastidores, a antiga extensão do site privado será removida.)

* [Upgrade através do PowerShell:](#enabling-through-powershell)

    1. Defina as definições de aplicação para ativar a extensão do site pré-instalada ApplicationSightsAgent. Ver [Ativação através do PowerShell](#enabling-through-powershell).
    2. Remova manualmente a extensão do site privado chamada Extensão de Insights de Aplicação para O Serviço de Aplicações Azure.

Se a atualização for feita a partir de uma versão anterior ao 2.5.1, verifique se os dlls ApplicationInsigths são removidos da pasta do caixote do lixo da aplicação [ver etapas de resolução de problemas](#troubleshooting).

## <a name="troubleshooting"></a>Resolução de problemas

Abaixo está o nosso guia de resolução de problemas passo a passo para monitorização baseada em extensões/agentes para aplicações baseadas em ASP.NET e ASP.NET Core em execução nos Serviços de Aplicações Azure.

> [!NOTE]
> A abordagem recomendada para monitorizar as aplicações java é utilizar a auto-instrumentação sem alterar o código. Siga as diretrizes para o [agente Application Insights Java 3.0](./java-in-process-agent.md).


1. Verifique se a aplicação é monitorizada via `ApplicationInsightsAgent` .
    * Verifique se a `ApplicationInsightsAgent_EXTENSION_VERSION` definição da aplicação está definida para um valor de "~2".
2. Certifique-se de que a aplicação satisfaz os requisitos a controlar.
    * Navegue para `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Screenshot da página de https://yoursitename.scm.azurewebsites/applicationinsights resultados](./media/azure-web-apps/app-insights-sdk-status.png)

    * Confirme que o `Application Insights Extension Status` é `Pre-Installed Site Extension, version 2.8.12.1527, is running.` 
    * Se não estiver em funcionamento, siga as [instruções de monitorização do Application Insights](#enable-application-insights)

    * Confirme que a fonte de estado existe e parece: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se não houver um valor semelhante, significa que a aplicação não está atualmente em execução ou não está suportada. Para garantir que a aplicação está em execução, tente visitar manualmente os pontos finais url/aplicação da aplicação, o que permitirá que as informações de tempo de execução fiquem disponíveis.

    * Confirme que `IKeyExists` é `true`
        * Se for `false` , adicione e com o seu guia `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTION_STRING` ikey às definições da sua aplicação.

    * Confirme que não há entradas para `AppAlreadyInstrumented` `AppContainsDiagnosticSourceAssembly` , e `AppContainsAspNetTelemetryCorrelationAssembly` .
        * Se existir alguma destas entradas, retire as seguintes embalagens da sua aplicação: `Microsoft.ApplicationInsights` `System.Diagnostics.DiagnosticSource` , e `Microsoft.AspNet.TelemetryCorrelation` .
        * Apenas para ASP.NET aplicações Core: caso a sua aplicação se refira a quaisquer pacotes de Insights de Aplicação, por exemplo, se tiver previamente instrumentalizado (ou tentado instrumentar) a sua aplicação com o [ASP.NET Core SDK,](./asp-net-core.md)permitindo que a integração do Serviço de Aplicações não produza efeitos e os dados podem não aparecer no Application Insights. Para corrigir o problema, no portal ligue "Interop with Application Insights SDK" e começará a ver os dados em Insights de Aplicação 
        > [!IMPORTANT]
        > Esta funcionalidade está em pré-visualização 

        ![Ativar a definição da app existente](./media/azure-web-apps/netcore-sdk-interop.png)

        Os dados serão agora enviados utilizando uma abordagem codificada, mesmo que a Application Insights SDK tenha sido originalmente utilizada ou tentada a ser utilizada.

        > [!IMPORTANT]
        > Se a aplicação utilizar a Application Insights SDK para enviar qualquer telemetria, essa telemetria será desativada – ou seja, telemetria personalizada – se for, por exemplo, qualquer método Track*() e quaisquer configurações personalizadas, como amostragem, serão desativadas. 


### <a name="php-and-wordpress-are-not-supported"></a>PHP e WordPress não são suportados

Os sites PHP e WordPress não são suportados. Atualmente, não existe um SDK/agente suportado oficialmente para monitorização do lado do servidor destas cargas de trabalho. No entanto, instrumentar manualmente as transações do lado do cliente num site PHP ou WordPress adicionando o JavaScript do lado do cliente às suas páginas web pode ser realizado utilizando o [JavaScript SDK](./javascript.md).

O quadro abaixo fornece uma explicação mais detalhada do que estes valores significam, as suas causas subjacentes e correções recomendadas:

|Valor problemático|Explicação|Correção
|---- |----|---|
| `AppAlreadyInstrumented:true` | Este valor indica que a extensão detetou que algum aspeto do SDK já está presente na Aplicação, e irá recuar. Pode ser devido a uma referência `System.Diagnostics.DiagnosticSource`  `Microsoft.AspNet.TelemetryCorrelation` a, ou `Microsoft.ApplicationInsights`  | Retire as referências. Algumas destas referências são adicionadas por padrão a partir de certos modelos do Estúdio Visual, e versões mais antigas do Visual Studio podem adicionar referências a `Microsoft.ApplicationInsights` .
|`AppAlreadyInstrumented:true` | Se a aplicação tiver como alvo ASP.NET Núcleo 2.1 ou 2.2, este valor indica que a extensão detetou que algum aspeto do SDK já está presente na Aplicação, e irá recuar | Recomenda-se [aos](https://github.com/aspnet/Announcements/issues/287) clientes em .NET Core 2.1,2.2 que utilizem Microsoft.AspNetCore.App meta-pacote. Além disso, ligue "Interop with Application Insights SDK" no portal (consulte as instruções acima).|
|`AppAlreadyInstrumented:true` | Este valor também pode ser causado pela presença dos dlls acima na pasta da aplicação a partir de uma implementação anterior. | Limpe a pasta da aplicação para garantir que estes dlls são removidos. Consulte o diretório de lixo da sua aplicação local e o diretório wwwroot no Serviço de Aplicações. (Para verificar o diretório wwwroot da sua aplicação web App Service: Advanced Tools (Kudu) > consola Debug > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Este valor indica que a extensão detetou referências `Microsoft.AspNet.TelemetryCorrelation` na aplicação e irá recuar. | Retire a referência.
|`AppContainsDiagnosticSourceAssembly**:true`|Este valor indica que a extensão detetou referências `System.Diagnostics.DiagnosticSource` na aplicação e irá recuar.| Para ASP.NET retire a referência. 
|`IKeyExists:false`|Este valor indica que a chave de instrumentação não está presente na AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY` . Causas possíveis: Os valores podem ter sido removidos acidentalmente, esqueceram-se de definir os valores no script de automação, etc. | Certifique-se de que a definição está presente nas definições da aplicação do Serviço de Aplicações da Aplicação.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression não é suportado

Se utilizar APPINSIGHTS_JAVASCRIPT_ENABLED=verdade nos casos em que o conteúdo é codificado, poderá obter erros como: 

- Erro de reescrita de URL 500
- 500.53 URL reescrever o erro do módulo com as regras de reescrita de saída de mensagens não pode ser aplicado quando o conteúdo da resposta HTTP estiver codificado ('gzip'). 

Isto deve-se à APPINSIGHTS_JAVASCRIPT_ENABLED configuração da aplicação estar a ser definida como verdadeira e codificação de conteúdos ao mesmo tempo. Este cenário ainda não está apoiado. A solução é remover APPINSIGHTS_JAVASCRIPT_ENABLED das definições da sua aplicação. Infelizmente, isto significa que, se a instrumentação JavaScript do lado do cliente/navegador ainda for necessária, são necessárias referências SDK manual para as suas páginas web. Siga as [instruções](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) de instrumentação manual com o JavaScript SDK.

Para obter as informações mais recentes sobre o agente/extensão application insights, consulte as [notas de lançamento](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>O website predefinido implementado com aplicações web não suporta monitorização automática do lado do cliente

Quando cria uma aplicação web com os `ASP.NET` tempos de `ASP.NET Core` execução ou tempos de execução nos Serviços de Aplicações Azure, implementa uma única página de HTML estática como um website inicial. A página web estática também carrega uma parte web gerida ASP.NET no IIS. Isto permite testar a monitorização do lado do servidor sem código, mas não suporta a monitorização automática do lado do cliente.

Se desejar testar o servidor sem código e a monitorização do lado do cliente para ASP.NET ou ASP.NET Core numa aplicação web Azure App Services recomendamos seguir os guias oficiais para [criar uma aplicação web Core ASP.NET](../../app-service/quickstart-dotnetcore.md) e criar uma [aplicação web ASP.NET Framework](../../app-service/quickstart-dotnet-framework.md) e, em seguida, usar as instruções no artigo atual para permitir a monitorização.

### <a name="connection-string-and-instrumentation-key"></a>Chave de ligação e instrumentação

Quando se utiliza uma monitorização codificada, é necessário apenas o fio de ligação. No entanto, recomendamos ainda a definição da chave de instrumentação para preservar a retrocompatibilidade com versões mais antigas do SDK quando estiver a ser executada a instrumentação manual.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Diferença entre métricas padrão de Insights de Aplicação vs métricas do Serviço de Aplicações Azure?

A Application Insights recolhe telemetria para os pedidos que o fizeram ao pedido. Se a falha ocorreu no WebApps/IIS, e o pedido não chegou à aplicação do utilizador, então o Application Insights não terá qualquer telemetria sobre o mesmo.

A duração `serverresponsetime` calculada por Application Insights não corresponde necessariamente ao tempo de resposta do servidor observado pelas Web Apps. Isto porque o Application Insights apenas conta a duração quando o pedido real atinge a aplicação do utilizador. Se o pedido estiver preso/em fila no IIS, esse tempo de espera será incluído nas métricas da Web App, mas não nas métricas de Insights de Aplicação.

## <a name="release-notes"></a>Notas de versão

Para obter as últimas atualizações e correções de erro [consulte as notas de lançamento](./web-app-extension-release-notes.md).

## <a name="next-steps"></a>Passos seguintes
* [Run the profiler on your live app](./profiler.md) (Executar o gerador de perfis na sua aplicação publicada).
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample) - monitorize Funções do Azure com o Application Insights
* [Ativar os diagnósticos do Azure](../agents/diagnostics-extension-to-application-insights.md) para serem enviados para o Application Insights.
* [Monitorizar as métricas de estado de funcionamento de serviço](../data-platform.md) para se certificar de que o serviço está disponível e a responder.
* [Receber notificações de alertas](../alerts/alerts-overview.md) sempre que ocorrem eventos operacionais ou quando as métricas ultrapassam um determinado limiar.
* Utilizar o [Application Insights para aplicações JavaScript e páginas Web](javascript.md) para obter telemetria de clientes a partir dos browsers que visitam as páginas Web.
* [Configurar os Testes Web de disponibilidade](monitor-web-app-availability.md) para ser alertado se o seu site estiver em baixo.
