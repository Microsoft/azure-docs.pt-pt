---
title: Criar um novo recurso do insights Aplicativo Azure | Microsoft Docs
description: Configurar manualmente o monitoramento de Application Insights para um novo aplicativo ao vivo.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/02/2019
ms.openlocfilehash: 1ba6d6e1774db4e9c95b107e1482a0c6ce532bb5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432621"
---
# <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Aplicativo Azure insights exibe dados sobre seu aplicativo em um *recurso*de Microsoft Azure. A criação de um novo recurso, portanto, faz parte da [configuração de Application insights para monitorar um novo aplicativo][start]. Depois de criar o novo recurso, você pode obter sua chave de instrumentação e usá-la para configurar o SDK do Application Insights. A chave de instrumentação vincula sua telemetria ao recurso.

## <a name="sign-in-to-microsoft-azure"></a>Iniciar sessão no Microsoft Azure

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Entre no [portal do Azure](https://portal.azure.com)e crie um recurso de Application insights:

![Clique no sinal ' + ' no canto superior esquerdo. Selecionar Ferramentas para Desenvolvedores seguido por Application Insights](./media/create-new-resource/new-app-insights.png)

   | Definições        |  Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor exclusivo | Nome que identifica o aplicativo que você está monitorando. |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do grupo de recursos novo ou existente para hospedar dados do App insights. |
   | **Localização** | E.U.A. Leste | Escolha um local perto de você ou próximo do local em que seu aplicativo está hospedado. |

> [!NOTE]
> Embora você possa usar o mesmo nome de recurso em grupos de recursos diferentes, pode ser benéfico usar um nome globalmente exclusivo. Isso pode ser útil se você planeja [executar consultas entre recursos](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) , pois ele simplifica a sintaxe necessária.

Insira os valores apropriados nos campos obrigatórios e, em seguida, selecione **revisar + criar**.

![Insira valores em campos obrigatórios e, em seguida, selecione "revisar + criar".](./media/create-new-resource/review-create.png)

Quando seu aplicativo tiver sido criado, um novo painel será aberto. Esse painel é onde você vê dados de uso e desempenho sobre seu aplicativo monitorado. 

## <a name="copy-the-instrumentation-key"></a>Copiar a chave de instrumentação

A chave de instrumentação identifica o recurso ao qual você deseja associar os dados de telemetria. Será necessário copiar para adicionar a chave de instrumentação ao código do aplicativo.

![Clique e copie a chave de instrumentação](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Instalar o SDK em seu aplicativo

Instale o SDK do Application Insights em seu aplicativo. Essa etapa depende muito do tipo de seu aplicativo.

Use a chave de instrumentação para configurar [o SDK que você instala em seu aplicativo][start].

O SDK inclui módulos padrão que enviam telemetria sem a necessidade de escrever nenhum código adicional. Para acompanhar ações do usuário ou diagnosticar problemas mais detalhadamente, [use a API][api] para enviar sua própria telemetria.

## <a name="creating-a-resource-automatically"></a>Criando um recurso automaticamente

### <a name="powershell"></a>PowerShell

Criar um novo recurso de Application Insights

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

Para obter a documentação completa do PowerShell para esse cmdlet e saber como recuperar a chave de instrumentação, consulte a [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0).

### <a name="azure-cli-preview"></a>CLI do Azure (versão prévia)

Para acessar a visualização Application Insights CLI do Azure comandos que você precisa primeiro executar:

```azurecli
 az extension add -n application-insights
```

Se você não executar o comando `az extension add`, verá uma mensagem de erro que diz: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Agora você pode executar o seguinte para criar o recurso de Application Insights:

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

Para obter a documentação completa de CLI do Azure para esse comando e saber como recuperar a chave de instrumentação, consulte a [documentação do CLI do Azure](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Passos seguintes
* [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Explorar métricas](../../azure-monitor/app/metrics-explorer.md)
* [Escrever consultas da Análise](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md
