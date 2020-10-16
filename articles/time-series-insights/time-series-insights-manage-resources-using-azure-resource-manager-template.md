---
title: Gerencie o seu ambiente utilizando modelos de Gestor de Recursos Azure - Azure Time Series Insights / Microsoft Docs
description: Aprenda a gerir o ambiente Azure Time Series Insights programático usando o Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: ee4d3957403e169d41fb9e3befa0d62e4b0d9075
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597854"
---
# <a name="create-azure-time-series-insights-gen-1-resources-using-azure-resource-manager-templates"></a>Criar recursos do Azure Time Series Insights Gen 1 utilizando modelos de Gestor de Recursos Azure

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo descreve como criar e implementar recursos da Série de Tempo Azure Insights utilizando [modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), PowerShell e o fornecedor de recursos Azure Time Series Insights.

A Azure Time Series Insights suporta os seguintes recursos:

   | Recurso | Descrição |
   | --- | --- |
   | Ambiente | Um ambiente Azure Time Series Insights é um agrupamento lógico de eventos que são lidos a partir de corretores de eventos, armazenados e disponibilizados para consulta. Para mais informações, leia [Planeie o seu ambiente Azure Time Series Insights](time-series-insights-environment-planning.md) |
   | Fonte de eventos | Uma fonte de evento é uma ligação a um corretor de eventos a partir do qual a Azure Time Series Insights lê e ingere eventos para o ambiente. Atualmente, as fontes de eventos apoiadas são o IoT Hub e o Event Hub. |
   | Conjunto de dados de referência | Conjuntos de dados de referência fornecem metadados sobre os eventos no ambiente. Os metadados nos conjuntos de dados de referência serão associados a eventos durante a entrada. Os conjuntos de dados de referência são definidos como recursos pelas suas propriedades-chave do evento. Os metadados reais que compõem o conjunto de dados de referência são carregados ou modificados através de APIs do plano de dados. |
   | Política de Acesso | As políticas de acesso autorizam a emissão de consultas de dados, manipulam dados de referência no ambiente e partilham consultas e perspetivas guardadas associadas ao ambiente. Para mais informações, leia [o acesso de dados do Grant a um ambiente Azure Time Series Insights utilizando o portal Azure](time-series-insights-data-access.md) |

Um modelo de Gestor de Recursos é um ficheiro JSON que define a infraestrutura e a configuração de recursos num grupo de recursos. Os seguintes documentos descrevem os ficheiros do modelo com maior detalhe:

- [Implementação do modelo do gestor de recursos Azure](../azure-resource-manager/templates/overview.md)
- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Microsoft.TimeSeriesInsights tipos de recursos](/azure/templates/microsoft.timeseriesinsights/allversions)

O modelo de quickstart [de 201 timeseries-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) é publicado no GitHub. Este modelo cria um ambiente Azure Time Series Insights, uma fonte de evento infantil configurada para consumir eventos a partir de um Centro de Eventos, e políticas de acesso que concedem acesso aos dados do ambiente. Se um Centro de Eventos existente não for especificado, um será criado com a implantação.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Especificar o modelo e os parâmetros de implantação

O procedimento que se segue descreve como usar o PowerShell para implementar um modelo de Gestor de Recursos Azure que cria um ambiente Azure Time Series Insights, uma fonte de evento infantil configurada para consumir eventos a partir de um Centro de Eventos, e políticas de acesso que concedem acesso aos dados do ambiente. Se um Centro de Eventos existente não for especificado, um será criado com a implantação.

1. Instale a Azure PowerShell seguindo as instruções em [Começar com a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

1. Clone ou copie o modelo [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) do GitHub.

   - Criar um ficheiro de parâmetros

     Para criar um ficheiro de parâmetros, copie o ficheiro [201-timeseriesinsights-environment-with-eventhub.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   - Parâmetros Obrigatórios

     | Parâmetro | Descrição |
     | --- | --- |
     | eventHubNamespaceName | O espaço de nome do centro de eventos de origem. |
     | eventHubName | O nome do centro de eventos de origem. |
     | nome do Grupo de Consumidores | O nome do grupo de consumidores que o serviço Azure Time Series Insights utilizará para ler os dados do centro de eventos. **NOTA:** Para evitar a contenção de recursos, este grupo de consumidores deve ser dedicado ao serviço Azure Time Series Insights e não partilhado com outros leitores. |
     | ambienteName | O nome do ambiente. O nome não pode incluir:   `<` , , , , , , , , , `>` e `%` `&` `:` `\\` `?` `/` quaisquer caracteres de controlo. Todos os outros carateres são permitidos.|
     | nome de EventoSource | O nome do recurso para crianças fonte do evento. O nome não pode incluir:   `<` , , , , , , , , , `>` e `%` `&` `:` `\\` `?` `/` quaisquer caracteres de controlo. Todos os outros carateres são permitidos. |

    <div id="optional-parameters"></div>

   - Parâmetros Opcionais

     | Parâmetro | Descrição |
     | --- | --- |
     | existenteEventHubResourceId | Um ID de recursos opcionais de um Centro de Eventos existente que será ligado ao ambiente Azure Time Series Insights através da fonte do evento. **NOTA:** O utilizador que implementa o modelo deve ter privilégios para executar a operação de listkeys no Centro de Eventos. Se nenhum valor for passado, um novo centro de eventos será criado pelo modelo. |
     | ambienteDisplayName | Um nome amigável opcional para mostrar em ferramentas ou interfaces de utilizador em vez do nome do ambiente. |
     | environmentSkuName | O nome do SKU. Para mais informações, leia a página de preços da [Série De Tempo Azure.](https://azure.microsoft.com/pricing/details/time-series-insights/)  |
     | environmentSkuCapacity | A capacidade unitária do Sku. Para mais informações, leia a página de preços da [Série De Tempo Azure.](https://azure.microsoft.com/pricing/details/time-series-insights/)|
     | ambienteDataRetentionTime | O período mínimo de tempo dos eventos do ambiente estará disponível para consulta. O valor deve ser especificado no formato ISO 8601, por exemplo `P30D` para uma política de retenção de 30 dias. |
     | nome de eventSourceDisplay | Um nome amigável opcional para mostrar em ferramentas ou interfaces de utilizador em vez do nome de origem do evento. |
     | eventSourceTimestampPropertyName | A propriedade do evento que será usada como o timetamp da fonte do evento. Se um valor não for especificado para o timetampPropertyName, ou se for especificado um fio nulo ou vazio, o tempo de criação do evento será utilizado. |
     | nome De EventSourceKey | O nome da chave de acesso partilhado que o serviço Azure Time Series Insights utilizará para ligar ao centro de eventos. |
     | accessPolicyReaderObjectIds | Uma lista de IDs de objetos dos utilizadores ou aplicações em Azure AD que devem ter acesso ao ambiente ao Leitor. O objectid principal de serviço pode ser obtido ligando para o **Get-AzADUser** ou para os cmdlets **Get-AzADServicePrincipal.** A criação de uma política de acesso aos grupos AD Azure ainda não é apoiada. |
     | acessoPolicyContributorObjectIds | Uma lista de IDs de objetos dos utilizadores ou aplicações em Azure AD que devem ter acesso ao ambiente. O objectid principal de serviço pode ser obtido ligando para o **Get-AzADUser** ou para os cmdlets **Get-AzADServicePrincipal.** A criação de uma política de acesso aos grupos AD Azure ainda não é apoiada. |

   - Como exemplo, o seguinte arquivo de parâmetros seria usado para criar um ambiente e uma fonte de eventos que lê eventos de um centro de eventos existente. Cria também duas políticas de acesso que concedem ao Contribuinte o acesso ao ambiente.

     ```JSON
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

   - Para mais informações, leia o artigo [Parâmetros.](../azure-resource-manager/templates/parameter-files.md)

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Implemente o modelo de arranque rápido localmente usando PowerShell

> [!IMPORTANT]
> As operações da linha de comando apresentadas abaixo descrevem o [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/).

1. Na PowerShell, faça login na sua conta Azure.

    - A partir de um pedido PowerShell, executar o seguinte comando:

      ```powershell
      Connect-AzAccount
      ```

    - É-lhe pedido que inicie sessão na sua conta Azure. Depois de iniciar sessão, execute o seguinte comando para visualizar as suas subscrições disponíveis:

      ```powershell
      Get-AzSubscription
      ```

    - Este comando devolve uma lista de subscrições disponíveis do Azure. Escolha uma subscrição para a sessão atual executando o seguinte comando. `<YourSubscriptionId>`Substitua-o pela assinatura Azure que pretende utilizar:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Criar um novo grupo de recursos se não existir.

   - Se não tiver um grupo de recursos existente, crie um novo grupo de recursos com o comando **New-AzResourceGroup.** Forneça o nome do grupo de recursos e a localização que pretende utilizar. Por exemplo:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   - Se for bem sucedido, é apresentado um resumo do novo grupo de recursos.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Teste a colocação.

   - Valide a sua implantação executando o `Test-AzResourceGroupDeployment` cmdlet. Ao testar a implementação, forneça parâmetros exatamente como faria ao executar a implementação.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Criar a implantação

    - Para criar a nova implantação, executar o `New-AzResourceGroupDeployment` cmdlet e fornecer os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para a sua implementação, o nome do seu grupo de recursos, e o caminho ou URL para o ficheiro do modelo. Se o parâmetro **Modo** não for especificado, é utilizado o valor predefinido de **Incremental.** Para mais informações, leia [implementações incrementais e completas.](../azure-resource-manager/templates/deployment-modes.md)

    - O seguinte comando solicita-lhe os cinco parâmetros necessários na janela PowerShell:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    - Para especificar um ficheiro de parâmetros, utilize o seguinte comando:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    - Também pode utilizar parâmetros de linha quando executar o cmdlet de implantação. O comando é o seguinte:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    - Para executar uma implementação [completa,](../azure-resource-manager/templates/deployment-modes.md) desa ajuste o parâmetro **de modo** para **completar:**

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Verificar a implementação

    - Se os recursos forem utilizados com sucesso, é apresentado um resumo da implantação na janela PowerShell:

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

1. Implemente o modelo de arranque rápido através do portal Azure

   - A página inicial do modelo de arranque rápido no GitHub também inclui um botão **Implementar para Azure.** Clicar nele abre uma página de Implementação personalizada no portal Azure. A partir desta página, pode introduzir ou selecionar valores para cada um dos parâmetros a partir dos [parâmetros ou tabelas](#required-parameters) [de parâmetros opcionais necessários.](#optional-parameters) Depois de preencher as definições, clicar no botão **'Comprar'** iniciará a implementação do modelo.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png" alt="The Deploy to Azure button."/>
    </a>

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a gestão programática dos recursos da Série de Tempo Azure Insights utilizando AS APIs do REST, leia [Azure Time Series Insights Management](https://docs.microsoft.com/rest/api/time-series-insights-management/).
