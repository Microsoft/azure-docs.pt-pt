---
title: Ver eventos de log da Atividade Azure no Monitor Azure
description: Veja o log in Azure Activity no Azure Monitor e recupere com powerShell, CLI e REST API.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 9df7593a9fd191d3a734fba5e81fb1aecba08345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275051"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Ver e recuperar eventos de log da Atividade Azure

O [Registo de Atividades do Azure](platform-logs-overview.md) fornece informações sobre eventos de nível de subscrição que ocorreram no Azure. Este artigo fornece detalhes sobre diferentes métodos para visualização e recuperação de eventos de Registo de Atividades.

## <a name="azure-portal"></a>Portal do Azure
Consulte o Registo de Atividades para todos os recursos do menu **Monitor** no portal Azure. Consulte o Registo de Atividades para obter um recurso específico da opção Registo de **Atividades** no menu desse recurso.

![Ver Registo de Atividades](./media/activity-logs-overview/view-activity-log.png)

Pode filtrar eventos de Registo de Atividadepelos seguintes campos:

* **Timespan**: O início e o fim do tempo para os eventos.
* **Categoria**: A categoria do evento descrita nas [categorias do Registo de Atividades](activity-log-view.md#categories-in-the-activity-log).
* **Subscrição**: Um ou mais nomes de subscrição Azure.
* **Grupo de recursos**: Um ou mais grupos de recursos dentro das subscrições selecionadas.
* **Recurso (nome)**: - O nome de um recurso específico.
* **Tipo**de recurso : O tipo de recurso, por exemplo _Microsoft.Compute/virtualmachines_.
* **Nome** da operação - O nome de uma operação do Gestor de Recursos Azure, por exemplo _Microsoft.SQL/servers/Write_.
* **Gravidade**: O nível de gravidade do evento. Os valores disponíveis são _Informativos,_ _Aviso,_ _Erro,_ _Crítico._
* **Evento iniciado por**: O utilizador que realizou a operação.
* **Pesquisa aberta**: Abrir a caixa de pesquisa de texto que procura essa corda em todos os campos em todos os eventos.

## <a name="categories-in-the-activity-log"></a>Categorias no registo de Atividades
Cada evento no Registo de Atividades tem uma categoria específica que são descritas na tabela seguinte. Para mais detalhes sobre os esquemas destas categorias, consulte o esquema do [evento Azure Activity Log](activity-log-schema.md). 

| Categoria | Descrição |
|:---|:---|
| Administrativa | Contém o registo de todas as operações de criação, atualização, eliminação e ação realizadas através do Gestor de Recursos. Exemplos de eventos administrativos incluem _criar máquina virtual_ e eliminar grupo de segurança de _rede_.<br><br>Todas as medidas tomadas por um utilizador ou aplicação utilizando o Gestor de Recursos são modeladas como uma operação num determinado tipo de recurso. Se o tipo de operação for _Write_, _Delete_, or _Action,_ os registos do início e do sucesso ou da falha dessa operação são registados na categoria Administrativa. Os eventos administrativos também incluem quaisquer alterações ao controlo de acesso baseado em papéis numa subscrição. |
| Service Health | Contém o registo de quaisquer incidentes de saúde de serviço que tenham ocorrido em Azure. Um exemplo de um evento de Saúde de Serviço _SQL Azure no Leste dos EUA está a passar por um tempo de inatividade._ <br><br>Serviço Os eventos de saúde vêm em Seis variedades: _Ação Necessária,_ _Recuperação Assistida,_ _Incidente,_ _Manutenção,_ _Informação_ou _Segurança._ Estes eventos só são criados se tiver um recurso na subscrição que seria impactado pelo evento.
| Estado de Funcionamento de Recursos | Contém o registo de quaisquer eventos de saúde de recursos que tenham ocorrido aos seus recursos Azure. Um exemplo de um evento de Saúde de Recursos é o estado de saúde da _Máquina Virtual alterado para indisponível_.<br><br>Os eventos de saúde de recursos podem representar um dos quatro estados de saúde: _Disponível,_ _Indisponível,_ _Degradado_e _Desconhecido._ Além disso, os eventos de Saúde de Recursos podem ser categorizados como sendo _iniciados_ pela plataforma ou _iniciados pelo utilizador_. |
| Alerta | Contém o registo de ativações para alertas Azure. Um exemplo de um evento de alerta é _cpu % no myVM foi superior a 80 nos últimos 5 minutos_.|
| Dimensionamento Automático | Contém o registo de quaisquer eventos relacionados com o funcionamento do motor de escala automática com base em quaisquer definições de escala automática que tenha definido na sua subscrição. Um exemplo de um evento de escala automática é a _ação de escala automática falhada._ |
| Recomendação | Contém eventos de recomendação do Consultor Azure. |
| Segurança | Contém o registo de quaisquer alertas gerados pelo Azure Security Center. Um exemplo de um evento de segurança é _ficheiro de extensão dupla suspeito executado_. |
| Política | Contém registos de todas as operações de ação de efeito realizadas pela Política Azure. Exemplos de eventos de política incluem _Auditoria_ e _Negação._ Todas as medidas tomadas pela Policy são modeladas como uma operação sobre um recurso. |

## <a name="view-change-history"></a>Ver história de mudança

Ao rever o Registo de Atividades, pode ajudar a ver as mudanças que aconteceram durante o tempo do evento. Pode ver esta informação com **o histórico de Mudanças.** Selecione um evento a partir do Registo de Atividades que pretende aprofundar. Selecione o separador **'Pré-visualização' do 'Alterar' para** ver quaisquer alterações associadas a esse evento.

![Alterar lista de história para um evento](media/activity-logs-overview/change-history-event.png)

Se houver alterações associadas ao evento, verá uma lista de alterações que pode selecionar. Isto abre a página **do histórico de alteração (Pré-visualização).** Nesta página vê-se as alterações ao recurso. Como pode ver pelo exemplo seguinte, somos capazes de ver não só que o VM mudou de tamanho, mas qual era o tamanho anterior do VM antes da mudança e para o que foi alterado.

![Alterar página de histórico mostrando diferenças](media/activity-logs-overview/change-history-event-details.png)

Para saber mais sobre a história da Mudança, consulte [obter alterações de recursos.](../../governance/resource-graph/how-to/get-resource-changes.md)






## <a name="powershell"></a>PowerShell
Utilize o cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) para recuperar o Registo de Atividade da PowerShell. Seguem-se alguns exemplos comuns.

> [!NOTE]
> `Get-AzLog`apenas fornece 15 dias de história. Use o parâmetro **-MaxEvents** para consultar os últimos eventos N para além de 15 dias. Para aceder a eventos com mais de 15 dias, utilize o REST API ou SDK. Se não incluir o **StartTime,** então o valor predefinido é **EndTime** menos uma hora. Se não incluir o **EndTime,** então o valor predefinido é o tempo atual. Todos os tempos estão na UTC.


Obtenha entradas de log criadas após uma hora específica de data:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Obtenha entradas de registo entre um intervalo de hora de data:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenha entradas de registo de um grupo de recursos específicos:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obtenha entradas de registo de um fornecedor de recursos específicos entre uma faixa de tempo de data:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenha entradas de registo com um chamador específico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Obtenha os últimos 1000 eventos:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
Utilize o registo de atividade do [monitor az](cli-samples.md#view-activity-log-for-a-subscription) para recuperar o Registo de Atividade do CLI. Seguem-se alguns exemplos comuns.


Consulte todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Obtenha entradas de registo de um grupo de recursos específicos:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Obtenha entradas de registo com um chamador específico:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Obtenha registos por chamada num tipo de recurso, dentro de um intervalo de data:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Utilize a [API REST Do Monitor Azure](https://docs.microsoft.com/rest/api/monitor/) para recuperar o Registo de Atividades de um cliente REST. Seguem-se alguns exemplos comuns.

Obtenha Registos de Atividade com filtro:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obtenha Registos de Atividade com filtro e selecione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenha Registos de Atividade com selecione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenha Registos de Atividade sem filtro ou selecione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="activity-logs-analytics-monitoring-solution"></a>Solução de monitorização de Logs de Atividade Analytics
A solução de monitorização do Azure Log Analytics inclui múltiplas consultas de registo e vistas para analisar os registos de Registo de Atividade sintetizadores no seu espaço de trabalho Log Analytics.

### <a name="prerequisites"></a>Pré-requisitos
Tem de criar uma definição de diagnóstico para enviar o registo de Atividade supor a sua subscrição a um espaço de trabalho do Log Analytics. Consulte os [registos da plataforma Do Tamina](resource-logs-collect-workspace.md)do Collect Azure no espaço de trabalho do Log Analytics no Monitor Azure .

### <a name="install-the-solution"></a>Instalar a solução
Utilize o procedimento em [Instalar uma solução de monitorização](../insights/solutions.md#install-a-monitoring-solution) para instalar a solução **Activity Log Analytics.** Não é necessária uma configuração adicional.

### <a name="use-the-solution"></a>Use a solução
Clique em **Registos** no topo da página **De registo** de atividade para abrir a solução de monitorização do Registo [de Atividades Analytics](activity-log-collect.md) para a subscrição. Ou aceda a todas as soluções de monitorização no menu monitor de **subscrição** no portal Azure. Selecione **Mais** na secção **Insights** para abrir a página **'Overview'** com os azulejos da solução. O azulejo **Azure Activity Logs** apresenta uma contagem do número de registos **azureActivity** no seu espaço de trabalho.

![Azulejos de logs de atividade azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Clique no azulejo **Azure Activity Logs** para abrir a vista Registos de Atividade sintetizações **do Azure.** A vista inclui as peças de visualização na tabela a seguir. Cada peça lista até 10 itens que correspondam aos critérios dessas peças para o intervalo de tempo especificado. Pode executar uma consulta de registo que devolve todos os registos correspondentes clicando **Ver tudo** na parte inferior da peça.

![Painel de logs de atividade azure](media/collect-activity-logs/activity-log-dash.png)

| Parte de visualização | Descrição |
| --- | --- |
| Entradas de registo de atividade sinuosa do Azure | Mostra um gráfico de barras do top Azure Activity Log registo total total para o intervalo de data que selecionou e mostra uma lista dos 10 melhores participantes da atividade. Clique na tabela de barras `AzureActivity`para fazer uma pesquisa de registo. Clique num item de chamada para executar uma pesquisa de registo devolvendo todas as entradas de Registo de Atividade para esse item. |
| Registos de Atividade por Estado | Mostra um gráfico de donut para o estado de Registo de Atividade seletiva do Azure para a gama de datas selecionada e uma lista dos dez melhores registos de estado. Clique na tabela para fazer `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`uma consulta de registo para . Clique num item de estado para executar uma pesquisa de registo devolvendo todas as entradas de Registo de Atividade para esse registo de estado. |
| Registos de Atividade por Recurso | Mostra o número total de recursos com Registos de Atividade e lista os dez melhores recursos com contagens recorde para cada recurso. Clique na área total para `AzureActivity | summarize AggregatedValue = count() by Resource`fazer uma pesquisa de registo, que mostra todos os recursos Do Azure disponíveis para a solução. Clique num recurso para executar uma consulta de registo devolvendo todos os registos de atividade para esse recurso. |
| Registos de Atividades por Fornecedor de Recursos | Mostra o número total de fornecedores de recursos que produzem Registos de Atividade e lista o top 10. Clique na área total para fazer `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`uma consulta de log para , que mostra todos os fornecedores de recursos Azure. Clique num fornecedor de recursos para executar uma consulta de registo devolvendo todos os registos de atividade para o fornecedor. |




## <a name="next-steps"></a>Passos seguintes

* [Leia uma visão geral dos registos da plataforma](platform-logs-overview.md)
* [Criar definição de diagnóstico para enviar registos de Atividade para outros destinos](diagnostic-settings.md)
