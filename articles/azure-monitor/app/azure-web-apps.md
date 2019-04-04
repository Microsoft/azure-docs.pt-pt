---
title: Monitorizar o desempenho de serviços de aplicações do Azure | Documentos da Microsoft
description: Desempenho de aplicações, monitorização dos serviços de aplicações do Azure. Gráfico de carga e tempo de resposta, informações de dependência e alertas sobre o desempenho.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: mbullwin
ms.openlocfilehash: 0c6be20bfb2a6f15335564a1aa98dc0ac88e3507
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905839"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorizar o desempenho do serviço de aplicações do Azure

Ativar a monitorização, no .NET e .NET Core, aplicativos web com base em execução nos serviços de aplicações do Azure agora é mais fácil do que nunca. Enquanto que anteriormente precisava instalar manualmente uma extensão de site, o agente/extensão mais recente foi agora integrado à imagem do serviço de aplicações por predefinição. Este artigo irá guiá-lo a ativar a monitorização do Application Insights, bem como fornecer orientações preliminar para automatizar o processo para implementações em larga escala.

> [!NOTE]
> Adicionar manualmente uma extensão de site do Application Insights através de **ferramentas de desenvolvimento** > **extensões** foi preterido. A versão estável mais recente da extensão já está [pré-instalado](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte da imagem do serviço de aplicações. Os ficheiros estão localizados no `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` e são atualizadas automaticamente com cada versão estável. Se seguir as instruções do agente com base para ativar a monitorização abaixo, este irá automaticamente remover a extensão preterida para.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enable-application-insights"></a>Ativar o Application Insights

Existem duas formas de ativar a monitorização de aplicações para aplicações de serviços de aplicações do Azure alojado:

* **Monitorização de aplicações com base em agente** (ApplicationInsightsAgent).  
    * Este método é mais fácil ativar e é necessária nenhuma configuração avançada. Ele é frequentemente referido como "runtime" monitorização. Para serviços de aplicações do Azure Recomendamos pelo menos ativar esse nível de monitorização e, em seguida, com base no seu cenário específico, que pode avaliar se a monitorização mais avançada através de instrumentação manual é necessária.

* **Manualmente instrumentar o aplicativo por meio de código** ao instalar o SDK do Application Insights.

    * Essa abordagem é muito mais personalizável, mas isso exige [adicionando uma dependência nos pacotes NuGet do SDK do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Este método, também significa que tem de gerir as atualizações para a versão mais recente dos pacotes por conta própria.

    * Se precisar de efetuar chamadas de API personalizadas para controlar eventos/dependências, não é capturadas por padrão com monitorização baseada em agente, seria necessário utilizar este método. Veja a [API para o artigo de métricas e eventos personalizado](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) para saber mais.

> [!NOTE]
> Se a monitorização de agentes com base e manual SDK com base em instrumentação é detetado que apenas as definições de instrumentação manual serão cumpridas. Este procedimento impede que os dados duplicados do enviados. Para saber mais sobre este check-out do [secção de resolução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) abaixo.

## <a name="enable-agent-based-monitoring-net"></a>Ativar com base em agente de monitorização .NET

1. **Selecione o Application Insights** no painel de controlo do Azure para o serviço de aplicações.

    ![Em definições, escolha Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que já configurou a um recurso do Application Insights para esta aplicação. 

     > [!NOTE]
     > Quando clica em **OK** para criar o novo recurso, será solicitado a **aplicar definições de monitorização**. Selecionando **continuar** irá ligar o seu novo recurso do Application Insights ao seu serviço de aplicações, fazendo assim, será também **acionar um reinício do serviço de aplicações**. 

     ![Instrumente a sua aplicação Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar o recurso a utilizar, pode escolher como pretende que o application insights para recolher dados por plataforma para a sua aplicação. Monitorização de aplicações do ASP.NET é por padrão com dois níveis diferentes de coleção.

    ![Escolher opções por plataforma](./media/azure-web-apps/choose-options-new.png)

   * .NET **conjunto básico** nível oferece funcionalidades essenciais de APM de instância única.

   * .NET **recomendado coleção** nível:
       * Adiciona as tendências de utilização de CPU, memória e e/s.
       * Correlaciona microsserviços em limites de pedido/dependência.
       * Recolhe as tendências de utilização e permite a correlação de resultados de disponibilidade para transações.
       * Recolhe as exceções não processadas pelo processo de host.
       * Melhora a precisão de métricas APM sob carga, quando a amostragem é usada.

3. Para configurar definições como a amostragem, o que pode controlar anteriormente através do ficheiro applicationinsights config agora podem interagir com essas mesmas definições através das definições de aplicação com um prefixo correspondente. 

    * Por exemplo, para alterar a percentagem de amostragem inicial, pode criar uma definição da aplicação de: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` e um valor de `100`.

    * Para a lista de definições de processador de telemetria de amostragem adaptável suportados, consulte a [código](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) e [associado à documentação](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Ativar com base em agente de monitorização .NET Core

São suportadas as seguintes versões do .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

Direcionando o framework completo do .NET Core, a implementação independente e ASP.NET Core 3.0 estão atualmente **nepodporuje** com a monitorização do agente/extensão com base. ([Instrumentação manual](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) por meio do código funcionará em todos os cenários anteriores.)

1. **Selecione o Application Insights** no painel de controlo do Azure para o serviço de aplicações.

    ![Em definições, escolha Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que já configurou a um recurso do Application Insights para esta aplicação. 

     > [!NOTE]
     > Quando clica em **OK** para criar o novo recurso, será solicitado a **aplicar definições de monitorização**. Selecionando **continuar** irá ligar o seu novo recurso do Application Insights ao seu serviço de aplicações, fazendo assim, será também **acionar um reinício do serviço de aplicações**. 

     ![Instrumente a sua aplicação Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar o recurso a utilizar, pode escolher como pretende que o Application Insights para recolher dados por plataforma para a sua aplicação. .NET core oferece **recomendado coleção** ou **desativado** para .NET Core 2.0, 2.1 e 2.2.

    ![Escolher opções por plataforma](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Ative o .NET de monitorização do lado do cliente

Monitorização do lado do cliente é uma opção para o ASP.NET. Para ativar a monitorização do lado do cliente:

* Selecione **definições** > * * * * as definições de aplicação ***
   * Em definições de aplicação, adicionar um novo **nome da definição de aplicação** e **valor**:

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `true`

   * **Guarde** as definições e **reinicie** a aplicação.

![Captura de ecrã da aplicação definições da interface do Usuário](./media/azure-web-apps/appinsights-javascript-enabled.png)

Para desativar a monitorização de remover o par de valor da chave associado das definições da aplicação de lado de cliente ou defina o valor como false.

## <a name="enable-client-side-monitoring-net-core"></a>Ativar o .NET Core de monitorização do lado do cliente

Monitorização do lado do cliente é **ativada por predefinição** para aplicações de .NET Core com **recomendado coleção**, independentemente de se a 'APPINSIGHTS_JAVASCRIPT_ENABLED' de definição de aplicação está presente.

Se por algum motivo desejar desativar a monitorização do lado do cliente:

* Selecione **configurações** > **as definições da aplicação**
   * Em definições de aplicação, adicionar um novo **nome da definição de aplicação** e **valor**:

     name: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `false`

   * **Guarde** as definições e **reinicie** a aplicação.

![Captura de ecrã da aplicação definições da interface do Usuário](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatizar a monitorização

Para ativar a recolha de telemetria com o Application Insights, apenas as definições de aplicação tem de ser definido:

   ![Definições de aplicação do serviço de aplicações com as definições disponíveis do Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definições de definições de aplicação

|Nome da definição de aplicação |  Definição | Valor |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensão principal, que controla a monitorização do tempo de execução. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Em default funcionalidades em modo apenas, essencial estão ativadas e assim garantir um desempenho ideal. | `default` Ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla se o motor de reescrita de binário `InstrumentationEngine` será ativado. Esta definição tem implicações de desempenho e tem impacto sobre a hora de início/inicialização a frio. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controles se o texto de tabela SQL e no Azure serão capturados, juntamente com as chamadas de dependência. Aviso de desempenho: esta definição exige que o `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Definições de aplicação de serviço de aplicações com o Azure Resource Manager

Podem ser geridas e configuradas com as definições da aplicação para os serviços aplicacionais [modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Este método pode ser utilizado durante a implantação de novos recursos do serviço de aplicações com a automatização do Azure Resource Manager ou para modificar as definições dos recursos existentes.

A estrutura básica das definições de aplicação JSON para um serviço de aplicações está ilustrado abaixo:

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

Para obter um exemplo de um modelo Azure Resource Manager com as definições de aplicação configurado para o Application Insights, isso [modelo](https://github.com/Andrew-MSFT/BasicImageGallery) pode ser útil, especificamente a partir da secção [linha 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatize a criação de um recurso do Application Insights e uma ligação para o serviço de aplicações criada recentemente.

Para criar um modelo Azure Resource Manager com todas as configurações de Application Insights padrão configuradas, iniciar o processo como se fosse criar uma nova aplicação Web com o Application Insights ativado.

Selecione **opções de automatização**

   ![Menu de criação de aplicações do serviço de aplicações web](./media/azure-web-apps/create-web-app.png)

Esta opção gera o modelo mais recente do Azure Resource Manager com todas as definições necessárias configuradas.

  ![Modelo de aplicação do serviço de aplicações web](./media/azure-web-apps/arm-template.png)

Abaixo está um exemplo, substitua todas as instâncias de `AppMonitoredSite` com o nome do site:

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
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> O modelo irá gerar definições da aplicação no modo "predefinido". Esse modo está desempenho otimizado, embora pode modificar o modelo para ativar qualquer que seja funcionalidades que preferir.

### <a name="enabling-through-powershell"></a>Ativar através do PowerShell

Para ativar a monitorização através do PowerShell da aplicação, apenas as configurações de aplicativo subjacente tem de ser alterado. Segue-se um exemplo, que permite a monitorização de aplicações para um Web site chamado "AppMonitoredSite" no grupo de recursos "AppMonitoredRG", e configura os dados sejam enviados para a chave de instrumentação "012345678-abcd-ef01-2345-6789abcd".

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Atualizar extensão/agente de monitorização

### <a name="upgrading-from-versions-289-and-up"></a>Atualizando de versões 2.8.9 e cópia de segurança

Atualização de versão 2.8.9 ocorre automaticamente, sem nenhuma ação adicional. Os bits de monitorização novos são fornecidos em segundo plano para o serviço de aplicações de destino e no reinício da aplicação irá ser captadas.

Para verificar qual é a versão da extensão estiver a executar visita `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Captura de ecrã do caminho do url http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Atualizar das versões 1.0.0 - 2.6.5

A partir da versão 2.8.9 é utilizada a extensão de site previamente instalado. Se é uma versão anterior, pode atualizar através de uma das seguintes formas:

* [Atualização ao ativar através do portal](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Mesmo que tenha a extensão do Application Insights para o serviço de aplicações do Azure instalada, a interface do Usuário mostra apenas **ativar** botão. Nos bastidores, a extensão de sites privados antigas será removida.)

* [Atualização através do PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Configure as definições de aplicação para ativar a extensão de site previamente instalado ApplicationInsightsAgent. Ver [ativação através do powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Remova manualmente a extensão de site privada com o nome extensão do Application Insights para o serviço de aplicações do Azure.

Se a atualização é feita a partir de uma versão anterior 2.5.1, verifique que as dlls ApplicationInsigths são removidas a partir da pasta bin do aplicativo [ver passos de resolução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Resolução de problemas

Segue-se nosso guia de resolução de problemas passo a passo para monitorizar/agente de extensão com base em .NET e .NET Core com base em aplicações em execução nos serviços de aplicações do Azure.

> [!NOTE]
> Aplicações de Java e node. js só são suportadas nos serviços de aplicações do Azure através de instrumentação do SDK com base em manual e, portanto, os passos abaixo não se aplicam a esses cenários.

1. Verifique se a aplicação é monitorizada por meio de `ApplicationInsightsAgent`.
    * Verifique se `ApplicationInsightsAgent_EXTENSION_VERSION` definição de aplicação está definida como um valor de "~ 2".
2. Certifique-se de que a aplicação cumpre os requisitos a serem monitoradas.
    * Navegar para `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Captura de ecrã do https://yoursitename.scm.azurewebsites/applicationinsights página de resultados](./media/azure-web-apps/app-insights-sdk-status.png)

    * Confirme que o `Application Insights Extension Status` é `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Se não está em execução, siga o [Ativar monitorização instruções do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Confirme que a origem de estado existe e é semelhante a: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se um valor semelhante não estiver presente, significa que a aplicação não está a ser executado ou não é suportada. Para garantir que a aplicação está em execução, tente manualmente visitar os url/aplicação pontos finais da aplicação, que vão permitir que as informações de tempo de execução para se tornarem disponíveis.

    * Confirme que `IKeyExists` é `true`
        * Se for falsa, adicione "APPINSIGHTS_INSTRUMENTATIONKEY com o guid de ikey para as definições da aplicação.

    * Confirme que não há nenhuma entrada para `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, e `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Se existir alguma dessas entradas, remover os seguintes pacotes da sua aplicação: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, e `Microsoft.AspNet.TelemetryCorrelation`.

A tabela abaixo fornece uma explicação mais detalhada sobre o que significa que estes valores, seus subjacente faz com que e recomendações de correção:

|Valor de problema|Explicação|Corrigir
|---- |----|---|
| `AppAlreadyInstrumented:true` | Este valor indica que a extensão detetou que algum aspecto do SDK já se encontra presente no aplicativo e será retirada. Tal pode dever-se uma referência a `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, ou `Microsoft.ApplicationInsights`  | Remova as referências. Algumas dessas referências são adicionadas, por predefinição, a partir de determinados modelos do Visual Studio e as versões mais antigas do Visual Studio podem adicionar referências aos `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Este valor também pode ser causado pela presença das dlls acima na pasta de aplicação de uma implementação anterior. | Limpe a pasta de aplicação para se certificar de que essas dlls são removidos.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Este valor indica que a extensão detetou referências a `Microsoft.AspNet.TelemetryCorrelation` no aplicativo e será retirada. | Remova a referência.
|`AppContainsDiagnosticSourceAssembly**:true`|Este valor indica que a extensão detetou referências a `System.Diagnostics.DiagnosticSource` no aplicativo e será retirada.| Remova a referência.
|`IKeyExists:false`|Este valor indica que a chave de instrumentação não está presente no AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Causas possíveis: Os valores podem ter sido removidas acidentalmente, Esqueceu-se de definir os valores no script de automação, etc. | Certifique-se de que a definição está presente nas definições da aplicação de serviço de aplicações.

Para as últimas informações sobre o Application Insights/extensão do agente, consulte a [notas de versão](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Passos Seguintes
* [Run the profiler on your live app](../app/profiler.md) (Executar o gerador de perfis na sua aplicação publicada).
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample) - monitorize Funções do Azure com o Application Insights
* [Ativar os diagnósticos do Azure](../platform/diagnostics-extension-to-application-insights.md) para serem enviados para o Application Insights.
* [Monitorizar as métricas de estado de funcionamento de serviço](../platform/data-platform.md) para se certificar de que o serviço está disponível e a responder.
* [Receber notificações de alertas](../platform/alerts-overview.md) sempre que ocorrem eventos operacionais ou quando as métricas ultrapassam um determinado limiar.
* Utilizar o [Application Insights para aplicações JavaScript e páginas Web](javascript.md) para obter telemetria de clientes a partir dos browsers que visitam as páginas Web.
* [Configurar os Testes Web de disponibilidade](monitor-web-app-availability.md) para ser alertado se o seu site estiver em baixo.
