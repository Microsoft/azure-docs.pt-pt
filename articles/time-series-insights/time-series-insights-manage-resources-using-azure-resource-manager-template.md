---
title: Gerenciar seu ambiente usando modelos de Azure Resource Manager – Azure Time Series Insights | Microsoft Docs
description: Saiba como gerenciar seu ambiente de Azure Time Series Insights de forma programática usando Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/06/2019
ms.custom: seodec18
ms.openlocfilehash: 4edf5189b54a5b1fb1b953064c5db1cd50930b84
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452852"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Criar Time Series Insights recursos usando modelos de Azure Resource Manager

Este artigo descreve como criar e implantar Time Series Insights recursos usando [Azure Resource Manager modelos](https://docs.microsoft.com/azure/azure-resource-manager/), o PowerShell e o provedor de recursos do time Series insights.

O Time Series Insights dá suporte aos seguintes recursos:

   | Recurso | Descrição |
   | --- | --- |
   | Ambiente | Um ambiente de Time Series Insights é um agrupamento lógico de eventos que são lidos de agentes de evento, armazenados e disponibilizados para consulta. Para obter mais informações, consulte [planejar seu ambiente de Azure Time Series insights](time-series-insights-environment-planning.md) |
   | Origem do Evento | Uma origem de evento é uma conexão com um agente de evento a partir do qual Time Series Insights lê e insome eventos no ambiente. As fontes de eventos com suporte no momento são Hub IoT e Hub de eventos. |
   | Conjunto de dados de referência | Os conjuntos de dados de referência fornecem metadados sobre os eventos no ambiente. Os metadados nos conjuntos de dados de referência serão associados a eventos durante a entrada. Os conjuntos de dados de referência são definidos como recursos por suas propriedades de chave de evento. Os metadados reais que compõem o conjunto de dados de referência são carregados ou modificados por meio de APIs de plano de dados. |
   | Política de Acesso | As políticas de acesso concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e compartilhar consultas salvas e perspectivas associadas ao ambiente. Para obter mais informações, leia [conceder acesso a dados para um ambiente de time Series insights usando portal do Azure](time-series-insights-data-access.md) |

Um modelo do Resource Manager é um arquivo JSON que define a infraestrutura e a configuração de recursos em um grupo de recursos. Os documentos a seguir descrevem os arquivos de modelo com mais detalhes:

- [Implantação de modelo de Azure Resource Manager](../azure-resource-manager/template-deployment-overview.md)
- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Tipos de recurso Microsoft. TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

O modelo de início rápido [201-timeseriesinsights-ambiente-com-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) é publicado no github. Este modelo cria um ambiente de Time Series Insights, uma origem de evento filho configurada para consumir eventos de um hub de eventos e políticas de acesso que concedem acesso aos dados do ambiente. Se um hub de eventos existente não for especificado, um será criado com a implantação.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Especificar modelo de implantação e parâmetros

O procedimento a seguir descreve como usar o PowerShell para implantar um modelo de Azure Resource Manager que cria um ambiente de Time Series Insights, uma fonte de eventos filho configurada para consumir eventos de um hub de eventos e políticas de acesso que concedem acesso ao dados do ambiente. Se um hub de eventos existente não for especificado, um será criado com a implantação.

1. Instale Azure PowerShell seguindo as instruções em [introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

1. Clone ou copie o modelo [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) do github.

   * Criar um arquivo de parâmetros

     Para criar um arquivo de parâmetros, copie o arquivo [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) .

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Parâmetros Obrigatórios

     | Parâmetro | Descrição |
     | --- | --- |
     | eventHubNamespaceName | O namespace do hub de eventos de origem. |
     | eventHubName | O nome do hub de eventos de origem. |
     | consumerGroupName | O nome do grupo de consumidores que o serviço de Time Series Insights usará para ler os dados do hub de eventos. **Observação:** Para evitar a contenção de recursos, esse grupo de consumidores deve ser dedicado ao serviço de Time Series Insights e não compartilhado com outros leitores. |
     | environmentName | O nome do ambiente. O nome não pode incluir: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`e quaisquer caracteres de controle. Todos os outros carateres são permitidos.|
     | eventSourceName | O nome do recurso filho de origem do evento. O nome não pode incluir: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`e quaisquer caracteres de controle. Todos os outros carateres são permitidos. |

    <div id="optional-parameters"></div>

   * Parâmetros Opcionais

     | Parâmetro | Descrição |
     | --- | --- |
     | existingEventHubResourceId | Uma ID de recurso opcional de um hub de eventos existente que será conectada ao ambiente de Time Series Insights por meio da origem do evento. **Observação:** O usuário que está implantando o modelo deve ter privilégios para executar a operação listkeys no Hub de eventos. Se nenhum valor for passado, um novo hub de eventos será criado pelo modelo. |
     | environmentDisplayName | Um nome amigável opcional para mostrar em ferramentas ou interfaces do usuário em vez do nome do ambiente. |
     | environmentSkuName | O nome do SKU. Para obter mais informações, consulte a [página de preços do time Series insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | A capacidade da unidade do SKU. Para obter mais informações, consulte a [página de preços do time Series insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | O período mínimo em que os eventos do ambiente estarão disponíveis para consulta. O valor deve ser especificado no formato ISO 8601, por exemplo `P30D` para uma política de retenção de 30 dias. |
     | eventSourceDisplayName | Um nome amigável opcional para mostrar em ferramentas ou interfaces do usuário em vez do nome da origem do evento. |
     | eventSourceTimestampPropertyName | A propriedade de evento que será usada como o carimbo de data/hora da origem do evento. Se um valor não for especificado para timestampPropertyName, ou se NULL ou Empty-String for especificado, a hora de criação do evento será usada. |
     | eventSourceKeyName | O nome da chave de acesso compartilhado que o serviço de Time Series Insights usará para se conectar ao Hub de eventos. |
     | accessPolicyReaderObjectIds | Uma lista de IDs de objeto dos usuários ou aplicativos no Azure AD que devem ter acesso de leitor ao ambiente. O objectId da entidade de serviço pode ser obtido chamando o cmdlet **Get-AzADUser** ou **Get-AzADServicePrincipal** . Ainda não há suporte para a criação de uma política de acesso para grupos do Azure AD. |
     | accessPolicyContributorObjectIds | Uma lista de IDs de objeto dos usuários ou aplicativos no Azure AD que devem ter acesso de colaborador ao ambiente. O objectId da entidade de serviço pode ser obtido chamando o cmdlet **Get-AzADUser** ou **Get-AzADServicePrincipal** . Ainda não há suporte para a criação de uma política de acesso para grupos do Azure AD. |

   * Por exemplo, o arquivo de parâmetros a seguir seria usado para criar um ambiente e uma origem de evento que lê eventos de um hub de eventos existente. Ele também cria duas políticas de acesso que concedem acesso de colaborador ao ambiente.

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
  
    * Para obter mais informações, consulte o artigo [parâmetros](../azure-resource-manager/templates/parameter-files.md) .

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Implantar o modelo de início rápido localmente usando o PowerShell

> [!IMPORTANT]
> As operações de linha de comando exibidas abaixo descrevem o [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/overview).

1. No PowerShell, faça logon em sua conta do Azure.

    * Em um prompt do PowerShell, execute o seguinte comando:

      ```powershell
      Connect-AzAccount
      ```

    * Você será solicitado a fazer logon em sua conta do Azure. Depois de fazer logon, execute o seguinte comando para exibir suas assinaturas disponíveis:

      ```powershell
      Get-AzSubscription
      ```

    * Esse comando retorna uma lista de assinaturas do Azure disponíveis. Escolha uma assinatura para a sessão atual executando o comando a seguir. Substitua `<YourSubscriptionId>` pelo GUID da assinatura do Azure que você deseja usar:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Crie um novo grupo de recursos se ele não existir.

   * Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos com o comando **New-AzResourceGroup** . Forneça o nome do grupo de recursos e o local que você deseja usar. Por exemplo:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Se for bem-sucedido, será exibido um resumo do novo grupo de recursos.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Teste a implantação.

   * Valide sua implantação executando o cmdlet `Test-AzResourceGroupDeployment`. Ao testar a implantação, forneça parâmetros exatamente como você faria ao executar a implantação.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Criar a implantação

    * Para criar a nova implantação, execute o cmdlet `New-AzResourceGroupDeployment` e forneça os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos e o caminho ou a URL para o arquivo de modelo. Se o parâmetro **Mode** não for especificado, o valor padrão de **incremental** será usado. Para obter mais informações, consulte [implantações incrementais e completas](../azure-resource-manager/deployment-modes.md).

    * O comando a seguir solicita os cinco parâmetros necessários na janela do PowerShell:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
      ```

    * Para especificar um arquivo de parâmetros em vez disso, use o seguinte comando:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Você também pode usar parâmetros embutidos ao executar o cmdlet de implantação. O comando é o seguinte:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Para executar uma implantação [completa](../azure-resource-manager/deployment-modes.md) , defina o parâmetro de **modo** como **concluído**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Verificar a implementação

    * Se os recursos forem implantados com êxito, um resumo da implantação será exibido na janela do PowerShell:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Implantar o modelo de início rápido por meio do portal do Azure

   * O home page do modelo de início rápido no GitHub também inclui um botão **implantar no Azure** . Clicar nele abre uma página de implantação personalizada na portal do Azure. Nessa página, você pode inserir ou selecionar valores para cada um dos parâmetros dos [parâmetros necessários](#required-parameters) ou tabelas de [parâmetros opcionais](#optional-parameters) . Depois de preencher as configurações, clicar no botão **comprar** iniciará a implantação do modelo.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre como gerenciar programaticamente Time Series Insights recursos usando APIs REST, consulte [Gerenciamento de time Series insights](https://docs.microsoft.com/rest/api/time-series-insights-management/).
