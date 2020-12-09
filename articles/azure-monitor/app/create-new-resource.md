---
title: Criar um novo recurso Azure Application Insights / Microsoft Docs
description: Configurar manualmente a monitorização do Application Insights para uma nova aplicação ao vivo.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3fd05e6bd68be89b964fe1ad32029bf44f3352ea
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906374"
---
# <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

O Azure Application Insights exibe dados sobre a sua aplicação num *recurso* Microsoft Azure . A criação de um novo recurso faz, portanto, parte da criação de [Application Insights para monitorizar uma nova aplicação.][start] Depois de ter criado o seu novo recurso, pode obter a sua chave de instrumentação e usá-la para configurar o SDK Application Insights. A chave de instrumentação liga a sua telemetria ao recurso.

## <a name="sign-in-to-microsoft-azure"></a>Inscreva-se no Microsoft Azure

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Inscreva-se no [portal Azure](https://portal.azure.com)e crie um recurso Application Insights:

![Clique no sinal '+' no canto superior esquerdo. Selecione Ferramentas de Desenvolvimento seguidas por Insights de Aplicações](./media/create-new-resource/new-app-insights.png)

   | Definições        |  Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | `Unique value` | Nome que identifica a aplicação que está a monitorizar. |
   | **Grupo de Recursos**     | `myResourceGroup`      | Nome para o novo grupo de recursos ou existente para hospedar dados app Insights. |
   | **Região** | `East US` | Escolha um local perto de si ou perto do local onde a sua aplicação está hospedada. |
   | **Modo de Recurso** | `Classic` ou `Workspace-based` | Os recursos baseados no espaço de trabalho estão atualmente em pré-visualização pública e permitem-lhe enviar a sua telemetria Application Insights para um espaço de trabalho comum do Log Analytics. Para mais informações, consulte o [artigo sobre recursos baseados no espaço de trabalho.](create-workspace-resource.md)

> [!NOTE]
> Embora possa usar o mesmo nome de recurso em diferentes grupos de recursos, pode ser benéfico usar um nome globalmente único. Isto pode ser útil se planeia [realizar consultas de recursos cruzados,](../log-query/cross-workspace-query.md#identifying-an-application) uma vez que simplifica a sintaxe necessária.

Introduza os valores adequados nos campos necessários e, em seguida, selecione **Review + create**.

![Introduza valores em campos necessários e, em seguida, selecione "review + create".](./media/create-new-resource/review-create.png)

Quando a sua aplicação foi criada, abre-se um novo painel. Este painel é onde você vê dados de desempenho e utilização sobre a sua aplicação monitorizada. 

## <a name="copy-the-instrumentation-key"></a>Copiar a chave de instrumentação

A chave de instrumentação identifica o recurso com o que pretende associar os seus dados de telemetria. Terá de copiar a tecla de instrumentação e adicioná-la ao código da sua aplicação.

> [!IMPORTANT]
> As novas regiões de Azure **requerem** a utilização de cordas de ligação em vez de teclas de instrumentação. [A cadeia de ligação](./sdk-connection-string.md?tabs=net) identifica o recurso com o que pretende associar os seus dados de telemetria. Também permite modificar os pontos finais que o seu recurso utilizará como destino para a sua telemetria. Terá de copiar o fio de ligação e adicioná-lo ao código da sua aplicação ou a uma variável ambiental.

## <a name="install-the-sdk-in-your-app"></a>Instale o SDK na sua aplicação

Instale o App Insights SDK na sua aplicação. Este passo depende muito do tipo da sua aplicação.

Utilize a chave de instrumentação para configurar [o SDK que instala na sua aplicação][start].

O SDK inclui módulos padrão que enviam telemetria sem ter de escrever qualquer código adicional. Para acompanhar as ações do utilizador ou diagnosticar mais detalhadamente os problemas, [utilize a API][api] para enviar a sua própria telemetria.

## <a name="creating-a-resource-automatically"></a>Criar um recurso automaticamente

### <a name="powershell"></a>PowerShell

Criar um novo recurso Application Insights

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Exemplo

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Resultados

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Para obter a documentação completa da PowerShell para este cmdlet, e para aprender a recuperar a chave de instrumentação consulte a [documentação Azure PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0).

### <a name="azure-cli-preview"></a>Azure CLI (pré-visualização)

Para aceder aos comandos de Pré-visualização Do CLI do Application Insights, primeiro tem de executar:

```azurecli
 az extension add -n application-insights
```

Se não executar o `az extension add` comando, verá uma mensagem de erro que diz: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Agora pode executar o seguinte para criar o seu recurso Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Exemplo

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Resultados

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Para obter a documentação completa do Azure CLI para este comando, e para aprender a recuperar a chave de instrumentação consulte a documentação do [Azure CLI](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Passos seguintes
* [Pesquisa de Diagnóstico](./diagnostic-search.md)
* [Explorar métricas](../platform/metrics-charts.md)
* [Escrever consultas da Análise](../log-query/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../platform/metrics-charts.md
[start]: ./app-insights-overview.md

