---
title: Exibir eventos do log de atividades do Azure no Azure Monitor
description: Exiba o log de atividades do Azure em Azure Monitor e recupere com o PowerShell, a CLI e a API REST.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 54a1d3e503ddb8b11109596decde94a2834dbf47
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529971"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Exibir e recuperar eventos do log de atividades do Azure

O [log de atividades do Azure](activity-logs-overview.md) fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. Este artigo fornece detalhes sobre diferentes métodos para exibir e recuperar eventos do log de atividades.

## <a name="azure-portal"></a>Portal do Azure
Exiba o log de atividades de todos os recursos no menu **monitorar** na portal do Azure. Exiba o log de atividades de um recurso específico da opção **log de atividades** no menu desse recurso.

![Exibir log de atividades](./media/activity-logs-overview/view-activity-log.png)

Você pode filtrar eventos do log de atividades pelos seguintes campos:

* **TimeSpan**: a hora de início e de término dos eventos.
* **Categoria**: a categoria de evento, conforme descrito em [categorias no log de atividades](activity-log-view.md#categories-in-the-activity-log).
* **Assinatura**: um ou mais nomes de assinatura do Azure.
* **Grupo de recursos**: um ou mais grupos de recursos dentro das assinaturas selecionadas.
* **Recurso (nome)** :-o nome de um recurso específico.
* **Tipo de recurso**: o tipo de recurso, por exemplo, _Microsoft. Compute/VirtualMachines_.
* **Nome da operação** -o nome de uma operação de Azure Resource Manager, por exemplo, _Microsoft. SQL/Servers/Write_.
* **Severidade**: o nível de severidade do evento. Os valores disponíveis são _informativo_, _aviso_, _erro_, _crítico_.
* **Evento iniciado por**: o usuário que realizou a operação.
* **Abrir pesquisa**: caixa de pesquisa de texto aberta que pesquisa essa cadeia de caracteres em todos os campos em todos os eventos.

## <a name="categories-in-the-activity-log"></a>Categorias no log de atividades
Cada evento no log de atividades tem uma categoria específica que são descritas na tabela a seguir. Para todos os detalhes sobre esquemas uma destas categorias, consulte [esquema de eventos de registo de atividades do Azure](activity-log-schema.md). 

| Categoria | Descrição |
|:---|:---|
| Administrativa | Contém o registro de todas as operações de criação, atualização, exclusão e ação executadas por meio do Resource Manager. Exemplos de eventos administrativos incluem _criar máquina virtual_ e _excluir grupo de segurança de rede_.<br><br>Cada ação tomada por um usuário ou aplicativo usando o Resource Manager é modelada como uma operação em um determinado tipo de recurso. Se o tipo de operação for _gravação_, _exclusão_ou _ação_, os registros de início e êxito ou falha da operação serão registrados na categoria administrativa. Os eventos administrativos também incluem quaisquer alterações no controle de acesso baseado em função em uma assinatura. |
| Service Health | Contém o registro de qualquer incidente de integridade do serviço que ocorreu no Azure. Um exemplo de SQL Azure de eventos de integridade do serviço _no leste dos EUA está apresentando tempo de inatividade_. <br><br>Os eventos de integridade do serviço são fornecidos em seis variedades: _ação necessária_, _recuperação assistida_, _incidente_, _manutenção_, _informações_ou _segurança_. Esses eventos serão criados somente se você tiver um recurso na assinatura que seria impactado pelo evento.
| Estado de Funcionamento de Recursos | Contém o registro dos eventos de integridade do recurso que ocorreram para os recursos do Azure. Um exemplo de um evento de Resource Health é o _status de integridade da máquina virtual alterado para indisponível_.<br><br>Resource Health eventos podem representar um dos quatro status de integridade: _disponível_, _indisponível_, _degradado_e _desconhecido_. Além disso, Resource Health eventos podem ser categorizados como sendo _iniciado pela plataforma_ ou _pelo usuário_. |
| Alerta | Contém o registro de ativações para alertas do Azure. Um exemplo de um evento de alerta é _% de CPU em myVM tem mais de 80 para os últimos 5 minutos_.|
| Dimensionamento automático | Contém o registro de todos os eventos relacionados à operação do mecanismo de dimensionamento automático com base em qualquer configuração de dimensionamento automático que você definiu em sua assinatura. Um exemplo de um evento de dimensionamento automático é a _ação de escalabilidade vertical com falha_. |
| Recomendação | Contém eventos de recomendação do Azure Advisor. |
| Segurança | Contém o registro de todos os alertas gerados pela central de segurança do Azure. Um exemplo de um evento de segurança é um _arquivo de extensão dupla suspeito executado_. |
| Política | Contém registros de todas as operações de ação de efeito executadas por Azure Policy. Exemplos de eventos de política incluem _auditoria_ e _negação_. Cada ação tomada pela política é modelada como uma operação em um recurso. |

## <a name="view-change-history"></a>Exibir histórico de alterações

Ao examinar o log de atividades, ele pode ajudar a ver quais alterações ocorreram durante esse evento. Você pode exibir essas informações com o **histórico de alterações**. Selecione um evento do log de atividades que você deseja que pareça mais profundo. Selecione a guia **histórico de alterações (versão prévia)** para exibir as alterações associadas a esse evento.

![Lista de histórico de alterações para um evento](media/activity-logs-overview/change-history-event.png)

Se houver alterações associadas ao evento, você verá uma lista de alterações que pode selecionar. Isso abre a página **histórico de alterações (versão prévia)** . Nessa página, você vê as alterações no recurso. Como você pode ver no exemplo a seguir, podemos ver não apenas que a VM mudou de tamanhos, mas qual era o tamanho anterior da VM antes da alteração e para que ela foi alterada.

![Página de histórico de alterações mostrando diferenças](media/activity-logs-overview/change-history-event-details.png)

Para saber mais sobre o histórico de alterações, consulte [obter alterações de recurso](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Use o cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) para recuperar o log de atividades do PowerShell. A seguir estão alguns exemplos comuns.

> [!NOTE]
> `Get-AzLog` fornece apenas 15 dias de histórico. Use o parâmetro **-MaxEvents** para consultar os últimos N eventos além de 15 dias. Para acessar eventos com mais de 15 dias, use a API REST ou o SDK. Se você não incluir **StartTime**, o valor padrão será **EndTime** menos uma hora. Se você não incluir **EndTime**, o valor padrão será current time. Todas as horas estão em UTC.


Obter entradas de log criadas após uma data e hora específica:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Obter entradas de log entre um intervalo de data/hora:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log de um grupo de recursos específico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obter entradas de log de um provedor de recursos específico entre um intervalo de data/hora:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log com um chamador específico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Obter os últimos 1000 eventos:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
Use [AZ monitor Activity-log](cli-samples.md#view-activity-log-for-a-subscription) para recuperar o log de atividades da CLI. A seguir estão alguns exemplos comuns.


Exiba todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Obter entradas de log de um grupo de recursos específico:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Obter entradas de log com um chamador específico:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Obter logs pelo chamador em um tipo de recurso, dentro de um intervalo de datas:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Use a [API rest Azure monitor](https://docs.microsoft.com/rest/api/monitor/) para recuperar o log de atividades de um cliente REST. A seguir estão alguns exemplos comuns.

Obter logs de atividade com filtro:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obter logs de atividade com filtro e selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter logs de atividade com SELECT:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter logs de atividade sem filtro ou selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="activity-logs-analytics-monitoring-solution"></a>Solução de monitoramento de análise de logs de atividades
A solução de monitoramento de Log Analytics do Azure inclui várias consultas de log e exibições para analisar os registros de log de atividades em seu espaço de trabalho do Log Analytics.

### <a name="prerequisites"></a>Pré-requisitos
Você deve criar uma configuração de diagnóstico para enviar o log de atividades da sua assinatura para um espaço de trabalho Log Analytics. Consulte [coletar logs da plataforma Azure no espaço de trabalho log Analytics no Azure monitor](resource-logs-collect-workspace.md).

### <a name="install-the-solution"></a>Instalar a solução
Use o procedimento em [instalar uma solução de monitoramento](../insights/solutions.md#install-a-monitoring-solution) para instalar a solução de **análise do log de atividades** . Não há nenhuma configuração adicional necessária.

### <a name="use-the-solution"></a>Utilizar a solução
Clique em **logs** na parte superior da página do **log de atividades** para abrir a solução de monitoramento de [análise do log de atividades](activity-log-collect.md) para a assinatura. Ou acesse todas as soluções de monitoramento em seu menu **Monitor** de assinatura no portal do Azure. Selecione **mais** na seção **insights** para abrir a página **visão geral** com os blocos de solução. O bloco **logs de atividade do Azure** exibe uma contagem do número de registros **AzureActivity** em seu espaço de trabalho.

![Mosaico de registos de atividades do Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Clique no bloco **logs de atividade do Azure** para abrir a exibição **logs de atividade do Azure** . A exibição inclui as partes de visualização na tabela a seguir. Cada parte lista até 10 itens que correspondem aos critérios das partes para o intervalo de tempo especificado. Você pode executar uma consulta de log que retorna todos os registros correspondentes clicando em **Ver todos** na parte inferior da parte.

![Dashboard de registos de atividades do Azure](media/collect-activity-logs/activity-log-dash.png)

| Parte de visualização | Descrição |
| --- | --- |
| Entradas de registo de atividades do Azure | Mostra um gráfico de barras dos totais principais registros de entrada do log de atividades do Azure para o intervalo de datas selecionado e mostra uma lista dos 10 principais chamadores de atividade. Clique para executar uma pesquisa de registos para o gráfico de barras `AzureActivity`. Clique em um item do chamador para executar uma pesquisa de logs retornando todas as entradas do log de atividades para esse item. |
| Registos de atividades por Estado | Mostra um gráfico de rosca para o status do log de atividades do Azure para o intervalo de datas selecionado e uma lista dos dez principais registros de status. Clique no gráfico para executar uma consulta de log para `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Clique em um item de status para executar uma pesquisa de logs que retorna todas as entradas do log de atividades para esse registro de status. |
| Registos de atividades por recurso | Mostra o número total de recursos com logs de atividade e lista os dez principais recursos com contagens de registros para cada recurso. Clique na área total para executar uma pesquisa de registos para `AzureActivity | summarize AggregatedValue = count() by Resource`, que mostra todos os recursos do Azure disponíveis para a solução. Clique em um recurso para executar uma consulta de log que retorna todos os registros de atividade para esse recurso. |
| Registos de atividade pelo fornecedor de recursos | Mostra o número total de provedores de recursos que produzem logs de atividades e lista os dez principais. Clique na área total para executar uma consulta de log para `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, que mostra todos os provedores de recursos do Azure. Clique em um provedor de recursos para executar uma consulta de log que retorna todos os registros de atividade do provedor. |




## <a name="next-steps"></a>Passos seguintes

* [Leia uma visão geral dos logs da plataforma](platform-logs-overview.md)
* [Criar configuração de diagnóstico para enviar logs de atividade para outros destinos](diagnostic-settings.md)
