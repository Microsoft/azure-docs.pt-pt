---
title: Consultas de auditoria em consultas de registo do Azure Monitor
description: Detalhes dos registos de auditoria de consulta de registo que fornecem telemetria sobre consultas de log executadas no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/03/2020
ms.openlocfilehash: 1c0247c5adfe60dc2436c832cf3d561882ae3a5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760166"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Consultas de auditoria em Registos do Monitor Azure (pré-visualização)
Os registos de auditoria de consulta de registo fornecem telemetria sobre consultas de registo executadas no Azure Monitor. Isto inclui informações como quando uma consulta foi executada, quem a executou, que ferramenta foi usada, o texto de consulta, e estatísticas de desempenho descrevendo a execução da consulta.


## <a name="configure-query-auditing"></a>Auditoria de consulta de configuração
A auditoria de consulta é ativada com uma [definição de diagnóstico](../platform/diagnostic-settings.md) no espaço de trabalho Log Analytics. Isto permite-lhe enviar dados de auditoria para o espaço de trabalho atual ou qualquer outro espaço de trabalho na sua subscrição, para Azure Event Hubs para enviar fora do Azure, ou para Azure Storage para arquivamento. 

### <a name="azure-portal"></a>Portal do Azure
Aceda à definição de diagnóstico de um espaço de trabalho Log Analytics no portal Azure em qualquer um dos seguintes locais:

- A partir do menu **Azure Monitor,** selecione **definições de Diagnóstico**e, em seguida, localize e selecione o espaço de trabalho.

    [![Definições de diagnóstico Azure Monitor ](media/log-query-audit/diagnostic-setting-monitor.png)](media/log-query-audit/diagnostic-setting-monitor.png#lightbox) 

- A partir do menu **de espaços de trabalho Log Analytics,** selecione o espaço de trabalho e, em seguida, selecione as **definições de Diagnóstico**.

    [![Definições de diagnóstico Log Analytics espaço de ](media/log-query-audit/diagnostic-setting-workspace.png) trabalho ](media/log-query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Modelo do Resource Manager
Pode obter um modelo de gestor de recursos de exemplo a partir da [definição](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace)de Diagnóstico para log analytics espaço de trabalho .

## <a name="audit-data"></a>Dados de auditoria
Um registo de auditoria é criado cada vez que uma consulta é executada. Se enviar os dados para um espaço de trabalho log analytics, é armazenado numa tabela chamada *LAQueryLogs*. A tabela seguinte descreve as propriedades em cada registo dos dados de auditoria.

| Campo | Descrição |
|:---|:---|
| TimeGenerated         | Tempo UTC quando a consulta foi submetida. |
| CorrelationId         | Identificação única para identificar a consulta. Pode ser usado em cenários de resolução de problemas quando contactar a Microsoft para obter assistência. |
| AADObjectId           | ID do Diretório Ativo Azure da conta de utilizador que iniciou a consulta.  |
| AADTenantId           | Identificação do inquilino da conta de utilizador que iniciou a consulta.  |
| AADEmail              | E-mail do inquilino da conta de utilizador que iniciou a consulta.  |
| AADClientId           | ID e nome resolvido da aplicação usada para iniciar a consulta. |
| RequestClientApp      | Nome resolvido da aplicação usada para iniciar a consulta. |
| ConsultaTimeRangeStart   | Início do intervalo de tempo selecionado para a consulta. Isto pode não ser povoado em certos cenários, como quando a consulta é iniciada a partir de Log Analytics, e o intervalo de tempo é especificado dentro da consulta e não no tempo escolhidor. |
| ConsultaTimeRangeEnd     | Fim do intervalo de tempo selecionado para a consulta. Isto pode não ser povoado em certos cenários, como quando a consulta é iniciada a partir de Log Analytics, e o intervalo de tempo é especificado dentro da consulta e não no tempo escolhidor.  |
| Texto de consulta             | Texto da consulta que foi executada. |
| RequestTarget         | A API URL foi usada para submeter a consulta.  |
| PedidoContexto        | Lista de recursos que a consulta foi solicitada para concorrer. Contém até três matrizes de cordas: espaços de trabalho, aplicações e recursos. As consultas de subscrição ou de grupo de recursos mostrarão como *recursos*. Inclui o alvo implícito pelo RequestTarget.<br>O ID de recursos para cada recurso será incluído se puder ser resolvido. Pode não ser resolvido se um erro for devolvido no acesso ao recurso. Neste caso, será utilizado o texto específico da consulta.<br>Se a consulta usar um nome ambíguo, como um nome de espaço de trabalho existente em várias subscrições, este nome ambíguo será usado. |
| PedidoContextFiltros | Conjunto de filtros especificados como parte da invocação de consulta. Inclui até três possíveis arrays de cordas:<br>- RecursosTipos - tipo de recurso para limitar o âmbito da consulta<br>- Workspaces - lista de espaços de trabalho para limitar a consulta<br>- WorkspaceRegions - lista de regiões do espaço de trabalho para limitar a consulta |
| Código de Resposta          | Código de resposta HTTP devolvido quando a consulta foi submetida. |
| RespostaDurationMs    | Hora da resposta ser devolvida.  |
| ResponseRowCount     | Número total de linhas devolvidas pela consulta. |
| StatsCPUTimeMs       | Tempo total de computação utilizado para computação, análise e recolha de dados. Só povoado se a consulta voltar com o código de estado 200. |
| StatsDataProcessedkB | Quantidade de dados que foram acedidos para processar a consulta. Influenciado pelo tamanho da tabela-alvo, o tempo de utilização, os filtros aplicados e o número de colunas referenciadas. Só povoado se a consulta voltar com o código de estado 200. |
| StatsDataProcessedStart | Tempo dos dados mais antigos que foram acedidos para processar a consulta. Influenciado pelo tempo explícito de consulta e filtros aplicados. Isto pode ser maior do que o tempo explícito devido à divisão de dados. Só povoado se a consulta voltar com o código de estado 200. |
| StatsDataProcessedEnd  |Tempo dos dados mais recentes que foram acedidos para processar a consulta. Influenciado pelo tempo explícito de consulta e filtros aplicados. Isto pode ser maior do que o tempo explícito devido à divisão de dados. Só povoado se a consulta voltar com o código de estado 200. |
| StatsWorkspace | Número de espaços de trabalho acedidos pela consulta. Só povoado se a consulta voltar com o código de estado 200. |
| StatsRegionCount | Número de regiões acedidas pela consulta. Só povoado se a consulta voltar com o código de estado 200. |

## <a name="considerations"></a>Considerações

- As consultas só são registadas quando executadas num contexto de utilizador. Nenhum serviço-a-serviço dentro de Azure será registado. Os dois conjuntos primários de consultas que esta exclusão engloba são cálculos de faturação e execuções de alerta automatizados. No caso de alertas, apenas a consulta de alerta agendada em si não será registada; a execução inicial do alerta no ecrã de criação de alerta é executada num contexto de utilizador e estará disponível para efeitos de auditoria. 
- As estatísticas de desempenho não estão disponíveis para consultas provenientes do proxy Azure Data Explorer. Todos os outros dados para estas consultas continuarão a ser preenchidos.
- A dica *h* sobre cordas que [obfusca as cordas literais](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) não terá um efeito nos registos de auditoria de consulta. As consultas serão capturadas exatamente como submetidas sem que a corda seja obfundada. Deve garantir que apenas os utilizadores que tenham direitos de conformidade para ver estes dados são capazes de o fazer utilizando os vários modos RBAC disponíveis nos espaços de trabalho do Log Analytics.
- Para consultas que incluam dados de vários espaços de trabalho, a consulta só será captada nos espaços de trabalho a que o utilizador tem acesso.

## <a name="costs"></a>Custos  
Não há custo para a Extensão de Diagnóstico Azure, mas pode incorrer em encargos para os dados ingeridos. Verifique [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para o destino onde está a recolher dados.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as definições de diagnóstico](../platform/diagnostic-settings.md).
- Saiba mais sobre [a otimização de consultas de registo.](query-optimization.md)
