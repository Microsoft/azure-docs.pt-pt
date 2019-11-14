---
title: Arquivar logs de recursos do Azure na conta de armazenamento | Microsoft Docs
description: Saiba como arquivar os logs de recursos do Azure para retenção de longo prazo em uma conta de armazenamento.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 306f6cb0b50b7befcbf51e6164a5da887d35616e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74030882"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Arquivar logs de recursos do Azure para a conta de armazenamento
[Os logs de recursos](resource-logs-overview.md) no Azure fornecem dados avançados e frequentes sobre a operação interna de um recurso do Azure. Este artigo descreve como coletar logs de recursos para uma conta de armazenamento do Azure para reter dados para arquivamento.

## <a name="prerequisites"></a>Pré-requisitos
Você precisará [criar uma conta de armazenamento do Azure](../../storage/common/storage-quickstart-create-account.md) se ainda não tiver uma. A conta de armazenamento não precisa estar na mesma assinatura que o recurso que envia logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.


> [!IMPORTANT]
> Atualmente, não há suporte para contas de Azure Data Lake Storage Gen2 como destino para configurações de diagnóstico, embora elas possam estar listadas como uma opção válida na portal do Azure.


Você não deve usar uma conta de armazenamento existente que tenha outros dados de não monitoramento armazenados nele para que você possa controlar melhor o acesso aos dados de monitoramento. No entanto, se você também estiver arquivando o [log de atividades](activity-logs-overview.md) em uma conta de armazenamento, poderá optar por usar essa mesma conta de armazenamento para manter todos os dados de monitoramento em um local central.

## <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico
Os logs de recursos não são coletados por padrão. Colete-os em uma conta de armazenamento do Azure e em outros destinos criando uma configuração de diagnóstico para um recurso do Azure. Consulte [criar configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md) para obter detalhes.


## <a name="data-retention"></a>Retenção de dados
A política de retenção define o número de dias para reter os dados de cada categoria de log e os dados de métrica armazenados em uma conta de armazenamento. Uma política de retenção pode ser qualquer número de dias entre 0 e 365. Uma política de retenção de zero Especifica que os eventos dessa categoria de log são armazenados indefinidamente.

As políticas de retenção são aplicadas por dia, portanto, no final de um dia (UTC), os logs do dia que estão além da política de retenção serão excluídos. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados. O processo de eliminação começa a meia-noite UTC, mas tenha em atenção que pode demorar até 24 horas para os registos para ser eliminado da sua conta de armazenamento. 


## <a name="schema-of-resource-logs-in-storage-account"></a>Esquema de logs de recursos na conta de armazenamento

Depois de criar a configuração de diagnóstico, um contêiner de armazenamento é criado na conta de armazenamento assim que um evento ocorre em uma das categorias de log habilitadas. Os BLOBs dentro do contêiner usam a seguinte convenção de nomenclatura:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, o blob de um grupo de segurança de rede pode ter um nome semelhante ao seguinte:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro da hora especificada no URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao ficheiro PT1H.json à medida que ocorrem. O valor de minuto (m = 00) é sempre 00, pois os eventos do log de recursos são divididos em BLOBs individuais por hora.

No arquivo PT1H. JSON, cada evento é armazenado com o formato a seguir. Isso usará um esquema comum de nível superior, mas será exclusivo para cada serviço do Azure, conforme descrito em [esquema de logs de recursos](resource-logs-overview.md#resource-logs-schema).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Os logs de plataforma são gravados no armazenamento de BLOB usando [linhas JSON](http://jsonlines.org/), em que cada evento é uma linha e o caractere de linha nova indica um novo evento. Esse formato foi implementado em novembro de 2018. Antes dessa data, os logs foram gravados no armazenamento de blob como uma matriz JSON de registros, conforme descrito em [preparar para a alteração de formato para Azure monitor os logs de plataforma arquivados em uma conta de armazenamento](resource-logs-blob-format.md).

## <a name="next-steps"></a>Passos seguintes

* [Baixe BLOBs para análise](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Arquive logs de Azure Active Directory com Azure monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
* [Leia mais sobre os logs de recursos](../../azure-monitor/platform/resource-logs-overview.md).

