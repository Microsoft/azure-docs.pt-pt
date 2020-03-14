---
title: Agregação de eventos com diagnósticos Windows Azure
description: Aprenda sobre agregação e recolha de eventos usando WAD para monitorização e diagnóstico de clusters Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: b9a448ff41c66fa3a38c124f7acde062bacbe9ba
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282500"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregação e recolha de eventos utilizando diagnósticos Windows Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando se gere um cluster azure service fabric, é uma boa ideia recolher os registos de todos os nós num local central. Ter os registos num local central ajuda-o a analisar e resolver problemas no seu cluster, ou problemas nas aplicações e serviços que estão a decorrer nesse cluster.

Uma forma de carregar e recolher registos é utilizar a extensão de Diagnósticos Do Windows Azure (WAD), que envia registos para o Armazenamento Azure, e também tem a opção de enviar registos para insights de aplicação Azure ou Hubs de Eventos. Também pode utilizar um processo externo para ler os eventos a partir do armazenamento e colocá-los num produto de plataforma de análise, como [registos do Monitor Azure](../log-analytics/log-analytics-service-fabric.md) ou outra solução de log-parsing.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Neste artigo são utilizadas as seguintes ferramentas:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Eventos da plataforma Service Fabric
O Tecido de Serviço configura-o com alguns [canais de registo fora da caixa,](service-fabric-diagnostics-event-generation-infra.md)dos quais os seguintes canais são pré-configurados com a extensão para enviar dados de monitorização e diagnóstico para uma tabela de armazenamento ou em qualquer outro lugar:
  * [Eventos operacionais](service-fabric-diagnostics-event-generation-operational.md): operações de nível mais elevado que a plataforma Service Fabric realiza. Exemplos incluem criação de aplicações e serviços, alterações do estado do nó e informação de upgrade. Estes são emitidos como Rastreio de Eventos para registos windows (ETW)
  * [Eventos de modelos de programação de atores fiáveis](service-fabric-reliable-actors-diagnostics.md)
  * [Eventos de modelos de programação de serviços fiáveis](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Implementar a extensão de Diagnósticoatravés do portal
O primeiro passo na recolha de registos é implantar a extensão de Diagnóstico na escala virtual de máquinas definidas no cluster Service Fabric. A extensão de Diagnóstico recolhe registos em cada VM e envia-os para a conta de armazenamento que especifica. Os passos seguintes descrevem como realizar isto para novos e existentes clusters através do portal Azure e modelos do Gestor de Recursos Azure.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Implementar a extensão de Diagnósticos como parte da criação de cluster através do portal Azure
Ao criar o seu cluster, no passo de configuração do cluster, expanda as definições opcionais e certifique-se de que o Diagnóstico está definido para **Ligar** (a definição predefinida).

![Definições de Diagnóstico Azure no portal para criação de cluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Recomendamos vivamente que descarregue o modelo antes de clicar em **Criar** no passo final. Para mais detalhes, consulte a [Configuração de um cluster de tecido](service-fabric-cluster-creation-via-arm.md)de serviço utilizando um modelo de Gestor de Recursos Azure . Você precisa do modelo para fazer alterações em que canais (listados acima) para recolher dados a partir de.

![Modelo de cluster](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Agora que está a agregar eventos no Armazenamento Azure, [instale registos do Azure Monitor](service-fabric-diagnostics-oms-setup.md) para obter informações e questioná-los no portal de registos do Monitor Azure

>[!NOTE]
>Atualmente não há forma de filtrar ou preparar os eventos que são enviados para as mesas. Se não implementar um processo para remover eventos da mesa, a tabela continuará a crescer (a tampa predefinida é de 50 GB). As instruções sobre como alterar isto estão [ainda abaixo neste artigo](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Além disso, existe um exemplo de um serviço de preparação de dados que está a funcionar na [amostra watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e recomenda-se que escreva um para si também, a menos que haja uma boa razão para armazenar registos para além de um prazo de 30 ou 90 dias.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Implementar a extensão de Diagnóstico através do Gestor de Recursos Azure

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Criar um cluster com a extensão de diagnóstico
Para criar um cluster utilizando o Gestor de Recursos, precisa de adicionar a configuração de diagnóstico JSON ao modelo completo do Gestor de Recursos. Fornecemos uma amostra de modelo de gestor de recursos de cluster de cinco VM com configuração de diagnóstico adicionada a ele como parte das amostras do nosso modelo de Gestor de Recursos. Pode vê-lo neste local na galeria Azure Samples: [Cluster de cinco nós com amostra](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/)de modelo de Gestor de Recursos de Diagnóstico .

Para ver a definição de Diagnósticos no modelo De Gestor de Recursos, abra o ficheiro azuredeploy.json e procure **iaaSDiagnostics**. Para criar um cluster utilizando este modelo, selecione o botão **Deploy para Azure** disponível no link anterior.

Em alternativa, pode descarregar a amostra do Gestor de Recursos, fazer alterações e criar um cluster com o modelo modificado utilizando o comando `New-AzResourceGroupDeployment` numa janela Azure PowerShell. Consulte o seguinte código para os parâmetros que passa para o comando. Para obter informações detalhadas sobre como implementar um grupo de recursos utilizando o PowerShell, consulte o artigo Implementar um grupo de recursos com o modelo do Gestor de [Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Adicione a extensão de diagnóstico a um cluster existente
Se tiver um cluster existente que não tenha Diagnósticos implementados, pode adicioná-lo ou atualizá-lo através do modelo de cluster. Modifique o modelo de Gestor de Recursos que é usado para criar o cluster existente ou descarregar o modelo a partir do portal como descrito anteriormente. Modificar o ficheiro template.json executando as seguintes tarefas:

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

 Em seguida, adicione à secção de parâmetros logo após as definições da conta de armazenamento, entre `supportLogStorageAccountName`. Substitua o nome da conta de armazenamento de texto do espaço *reservado, com* o nome da conta de armazenamento que deseja.

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
Em seguida, atualize a secção `VirtualMachineProfile` do ficheiro template.json adicionando o seguinte código dentro da matriz de extensões. Certifique-se de adicionar uma vírina no início ou no fim, dependendo de onde está inserida.

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

Depois de modificar o ficheiro template.json conforme descrito, republique o modelo de Gestor de Recursos. Se o modelo foi exportado, executar o ficheiro deploy.ps1 republique o modelo. Depois de se implantar, certifique-se de que o Estado de **Provisioning** é **bem sucedido**.

> [!TIP]
> Se vai implantar contentores no seu cluster, ative o WAD a recolher estatísticas de estivadores adicionando isto à sua secção **WadCfg > DiagnosticMonitorConfiguration.**
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Quota de armazenamento de atualização

Uma vez que as tabelas povoadas pela extensão crescem até que a quota seja atingida, é melhor considerar a diminuição do tamanho da quota. O valor predefinido é de 50 GB e é configurável no modelo sob o campo `overallQuotaInMB` sob `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Configurações de recolha de registos
Os registos de canais adicionais também estão disponíveis para recolha, aqui estão algumas das configurações mais comuns que você pode fazer no modelo para clusters em execução em Azure.

* Canal Operacional - Base: Ativado por defeito, operações de alto nível realizadas pela Service Fabric e pelo cluster, incluindo eventos para um nó que se aproxima, uma nova aplicação a ser implementada, ou uma reversão de atualização, etc. Para obter uma lista de eventos, consulte [eventos do Canal Operacional.](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational)
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Canal Operacional - Detalhado: Isto inclui relatórios de saúde e decisões de equilíbrio de carga, além de tudo no canal operacional base. Estes eventos são gerados pelo sistema ou pelo seu código utilizando as APIs de informação sobre saúde ou carga, tais como [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) ou [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Para ver estes eventos no Visualiza Studio's Diagnostic Event Viewer adicione "Microsoft-ServiceFabric:4:0x400000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000" à lista de fornecedor

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Data and Messaging Channel - Base: Registos e eventos críticos gerados nas mensagens (atualmente apenas no ReverseProxy) e na trajetória de dados, além de registos de canais operacionais detalhados. Estes eventos são falhas de processamento de pedidos e outras questões críticas no ReverseProxy, bem como pedidos processados. **Esta é a nossa recomendação para uma exploração madeireira abrangente.** Para ver estes eventos no VisualizaStudio's Diagnostic Event Viewer, adicione "Microsoft-ServiceFabric:4:0x4000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Canal de Dados e Mensagens - Detalhado: Canal Verbose que contém todos os registos não críticos a partir de dados e mensagens no cluster e no canal operacional detalhado. Para uma resolução detalhada de problemas de todos os eventos de procuração inversa, consulte o [guia de diagnóstico de procuração inversa](service-fabric-reverse-proxy-diagnostics.md).  Para ver estes eventos no visualizador de eventos de diagnóstico do Visual Studio, adicione "Microsoft-ServiceFabric:4:0x4000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000" à lista de fornecedores da ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Este canal tem um volume muito elevado de eventos, permitindo a recolha de eventos a partir deste canal detalhado resulta em muitos vestígios sendo gerados rapidamente, e pode consumir capacidade de armazenamento. Só ligue isto se for absolutamente necessário.


Para ativar o **Canal Operacional Base** a nossa recomendação para uma exploração madeireira abrangente com a menor quantidade de ruído, o `EtwManifestProviderConfiguration` no `WadCfg` do seu modelo seria o seguinte:

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

## <a name="collect-from-new-eventsource-channels"></a>Recolha a partir de novos canais EventSource

Para atualizar diagnósticos para recolher registos de novos canais EventSource que representam uma nova aplicação que está prestes a implementar, execute os mesmos passos descritos anteriormente para a configuração de Diagnósticos para um cluster existente.

Atualize a secção `EtwEventSourceProviderConfiguration` no ficheiro template.json para adicionar entradas para os novos canais EventSource antes de aplicar a atualização de configuração utilizando o comando `New-AzResourceGroupDeployment` PowerShell. O nome da fonte do evento é definido como parte do seu código no ficheiro ServiceEventSource.cs gerado pelo Estúdio Visual.

Por exemplo, se a sua fonte de evento for chamada My-Eventsource, adicione o seguinte código para colocar os eventos do My-Eventsource numa tabela chamada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para recolher contadores de desempenho ou registos de eventos, modifique o modelo do Gestor de Recursos utilizando os exemplos fornecidos na [Create a Windows virtual machine com monitorização e diagnóstico utilizando um modelo de Gestor](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)de Recursos Azure . Em seguida, republique o modelo de Gestor de Recursos.

## <a name="collect-performance-counters"></a>Colete contadores de desempenho

Para recolher métricas de desempenho do seu cluster, adicione os contadores de desempenho à sua "WadCfg > DiagnosticMonitorConfiguration" no modelo De Gestor de Recursos para o seu cluster. Consulte [a monitorização do desempenho com](service-fabric-diagnostics-perf-wad.md) a WAD para obter passos na modificação do seu `WadCfg` para recolher contadores de desempenho específicos. [Balcões](service-fabric-diagnostics-event-generation-perf.md) de desempenho de tecido de serviço de referência para uma lista de contadores de desempenho que recomendamos a recolha.
  
Se estiver a usar um afundado application Insights, como descrito na secção abaixo, e quiser que estas métricas apareçam em Insights de Aplicação, certifique-se de adicionar o nome do lavatório na secção "pias", como mostrado acima. Isto enviará automaticamente os contadores de desempenho que estão configurados individualmente para o seu recurso Application Insights.


## <a name="send-logs-to-application-insights"></a>Enviar registos para Informações de Aplicação

### <a name="configuring-application-insights-with-wad"></a>Configurar insights de aplicação com WAD

>[!NOTE]
>Isto só é aplicável aos clusters windows no momento.

Existem duas formas primárias de enviar dados de WAD para Azure Application Insights, que é conseguido adicionando um afundado de Aplicação Insights à configuração WAD, através do portal Azure ou através de um modelo de Gestor de Recursos Azure.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adicione uma chave de instrumentação de insights de aplicação ao criar um cluster no portal Azure

![Adicionar um AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Ao criar um cluster, se o Diagnóstico for ligado, um campo opcional para introduzir uma chave de instrumentação de insights de aplicação mostrará. Se colar aqui a chave de insights de aplicação, o afundador de Insights de Aplicação é automaticamente configurado para si no modelo de Gestor de Recursos que é usado para implementar o seu cluster.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Adicione o afundatório de insights de aplicação ao modelo de Gestor de Recursos

No modelo "WadCfg" do gestor de recursos, adicione um "Sink" incluindo as duas seguintes alterações:

1. Adicione a configuração do lavatório diretamente após a declaração do `DiagnosticMonitorConfiguration` estiver concluída:

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

2. Incluir o lavatório no `DiagnosticMonitorConfiguration` adicionando a seguinte linha no `DiagnosticMonitorConfiguration` do `WadCfg` (mesmo antes de declarar o `EtwProviders`):

    ```json
    "sinks": "applicationInsights"
    ```

Em ambos os códigos anteriores, o nome "applicationInsights" foi usado para descrever a pia. Isto não é um requisito e enquanto o nome da pia estiver incluído em "pias", pode definir o nome em qualquer corda.

Atualmente, os registos do cluster aparecem como **vestígios** no visualizador de registo da Application Insights. Uma vez que a maioria dos vestígios provenientes da plataforma são de nível "Informational", também pode considerar alterar a configuração do lavatório para apenas enviar registos do tipo "Aviso" ou "Erro". Isto pode ser feito adicionando "Canais" à sua pia, como demonstrado [neste artigo.](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

>[!NOTE]
>Se utilizar uma chave de insights de aplicação incorreta, quer no portal quer no modelo do Gestor de Recursos, terá de alterar manualmente a chave e atualizá-la do cluster/reimplantá-la.

## <a name="next-steps"></a>Passos seguintes

Depois de configurar corretamente os diagnósticos do Azure, verá os dados nas tabelas de Armazenamento dos registos ETW e EventSource. Se optar por utilizar registos do Monitor Azure, kibana ou qualquer outra plataforma de análise e visualização de dados que não esteja configurada diretamente no modelo de Gestor de Recursos, certifique-se de configurar a plataforma da sua escolha para ler nos dados a partir destas tabelas de armazenamento. Fazer isto para os registos do Monitor Azure é relativamente trivial, e é explicado em [Evento e análise](service-fabric-diagnostics-event-analysis-oms.md)de registos. Application Insights é um caso um pouco especial neste sentido, uma vez que pode ser configurado como parte da configuração de extensão de diagnóstico, por isso consulte o [artigo apropriado](service-fabric-diagnostics-event-analysis-appinsights.md) se optar por usar IA.

>[!NOTE]
>Não existe atualmente forma de filtrar ou preparar os eventos que são enviados para a mesa. Se não implementar um processo para remover eventos da mesa, a mesa continuará a crescer. Atualmente, existe um exemplo de um serviço de tratamento de dados que está a funcionar na [amostra watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e recomenda-se que escreva um para si também, a menos que haja uma boa razão para armazenar registos para além de um prazo de 30 ou 90 dias.

* [Saiba como recolher contadores de desempenho ou registos utilizando a extensão de Diagnósticos](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Análise de Eventos e Visualização com Insights de Aplicação](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise e Visualização de Eventos com registos do Monitor Azure](service-fabric-diagnostics-event-analysis-oms.md)
* [Análise de Eventos e Visualização com Insights de Aplicação](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise e Visualização de Eventos com registos do Monitor Azure](service-fabric-diagnostics-event-analysis-oms.md)
