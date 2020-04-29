---
title: Monitorização do Serviço Blockchain Azure (ABS)
description: Monitorização do Serviço Blockchain Azure através do Monitor Azure
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76293254"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitor Azure Blockchain Service através do Monitor Azure  

À medida que os clientes executam cenários de blockchain de nível de produção no Serviço Blockchain Azure (ABS), torna-se fundamental monitorizar os recursos para disponibilidade, desempenho e operações. Este artigo descreve os dados de monitorização gerados pelo Azure Blockchain Service e como se pode usar as várias funcionalidades e integrações do Azure Monitor para analisar e alertar, para gerir ambientes de nível de produção.  

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O Azure Blockchain Service cria dados de monitorização utilizando o Azure Monitor, que é um serviço de monitorização de pilhas completa sintetizado em Azure que fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure. Para obter mais informações sobre o Monitor Azure, consulte [a Monitorização dos recursos do Azure com o Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
 

As seguintes secções baseiam-se neste artigo descrevendo os dados específicos recolhidos do Serviço Blockchain Azure e fornecendo exemplos para configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitorize os dados recolhidos do Serviço Blockchain Azure  

O Azure Blockchain Service recolhe o mesmo tipo de dados de monitorização que outros recursos do Azure, que são descritos na [Monitorização](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) de dados a partir de recursos Do Azure. Consulte a referência de dados do [Monitor Azure Blockchain Service](#monitor-azure-blockchain-service-data-reference) para obter uma referência detalhada dos registos e métricas criados pelo Azure Blockchain Service.

A página geral do portal Azure para cada recurso do Serviço Azure Blockchain inclui uma breve visão das transações, incluindo os pedidos tratados e blocos processados. Alguns destes dados são recolhidos automaticamente e disponíveis para análise uma vez que você cria o recurso membro do Serviço Azure Blockchain, enquanto você pode ativar a recolha adicional de dados com configuração adicional.

## <a name="diagnostic-settings"></a>Definições de diagnóstico  

As métricas da plataforma e o registo de atividade são recolhidos automaticamente, mas é necessário criar uma definição de diagnóstico para recolher registos de recursos ou encaminhar para fora do Monitor Azure. Consulte a [definição de diagnóstico Create para recolher registos e métricas de plataforma em Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) para o processo detalhado para criar uma definição de diagnóstico utilizando o portal Azure, CLI ou PowerShell.

Quando cria uma definição de diagnóstico, especifica quais as categorias de registos a recolher. As categorias para o Serviço Blockchain Azure estão listadas abaixo.

**Registos** de procuração blockchain – Selecione a categoria se pretender monitorizar os registos de procuração NGNIX. Todos os detalhes da transação do cliente estão disponíveis para fins de auditoria e dedepuração.  

**Registos** de aplicações Blockchain – Selecione a categoria para obter registos da aplicação blockchain hospedada pelo serviço gerido. Por exemplo, para um membro abs-quórum, estes registos seriam os registos do próprio Quorum.  

**Pedidos métricos**: Selecione a opção de recolher dados métricos do Azure Cosmos DB para os destinos na definição de diagnóstico, que é recolhida automaticamente nas Métricas Azure. Recolher dados métricos com registos de recursos para analisar ambos os tipos de dados em conjunto e enviar dados métricos para fora do Monitor Azure.

## <a name="analyze-metric-data"></a>Analisar dados métricos  

Pode analisar métricas para o Serviço Blockchain Azure com o explorador de Métricas, navegar para o separador Metrics sob a secção de monitorização na lâmina de recursos ABS. Consulte Começar com o [Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) para obter detalhes sobre a utilização da ferramenta. As métricas completas para o Serviço Blockchain Azure estão nas métricas padrão do Serviço Azure Blockchain.

Pode utilizar a dimensão do **nó** ao adicionar um filtro ou dividir as métricas, que basicamente fornece valores métricos por nós de transação e nós validadores do membro ABS.

## <a name="analyze-log-data"></a>Analisar dados de registo

Aqui estão algumas consultas que pode introduzir na barra de pesquisa de Log para ajudá-lo a monitorizar os seus membros do Serviço Azure Blockchain. Estas consultas funcionam com a [nova linguagem.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)

Para consultar as condições de erro nos registos da aplicação Blockchain, utilize a consulta abaixo:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Para consultar as condições de erro nos registos de procuração blockchain, use a consulta abaixo  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Pode utilizar os filtros de tempo disponíveis nos registos Do Azure para filtrar a consulta para um intervalo de tempo específico.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Referência de dados do Monitor Azure Blockchain Service  

Este artigo fornece uma referência de dados de registo e métricas recolhidos para analisar o desempenho e disponibilidade do Serviço Azure Blockchain.  

### <a name="resource-logs"></a>Registos do recurso

Todos os registos de recursos partilham um esquema comum de alto nível com poucas propriedades únicas específicas do serviço blockchain. Pode consultar o artigo Logs de recursos de [nível superior,](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)detalhes das propriedades específicas do Serviço Blockchain Azure são cobertos abaixo  

A tabela seguinte lista as propriedades dos registos de proxy Azure Blockchain quando são recolhidas em Registos de Monitor estoque sinuoso ou armazenamento azure.  


| Nome da propriedade  | Descrição |
|:---|:---|
| hora | A data e a hora (UTC) quando ocorreu a operação ocorreram. |
| recursosID  | O recurso do Serviço Azure Blockchain para o qual os registos estão ativados.  |
| categoria  |Para o Serviço Blockchain Azure, os valores possíveis são **Proxylogs** e **Applicationlogs**. |
| operationName  | O nome da operação representada por este evento.   |
| Nível de log  | Por predefinição, o Serviço Azure Blockchain permite o nível de registo **informativo.**   |
| Localização do Nó  | Região azul onde o membro blockchain é implantado.  |
| Nome blockchainNode  | O nome do nó do membro do Serviço Azure Blockchain em que a operação é executada.   |
| Método EthMethod  | O método, que é chamado pelo protocolo de blockchain subjacente, no Quórum, pode ser eth_sendTransactions, eth_getBlockByNumber etc.  |
| Agente  | O agente utilizador que está a agir em nome de um utilizador, como o navegador Mozilla, Edge, etc. Exemplos dos valores são: "Mozilla/5.0 (Linux x64) nó.js/8.16.0 v8/6.2.414.77"  |
| Código   | Códigos de erro HTTP. Normalmente 4XX e 5XX são condições de erro.  |
| Anfitrião do Nó  | O nome DNS do nó.   |
| Nome método de pedido | Método HTTP chamado, os valores possíveis aqui são PUT para criar membro, GET para obter detalhes do membro existente, DELETE for delete member, PATCH for update member.   |
| Nome blockchainMember  | Nome do membro do Serviço Azure Blockchain fornecido pelo utilizador.  |
| Consórcio | Nome do consórcio conforme fornecido pelo utilizador.   |
| Remote  | O IP do cliente onde o pedido está vindo.  |
| Tamanho de pedido  | Tamanho do pedido feito em bytes.  |
| Hora do Pedido  | A duração do pedido em milissegundos.|




A tabela seguinte lista as propriedades dos registos de aplicações Azure Blockchain.


| Nome da propriedade  | Descrição |
|:---|:---|
| hora | A data e a hora (UTC) quando ocorreu a operação ocorreram. |
| recursosID  | O recurso do Serviço Azure Blockchain para o qual os registos estão ativados.|
| categoria  |Para o Serviço Blockchain Azure, o valor possível são **Proxylogs** e **Applicationlogs**.  |
| operationName  | O nome da operação representada por este evento.   |
| Nível de log  | Por predefinição, o Serviço Azure Blockchain permite o nível de registo **informativo.**   |
| Localização do Nó  | Região azul onde o membro blockchain é implantado.  |
| Nome blockchainNode  | O nome do nó do membro do Serviço Azure Blockchain em que a operação é executada.   |
| Mensagem blockchain    | Este campo conterá o registo de aplicação Blockchain que são os registos simples de dados. Para o ABS-Quorum, isto teria registos de Quórum. Tem informações sobre que tipo de entrada de registo é que é informacional, erro, aviso e uma cadeia que dá mais informações sobre a ação executada.   |
| TenantID    | O inquilino específico da região do Serviço Azure Blockchain. O formato deste https://westlake-rp-prodcampo é . <region>.cloudapp.azure.com onde a região especifica a região azure do membro destacado.       |
| SourceSystem   | O sistema povoa os troncos, neste caso é **Azure**.    |



### <a name="metrics"></a>Métricas

As tabelas seguintes listam as métricas da plataforma recolhidas para o Serviço Blockchain Azure. Todas as métricas são armazenadas nas métricas padrão do Serviço **Azure Blockchain.**

Para obter uma lista de todas as métricas suportadas pelo Azure Monitor (incluindo o Serviço Azure Blockchain), consulte [as métricas suportadas pelo Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)

### <a name="blockchain-metrics"></a>Métricas blockchain

A tabela seguinte especifica a lista das métricas blockchain que são recolhidas para o recurso membro do Serviço Blockchain Azure.


| Nome da métrica | Unidade  |  Tipo de agregação| Descrição   |
|---|---|---|---|
| Transações Pendentes   | Contagem  |  Média | O número de transações que estão à espera de ser minadas.   |
| Blocos processados   | Contagem  | Soma  |  O número de blocos processados em cada intervalo de tempo. Atualmente o tamanho do bloco é de 5 segundos, portanto, num minuto cada nó processará 12 blocos e 60 blocos em 5 minutos.   |
|Transações Processadas    | Contagem  | Soma  | O número de transações processadas num bloco.    |
|Transações em fila    |  Contagem | Média  | O número de transações que não podem ser imediatamente minadas. Pode ser porque chegaram fora de ordem e o futuro está à espera que a transação anterior chegue. Ou, podem ser duas transações que só têm o mesmo número utilizado uma vez (nonce) e o mesmo valor de gás, pelo que a segunda não pode ser minada.   |

### <a name="connection-metrics"></a>Métricas de ligação  

A tabela seguinte lista as diferentes métricas de ligação que são recolhidas para o recurso membro do Serviço Blockchain Azure. Estas são métricas de procuração NGINX.


| Nome da métrica | Unidade  |  Tipo de agregação| Descrição |
|---|---|---|---|
| Conexões Aceites   | Contagem  |  Soma | O número total de ligações de clientes aceites.   |
| Conexões Ativas  | Contagem  | Média  |  O número atual de ligações ativas do cliente, incluindo ligações de espera.    |
|Conexões manuseadas    | Contagem  | Soma  | O número total de ligações manipuladas. Geralmente, o valor do parâmetro é o mesmo que as ligações aceites, a menos que tenham sido atingidos alguns limites de recursos.     |
|Pedidos tratados     |  Contagem | Soma  | O número total de pedidos de clientes.  |


### <a name="performance-metrics"></a>Métricas de Desempenho

A tabela seguinte lista as métricas de desempenho recolhidas para cada um dos nós do recurso membro Azure Blockchain.  


| Nome da métrica | Unidade  |  Tipo de agregação| Descrição   |
|---|---|---|---|
| Percentagem de utilização do CPU   | Percentagem  |  Máx. | A percentagem do uso da CPU.     |
| IO Ler Bytes   | Kilobytes   | Soma  |  A soma de IO lida bytes em todos os nós do recurso membro blockchain.      |
|IO Write Bytes     | Kilobytes   | Soma  | A soma da IO escreve bytes em todos os nós do recurso membro blockchain.     |
|Limite de memória       |  Gigabytes   | Média    | Memória máxima disponível para o processo blockchain por nó. |
|Utilização de Memória     | Gigabytes  |  Média | A quantidade de memória usada média em todos os nós.  |
| Percentagem de utilização da memória     | Percentagem   | Média  |  A percentagem da memória usada média em todos os nós.       |
|Utilização de armazenamento      | Gigabytes   | Média  | O GB de armazenamento usado media em média em todos os nós.       |


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [o Blockchain Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) para capturar e transformar dados blockchain na Rede de Eventos Azure.
