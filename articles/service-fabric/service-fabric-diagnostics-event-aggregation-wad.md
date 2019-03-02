---
title: Agregação de eventos de recursos de infraestrutura de serviço do Azure com o Windows diagnóstico do Azure | Documentos da Microsoft
description: Saiba mais sobre a agregação e a recolha de eventos com WAD para monitorização e diagnóstico de clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: afc833775894a01e8061401fe7601267f09edded
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243249"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregação de eventos e coleções com o Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando estiver a executar um cluster do Azure Service Fabric, é uma boa idéia para recolher os registos de todos os nós numa localização central. Ter os registos numa localização central ajuda a analisar e resolver problemas no seu cluster, ou problemas em aplicações e serviços em execução nesse cluster.

Uma forma de carregar e recolher registos é utilizar a extensão do Windows Azure Diagnostics (WAD), que carrega os registos para o armazenamento do Azure e, também tem a opção para enviar registos para o Azure Application Insights ou Hubs de eventos. Também pode utilizar um processo externo para ler os eventos de armazenamento e colocá-los num produto de plataforma de análise, como [registos do Azure Monitor](../log-analytics/log-analytics-service-fabric.md) ou outra solução de análise de registos.

## <a name="prerequisites"></a>Pré-requisitos
As seguintes ferramentas são usadas neste artigo:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Eventos de plataforma do Service Fabric
Service Fabric configura com poucas [canais de registo de out-of-the-box](service-fabric-diagnostics-event-generation-infra.md), de que estão pré-configuradas com a extensão para enviar a monitorização e os dados de diagnóstico para uma tabela de armazenamento ou em outro lugar os seguintes canais:
  * [Eventos operacionais](service-fabric-diagnostics-event-generation-operational.md): operações de nível superior que executa a plataforma do Service Fabric. Os exemplos incluem a criação de aplicativos e serviços, as alterações de estado do nó e informações de atualização. Estes são emitidos como registos de rastreio de eventos para Windows (ETW)
  * [Eventos do modelo de programação de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Eventos do modelo de programação dos Reliable Services](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Implementar a extensão de diagnóstico através do portal
Recolher registos a primeira etapa é implantar a extensão de diagnóstico em nós de conjunto de dimensionamento de máquina virtual no cluster do Service Fabric. A extensão de diagnóstico recolhe registos em cada VM e carrega-os para a conta de armazenamento que especificar. Os passos seguintes descrevem como fazer isso para clusters de novas e existentes através do portal do Azure e os modelos Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Implementar a extensão de diagnóstico como parte da criação do cluster através do portal do Azure
Ao criar o cluster, o passo de configuração de cluster, expanda as definições opcionais e certifique-se de que o diagnóstico está definido como **no** (a predefinição).

![Definições de diagnóstico do Azure no portal para a criação do cluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

É altamente recomendável que baixe o modelo **antes de clicar em criar** no último passo. Para obter detalhes, consulte [configurar um cluster do Service Fabric com um modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Terá do modelo para fazer alterações nos quais canais (listadas acima) para recolher dados a partir de.

![Modelo de cluster](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Agora que está a agregar eventos no armazenamento do Azure, [configurar os registos do Azure Monitor](service-fabric-diagnostics-oms-setup.md) registos do portal para obter informações e consultá-los no Azure Monitor

>[!NOTE]
>Atualmente, não existe nenhuma forma de filtrar ou prepará os eventos que são enviados para as tabelas. Se não implemente um processo para remover os eventos da tabela, a tabela continuará a crescer (o limite predefinido é 50 GB). Instruções sobre como alterar desta funcionalidade são [mais abaixo neste artigo](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Além disso, há um exemplo de um serviço de tratamento de dados em execução [exemplo de Watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e é recomendado que escreve um para si, a menos que exista um bom motivo para que possa armazenar os registos para além de um período de tempo do dia 30 ou 90.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Implementar a extensão de diagnóstico através do Gestor de recursos do Azure

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Criar um cluster com a extensão de diagnóstico
Para criar um cluster com o Resource Manager, terá de adicionar a configuração de diagnósticos JSON para o modelo do Resource Manager completo. Fornecemos um modelo de Gestor de recursos de cluster de cinco-VM de exemplo com a configuração de diagnóstico, adicionada a ele como parte dos nossos exemplos de modelo do Resource Manager. Pode vê-lo nesta localização na Galeria de exemplos do Azure: [Cluster de cinco nós com o exemplo de modelo do Gestor de recursos de diagnóstico](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Para ver a definição de diagnóstico no modelo do Resource Manager, abra o ficheiro azuredeploy JSON e procure **IaaSDiagnostics**. Para criar um cluster ao utilizar este modelo, selecione o **implementar no Azure** botão disponível na ligação anterior.

Em alternativa, pode transferir o exemplo do Gerenciador de recursos, alterá-lo e criar um cluster com o modelo modificado utilizando o `New-AzureRmResourceGroupDeployment` comando numa janela do PowerShell do Azure. Consulte o seguinte código para os parâmetros que passa para o comando. Para obter informações detalhadas sobre como implementar um grupo de recursos com o PowerShell, consulte o artigo [implementar um grupo de recursos com o modelo Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Adicionar a extensão de diagnóstico a um cluster existente
Se tiver um cluster existente que não tem o diagnóstico implementado, pode adicionar ou atualizá-lo por meio do modelo de cluster. Modifique o modelo do Resource Manager que é utilizado para criar o cluster existente ou transferir o modelo do portal conforme descrito anteriormente. Modifique o ficheiro Template JSON, efetuando as seguintes tarefas:

Adicione um novo recurso de armazenamento para o modelo ao adicionar a secção de recursos.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Em seguida, adicione a secção de parâmetros apenas depois das definições de conta de armazenamento, entre `supportLogStorageAccountName`. Substitua o texto de marcador de posição *nome da conta de armazenamento aqui* com o nome da conta de armazenamento que pretende.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Em seguida, atualize o `VirtualMachineProfile` secção do ficheiro Template, adicionando o seguinte código dentro da matriz de extensões. Certifique-se de que adicione uma vírgula no início ou fim, dependendo de qual é inserido.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Depois de modificar o ficheiro Template conforme descrito, voltar a publicar o modelo do Resource Manager. Se o modelo foi exportado, executando o arquivo deploy.ps1 republishes o modelo. Depois de implementar, certifique-se de que **ProvisioningState** é **Succeeded**.

> [!TIP]
> Se pretender implementar contentores no seu cluster, ative WAD recolher estatísticas de docker ao adicionar esta opção para sua **WadCfg > DiagnosticMonitorConfiguration** secção.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Atualizar a quota de armazenamento

Desde as tabelas preenchidas pela extensão do cresce até que a quota for atingida, convém considerar a diminuir o tamanho de quota. O valor predefinido é 50 GB e pode ser configurado no modelo sob o `overallQuotaInMB` em `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Configurações de coleção de registo
Também estão disponíveis para a recolha de registos de canais adicionais, aqui estão algumas das configurações mais comuns, que pode fazer no modelo para clusters em execução no Azure.

* Canal operacional - Base: Ativado por predefinição, o alto nível operações executadas pelo Service Fabric e o cluster, incluindo eventos para um nó em breve, uma nova aplicação que está sendo implantado ou reverter uma atualização, etc. Para obter uma lista de eventos, consulte [eventos operacionais do canal](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Canal operacional - detalhada: Isto inclui relatórios de estado de funcionamento e decisões, bem como tudo no canal operacional de base de balanceamento de carga. Esses eventos são gerados pelo sistema ou o seu código usando o estado de funcionamento ou APIs de relatórios de carga, tal como [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) ou [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Para ver esses eventos no Visualizador de eventos de diagnóstico do Visual Studio adicionar "Microsoft-ServiceFabric:4:0x4000000000000008" para a lista de fornecedores ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Base de dados e do canal de mensagens: Registos críticos e os eventos gerados no sistema de mensagens (atualmente apenas o ReverseProxy) e o caminho de dados, além disso, para registos de canal operacional detalhadas. Esses eventos são o processamento de falhas e outros problemas críticos no ReverseProxy de solicitação, bem como pedidos processados. **Esta é a nossa recomendação para o registo abrangente**. Para ver esses eventos no Visualizador de eventos de diagnóstico do Visual Studio, adicione "Microsoft-ServiceFabric:4:0x4000000000000010" para a lista de fornecedores ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Canal de mensagens - detalhadas de & dados: Canal verboso, que contém todos os registos não críticas dos dados e de mensagens no cluster e o canal operacional detalhado. Para resolução de problemas de todos os eventos de proxy inverso, veja a [guia de diagnóstico de proxy inverso](service-fabric-reverse-proxy-diagnostics.md).  Para ver esses eventos no Visualizador de eventos de diagnóstico do Visual Studio, adicione "Microsoft-ServiceFabric:4:0x4000000000000020" para a lista de fornecedores ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Este canal tem um volume muito elevado de eventos, ativar recolha de eventos a partir detalhadas resultados de canais em muitos rastreios a ser gerados rapidamente e pode consumir a capacidade de armazenamento. Apenas ative esta opção se for absolutamente necessário.


Para ativar a **canal operacional de Base** nossa recomendação para o registo abrangente com a menor quantidade de ruído, o `EtwManifestProviderConfiguration` no `WadCfg` do seu modelo teria uma aparência semelhante ao seguinte:

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Recolher a partir de novos canais de EventSource

A atualizar diagnósticos para recolher registos de novos canais de EventSource que representam um novo aplicativo que está sobre a implementação, efetue o mesmo cluster passos, conforme descritos anteriormente para a configuração de diagnósticos para um existente.

Atualizar o `EtwEventSourceProviderConfiguration` seção no arquivo Template para adicionar entradas para os novos canais de EventSource antes de aplicar a configuração de atualização utilizando o `New-AzureRmResourceGroupDeployment` comando do PowerShell. O nome da origem do evento é definido como parte do seu código no ficheiro ServiceEventSource.cs gerados pelo Visual Studio.

Por exemplo, se a origem do evento é o nome meu Eventsource, adicione o seguinte código para colocar os eventos do meu Eventsource numa tabela chamada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para recolher contadores de desempenho ou registos de eventos, modificar o modelo do Resource Manager ao utilizar os exemplos fornecidos [criar uma máquina virtual do Windows com monitorização e diagnósticos com um modelo Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Em seguida, voltar a publicar o modelo do Resource Manager.

## <a name="collect-performance-counters"></a>Recolher contadores de desempenho

Para recolher métricas de desempenho do seu cluster, adicione os contadores de desempenho para o seu "WadCfg > DiagnosticMonitorConfiguration" no modelo do Resource Manager para o seu cluster. Ver [monitorização de desempenho com WAD](service-fabric-diagnostics-perf-wad.md) para obter os passos sobre como modificar seu `WadCfg` para recolher contadores de desempenho específicos. Referência [contadores de desempenho do Service Fabric](service-fabric-diagnostics-event-generation-perf.md) para contadores de uma lista de desempenho que recomendamos a recolher.
  
Se estiver a utilizar um coletor do Application Insights, conforme descrito na secção abaixo e desejar estas métricas a aparecer no Application Insights, em seguida, certifique-se de adicionar o nome de sink na secção "sinks", conforme mostrado acima. Isso enviará automaticamente os contadores de desempenho que são configurados individualmente para o recurso do Application Insights.


## <a name="send-logs-to-application-insights"></a>Enviar registos ao Application Insights

### <a name="configuring-application-insights-with-wad"></a>Configuração do Application Insights com WAD

>[!NOTE]
>Isto só é aplicável a clusters do Windows neste momento.

Existem duas formas principais de enviar dados a partir de WAD para o Azure Application Insights, que é feito pela adição de um coletor do Application Insights à configuração do WAD, através do portal do Azure ou através de um modelo Azure Resource Manager.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adicionar uma chave de instrumentação do Application Insights ao criar um cluster no portal do Azure

![Adicionar um AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Ao criar um cluster, se o diagnóstico está ativado "Ativado", mostra um campo opcional para introduzir uma chave de instrumentação do Application Insights. Se colar a chave do Application Insights aqui, o sink do Application Insights é configurado automaticamente para no modelo do Resource Manager que é utilizado para implementar o seu cluster.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Adicionar o Sink do Application Insights para o modelo do Resource Manager

O "WadCfg" do modelo do Resource Manager, adicione um "Sink", incluindo as seguintes duas alterações:

1. Adicionar a configuração de sink diretamente após a declaração do `DiagnosticMonitorConfiguration` concluída:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Incluir o coletor no `DiagnosticMonitorConfiguration` adicionando a seguinte linha no `DiagnosticMonitorConfiguration` da `WadCfg` (logo antes o `EtwProviders` são declarados):

    ```json
    "sinks": "applicationInsights"
    ```

Em ambos os fragmentos de código anteriores, o nome "Application Insights" foi utilizado para descrever o sink. Não é um requisito e, desde que o nome do coletor está incluído no "sinks", pode definir o nome em qualquer cadeia de caracteres.

Atualmente, os registos do cluster aparecem como **rastreios** no Visualizador de log do Application Insights. Uma vez que a maioria dos rastreios a chegar da plataforma é de nível de "Informativo", pode também considere alterar a configuração de sink para enviar apenas os registos do tipo "Aviso" ou "Erro". Isso pode ser feito pela adição de "Canais" para o seu coletor, como demonstrado na [este artigo](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Se utilizar uma chave incorreta do Application Insights no portal ou no modelo do Resource Manager, terá de alterar manualmente a chave e atualizar o cluster / reimplantá-la.

## <a name="next-steps"></a>Passos Seguintes

Assim que tiver configurado corretamente o diagnóstico do Azure, verá dados das tabelas de armazenamento dos registos ETW e EventSource. Se optar por utilizar os registos do Azure Monitor, Kibana ou quaisquer outra análises e visualização de plataforma de dados que não esteja configurada diretamente no modelo do Resource Manager, certifique-se configurar a plataforma à sua escolha para ler os dados a partir dessas tabelas de armazenamento. Fazendo isso para registos do Azure Monitor é relativamente trivial e é explicada na [análise de registo e evento](service-fabric-diagnostics-event-analysis-oms.md). O Application Insights é um pouco de um caso especial nesse sentido, uma vez que ele pode ser configurado como parte da configuração da extensão de diagnóstico, portanto, consulte a [article apropriada](service-fabric-diagnostics-event-analysis-appinsights.md) se optar por utilizar a IA.

>[!NOTE]
>Atualmente, não existe nenhuma forma de filtrar ou prepará os eventos que são enviados para a tabela. Se não implemente um processo para remover os eventos da tabela, a tabela irá continuar a crescer. Atualmente, não há um exemplo de um serviço de tratamento de dados em execução [exemplo de Watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e é recomendado que escreve um para si, a menos que exista um bom motivo para que possa armazenar os registos para além de um período de tempo do dia 30 ou 90.

* [Saiba como recolher contadores de desempenho ou registos ao utilizar a extensão de diagnóstico](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Análise de eventos e visualização com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise de eventos e visualização com os registos do Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
