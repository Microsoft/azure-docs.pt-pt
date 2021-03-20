---
title: Serviço de Monitorização do Blockchain Azure (ABS)
description: Serviço de Monitorização do Azure Blockchain através do Azure Monitor
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 05147f48c4cde4cc97bf6cc9cae5c8220a389ebd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594915"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitor Azure Blockchain Service através do Azure Monitor  

À medida que os clientes executam cenários de blockchain de nível de produção no Azure Blockchain Service (ABS), torna-se fundamental monitorizar os recursos para disponibilidade, desempenho e operações. Este artigo descreve os dados de monitorização gerados pelo Azure Blockchain Service e como se pode usar as várias funcionalidades e integrações do Azure Monitor para analisar e alertar, para gerir ambientes de nível de produção.  

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O Azure Blockchain Service cria dados de monitorização utilizando o Azure Monitor, que é um serviço completo de monitorização de pilhas em Azure que fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure. Para obter mais informações sobre o Azure Monitor, consulte [os recursos de Monitor Azure com o Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
 

As secções seguintes baseiam-se neste artigo, descrevendo os dados específicos recolhidos do Serviço Azure Blockchain e fornecendo exemplos para configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitor de dados recolhidos do Serviço Azure Blockchain  

O Azure Blockchain Service recolhe o mesmo tipo de dados de monitorização que outros recursos Azure, que são descritos na monitorização de [dados a](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) partir de recursos Azure. Consulte [a referência de dados do Serviço Blockchain Monitor Azure](#monitor-azure-blockchain-service-data-reference) para uma referência detalhada dos registos e métricas criados pelo Azure Blockchain Service.

A página geral no portal Azure para cada recurso membro do Azure Blockchain Service inclui uma breve visão das transações, incluindo os pedidos tratados e blocos processados. Alguns destes dados são recolhidos automaticamente e disponíveis para análise assim que criar o recurso membro do Azure Blockchain Service, enquanto pode ativar a recolha adicional de dados com configuração adicional.

## <a name="diagnostic-settings"></a>Definições de diagnóstico  

As métricas da plataforma e o registo de Atividade são recolhidos automaticamente, mas tem de criar uma definição de diagnóstico para recolher registos de recursos ou reencaizá-los para fora do Azure Monitor. Consulte [Criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure](../../azure-monitor/essentials/diagnostic-settings.md) para o processo detalhado para criar uma definição de diagnóstico utilizando o portal Azure, CLI ou PowerShell.

Quando cria uma definição de diagnóstico, especifica quais as categorias de registos a recolher. As categorias para o Serviço Azure Blockchain estão listadas abaixo.

**Registos de procuração blockchain** – Selecione a categoria se pretender monitorizar os registos de procuração NGNIX. Todos os detalhes da transação do cliente estão disponíveis para fins de auditoria e depuração.  

**Registos de aplicações blockchain** – Selecione a categoria para obter registos da aplicação blockchain hospedada pelo serviço gerido. Por exemplo, para um membro ABS-Quorum, estes registos seriam os registos do próprio Quorum.  

**Pedidos métricos**: Selecione a opção de recolher dados métricos da Azure Cosmos DB para os destinos na definição de diagnóstico, que é recolhida automaticamente em Azure Metrics. Recolher dados métricos com registos de recursos para analisar ambos os tipos de dados em conjunto e enviar dados métricos fora do Azure Monitor.

## <a name="analyze-metric-data"></a>Analisar dados métricos  

Pode analisar métricas para o Serviço Azure Blockchain com o explorador Métricas, navegar para o separador Métricas na secção de Monitorização na lâmina de recursos ABS. Consulte [o Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md) para obter detalhes sobre a utilização da ferramenta. As métricas completas para o Serviço Azure Blockchain estão no namespace Azure Blockchain Service métricas.

Pode utilizar a dimensão **do nó** ao adicionar um filtro ou dividir as métricas, que basicamente fornece valores métricos por nó de transação e nódos de validador do membro ABS.

## <a name="analyze-log-data"></a>Analisar dados de registo

Aqui ficam algumas consultas que pode introduzir na barra de pesquisa log para ajudá-lo a monitorizar os seus membros do Serviço Azure Blockchain. Estas consultas funcionam com a [nova linguagem.](../../azure-monitor/logs/log-query-overview.md)

Para consultar as condições de erro nos registos de aplicações blockchain, utilize a consulta abaixo:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Para consultar as condições de erro nos registos de procuração blockchain, utilize a consulta abaixo  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Pode utilizar os filtros de tempo disponíveis nos registos Azure para filtrar a consulta para uma gama de tempo específica.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Referência de dados do Serviço Azure Blockchain  

Este artigo fornece uma referência de dados de log e métrica recolhidos para analisar o desempenho e disponibilidade do Serviço Azure Blockchain.  

### <a name="resource-logs"></a>Registos do recurso

Todos os registos de recursos partilham um esquema comum de alto nível com poucas propriedades únicas específicas do serviço blockchain. Pode consultar o [esquema de registos de recursos de nível superior,](../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)detalhes das propriedades específicas do Serviço Azure Blockchain são cobertos abaixo  

A tabela que se segue lista as propriedades dos registos de procuração Azure Blockchain quando são recolhidas em Registos monitores Azure ou Azure Storage.  


| Nome da propriedade  | Description |
|:---|:---|
| hora | A data e a hora (UTC) quando ocorreu a operação. |
| recursoID  | O recurso Azure Blockchain Service para o qual os registos estão ativados.  |
| categoria  |Para o Serviço Azure Blockchain, os valores possíveis são **Proxylogs** e **Applicationlogs**. |
| operationName  | O nome da operação representada por este evento.   |
| Nível de registo  | Por predefinição, o Serviço Azure Blockchain permite o nível de registo **informacional.**   |
| NodeLocação  | Região de Azure onde o membro blockchain é implantado.  |
| Nome de BlockchainNode  | O nome do nó do membro do Serviço Azure Blockchain em que a operação é realizada.   |
| EthMethod  | O método, que é chamado pelo protocolo blockchain subjacente, em Quórum, poderia ser eth_sendTransactions, eth_getBlockByNumber etc.  |
| Agente  | O agente utilizador que está a agir em nome de um utilizador, como o web browser Mozilla, Edge etc. Exemplos dos valores são: "Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77"  |
| Código   | Códigos de erro HTTP. Normalmente 4XX e 5XX são condições de erro.  |
| NodeHost  | O nome DNS do nó.   |
| PedidoMetadName | Método HTTP chamado, os valores possíveis aqui são PUT for create member, GET for get details of existing member, DELETE for delete member, PATCH for updateing member.   |
| BlockchainMemberName  | Nome do membro do Serviço Azure Blockchain fornecido pelo utilizador.  |
| Consórcio | Nome do consórcio conforme fornecido pelo utilizador.   |
| Remoto  | O IP do cliente para onde o pedido está a chegar.  |
| Solicitação  | Tamanho do pedido feito em bytes.  |
| Hora do Pedido  | A duração do pedido em milissegundos.|




A tabela que se segue lista as propriedades dos registos de aplicações do Azure Blockchain.


| Nome da propriedade  | Description |
|:---|:---|
| hora | A data e a hora (UTC) quando ocorreu a operação. |
| recursoID  | O recurso Azure Blockchain Service para o qual os registos estão ativados.|
| categoria  |Para o Serviço Azure Blockchain, o valor possível são **Proxylogs** e **Applicationlogs**.  |
| operationName  | O nome da operação representada por este evento.   |
| Nível de registo  | Por predefinição, o Serviço Azure Blockchain permite o nível de registo **informacional.**   |
| NodeLocação  | Região de Azure onde o membro blockchain é implantado.  |
| Nome de BlockchainNode  | O nome do nó do membro do Serviço Azure Blockchain em que a operação é realizada.   |
| BlockchainMessage    | Este campo conterá o registo de aplicação blockchain que são os registos simples de dados. Para o ABS-Quorum, isto teria registos de Quórum. Tem informações sobre que tipo de entrada de registo é que é informativa, erro, aviso e uma cadeia que dá mais informações sobre a ação executada.   |
| TenantID    | O inquilino específico da região do Serviço Azure Blockchain. O formato deste campo https://westlake-rp-prod <region> é. . cloudapp.azure.com onde a região especifica a região de Azure do membro destacado.       |
| SourceSystem   | O sistema povoa os troncos, neste caso é **Azure.**    |



### <a name="metrics"></a>Métricas

As tabelas que se seguem listam as métricas da plataforma recolhidas para o Serviço Azure Blockchain. Todas as métricas são armazenadas no espaço de nome As métricas padrão **do Serviço Azure Blockchain.**

Para obter uma lista de todas as métricas suportadas pelo Azure Monitor (incluindo o Serviço Azure Blockchain), consulte [as métricas suportadas pelo Azure Monitor](../../azure-monitor/essentials/metrics-supported.md).

### <a name="blockchain-metrics"></a>Métricas blockchain

A tabela seguinte especifica a lista de métricas blockchain que são recolhidas para o recurso membro do Serviço Azure Blockchain.


| Nome da métrica | Unidade  |  Tipo de agregação| Description   |
|---|---|---|---|
| Transações Pendentes   | de palavras  |  Média | O número de transações que estão à espera de ser minadas.   |
| Blocos processados   | de palavras  | Soma  |  O número de blocos processados em cada intervalo de tempo. Atualmente o tamanho do bloco é de 5 segundos, portanto, em um minuto cada nó irá processar 12 blocos e 60 blocos em 5 minutos.   |
|Transações Processadas    | de palavras  | Soma  | O número de transações processadas num bloco.    |
|Transações em fila    |  de palavras | Média  | O número de transações que não podem ser imediatamente extraídas. Pode ser porque chegaram fora de ordem e o futuro está à espera que a transação prévia chegue. Ou, pode ser que duas transações tenham o mesmo número usado apenas uma vez (nonce) e o mesmo valor de gás, portanto a segunda não pode ser extraída.   |

### <a name="connection-metrics"></a>Métricas de ligação  

A tabela que se segue lista as diferentes métricas de ligação que são recolhidas para o recurso membro do Serviço Azure Blockchain. Estas são métricas de procuração NGINX.


| Nome da métrica | Unidade  |  Tipo de agregação| Description |
|---|---|---|---|
| Conexões Aceites   | de palavras  |  Soma | O número total de ligações de clientes aceites.   |
| Ligações Ativas  | de palavras  | Média  |  O número atual de ligações ativas ao cliente, incluindo ligações de espera.    |
|Conexões manuseidas    | de palavras  | Soma  | O número total de ligações manuseadas. Geralmente, o valor do parâmetro é o mesmo que as ligações aceites, a menos que alguns limites de recursos tenham sido atingidos.     |
|Pedidos Tratados     |  de palavras | Soma  | O número total de pedidos de clientes.  |


### <a name="performance-metrics"></a>Métricas de Desempenho

A tabela seguinte lista as métricas de desempenho que são recolhidas para cada um dos nós do recurso membro do Azure Blockchain.  


| Nome da métrica | Unidade  |  Tipo de agregação| Description   |
|---|---|---|---|
| Percentagem de utilização do CPU   | Percentagem  |  Máx | A percentagem do uso do CPU.     |
| IO Ler Bytes   | Kilobytes   | Soma  |  A soma de IO lê bytes em todos os nós do recurso membro blockchain.      |
|IO Escrever Bytes     | Kilobytes   | Soma  | A soma de IO escreve bytes em todos os nós do recurso membro blockchain.     |
|Limite de memória       |  Gigabytes   | Média    | Memória máxima disponível para o processo blockchain por nó. |
|Utilização de Memória     | Gigabytes  |  Média | A quantidade de memória usada média em todos os nós.  |
| Percentagem de Utilização de Memória     | Percentagem   | Média  |  A percentagem da memória usada média em todos os nós.       |
|Utilização de armazenamento      | Gigabytes   | Média  | O GB de armazenamento utilizado em média em todos os nós.       |


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [o Blockchain Data Manager](./data-manager.md) para capturar e transformar dados blockchain para Azure Event Grid.
