---
title: Registos de recursos do Archive Azure para a conta de armazenamento [ Microsoft Docs
description: Saiba como arquivar os seus registos de recursos Azure para retenção a longo prazo numa conta de armazenamento.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 787640ef08ee91220f42065af155c372247f0136
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804610"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Registos de recursos do Archive Azure para conta de armazenamento
[Os registos da plataforma](platform-logs-overview.md) no Azure, incluindo registos de registos de atividades do Azure e de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure de que dependem.  Este artigo descreve a recolha de registos de plataformas numa conta de armazenamento Azure para reter dados para arquivamento.

## <a name="prerequisites"></a>Pré-requisitos
Precisa criar uma conta de [armazenamento Azure](../../storage/common/storage-account-create.md) se ainda não tiver uma. A conta de armazenamento não tem de estar na mesma subscrição que os registos de envio de recursos, desde que o utilizador que configura a definição tenha acesso rBAC adequado a ambas as subscrições.

> [!IMPORTANT]
> Para enviar os dados para armazenamento imutável, delineie a política imutável para a conta de armazenamento como descrito no set e gere as políticas de [imutabilidade para](../../storage/blobs/storage-blob-immutability-policies-manage.md)o armazenamento de Blob . Deve seguir todos os passos deste artigo, incluindo permitir que as bolhas de apêndice protegidos escrevam.

> [!IMPORTANT]
> As contas do Azure Data Lake Storage Gen2 não são atualmente suportadas como destino para configurações de diagnóstico, mesmo que possam ser listadas como uma opção válida no portal Azure.


Não deve utilizar uma conta de armazenamento existente que tenha outros dados não monitorizadores armazenados na si para que possa controlar melhor o acesso aos dados. No entanto, se estiver a arquivar os registos de registos de atividade e de recursos, poderá optar por utilizar a mesma conta de armazenamento para manter todos os dados de monitorização num local central.

## <a name="create-a-diagnostic-setting"></a>Criar uma definição de diagnóstico
Envie registos de plataformas para armazenamento e outros destinos criando uma definição de diagnóstico para um recurso Azure. Consulte [a definição de diagnóstico Para recolher registos e métricas em Azure](diagnostic-settings.md) para obter mais detalhes.


## <a name="collect-data-from-compute-resources"></a>Recolher dados de recursos computacionais
As definições de diagnóstico recolherão registos de recursos para os recursos computacionais do Azure, como qualquer outro recurso, mas não o seu sistema operativo ou cargas de trabalho. Para recolher estes dados, instale o agente de [Diagnóstico si azul](diagnostics-extension-overview.md)e windows. 


## <a name="schema-of-platform-logs-in-storage-account"></a>Esquema de registos de plataforma na conta de armazenamento

Uma vez criado o ajuste de diagnóstico, um recipiente de armazenamento é criado na conta de armazenamento assim que um evento ocorre em uma das categorias de registo habilitado. As bolhas no interior do contentor utilizam a seguinte convenção de nomeação:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, a bolha para um grupo de segurança de rede pode ter um nome semelhante ao seguinte:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro da hora especificada no URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao ficheiro PT1H.json à medida que ocorrem. O valor minúsculo (m=00) é sempre 00, uma vez que os eventos de registo de recursos são divididos em bolhas individuais por hora.

Dentro do ficheiro PT1H.json, cada evento é armazenado com o seguinte formato. Isto utilizará um esquema de nível superior comum, mas será único para cada serviço Azure, conforme descrito em [esquema de logs de recursos](diagnostic-logs-schema.md) e log [sinuoso](activity-log-schema.md)de atividade.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Os registos da plataforma são escritos para armazenamento blob usando [linhas JSON](http://jsonlines.org/), onde cada evento é uma linha e o personagem newline indica um novo evento. Este formato foi implementado em novembro de 2018. Antes desta data, os registos foram escritos para o armazenamento blob como um conjunto json de registos descrito son como descrito na Prepare para a alteração do [formato para registos da plataforma Azure Monitor arquivados numa conta](resource-logs-blob-format.md)de armazenamento .

## <a name="next-steps"></a>Passos seguintes

* [Leia mais sobre registos](platform-logs-overview.md)de recursos .
* [Crie uma definição de diagnóstico para recolher registos e métricas em Azure](diagnostic-settings.md).
* [Baixe bolhas para análise](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Registos de Diretório Ativo Archive Azure com Monitor Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
