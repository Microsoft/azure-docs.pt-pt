---
title: Monitorizar a saúde do espaço de trabalho log Analytics no Azure Monitor
description: Descreve como monitorizar a saúde do seu espaço de trabalho Log Analytics utilizando dados na tabela Operação.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 9eda0acc15badfe7bb2e754d887786aa990d6e24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034970"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Monitorizar a saúde do espaço de trabalho log Analytics no Azure Monitor
Para manter o desempenho e disponibilidade do seu espaço de trabalho Log Analytics no Azure Monitor, é necessário ser capaz de detetar proativamente quaisquer problemas que surjam. Este artigo descreve como monitorizar a saúde do seu espaço de trabalho Log Analytics utilizando dados na tabela [Operação.](/azure/azure-monitor/reference/tables/operation) Esta tabela está incluída em todos os espaços de trabalho do Log Analytics e contém erros e avisos que ocorrem no seu espaço de trabalho. Deve rever regularmente estes dados e criar alertas para ser notificado proativamente quando houver incidentes importantes no seu espaço de trabalho.

## <a name="_logoperation-function"></a>função _LogOperation

O Azure Monitor Logs envia detalhes sobre quaisquer problemas para a tabela [Operação](/azure/azure-monitor/reference/tables/operation) no espaço de trabalho onde ocorreu o problema. A função do sistema **_LogOperation** baseia-se na tabela **Operação** e fornece um conjunto simplificado de informações para análise e alerta.

## <a name="columns"></a>Colunas

A função **_LogOperation** devolve as colunas na tabela seguinte.

| Coluna | Descrição |
|:---|:---|
| TimeGenerated | Hora do incidente ocorreu na UTC. |
| Categoria  | Grupo de categoria de operação. Pode ser usado para filtrar tipos de operações e ajudar a criar auditorias e alertas de sistema mais precisos. Consulte a secção abaixo para obter uma lista de categorias. |
| Operação  | Descrição do tipo de operação. Isto pode indicar um dos limites do Log Analytics, tipo de operação ou parte de um processo. |
| Level | Nível de gravidade da questão:<br>- Informação: Não é necessária atenção específica.<br>- Aviso: O processo não foi concluído como esperado e é necessária atenção.<br>- Erro: Processo falhado e é necessária atenção urgente. 
| Detalhes | A descrição detalhada da operação inclui mensagem de erro específica se existir. |
| _ResourceId | Identificação de recursos do recurso Azure relacionado com a operação.  |
| Computador | Nome do computador se a operação estiver relacionada com um agente do Monitor Azure. |
| CorrelationId | Usado para agrupar operações relacionadas consecutivas. |


## <a name="categories"></a>Categorias

A tabela seguinte descreve as categorias da função _LogOperation. 

| Categoria | Descrição |
|:---|:---|
| Ingestão           | Operações que fazem parte do processo de ingestão de dados. Veja abaixo mais detalhes. |
| Agente               | Indica um problema com a instalação do agente. |
| Recolha de dados     | Operações relacionadas com processos de recolha de dados. |
| Orientação de solução  | A operação do tipo *ConfigurationScope* foi processada. |
| Solução de avaliação | Foi executado um processo de avaliação. |


### <a name="ingestion"></a>Ingestão
As operações de ingestão são questões que ocorreram durante a ingestão de dados, incluindo a notificação sobre a chegada aos limites do espaço de trabalho Azure Log Analytics. As condições de erro nesta categoria podem sugerir a perda de dados, pelo que são particularmente importantes para monitorizar. A tabela abaixo fornece detalhes sobre estas operações. Consulte [os limites de serviço do Azure Monitor](../service-limits.md#log-analytics-workspaces) para os limites de serviço para os espaços de trabalho do Log Analytics.


| Operação | Level | Detalhes | Artigo relacionado |
|:---|:---|:---|:---|
| Tronco personalizado | Erro   | Limite de coluna de campos personalizados alcançado. | [Limites de serviço do Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Tronco personalizado | Erro   | A ingestão de registos personalizados falhou. | |
| Metadados. | Erro | Erro de configuração detetado. | |
| Recolha de dados | Erro   | Os dados foram retirados porque o pedido foi criado mais cedo do que o número de dias definidos. | [Gerir a utilização e os custos com Registos do Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached)
| Recolha de dados | Informações    | A configuração da máquina de recolha é detetada.| |
| Recolha de dados | Informações    | A recolha de dados começou devido ao novo dia. | [Gerir a utilização e os custos com Registos do Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Recolha de dados | Aviso | A recolha de dados parou devido ao limite diário atingido.| [Gerir a utilização e os custos com Registos do Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Processamento de dados | Erro   | Formato JSON inválido. | [Envie dados de registo para O Monitor de Azure com a API do Colecionador de Dados HTTP (pré-visualização pública)](../logs/data-collector-api.md#request-body) | 
| Processamento de dados | Aviso | O valor foi aparado até ao tamanho máximo permitido. | [Limites de serviço do Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Processamento de dados | Aviso | Valor de campo aparado à medida que o limite de tamanho atingido. | [Limites de serviço do Azure Monitor](../service-limits.md#log-analytics-workspaces) | 
| Taxa de ingestão | Informações | Limite de taxa de ingestão aproximando-se dos 70%. | [Limites de serviço do Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Taxa de ingestão | Aviso | Limite de taxa de ingestão aproximando-se do limite. | [Limites de serviço do Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Taxa de ingestão | Erro   | Limite de taxa atingido. | [Limites de serviço do Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Armazenamento | Erro   | Não é possível aceder à conta de armazenamento, uma vez que as credenciais utilizadas são inválidas.  |



   

## <a name="alert-rules"></a>Regras de alertas
Utilize [alertas de consulta de registo](../alerts/alerts-log-query.md) no Azure Monitor para ser notificado proactivamente quando um problema for detetado no seu espaço de trabalho Log Analytics. Deve usar uma estratégia que lhe permita responder em tempo útil às questões, minimizando os seus custos. A sua subscrição é cobrada por cada regra de alerta com um custo dependendo da frequência que é avaliada.

Uma estratégia recomendada é começar com duas regras de alerta baseadas no nível da questão. Utilize uma frequência curta, como a cada 5 minutos para erros e uma frequência mais longa, como 24 horas para advertências. Uma vez que os erros indicam uma possível perda de dados, pretende responder-lhes rapidamente para minimizar qualquer perda. Os avisos normalmente indicam um problema que não requer atenção imediata, para que possa revê-los diariamente.

Utilize o processo em [Criar, ver e gerir alertas de registo utilizando o Azure Monitor](../alerts/alerts-log.md) para criar as regras de alerta de registo. As seguintes secções descrevem os detalhes de cada regra.


| Consulta | Valor do limiar | Período | Frequência |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Estas regras de alerta responderão da mesma forma a todas as operações com Erro ou Aviso. À medida que se familiariza com as operações que estão a gerar alertas, talvez queira responder de forma diferente para determinadas operações. Por exemplo, pode querer enviar notificações a diferentes pessoas para operações específicas. 

Para criar uma regra de alerta para uma operação específica, utilize uma consulta que inclua as colunas **de categoria** e **operação.** 

O exemplo a seguir cria um alerta de alerta quando a taxa de volume de ingestão atingiu 80% do limite.

- Destino: Selecione o seu espaço de trabalho Log Analytics
- Critérios:
  - Nome do sinal: Pesquisa de registo personalizado
  - Consulta de pesquisa: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Com base em: Número de resultados
  - Condição: Maior do que
  - Limiar: 0
  - Período: 5 (minutos)
  - Frequência: 5 (minutos)
- Nome da regra de alerta: Limite de dados diário atingido
- Gravidade: Aviso (Sev 1)


O exemplo a seguir cria um alerta quando a recolha de dados atingiu o limite diário. 

- Destino: Selecione o seu espaço de trabalho Log Analytics
- Critérios:
  - Nome do sinal: Pesquisa de registo personalizado
  - Consulta de pesquisa: `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - Com base em: Número de resultados
  - Condição: Maior do que
  - Limiar: 0
  - Período: 5 (minutos)
  - Frequência: 5 (minutos)
- Nome da regra de alerta: Limite de dados diário atingido
- Gravidade: Aviso (Sev 1)



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os alertas de registo.](../alerts/alerts-log.md)
- [Recolher dados de auditoria](./query-audit.md) de consulta para o seu espaço de trabalho.