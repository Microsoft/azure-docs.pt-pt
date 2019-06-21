---
title: Registos de diagnóstico do Azure Stream à área de trabalho do Log Analytics no Azure Monitor
description: Aprenda a transmitir em fluxo registos de diagnóstico do Azure para uma área de trabalho do Log Analytics no Azure Monitor.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 13eb1a8fcea2f74cda5921a51b8c2e8816be975f
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303689"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Registos de diagnóstico do Azure Stream à área de trabalho do Log Analytics no Azure Monitor

**[Registos de diagnóstico do Azure](diagnostic-logs-overview.md)**  podem ser transmitidos em tempo real para uma área de trabalho do Log Analytics no Azure Monitor com o portal, cmdlets do PowerShell ou da CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>O que pode fazer com o diagnóstico de registos numa área de trabalho do Log Analytics

O Azure Monitor proporciona uma ferramenta de consulta e análise de registo flexível que permite que pode obter informações sobre os dados de registos não processados gerados a partir de recursos do Azure. Alguns recursos incluem:

* **Consulta de registo** -escrita consultas avançadas sobre os dados de registo, correlacionar os registos de várias origens e a gerarem gráficos que podem ser afixadas ao dashboard do Azure.
* **Alertas** -detetar quando um ou mais eventos correspondem a uma consulta específica e ser notificados com uma chamada de e-mail ou webhook com alertas do Azure Monitor.
* **O Advanced analytics** - aplicam-se de aprendizagem automática e padrão de algoritmos correspondentes para identificar possíveis problemas revelados pelos seus registos.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Ativar a transmissão em fluxo de registos de diagnóstico para a área de trabalho do Log Analytics

Pode ativar a transmissão em fluxo de registos de diagnóstico através de programação, através do portal, ou utilizando o [APIs de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). De qualquer forma, criar uma definição de diagnóstico em que especificar uma área de trabalho do Log Analytics e as categorias de registo e métricas que deseja enviar para área de trabalho. Um diagnóstico **categoria de registo** é um tipo de registo que pode fornecer um recurso.

A área de trabalho do Log Analytics não tem de estar na mesma subscrição que o recurso emite os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para ambas as subscrições.

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica "Mensagens recebidas" num Hub de eventos pode ser explorada e representada um por nível de fila. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Registos de diagnóstico do Stream com o portal
1. No portal, navegue para o Azure Monitor e clique em **das definições de diagnóstico** no **definições** menu.


2. Opcionalmente, filtrar a lista por grupo de recursos ou tipo de recurso, em seguida, clique no recurso para o qual pretende configurar uma definição de diagnóstico.

3. Se não existem definições existem no recurso que selecionou, lhe for pedido para criar uma definição. Clique em "Ativar diagnósticos."

   ![Adicionar definição de diagnóstico - sem definições existentes](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Se existirem definições existentes no recurso, verá uma lista das definições já configurada neste recurso. Clique em "Adicionar definição de diagnóstico".

   ![Adicionar definição de diagnóstico - existente definições](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Dê a sua definição de um nome e marque a caixa **enviar para o Log Analytics**, em seguida, selecione uma área de trabalho do Log Analytics.

   ![Adicionar definição de diagnóstico - existente definições](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Clique em **Guardar**.

Após alguns instantes, a nova definição é apresentada na sua lista de definições para este recurso, e os registos de diagnóstico são transmitidas em fluxo para essa área de trabalho assim que novos dados de evento são gerados. Pode haver até 15 minutos entre quando um evento é emitido e quando for apresentada no Log Analytics.

### <a name="via-powershell-cmdlets"></a>Cmdlets do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para ativar a transmissão em fluxo através do [Cmdlets do Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), pode utilizar o `Set-AzDiagnosticSetting` cmdlet com estes parâmetros:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Tenha em atenção que a propriedade workspaceID pega a ID de recurso Azure completo da área de trabalho, não a área de trabalho/chave de ID apresentado no portal do Log Analytics.

### <a name="via-azure-cli"></a>Através da CLI do Azure

Para ativar a transmissão em fluxo através do [CLI do Azure](../../azure-monitor/platform/cli-samples.md), pode utilizar o [az monitor diagnostic-settings criar](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) comando.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Pode adicionar categorias adicionais no registo de diagnóstico através da adição de dicionários para a matriz JSON passada como o `--logs` parâmetro.

O `--resource-group` argumento só é necessária se `--workspace` não é um ID de objeto.

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Como faço para consultar os dados a partir de uma área de trabalho do Log Analytics?

No painel de registos no portal do Azure Monitor, pode consultar os registos de diagnóstico como parte da solução de gestão de registos na tabela AzureDiagnostics. Também existem [várias soluções de monitorização para recursos do Azure](../../azure-monitor/insights/solutions.md) pode instalar para obter informações imediatas sobre os dados de registo estão a enviar para o Azure Monitor.

## <a name="azure-diagnostics-vs-resource-specific"></a>Vs de diagnóstico do Azure recursos específicos  
Depois de um destino do Log Analytics é habilitado numa configuração de diagnósticos do Azure, existem duas maneiras distintas que dados irão aparecer na sua área de trabalho:  
- **Diagnóstico do Azure** -este é o método herdado usado atualmente pela maioria dos serviços do Azure. Neste modo, todos os dados a partir de qualquer definição de diagnóstico apontava para uma determinada área de trabalho terminarão no _AzureDiagnostics_ tabela. 
<br><br>Uma vez que muitos recursos enviam dados para a mesma tabela (_AzureDiagnostics_), o esquema desta tabela é o conjunto superutilizadores dos esquemas de todos os tipos de dados diferentes a ser recolhidos. Por exemplo, se tiver criado as definições de diagnóstico para a coleção dos seguintes tipos de dados, tudo a ser enviados para a mesma área de trabalho:
    - Registos de 1 de recursos (ter um esquema consiste em colunas A, B e C) de auditoria  
    - Registos de erros de 2 de recursos (ter um esquema consiste em colunas D, E e F)  
    - Registos de fluxo de dados de 3 de recursos (ter um esquema formada por colunas G, H e eu)  

    A tabela de AzureDiagnostics será semelhante ao seguinte, com alguns dados de exemplo:  

    | ResourceProvider | Category | A | B | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
    | Microsoft.Resource2 | ErrorLogs | | | | q1 | W1 | e1 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | ErrorLogs | | | | q2 | W2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- **Recursos específicos** -neste modo, as tabelas individuais na área de trabalho selecionada são criadas por cada categoria selecionada na configuração das definições de diagnóstico. Este método mais recente torna muito mais fácil encontrar exatamente o que pretende localizar por meio de explícita separação de preocupações: uma tabela para cada categoria. Além disso, ele fornece benefícios em seu suporte para tipos dinâmicos. Já pode ver este modo para selecionar tipos de recursos do Azure, por exemplo [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ou [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) registos. Em última análise, podemos esperar que todos os tipos de dados para migrar para o modo de recursos específicos. 

    No exemplo acima, isso poderia resultar em três tabelas que está sendo criadas: 
    - Tabela _AuditLogs_ da seguinte forma:

        | ResourceProvider | Category | A | B | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | ... |

    - Tabela _registos de erros_ da seguinte forma:  

        | ResourceProvider | Category | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | ErrorLogs | q1 | W1 | e1 |
        | Microsoft.Resource2 | ErrorLogs | q2 | W2 | e2 |
        | ... |

    - Tabela _DataFlowLogs_ da seguinte forma:  

        | ResourceProvider | Category | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    Outras vantagens de utilizar o modo de recursos específicos incluem um desempenho melhorado de latência de ingestão e tempos de consulta, melhor capacidade de deteção de esquemas e sua estrutura, a capacidade de conceder direitos de RBAC numa tabela específica e muito mais.

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Selecionar modo de diagnóstico do Azure vs recursos específicos
Mais recursos do Azure, não terá uma escolha se pretende utilizar o modo de diagnóstico do Azure ou recursos específicos; os dados irão fluir automaticamente através do método que o recurso selecionada para utilizar. Consulte a documentação fornecida pelo recurso que ativou para enviar dados para o Log Analytics para obter mais detalhes em que modo é que está a ser empregado. 

Conforme indicado na secção anterior, em última análise, é o objetivo do Azure Monitor para que todos os serviços no Azure, utilize o modo de recursos específicos. Para facilitar essa transição e certifique-se de que nenhum dado seja perdido como parte do mesmo, alguns serviços do Azure quando a adesão ao Log Analytics irá fornecer-lhe com uma seleção de modo:  
   ![Diagnóstico Seletor de modo de definições](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

Estamos **vivamente** Recomendamos que, para evitar potencialmente difícil migrações mais adiante, qualquer recentemente criado utilize definições de diagnóstico de modo centralizado em recursos.  

Para definições de diagnóstico existentes, uma vez ativada por um determinado recurso do Azure, será capaz de alternar retroativamente do diagnóstico do Azure para o modo de recursos específicos. Os dados ingeridos anteriormente continuarão disponíveis na _AzureDiagnostics_ até que a idade dos fora, conforme configurado na sua configuração de retenção no espaço de trabalho, mas quaisquer novos dados serão enviados para a tabela dedicada de tabela. Isso significa que, de qualquer consulta que tem de abranger os dados antigos e novos (até que os dados antigos totalmente idade dos), um [União](https://docs.microsoft.com/azure/kusto/query/unionoperator) operador nas suas consultas serão necessárias para combinar os dois conjuntos de dados.

Consulte para notícias sobre o Azure novos registos de suporte no modo de recursos específicos de serviços no [Azure atualiza as](https://azure.microsoft.com/updates/) blogue!

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Conhecido limitação: limite de coluna em AzureDiagnostics
Existe um limite explícito de qualquer tabela registo do Azure não ter mais de 500 colunas. Uma vez atingido, quaisquer linhas que contêm dados com qualquer coluna fora da primeira 500 serão ignoradas durante a ingestão. A tabela de AzureDiagnostics é em particular suscetível a ser afetado este limite. Normalmente, isto acontece porque uma grande variedade de origens de dados são enviados para a mesma área de trabalho, ou várias origens de dados verboso a ser enviadas para a mesma área de trabalho. 

#### <a name="azure-data-factory"></a>Azure Data Factory  
O Azure Data Factory, devido a um conjunto muito detalhado de registos, é um recurso que é conhecido por ser particularmente afetadas por este limite. Em particular, para quaisquer definições de diagnóstico configurada antes dos recursos específicos de modo foi explicitamente escolhendo a utilizar o modo de recursos específicos por motivos de compatibilidade reversa ou ativada:  
- *Parâmetros de utilizador definidos em relação a qualquer atividade no seu pipeline*: haverá uma nova coluna criada para cada parâmetro exclusivamente-com o nome de utilizador em relação a qualquer atividade. 
- *Atividade entradas e saídas*: estas variam de atividade para atividade e gerar um grande número de colunas devido a respetiva natureza verbosa. 
 
Como com as mais amplas propostas de solução abaixo, recomenda-se para migrar os seus registos ao utilizar o modo de recursos específicos logo que possível. Se não conseguir fazê-lo imediatamente, uma alternativa intermediária é isolar os registos do ADF para sua própria área de trabalho para minimizar a possibilidade destes registos afetar outros tipos de registos recolhidos em suas áreas de trabalho. 
 
#### <a name="workarounds"></a>Soluções alternativas
A curto prazo, até que todos os serviços do Azure estão ativados no modo de recursos específicos, para todos os serviços não, mas que suporta o modo de recursos específicos, é recomendável separar os tipos de dados verboso publicados por estes serviços em áreas de trabalho separadas para reduzir a possibilidade de atingir o limite.  
 
Longo prazo, o diagnóstico do Azure vai estar mudando na direção de todos os serviços do Azure que suporta o modo de recursos específicos. Recomendamos a mudança para este modo logo que possível para reduzir a possibilidade de que está a ser afetado por esta limitação de 500 coluna.  


## <a name="next-steps"></a>Passos Seguintes

* [Leia mais sobre os Registos de Diagnóstico do Azure](diagnostic-logs-overview.md)

