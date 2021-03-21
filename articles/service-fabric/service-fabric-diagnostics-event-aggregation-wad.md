---
title: Agregação de eventos com diagnósticos windows Azure
description: Saiba mais sobre agregação e recolha de eventos utilizando o WAD para monitorização e diagnóstico de clusters de tecidos de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: eaf40bafb68820b56fc9bf00dd4c11fbb337406f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613464"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregação e recolha de eventos usando o Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando você está executando um cluster de tecido de serviço Azure, é uma boa ideia recolher os registos de todos os nós em um local central. Ter os registos numa localização central ajuda-o a analisar e resolver problemas no seu cluster, ou problemas nas aplicações e serviços que estão a decorrer nesse cluster.

Uma forma de carregar e recolher registos é utilizar a extensão do Windows Azure Diagnostics (WAD), que faz o upload de registos para o Azure Storage, e também tem a opção de enviar registos para Azure Application Insights ou Event Hubs. Também pode utilizar um processo externo para ler os eventos a partir do armazenamento e colocá-los num produto da plataforma de análise, como [registos Azure Monitor](./service-fabric-diagnostics-oms-setup.md) ou outra solução de registo.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
As seguintes ferramentas são utilizadas neste artigo:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/)
* [Modelo Azure Resource Manager](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)

## <a name="service-fabric-platform-events"></a>Eventos da plataforma de tecido de serviço
O Service Fabric configura-o com alguns [canais de registo fora da caixa,](service-fabric-diagnostics-event-generation-infra.md)dos quais os seguintes canais são pré-configurados com a extensão para enviar dados de monitorização e diagnóstico para uma mesa de armazenamento ou em qualquer outro lugar:
  * [Eventos operacionais:](service-fabric-diagnostics-event-generation-operational.md)operações de nível superior que a plataforma Service Fabric realiza. Exemplos incluem a criação de aplicações e serviços, alterações do estado do nó e atualização de informações. Estes são emitidos como rastreadores de eventos para registos windows (ETW)
  * [Eventos de modelos de programação de atores confiáveis](service-fabric-reliable-actors-diagnostics.md)
  * [Eventos de modelos de programação de serviços fiáveis](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Implementar a extensão de Diagnóstico através do portal
O primeiro passo na recolha de registos é a implementação da extensão de Diagnóstico nos nós de escala de máquina virtual no cluster de Tecido de Serviço. A extensão De Diagnóstico recolhe registos em cada VM e envia-os para a conta de armazenamento que especifica. Os passos seguintes descrevem como conseguir isso para novos clusters existentes através do portal Azure e modelos Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Implementar a extensão de Diagnóstico como parte da criação de clusters através do portal Azure
Ao criar o seu cluster, na fase de configuração do cluster, expanda as definições opcionais e certifique-se de que o Diagnóstico está definido para **On** (a definição predefinida).

![Definições de Diagnóstico Azure no portal para criação de clusters](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Recomendamos vivamente que descarregue o modelo **antes de clicar** em Criar no passo final. Para obter mais informações, consulte a [Configuração de um cluster de tecido de serviço utilizando um modelo de Gestor de Recursos Azure](service-fabric-cluster-creation-via-arm.md). Precisa do modelo para fazer alterações em que canais (listados acima) para recolher dados.

![Modelo de cluster](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Agora que está a agregar eventos no Azure Storage, [crie registos do Azure Monitor](service-fabric-diagnostics-oms-setup.md) para obter informações e questioná-los no portal de registos do Azure Monitor.

>[!NOTE]
>Atualmente não há como filtrar ou preparar os eventos que são enviados para as mesas. Se não implementar um processo para remover eventos da tabela, a tabela continuará a crescer (a tampa padrão é de 50 GB). Instruções sobre como alterar esta alteração estão [mais abaixo neste artigo](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Além disso, existe um exemplo de um serviço de preparação de dados em execução na [amostra watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e é recomendado que escreva um para si também, a menos que haja uma boa razão para armazenar registos para além de um prazo de 30 ou 90 dias.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Implementar a extensão de Diagnóstico através do Gestor de Recursos Azure

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Criar um cluster com a extensão de diagnóstico
Para criar um cluster utilizando o Gestor de Recursos, é necessário adicionar o JSON de configuração de Diagnóstico ao modelo completo do Gestor de Recursos. Fornecemos um modelo de gestor de recursos de cluster de cinco VM com configuração de Diagnóstico adicionado a ele como parte das nossas amostras de modelo de Gestor de Recursos. Pode vê-lo neste local na galeria Azure Samples: [Cluster de cinco nós com amostra do Modelo do Gestor de Recursos de Diagnóstico](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Para ver a definição de Diagnóstico no modelo de Gestor de Recursos, abra a azuredeploy.jsno ficheiro e procure por **IaaSDiagnostics**. Para criar um cluster utilizando este modelo, selecione o botão **Implementar para Azul** disponível no link anterior.

Em alternativa, pode descarregar a amostra do Gestor de Recursos, fazer alterações e criar um cluster com o modelo modificado utilizando o `New-AzResourceGroupDeployment` comando numa janela Azure PowerShell. Consulte o seguinte código para os parâmetros que transmite para o comando. Para obter informações detalhadas sobre como implementar um grupo de recursos utilizando o PowerShell, consulte o artigo [Implementar um grupo de recursos com o modelo Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Adicione a extensão de diagnóstico a um cluster existente
Se tiver um cluster existente que não tenha Diagnósticos implementados, pode adicioná-lo ou atualizá-lo através do modelo de cluster. Modifique o modelo de Gestor de Recursos que é usado para criar o cluster existente ou descarregue o modelo a partir do portal, conforme descrito anteriormente. Modificar a template.jsem ficheiro executando as seguintes tarefas:

Adicione um novo recurso de armazenamento ao modelo adicionando à secção de recursos.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Em seguida, adicione à secção de parâmetros logo após as definições da conta de armazenamento, entre `supportLogStorageAccountName` . Substitua o *nome* da conta de armazenamento de texto do espaço reservado aqui pelo nome da conta de armazenamento que você gostaria.

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
Em seguida, atualize a `VirtualMachineProfile` secção do template.jsem ficheiro adicionando o seguinte código dentro do conjunto de extensões. Certifique-se de adicionar uma vírgula no início ou no fim, dependendo de onde está inserida.

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
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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

Depois de modificar o template.jsno ficheiro conforme descrito, reebine o modelo de Gestor de Recursos. Se o modelo foi exportado, executar o ficheiro deploy.ps1 republica o modelo. Depois de implementar, certifique-se de que **o ProvisioningState** é **bem sucedido**.

> [!TIP]
> Se vai colocar contentores no seu cluster, ative o WAD a recolher estatísticas de estivadores adicionando-as à secção **WadCfg > DiagnosticMonitorConfiguration.**

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},
```

### <a name="update-storage-quota"></a>Atualizar quota de armazenamento

Uma vez que as tabelas povoadas pela extensão crescem até que a quota seja atingida, talvez deva considerar a diminuição do tamanho da quota. O valor predefinido é de 50 GB e é configurável no modelo sob o `overallQuotaInMB` campo sob `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Configurações de recolha de registos
Registos de canais adicionais também estão disponíveis para recolha, aqui estão algumas das configurações mais comuns que você pode fazer no modelo para clusters em execução em Azure.

* Canal Operacional - Base: Habilitado por defeito, operações de alto nível realizadas pela Service Fabric e pelo cluster, incluindo eventos para um nó que está a chegar, uma nova aplicação a ser implementada, ou um rollback de upgrade, etc. Para obter uma lista de eventos, consulte os [Eventos do Canal Operacional](./service-fabric-diagnostics-event-generation-operational.md).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Canal Operacional - Detalhado: Isto inclui relatórios de saúde e decisões de equilíbrio de carga, além de tudo no canal operacional base. Estes eventos são gerados pelo sistema ou pelo seu código utilizando as APIs de saúde ou de relatório de carga, tais como [ReportPartitionHealth](/previous-versions/azure/reference/mt645153(v=azure.100)) ou [ReportLoad](/previous-versions/azure/reference/mt161491(v=azure.100)). Para ver estes eventos no Visualizador de Eventos de Diagnóstico do Visual Studio adicione "Microsoft-ServiceFabric:4:0x4000000000000008" à lista de fornecedores da ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Data and Messaging Channel - Base: Registos críticos e eventos gerados nas mensagens (atualmente apenas o ReverseProxy) e na trajetória de dados, além de registos de canais operacionais detalhados. Estes eventos são falhas de processamento de pedidos e outras questões críticas no ReverseProxy, bem como pedidos processados. **Esta é a nossa recomendação para a exploração madeireira abrangente.** Para ver estes eventos no Visualizador de Eventos de Diagnóstico do Visual Studio, adicione "Microsoft-ServiceFabric:4:0x4000000000000010" à lista de fornecedores da ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Data & Canal de Mensagens - Detalhado: Canal Verbose que contém todos os registos não críticos de dados e mensagens no cluster e no canal operacional detalhado. Para uma resolução detalhada de problemas de todos os eventos de procuração inversa, consulte o [guia de diagnóstico de procuração inversa](service-fabric-reverse-proxy-diagnostics.md).  Para ver estes eventos no visualizador de Eventos de Diagnóstico do Visual Studio, adicione "Microsoft-ServiceFabric:4:0x4000000000000020" à lista de fornecedores da ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Este canal tem um volume muito elevado de eventos, permitindo a recolha de eventos a partir deste canal detalhado resulta em muitos vestígios que são gerados rapidamente, e pode consumir capacidade de armazenamento. Só ligue isto se for absolutamente necessário.


Para ativar o **Canal Operacional base** a nossa recomendação para o registo completo com a menor quantidade de ruído, o do seu modelo seria `EtwManifestProviderConfiguration` `WadCfg` semelhante:

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
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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

## <a name="collect-from-new-eventsource-channels"></a>Recolha de novos canais EventSource

Para atualizar diagnósticos para recolher registos de novos canais EventSource que representam uma nova aplicação que está prestes a implementar, execute os mesmos passos que previamente descrito para a configuração de Diagnóstico para um cluster existente.

Atualize a `EtwEventSourceProviderConfiguration` secção no template.jsno ficheiro para adicionar entradas para os novos canais EventSource antes de aplicar a atualização de configuração utilizando o `New-AzResourceGroupDeployment` comando PowerShell. O nome da fonte do evento é definido como parte do seu código no ficheiro .cs ServiceEventSource gerado pelo Estúdio Visual.

Por exemplo, se a sua fonte de eventos for chamada My-Eventsource, adicione o seguinte código para colocar os eventos de My-Eventsource numa tabela chamada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para recolher contadores de desempenho ou registos de eventos, modifique o modelo de Gestor de Recursos utilizando os exemplos fornecidos na [Create a virtual machine Windows com monitorização e diagnóstico utilizando um modelo de Gestor de Recursos Azure](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json). Em seguida, reeditar o modelo de Gestor de Recursos.

## <a name="collect-performance-counters"></a>Recolha contadores de desempenho

Para recolher as métricas de desempenho do seu cluster, adicione os contadores de desempenho ao seu "WadCfg > DiagnosticMonitorConfiguration" no modelo de Gestor de Recursos do seu cluster. Consulte [a monitorização de desempenho com o WAD](service-fabric-diagnostics-perf-wad.md) para obter etapas para modificar o seu para recolher `WadCfg` contadores de desempenho específicos. [Contadores de desempenho do tecido de serviço de](service-fabric-diagnostics-event-generation-perf.md) referência para uma lista de contadores de desempenho que recomendamos recolher.
  
Se estiver a utilizar uma pia Application Insights, como descrito na secção abaixo, e quiser que estas métricas apareçam no Application Insights, certifique-se de adicionar o nome da pia na secção "pias", como mostrado acima. Isto enviará automaticamente os contadores de desempenho que são configurados individualmente para o seu recurso Application Insights.


## <a name="send-logs-to-application-insights"></a>Enviar registos para Insights de Aplicação

### <a name="configuring-application-insights-with-wad"></a>Configurar insights de aplicações com WAD

>[!NOTE]
>Isto só é aplicável aos clusters Windows no momento.

Existem duas formas primárias de enviar dados do WAD para a Azure Application Insights, o que é conseguido adicionando uma pia application Insights à configuração WAD, através do portal Azure ou através de um modelo de Gestor de Recursos Azure.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adicione uma chave de instrumentação de insights de aplicação ao criar um cluster no portal Azure

![Adicionar um AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Ao criar um cluster, se o Diagnóstico for ligado "On", aparecerá um campo opcional para introduzir uma tecla de instrumentação de insights de aplicação. Se colar aqui a sua Chave de Insights de Aplicação, a pia Application Insights é automaticamente configurada para si no modelo de Gestor de Recursos que é utilizado para implantar o seu cluster.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Adicione o Sink de Insights de Aplicação ao modelo de Gestor de Recursos

No "WadCfg" do modelo de Gestor de Recursos, adicione um "Sink" incluindo as seguintes duas alterações:

1. Adicione a configuração da pia imediatamente após a declaração do ser `DiagnosticMonitorConfiguration` concluído:

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

2. Incluir a Pia no através da `DiagnosticMonitorConfiguration` adição da seguinte linha no `DiagnosticMonitorConfiguration` `WadCfg` (logo antes do `EtwProviders` declarado):

    ```json
    "sinks": "applicationInsights"
    ```

Em ambos os cortes de código anteriores, o nome "applicationInsights" foi usado para descrever a pia. Isto não é um requisito e enquanto o nome da pia estiver incluído em "pias", pode definir o nome em qualquer cadeia.

Atualmente, os registos do cluster aparecem como vestígios no visualizador de **registos** da Application Insights. Uma vez que a maioria dos vestígios provenientes da plataforma são de nível "Informativo", também pode considerar alterar a configuração do lavatório apenas para enviar registos do tipo "Aviso" ou "Erro". Isto pode ser feito adicionando "Canais" à sua pia, como demonstrado [neste artigo.](../azure-monitor/agents/diagnostics-extension-to-application-insights.md)

>[!NOTE]
>Se utilizar uma Chave de Insights de Aplicação incorreta no portal ou no seu modelo de Gestor de Recursos, terá de alterar manualmente a tecla e atualizar o cluster /recolocar o cluster/recolocar o cluster/recolocar.

## <a name="next-steps"></a>Passos seguintes

Uma vez configurados corretamente os diagnósticos Azure, verá dados nas suas tabelas de Armazenamento a partir dos registos ETW e EventSource. Se optar por utilizar registos do Azure Monitor, Kibana ou qualquer outra plataforma de análise e visualização de dados que não esteja configurada diretamente no modelo de Gestor de Recursos, certifique-se de configurar a plataforma da sua escolha para ler nos dados destas tabelas de armazenamento. Fazê-lo para registos do Azure Monitor é relativamente trivial, e é explicado na [análise de Evento e registo](service-fabric-diagnostics-event-analysis-oms.md). O Application Insights é um caso um pouco especial neste sentido, uma vez que pode ser configurado como parte da configuração da extensão de Diagnóstico, por isso consulte o [artigo apropriado](service-fabric-diagnostics-event-analysis-appinsights.md) se optar por utilizar AI.

>[!NOTE]
>Atualmente, não existe forma de filtrar ou preparar os eventos que são enviados para a mesa. Se não implementar um processo para remover eventos da mesa, a mesa continuará a crescer. Atualmente, existe um exemplo de um serviço de preparação de dados em execução na [amostra watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e é recomendado que escreva um para si também, a menos que exista uma boa razão para armazenar registos para além de um prazo de 30 ou 90 dias.

* [Saiba como recolher contadores de desempenho ou registos utilizando a extensão de Diagnóstico](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)
* [Análise e Visualização de Eventos com Insights de Aplicações](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise e Visualização de Eventos com registos do Monitor Azure](service-fabric-diagnostics-event-analysis-oms.md)
* [Análise e Visualização de Eventos com Insights de Aplicações](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise e Visualização de Eventos com registos do Monitor Azure](service-fabric-diagnostics-event-analysis-oms.md)
