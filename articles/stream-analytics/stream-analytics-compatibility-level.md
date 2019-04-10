---
title: Compreender o nível de compatibilidade para tarefas do Azure Stream Analytics
description: Saiba como configurar um nível de compatibilidade para uma tarefa do Azure Stream Analytics e as principais alterações no nível de compatibilidade mais recentes
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361400"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nível de compatibilidade para tarefas do Azure Stream Analytics
 
Nível de compatibilidade refere-se aos comportamentos específicos da versão de um serviço do Azure Stream Analytics. O Azure Stream Analytics é um serviço gerido, com as atualizações de funcionalidade regular e melhorias de desempenho. Normalmente, as atualizações são efetuadas automaticamente disponíveis aos utilizadores finais. No entanto, alguns recursos novos podem introduzir alterações importantes, tal como alteração no comportamento de uma tarefa existente, alterar nos processos de consumo de dados a partir destas tarefas, etc. Um nível de compatibilidade é utilizado para representar as alterações importantes introduzidas no Stream Analytics. Alterações importantes sempre são introduzidas com um novo nível de compatibilidade. 

Nível de compatibilidade torna-se de que as tarefas existentes executados sem qualquer falha. Quando cria uma nova tarefa de Stream Analytics, é melhor prática para criá-la utilizando o nível de compatibilidade mais recentes. 
 
## <a name="set-a-compatibility-level"></a>Definir um nível de compatibilidade 

Nível de compatibilidade controla o comportamento de tempo de execução de uma tarefa do stream analytics. Pode definir o nível de compatibilidade de uma tarefa do Stream Analytics através do portal ou utilizando o [criar a chamada de REST API de tarefa](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). O Azure Stream Analytics suporta atualmente dois compatibilidade níveis-"1.0" e "1.1". Por predefinição, o nível de compatibilidade é definido como "1.0" que foi introduzida durante a disponibilidade geral do Azure Stream Analytics. Para atualizar o valor predefinido, navegue para a sua tarefa do Stream Analytics existente > selecione o **nível de compatibilidade** opção **configurar** secção e altere o valor. 

Certifique-se de que parar a tarefa antes de atualizar o nível de compatibilidade. Não é possível atualizar o nível de compatibilidade, se o seu trabalho está num Estado em execução. 

![Nível de compatibilidade do Stream Analytics no portal do Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
Ao atualizar o nível de compatibilidade, o compilador de T-SQL valida a tarefa com a sintaxe que corresponde ao nível de compatibilidade selecionado. 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>Grandes alterações no nível de compatibilidade mais recente (1.2)

As seguintes alterações principais foram introduzidas no nível de compatibilidade 1.2:

### <a name="geospatial-functions"></a>Funções geoespaciais 

**Versões anteriores:** O Azure Stream Analytics utilizadas cálculos geográficos.

**versão atual:** O Azure Stream Analytics permite que geométricos geo prevista coordenadas de computação. Não há nenhuma alteração na assinatura das funções geoespaciais. No entanto, sua semântica é ligeiramente diferente, permitindo que a computação mais precisa do que antes.

O Azure Stream Analytics oferece suporte a indexação de dados de referência do geoespacial. Dados de referência que contém os elementos de dados geoespaciais podem ser indexados para um cálculo de associação mais rápido.

As funções geoespaciais atualizado trazem a expressividade completa do formato de dados geoespaciais Well Known Text (well-). Pode especificar outros componentes de geoespaciais que não eram suportados anteriormente com GeoJson.

Para obter mais informações, consulte [atualiza para recursos geoespaciais no Azure Stream Analytics – Cloud e de IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Execução paralela da consulta para origens de entrada com várias partições 

**Versões anteriores:** Consultas do Stream Analytics do Azure necessários a utilização da cláusula PARTITION BY para paralelizar o processamento de consultas entre partições de origem de entrada.

**versão atual:** Se a lógica de consulta pode ser colocado em paralelo em partições de origem de entrada, o Azure Stream Analytics cria instâncias de consulta separada e executa os cálculos em paralelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integração nativa de API em massa com a saída do cosmos DB

**Versões anteriores:** O comportamento de upsert era *inserir ou intercalar*.

**versão atual:** Integração nativa de API em massa com a saída do CosmosDB maximiza o débito e processa com eficiência a limitação de pedidos.

É o comportamento de upsert *inserir ou substituir*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset ao escrever a saída SQL

**Versões anteriores:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) tipos foram ajustados para UTC.

**versão atual:** Já não é ajustado DateTimeOffset.

### <a name="strict-validation-of-prefix-of-functions"></a>Validação estrita de prefixo de funções

**Versões anteriores:** Não ocorreu nenhuma validação estrita de prefixos de função.

**versão atual:** O Azure Stream Analytics tem uma validação estrita de prefixos de função. Adicionar um prefixo para uma função incorporada causa um erro. Por exemplo,`myprefix.ABS(…)` não é suportada.

Adicionar um prefixo para os agregados internos também resulta num erro. Por exemplo, `myprefix.SUM(…)` não é suportada.

Usando o prefixo "system" para quaisquer resultados de funções definidas pelo utilizador em erro.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Não permitir matriz e objeto como propriedades chave no adaptador de saída do Cosmos DB

**Versões anteriores:** Os tipos de matriz e objeto foram suportados como uma propriedade de chave.

**versão atual:** Tipos de matriz e objeto já não são suportados como uma propriedade de chave.


## <a name="next-steps"></a>Passos Seguintes
* [Resolver problemas de entradas do Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [O estado de funcionamento do Stream Analytics recursos](stream-analytics-resource-health.md)
