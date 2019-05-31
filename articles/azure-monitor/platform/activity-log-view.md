---
title: Ver eventos de registo de atividades do Azure no Azure Monitor
description: Ver o registo de atividade do Azure no Azure Monitor e obter com o PowerShell, CLI e REST API.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248118"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Ver e obter eventos de registo de atividade do Azure

O [registo de atividades do Azure](activity-logs-overview.md) fornece informações sobre os eventos de nível de assinatura que ocorreram no Azure. Este artigo fornece detalhes sobre diferentes métodos para visualização e a recuperação de eventos de registo de atividades.

## <a name="azure-portal"></a>Portal do Azure
Ver o registo de atividade para todos os recursos a partir da **Monitor** menu no portal do Azure. Ver o registo de atividade para um recurso específico do **registo de atividades** opção no menu desse recurso.

![Ver o registo de atividade](./media/activity-logs-overview/view-activity-log.png)

Pode filtrar eventos de registo de atividades pelos seguintes campos:

* **Período de tempo**: A hora de início e de fim para eventos.
* **Categoria**: A categoria de evento, conforme descrito em [categorias no registo de atividades](activity-logs-overview.md#categories-in-the-activity-log).
* **Subscrição**: Um ou mais nomes de subscrição do Azure.
* **Grupo de recursos**: Um ou mais grupos de recursos em subscrições selecionadas.
* **O recurso (nome)** :-o nome de um recurso específico.
* **Tipo de recurso**: O tipo de recurso, por exemplo _Microsoft.Compute/virtualmachines_.
* **Nome da operação** -o nome de uma operação do Azure Resource Manager, por exemplo _Microsoft.SQL/servers/Write_.
* **Gravidade**: O nível de gravidade do evento. Valores disponíveis são _informativo_, _aviso_, _erro_, _crítico_.
* **Evento iniciado por**: O utilizador que executou a operação.
* **Abra pesquisa**: Abra a caixa de pesquisa de texto que procura essa cadeia de caracteres em todos os campos em todos os eventos.

### <a name="view-change-history"></a>Ver histórico de alteração

Ao revisar o registo de atividades, pode ajudar a ver o que aconteceram alterações durante essa hora do evento. Pode ver estas informações com **histórico de alterações**. Selecione um evento no registo de atividades que pretende ver mais aprofundadamente em. Selecione o **(pré-visualização) de histórico de alterações** separador para ver qualquer associados a alterações com esse evento.

![Lista de histórico de alteração de um evento](media/activity-logs-overview/change-history-event.png)

Se existirem quaisquer alterações associadas com o evento, verá uma lista de alterações que pode selecionar. Esta ação abre o **(pré-visualização) de histórico de alterações** página. Nesta página Ver as alterações ao recurso. Como pode ver no exemplo seguinte, é possível não só vê que a VM foi alterada tamanhos, mas o que o tamanho da VM anterior foi antes da alteração e o que foi alterada para.

![Página de histórico de alteração que mostra as diferenças](media/activity-logs-overview/change-history-event-details.png)

Para saber mais sobre o histórico de alterações, veja [obter as alterações de recursos](../../governance/resource-graph/how-to/get-resource-changes.md).


## <a name="log-analytics-workspace"></a>Área de trabalho do log Analytics
Clique em **registos** na parte superior a **registo de atividades** página para abrir o [solução de monitorização Log Analytics da atividade](activity-log-collect.md) para a subscrição. Isso permitirá que veja a análise para o registo de atividades e para executar [consultas de registo](../log-query/log-query-overview.md) com o **AzureActivity** tabela. Se o registo de Atividades não está ligado a uma área de trabalho do Log Analytics, será solicitado a efetuar esta configuração.



## <a name="powershell"></a>PowerShell
Utilize o [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet para obter o registo de atividades do PowerShell. Seguem-se alguns exemplos comuns.

> [!NOTE]
> `Get-AzLog` Fornece apenas 15 dias do histórico. Utilize o **- MaxEvents** parâmetro para consultar os eventos de N últimos para além de 15 dias. Para aceder a eventos com mais de 15 dias, utilize a REST API ou SDK. Se não incluir **StartTime**, em seguida, o valor predefinido é **EndTime** menos uma hora. Se não incluir **EndTime**, em seguida, o valor predefinido é a hora atual. Todas as horas são em formato UTC.


Obter entradas de log criadas após uma data determinada hora:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Obter entradas de registo entre um intervalo de tempo de datas:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log do grupo de recursos específico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obter entradas de registo de um fornecedor de recursos específico entre um intervalo de tempo de datas:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log com um chamador específico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Obtenha os últimas 1 000 eventos:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
Uso [registo de atividade do monitor az](cli-samples.md#view-activity-log-for-a-subscription) para obter o registo de atividade da CLI. Seguem-se alguns exemplos comuns.


Ver todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Obter entradas de log do grupo de recursos específico:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Obter entradas de log com um chamador específico:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Obter registos pelo autor da chamada num tipo de recurso, dentro de um intervalo de datas:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Utilize o [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) para obter o registo de atividade de um cliente REST. Seguem-se alguns exemplos comuns.

Obter registos de atividades com o filtro:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obter registos de atividades com o filtro e selecione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter registos de atividades de determinados:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter registos de atividade sem filtro ou selecione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Próximos Passos

* [Leia uma visão geral do registo de atividades](activity-logs-overview.md)
* [Arquivar o registo de atividades para o armazenamento ou transmitir para Hubs de eventos](activity-log-export.md)
* [O registo de atividades do Azure para os Hubs de eventos do Stream](activity-logs-stream-event-hubs.md)
* [Arquivar o registo de atividades do Azure para o armazenamento](archive-activity-log.md)

