---
title: Agregação de eventos Service Fabric do Azure com o Windows Diagnóstico do Azure | Microsoft Docs
description: Saiba mais sobre como agregar e coletar eventos usando o WAD para monitoramento e diagnóstico de clusters de Service Fabric do Azure.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: 555a8a823526a51b045b4a0314ef7610bf728e5b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242982"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregação e coleta de eventos usando o Windows Diagnóstico do Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central ajuda você a analisar e solucionar problemas no cluster ou problemas nos aplicativos e serviços em execução nesse cluster.

Uma maneira de carregar e coletar logs é usar a extensão do Windows Diagnóstico do Azure (WAD), que carrega os logs no armazenamento do Azure e também tem a opção de enviar logs para Aplicativo Azure insights ou hubs de eventos. Você também pode usar um processo externo para ler os eventos do armazenamento e colocá-los em um produto da plataforma de análise, como [logs de Azure monitor](../log-analytics/log-analytics-service-fabric.md) ou outra solução de análise de log.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
As seguintes ferramentas são usadas neste artigo:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [O Azure PowerShell](/powershell/azure/overview)
* [Modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric eventos da plataforma
Service Fabric define alguns [canais de log prontos para uso](service-fabric-diagnostics-event-generation-infra.md), dos quais os canais a seguir são pré-configurados com a extensão para enviar dados de monitoramento e diagnóstico para uma tabela de armazenamento ou em outro lugar:
  * [Eventos operacionais](service-fabric-diagnostics-event-generation-operational.md): operações de nível superior que a plataforma Service Fabric executa. Os exemplos incluem a criação de aplicativos e serviços, as alterações de estado do nó e as informações de atualização. Eles são emitidos como logs ETW (rastreamento de eventos para Windows)
  * [Eventos de modelo de programação de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Eventos de modelo de programação de Reliable Services](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Implantar a extensão de diagnóstico por meio do portal
A primeira etapa na coleta de logs é implantar a extensão de diagnóstico nos nós do conjunto de dimensionamento de máquinas virtuais no Cluster Service Fabric. A extensão de diagnóstico coleta logs em cada VM e os carrega na conta de armazenamento que você especificar. As etapas a seguir descrevem como fazer isso para clusters novos e existentes por meio dos modelos portal do Azure e Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Implantar a extensão de diagnóstico como parte da criação do cluster por meio de portal do Azure
Ao criar o cluster, na etapa configuração do cluster, expanda as configurações opcionais e verifique se o diagnóstico está definido como **ativado** (a configuração padrão).

![Diagnóstico do Azure configurações no portal para a criação do cluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

É altamente recomendável que você baixe o modelo **antes de clicar em criar** na etapa final. Para obter detalhes, consulte [configurar um cluster de Service Fabric usando um modelo de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Você precisa do modelo para fazer alterações em quais canais (listados acima) para coletar dados.

![Modelo de cluster](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Agora que você está agregando eventos no armazenamento do Azure, [configure Azure monitor logs](service-fabric-diagnostics-oms-setup.md) para obter informações e consultá-los no portal de logs de Azure monitor

>[!NOTE]
>Atualmente, não há nenhuma maneira de filtrar ou groomar os eventos que são enviados para as tabelas. Se você não implementar um processo para remover eventos da tabela, a tabela continuará crescendo (a extremidade padrão é 50 GB). As instruções sobre como alterar isso estão [mais adiante neste artigo](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Além disso, há um exemplo de um serviço de grooming de dados em execução no [exemplo de Watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e é recomendável que você escreva um para você mesmo, a menos que haja um bom motivo para armazenar logs além de um período de 30 ou 90 dias.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Implantar a extensão de diagnóstico por meio de Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Criar um cluster com a extensão de diagnóstico
Para criar um cluster usando o Gerenciador de recursos, você precisa adicionar o JSON de configuração de diagnóstico ao modelo completo do Resource Manager. Fornecemos um exemplo de modelo do Resource Manager de cluster de cinco VMs com a configuração de diagnóstico adicionada a ela como parte de nossos exemplos de modelo do Resource Manager. Você pode vê-lo neste local na Galeria de exemplos do Azure: [cluster de cinco nós com exemplo de modelo do Gerenciador de recursos de diagnóstico](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Para ver a configuração de diagnóstico no modelo do Resource Manager, abra o arquivo azuredeploy. JSON e procure **IaaSDiagnostics**. Para criar um cluster usando este modelo, selecione o botão **implantar no Azure** disponível no link anterior.

Como alternativa, você pode baixar o exemplo do Resource Manager, fazer alterações nele e criar um cluster com o modelo modificado usando o comando `New-AzResourceGroupDeployment` em uma janela Azure PowerShell. Consulte o código a seguir para os parâmetros que você passa para o comando. Para obter informações detalhadas sobre como implantar um grupo de recursos usando o PowerShell, consulte o artigo [implantar um grupo de recursos com o modelo de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Adicionar a extensão de diagnóstico a um cluster existente
Se você tiver um cluster existente que não tenha o diagnóstico implantado, poderá adicioná-lo ou atualizá-lo por meio do modelo de cluster. Modifique o modelo do Resource Manager que é usado para criar o cluster existente ou baixe o modelo do portal, conforme descrito anteriormente. Modifique o arquivo template. JSON executando as seguintes tarefas:

Adicione um novo recurso de armazenamento ao modelo adicionando à seção recursos.

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

 Em seguida, adicione à seção de parâmetros logo após as definições da conta de armazenamento, entre `supportLogStorageAccountName`. Substitua o texto do espaço reservado *nome da conta de armazenamento aqui* pelo nome da conta de armazenamento que você deseja.

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
Em seguida, atualize a seção `VirtualMachineProfile` do arquivo template. JSON adicionando o código a seguir dentro da matriz de extensões. Certifique-se de adicionar uma vírgula no início ou no final, dependendo de onde ela é inserida.

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

Depois de modificar o arquivo template. JSON conforme descrito, Republique o modelo do Resource Manager. Se o modelo foi exportado, a execução do arquivo Deploy. ps1 republicará o modelo. Depois de implantar o, verifique se o **ProvisioningState** foi **bem-sucedido**.

> [!TIP]
> Se você pretende implantar contêineres em seu cluster, habilite WAD para selecionar as estatísticas do Docker adicionando-as à seção **WadCfg > DiagnosticMonitorConfiguration** .
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Atualizar cota de armazenamento

Como as tabelas preenchidas pela extensão crescem até que a cota seja atingida, talvez você queira considerar a diminuição do tamanho da cota. O valor padrão é 50 GB e é configurável no modelo no campo `overallQuotaInMB` em `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Configurações de coleta de log
Os logs de canais adicionais também estão disponíveis para coleta, aqui estão algumas das configurações mais comuns que você pode fazer no modelo para clusters em execução no Azure.

* Canal operacional-base: habilitada por padrão, operações de alto nível executadas por Service Fabric e o cluster, incluindo eventos para um nó surgindo, um novo aplicativo sendo implantado ou uma reversão de atualização, etc. Para obter uma lista de eventos, consulte [eventos de canal operacional](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Canal operacional-detalhado: isso inclui relatórios de integridade e decisões de balanceamento de carga, além de tudo no canal operacional de base. Esses eventos são gerados pelo sistema ou pelo seu código usando as APIs de integridade ou de relatório de carga, como [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) ou [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Para exibir esses eventos no diagnóstico do Visual Studio, Visualizador de Eventos adicione "Microsoft-0x4000000000000008:4:" à lista de provedores de ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Canal de dados e mensagens-base: logs críticos e eventos gerados no sistema de mensagens (atualmente, apenas o ReverseProxy) e o caminho de dados, além de logs de canais operacionais detalhados. Esses eventos são falhas de processamento de solicitação e outros problemas críticos no ReverseProxy, bem como solicitações processadas. **Essa é nossa recomendação para registro em log abrangente**. Para exibir esses eventos no Visualizador de Eventos de diagnóstico do Visual Studio, adicione "Microsoft-0x4000000000000010:4:" à lista de provedores de ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Data & canal de mensagens – detalhado: canal detalhado que contém todos os logs não críticos de dados e mensagens no cluster e o canal operacional detalhado. Para obter uma solução de problemas detalhada de todos os eventos de proxy reverso, consulte o [Guia de diagnóstico de proxy reverso](service-fabric-reverse-proxy-diagnostics.md).  Para exibir esses eventos no Visualizador de eventos de diagnóstico do Visual Studio, adicione "Microsoft-perfabric: 4:0x4000000000000020" à lista de provedores de ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Esse canal tem um volume muito alto de eventos, a habilitação da coleta de eventos desse canal detalhado resulta na geração rápida de muitos rastreamentos e pode consumir capacidade de armazenamento. Ative isso apenas se for absolutamente necessário.


Para habilitar o **canal operacional base** , nossa recomendação para o registro em log abrangente com a menor quantidade de ruído, o `EtwManifestProviderConfiguration` na `WadCfg` do modelo seria semelhante ao seguinte:

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

## <a name="collect-from-new-eventsource-channels"></a>Coletar de novos canais EventSource

Para atualizar o diagnóstico para coletar logs de novos canais EventSource que representam um novo aplicativo que você está prestes a implantar, execute as mesmas etapas descritas anteriormente para a configuração de diagnóstico para um cluster existente.

Atualize a seção `EtwEventSourceProviderConfiguration` no arquivo template. JSON para adicionar entradas para os novos canais EventSource antes de aplicar a atualização de configuração usando o comando `New-AzResourceGroupDeployment` PowerShell. O nome da origem do evento é definido como parte do seu código no arquivo ServiceEventSource.cs gerado pelo Visual Studio.

Por exemplo, se a origem do evento for nomeada como My-EventSource, adicione o código a seguir para posicionar os eventos de My-EventSource em uma tabela chamada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para coletar contadores de desempenho ou logs de eventos, modifique o modelo do Resource Manager usando os exemplos fornecidos em [criar uma máquina virtual do Windows com monitoramento e diagnóstico usando um modelo de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Em seguida, Republique o modelo do Resource Manager.

## <a name="collect-performance-counters"></a>Coletar contadores de desempenho

Para coletar métricas de desempenho do cluster, adicione os contadores de desempenho ao seu "WadCfg > DiagnosticMonitorConfiguration" no modelo do Resource Manager para o cluster. Consulte [monitoramento de desempenho com wad](service-fabric-diagnostics-perf-wad.md) para ver as etapas de modificação de seu `WadCfg` para coletar contadores de desempenho específicos. Referencie [Service Fabric contadores de desempenho](service-fabric-diagnostics-event-generation-perf.md) para obter uma lista de contadores de desempenho que recomendamos coletar.
  
Se você estiver usando um coletor de Application Insights, conforme descrito na seção abaixo, e quiser que essas métricas sejam exibidas na Application Insights, certifique-se de adicionar o nome do coletor na seção "coletores", como mostrado acima. Isso enviará automaticamente os contadores de desempenho que são configurados individualmente para o recurso de Application Insights.


## <a name="send-logs-to-application-insights"></a>Enviar logs para Application Insights

### <a name="configuring-application-insights-with-wad"></a>Configurando Application Insights com WAD

>[!NOTE]
>Isso só é aplicável a clusters do Windows no momento.

Há duas maneiras principais de enviar dados do WAD para o Aplicativo Azure insights, que é obtido adicionando um coletor de Application Insights à configuração do WAD, por meio do portal do Azure ou por meio de um modelo de Azure Resource Manager.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adicione uma chave de instrumentação de Application Insights ao criar um cluster no portal do Azure

![Adicionando um AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Ao criar um cluster, se o diagnóstico for ativado, um campo opcional para inserir uma chave de instrumentação de Application Insights será exibido. Se você colar sua chave de Application Insights aqui, o coletor de Application Insights será configurado automaticamente para você no modelo do Resource Manager que é usado para implantar o cluster.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Adicionar o coletor de Application Insights ao modelo do Resource Manager

No "WadCfg" do modelo do Resource Manager, adicione um "coletor", incluindo as duas alterações a seguir:

1. Adicione a configuração do coletor diretamente depois que a declaração da `DiagnosticMonitorConfiguration` for concluída:

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

2. Inclua o coletor no `DiagnosticMonitorConfiguration` adicionando a linha a seguir na `DiagnosticMonitorConfiguration` da `WadCfg` (direita antes que os `EtwProviders` sejam declarados):

    ```json
    "sinks": "applicationInsights"
    ```

Nos dois trechos de código anteriores, o nome "applicationInsights" foi usado para descrever o coletor. Isso não é um requisito e, desde que o nome do coletor esteja incluído em "coletores", você pode definir o nome como qualquer cadeia de caracteres.

Atualmente, os logs do cluster aparecem como **rastreamentos** no Visualizador de log do Application insights. Como a maioria dos rastreamentos provenientes da plataforma são do nível "informativo", você também pode considerar alterar a configuração do coletor para enviar somente logs do tipo "aviso" ou "erro". Isso pode ser feito adicionando "canais" ao seu coletor, conforme demonstrado neste [artigo](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Se você usar uma chave de Application Insights incorreta no portal ou no modelo do Resource Manager, será necessário alterar manualmente a chave e atualizar o cluster/reimplantá-la.

## <a name="next-steps"></a>Passos seguintes

Depois de configurar corretamente o diagnóstico do Azure, você verá os dados nas tabelas de armazenamento dos logs ETW e EventSource. Se você optar por usar logs de Azure Monitor, Kibana ou qualquer outra plataforma de análise e visualização de dados que não esteja configurada diretamente no modelo do Resource Manager, certifique-se de configurar a plataforma de sua escolha para ler os dados dessas tabelas de armazenamento. Fazer isso para Azure Monitor logs é relativamente trivial e é explicado em [análise de eventos e log](service-fabric-diagnostics-event-analysis-oms.md). Application Insights é um pouco um caso especial nesse sentido, já que ele pode ser configurado como parte da configuração da extensão de diagnóstico, portanto, consulte o [artigo apropriado](service-fabric-diagnostics-event-analysis-appinsights.md) se você optar por usar o ia.

>[!NOTE]
>Atualmente, não há nenhuma maneira de filtrar ou groomar os eventos que são enviados para a tabela. Se você não implementar um processo para remover eventos da tabela, a tabela continuará crescendo. Atualmente, há um exemplo de um serviço de grooming de dados em execução no [exemplo de Watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e é recomendável que você escreva um para você mesmo, a menos que haja um bom motivo para você armazenar logs além de um período de 30 ou 90 dias.

* [Saiba como coletar contadores de desempenho ou logs usando a extensão de diagnóstico](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Análise e visualização de eventos com Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise de eventos e visualização com logs de Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Análise e visualização de eventos com Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise de eventos e visualização com logs de Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
