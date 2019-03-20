---
title: Como gerir o seu ambiente do Azure Time Series Insights utilizando modelos Azure Resource Manager | Documentos da Microsoft
description: Este artigo descreve como gerir o seu ambiente do Azure Time Series Insights através de programação com o Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/08/2017
ms.custom: seodec18
ms.openlocfilehash: fe348daa4613e0b515244686e48ed63a41991d81
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009385"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Criar recursos do Time Series Insights com modelos Azure Resource Manager

Este artigo descreve como criar e implementar recursos de Time Series Insights com modelos do Azure Resource Manager, PowerShell e o fornecedor de recursos do Time Series Insights.

O Time Series Insights suporta os seguintes recursos:

   | Recurso | Descrição |
   | --- | --- |
   | Ambiente | Um ambiente do Time Series Insights é um agrupamento lógico de eventos que são ler a partir de mediadores de eventos, armazenados e disponibilizados para consulta. Para obter mais informações consulte [planejar seu ambiente do Azure Time Series Insights](time-series-insights-environment-planning.md) |
   | Origem do Evento | Uma origem de evento é uma ligação para um mediador de eventos a partir do qual o Time Series Insights lê e ingere eventos para o ambiente. Origens de eventos atualmente suportados são o IoT Hub e Hub de eventos. |
   | Conjunto de dados de referência | Conjuntos de dados de referência fornecem metadados sobre os eventos no ambiente. Metadados em conjuntos de dados de referência irão ser associado a eventos durante a entrada. Conjuntos de dados de referência são definidos como recursos por suas propriedades de chave do evento. Os metadados real que compõe o conjunto de dados de referência é carregado ou modificado por meio de APIs de plano de dados. |
   | Política de Acesso | Políticas de acesso concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e partilhar consultas guardadas e perspetivas associadas ao ambiente. Para obter mais informações consulte [conceder acesso a dados para um ambiente do Time Series Insights com o portal do Azure](time-series-insights-data-access.md) |

Um modelo do Resource Manager é um ficheiro JSON que define a infraestrutura e a configuração de recursos num grupo de recursos. Para obter mais informações, consulte os seguintes documentos:

- [Gestor de recursos descrição geral do Azure - implementação do modelo](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Tipos de recursos de Microsoft.TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

O [201-timeseriesinsights-ambiente-com-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) modelo de início rápido é publicado no GitHub. Este modelo cria um ambiente do Time Series Insights, uma fonte de eventos de subordinados configurado para consumir eventos a partir de um Hub de eventos e políticas que concedem acesso aos dados do ambiente de acesso. Se não for especificado um Hub de eventos existente, será criada com a implementação.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Implementar o modelo de início rápido com o PowerShell localmente

O procedimento seguinte descreve como utilizar o PowerShell para implementar um modelo do Azure Resource Manager que cria um ambiente do Time Series Insights, uma fonte de eventos de subordinados configurado para consumir eventos a partir de um Hub de eventos e políticas que concedem acesso a de acesso a dados do ambiente. Se não for especificado um Hub de eventos existente, será criada com a implementação.

O fluxo de trabalho aproximado é o seguinte:

1. Instale o PowerShell.
1. Crie o modelo e um ficheiro de parâmetros.
1. No PowerShell, inicie sessão na sua conta do Azure.
1. Se não existir, crie um novo grupo de recursos.
1. Teste a implantação.
1. Implemente o modelo.

### <a name="install-powershell"></a>Instalar o PowerShell

Instalar o Azure PowerShell, seguindo as instruções em [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Criar um modelo

Clone ou copie o [201-timeseriesinsights-ambiente-com-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) modelo a partir do GitHub.

### <a name="create-a-parameters-file"></a>Crie um ficheiro de parâmetros

Para criar um ficheiro de parâmetros, copiar os [201-timeseriesinsights-ambiente-com-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) ficheiro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Parâmetros necessários

   | Parâmetro | Descrição |
   | --- | --- |
   | eventHubNamespaceName | O espaço de nomes do hub de eventos de origem. |
   | eventHubName | O nome do hub de eventos de origem. |
   | consumerGroupName | O nome do grupo de consumidor que o serviço do Time Series Insights irá utilizar para ler os dados do hub de eventos. **NOTA:** Para evitar a contenção de recursos, este grupo de consumidor tem de ser dedicado para o serviço do Time Series Insights e não partilhado com outros leitores. |
   | environmentName | O nome do ambiente. O nome não pode incluir: ' <', ' >', '%', '&', ': ','\\','?', '/' e quaisquer carateres de controlo. Todos os outros carateres são permitidos.|
   | eventSourceName | O nome do recurso de subordinado de origem de evento. O nome não pode incluir: ' <', ' >', '%', '&', ': ','\\','?', '/' e quaisquer carateres de controlo. Todos os outros carateres são permitidos. |

#### <a name="optional-parameters"></a>Parâmetros opcionais

   | Parâmetro | Descrição |
   | --- | --- |
   | existingEventHubResourceId | Um ID de recurso opcional de um Hub de eventos existente que será ligado para o ambiente de Time Series Insights por meio de origem do evento. **NOTA:** O utilizador a implementar o modelo tem de ter privilégios para executar a operação de listkeys no Hub de eventos. Se nenhum valor for transmitido, será criado um novo hub de eventos pelo modelo. |
   | environmentDisplayName | Um nome amigável opcional para mostrar nas interfaces de utilizador ou as ferramentas em vez do nome de ambiente. |
   | environmentSkuName | O nome do SKU. Para obter mais informações, consulte a [página de preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | A capacidade de unidade do Sku. Para obter mais informações, consulte a [página de preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | O período de tempo mínimo eventos do ambiente estarão disponíveis para consulta. O valor tem de ser especificado no formato ISO 8601, por exemplo "P30D" para uma política de retenção de 30 dias. |
   | eventSourceDisplayName | Um nome amigável opcional para mostrar nas interfaces de utilizador ou as ferramentas em vez do nome de origem do evento. |
   | eventSourceTimestampPropertyName | A propriedade de evento que será utilizada como o timestamp a fonte de eventos. Se não for especificado um valor para timestampPropertyName, ou se null ou a cadeia vazia é especificada, será utilizada a hora de criação do evento. |
   | eventSourceKeyName | O nome da chave de acesso partilhado que o serviço do Time Series Insights irá utilizar para ligar ao hub de eventos. |
   | accessPolicyReaderObjectIds | Uma lista de IDs dos utilizadores ou aplicações no Azure AD que devem ter acesso de leitor ao ambiente de objetos. O objectId do principal de serviço pode ser obtido ao chamar o **Get-AzureRMADUser** ou o **Get-AzureRMADServicePrincipal** cmdlets. Ainda, a criar uma política de acesso para grupos do Azure AD não é suportada. |
   | accessPolicyContributorObjectIds | Uma lista de IDs dos utilizadores ou aplicações no Azure AD que devem ter acesso de Contribuidor para o ambiente de objetos. O objectId do principal de serviço pode ser obtido ao chamar o **Get-AzureRMADUser** ou o **Get-AzureRMADServicePrincipal** cmdlets. Ainda, a criar uma política de acesso para grupos do Azure AD não é suportada. |

Por exemplo, ficheiro de parâmetros seguintes seria usado para criar um ambiente e uma origem de evento que lê eventos a partir do hub de eventos existente. Ele também cria duas políticas de acesso que concedem acesso de contribuinte ao ambiente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

Para obter mais informações, consulte a [parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) artigo.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Inicie sessão no Azure e definir a subscrição do Azure

A partir de uma linha de comandos do PowerShell, execute o seguinte comando:

```powershell
Connect-AzureRmAccount
```

São-lhe pedido para iniciar sessão na sua conta do Azure. Após fazer logon, execute o seguinte comando para ver as subscrições disponíveis:

```powershell
Get-AzureRMSubscription
```

Este comando devolve uma lista de subscrições do Azure disponíveis. Escolha uma subscrição para a sessão atual ao executar o comando seguinte. Substitua `<YourSubscriptionId>` com o GUID da subscrição do Azure que pretende utilizar:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos

Se não tiver um recurso existente de grupo, criar um novo grupo de recursos com o **New-AzureRmResourceGroup** comando. Forneça o nome do grupo de recursos e localização que pretende utilizar. Por exemplo:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Se tiver êxito, é apresentado um resumo do novo grupo de recursos.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testar a implementação

Validar a sua implementação ao executar o `Test-AzureRmResourceGroupDeployment` cmdlet. Ao testar a implementação, fornece os parâmetros exatamente como faria quando executar a implantação.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Criar a implementação

Para criar a nova implementação, execute o `New-AzureRmResourceGroupDeployment` cmdlet e forneça os parâmetros necessários, quando lhe for pedido. Os parâmetros incluem um nome para a sua implementação, o nome do seu grupo de recursos e o caminho ou URL para o ficheiro de modelo. Se o **modo** parâmetro não for especificado, o valor predefinido de **Incremental** é utilizado. Para obter mais informações, consulte [Incremental e completas implementações](../azure-resource-manager/deployment-modes.md).

O seguinte comando pede-lhe os cinco parâmetros necessários na janela do PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Para especificar um ficheiro de parâmetros em vez disso, utilize o seguinte comando:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Também pode utilizar parâmetros inline ao executar o cmdlet de implementação. O comando é o seguinte:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar uma [concluída](../azure-resource-manager/deployment-modes.md) implantação, defina o **modo** parâmetro **concluída**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Verificar a implementação

Se os recursos são implementados com êxito, é apresentado um resumo da implementação na janela do PowerShell:

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Implementar o modelo de início rápido através do portal do Azure

Página inicial do modelo de início rápido no GitHub também inclui um **implementar no Azure** botão. Clicar nele abre uma página de implementação personalizada no portal do Azure. Nesta página, introduza ou selecione os valores para cada um dos parâmetros do [parâmetros obrigatórios](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) ou [parâmetros opcionais](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) tabelas. Depois de preencher as definições, clicar o **Compra** botão irá iniciar a implementação do modelo.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Passos Seguintes

- Para informações sobre como gerir programaticamente os recursos do Time Series Insights com as APIs REST, consulte [gerenciamento de informações de série de tempo](https://docs.microsoft.com/rest/api/time-series-insights-management/).
