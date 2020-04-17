---
title: Gerencie o seu ambiente utilizando modelos de Gestor de Recursos Azure - Azure Time Series Insights [ Microsoft Docs
description: Aprenda a gerir o seu ambiente Azure Time Series Insights programáticamente utilizando o Gestor de Recursos Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: a670e32058794daeaa233464ba7d054f45ef25e3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536323"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Criar recursos de Insights da Série de Tempo usando modelos de Gestor de Recursos Azure

Este artigo descreve como criar e implementar recursos da Time Series Insights utilizando modelos de [Gestor de Recursos Azure,](https://docs.microsoft.com/azure/azure-resource-manager/)PowerShell e o fornecedor de recursos Time Series Insights.

Time Series Insights suporta os seguintes recursos:

   | Recurso | Descrição |
   | --- | --- |
   | Ambiente | Um ambiente time series Insights é um agrupamento lógico de eventos que são lidos a partir de corretores de eventos, armazenados e disponibilizados para consulta. Para mais informações, leia [Planifique o seu ambiente DeInsights](time-series-insights-environment-planning.md) da Série De Tempo Azure |
   | Fonte de Evento | Uma fonte de evento é uma ligação a um corretor de eventos a partir do qual time Series Insights lê e ingere eventos no ambiente. Atualmente, fontes de eventos apoiadas são IoT Hub e Event Hub. |
   | Conjunto de dados de referência | Os conjuntos de dados de referência fornecem metadados sobre os eventos no ambiente. Os metadados nos conjuntos de dados de referência serão acompanhados de eventos durante a entrada. Os conjuntos de dados de referência são definidos como recursos pelas suas propriedades chave de eventos. Os metadados reais que compõem o conjunto de dados de referência são carregados ou modificados através de APIs de plano de dados. |
   | Política de Acesso | As políticas de acesso concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e partilhar consultas e perspetivas guardadas associadas ao ambiente. Para mais informações, leia [grant acesso de dados a um ambiente Time Series Insights usando o portal Azure](time-series-insights-data-access.md) |

Um modelo de Gestor de Recursos é um ficheiro JSON que define a infraestrutura e a configuração de recursos num grupo de recursos. Os seguintes documentos descrevem os ficheiros de modelo com maior detalhe:

- [Implantação do modelo do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)
- [Implementar recursos com modelos do Resource Manager e do Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Microsoft.TimeSeriesInsights tipos de recursos](/azure/templates/microsoft.timeseriesinsights/allversions)

O modelo de quickstart quickstart [de 201 timeseries-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) é publicado no GitHub. Este modelo cria um ambiente time series insights, uma fonte de evento infantil configurada para consumir eventos a partir de um Hub de Eventos, e políticas de acesso que concedem acesso aos dados do ambiente. Se um Hub de Eventos existente não for especificado, um será criado com a implementação.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Especificar modelo e parâmetros de implementação

O procedimento seguinte descreve como usar o PowerShell para implementar um modelo de Gestor de Recursos Azure que cria um ambiente Time Series Insights, uma fonte de evento infantil configurada para consumir eventos a partir de um Hub de Eventos, e políticas de acesso que dão acesso aos dados do ambiente. Se um Hub de Eventos existente não for especificado, um será criado com a implementação.

1. Instale o Azure PowerShell seguindo as instruções em [Iniciar-se com a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

1. Clone ou copie o modelo [201 timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) do GitHub.

   * Criar um ficheiro de parâmetros

     Para criar um ficheiro de parâmetros, copie o ficheiro [201 timeseriesinsights-environment-with-eventhub.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Parâmetros Obrigatórios

     | Parâmetro | Descrição |
     | --- | --- |
     | eventHubNamespaceName | O espaço de nome do centro de eventos de origem. |
     | eventHubName | O nome do centro de eventos de origem. |
     | consumerGroupName | O nome do grupo de consumidores que o serviço Time Series Insights utilizará para ler os dados do centro do evento. **NOTA:** Para evitar a contenção de recursos, este grupo de consumidores deve dedicar-se ao serviço Time Series Insights e não partilhado com outros leitores. |
     | nome ambiente | O nome do ambiente. O nome não `<` `>`pode `%` `&`incluir: , , `:`, `\\`, , `?`, `/`e quaisquer caracteres de controlo. Todos os outros carateres são permitidos.|
     | eventoSourceName | O nome do recurso infantil fonte do evento. O nome não `<` `>`pode `%` `&`incluir: , , `:`, `\\`, , `?`, `/`e quaisquer caracteres de controlo. Todos os outros carateres são permitidos. |

    <div id="optional-parameters"></div>

   * Parâmetros Opcionais

     | Parâmetro | Descrição |
     | --- | --- |
     | existenteEventHubResourceId | Um ID de recurso opcional de um Hub de Eventos existente que será conectado ao ambiente Time Series Insights através da fonte do evento. **NOTA:** O utilizador que implementa o modelo deve ter privilégios para executar a operação de listkeys no Centro de Eventos. Se nenhum valor for passado, um novo hub de evento será criado pelo modelo. |
     | ambienteDisplayName | Um nome amigável opcional para mostrar nas interfaces de ferramentas ou utilizadores em vez do nome ambiente. |
     | ambienteSkuName | O nome do SKU. Para mais informações, leia a página de preços da [Série Time Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | ambienteSkuCapacity | A capacidade unitária do Sku. Para mais informações, leia a página de preços da [Série Time Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | ambienteDataRetentionTime | Os tempos mínimos que os eventos ambientais estarão disponíveis para consulta. O valor deve ser especificado no formato ISO `P30D` 8601, por exemplo, para uma política de retenção de 30 dias. |
     | eventoSourceDisplayName | Um nome amigável opcional para mostrar nas interfaces de ferramentas ou utilizadores em vez do nome de origem do evento. |
     | eventoSourceTimestampPropertyName | A propriedade do evento que será usada como carimbo de tempo da fonte do evento. Se não for especificado um valor para o timestampPropertyName, ou se for especificado o fio nulo ou vazio, o tempo de criação do evento será utilizado. |
     | eventoSourceKeyName | O nome da chave de acesso partilhado que o serviço Time Series Insights utilizará para se ligar ao centro do evento. |
     | acessoPolicyReaderObjectIds | Uma lista de IDs de objetos dos utilizadores ou aplicações em Azure AD que devem ter o leitor de acesso ao ambiente. O objectid principal do serviço pode ser obtido ligando para o **Get-AzADUser** ou para os cmdlets **Get-AzADServicePrincipal.** A criação de uma política de acesso aos grupos AD Azure ainda não é apoiada. |
     | acessoPolicyContributorObjectIds | Uma lista de IDs de objetos dos utilizadores ou aplicações em AD Azure que devem ter acesso ao ambiente. O objectid principal do serviço pode ser obtido ligando para o **Get-AzADUser** ou para os cmdlets **Get-AzADServicePrincipal.** A criação de uma política de acesso aos grupos AD Azure ainda não é apoiada. |

   * Como exemplo, o ficheiro de parâmetros seguintes seria usado para criar um ambiente e uma fonte de evento que lê eventos a partir de um centro de eventos existente. Cria também duas políticas de acesso que concedem ao Contribuinte o acesso ao ambiente.

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

    * Para mais informações, leia o artigo [Parâmetros.](../azure-resource-manager/templates/parameter-files.md)

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Implemente o modelo de arranque rápido localmente usando powerShell

> [!IMPORTANT]
> As operações de linha de comando apresentadas abaixo descrevem o [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/overview).

1. Na PowerShell, inicie sessão na sua conta Azure.

    * A partir de um pedido powerShell, executar o seguinte comando:

      ```powershell
      Connect-AzAccount
      ```

    * É-lhe pedido que aceda à sua conta Azure. Depois de iniciar sessão, execute o seguinte comando para ver as subscrições disponíveis:

      ```powershell
      Get-AzSubscription
      ```

    * Este comando devolve uma lista de subscrições azure disponíveis. Escolha uma subscrição para a sessão atual executando o seguinte comando. Substitua-a `<YourSubscriptionId>` pelo GUID para a subscrição Azure que pretende utilizar:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Crie um novo grupo de recursos se não existir.

   * Se não tiver um grupo de recursos existente, crie um novo grupo de recursos com o comando **New-AzResourceGroup.** Forneça o nome do grupo de recursos e localização que pretende utilizar. Por exemplo:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Se for bem sucedido, é apresentado um resumo do novo grupo de recursos.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Teste a implantação.

   * Valide a sua `Test-AzResourceGroupDeployment` implantação executando o cmdlet. Ao testar a implementação, forneça parâmetros exatamente como faria ao executar a implementação.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Criar a implantação

    * Para criar a nova `New-AzResourceGroupDeployment` implantação, corra o cmdlet e forneça os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para a sua implementação, o nome do seu grupo de recursos, e o caminho ou URL para o ficheiro do modelo. Se **o** parâmetro modo não for especificado, o valor padrão do **Incremental** é utilizado. Para mais informações, leia [As implementações Incrementais e completas.](../azure-resource-manager/templates/deployment-modes.md)

    * O seguinte comando solicita-lhe os cinco parâmetros necessários na janela PowerShell:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    * Para especificar um ficheiro de parâmetros, utilize o seguinte comando:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Também pode utilizar parâmetros de linha quando executa o cmdlet de implantação. O comando é o seguinte:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Para executar uma implementação [completa,](../azure-resource-manager/templates/deployment-modes.md) defina **o** parâmetro modo para **completar:**

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Verificar a implementação

    * Se os recursos forem implantados com sucesso, um resumo da implementação é apresentado na janela PowerShell:

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

1. Implementar o modelo de arranque rápido através do portal Azure

   * A página inicial do modelo quickstart no GitHub também inclui um botão **Deploy para Azure.** Clicar nele abre uma página de implementação personalizada no portal Azure. A partir desta página, pode introduzir ou selecionar valores para cada um dos parâmetros a partir dos [parâmetros necessários](#required-parameters) ou tabelas de [parâmetros opcionais.](#optional-parameters) Depois de preencher as definições, clicar no botão **Comprar** iniciará a implementação do modelo.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a gestão programática dos recursos da Time Series Insights utilizando APIs REST, leia time [series Insights Management](https://docs.microsoft.com/rest/api/time-series-insights-management/).
