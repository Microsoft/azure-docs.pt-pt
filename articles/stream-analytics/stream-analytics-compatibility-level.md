---
title: Compreender o nível de compatibilidade para tarefas do Azure Stream Analytics
description: Saiba como configurar um nível de compatibilidade para uma tarefa do Azure Stream Analytics e as principais alterações no nível de compatibilidade mais recentes
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: e4bbfdbcf7a295089570d4c8b77b07fd7270b3fd
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998265"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nível de compatibilidade para tarefas do Azure Stream Analytics

Este artigo descreve a opção de nível de compatibilidade no Azure Stream Analytics. Stream Analytics é um serviço gerido, com as atualizações de funcionalidade regular e melhorias de desempenho. A maioria das atualizações de tempos de execução do serviço está automaticamente disponíveis para os utilizadores finais. 

No entanto, algumas novas funcionalidades no serviço podem introduzir uma alteração importante, como uma alteração no comportamento de uma tarefa existente, ou uma alteração nos dados de forma é consumida em tarefas em execução. Pode manter as suas tarefas do Stream Analytics existentes em execução sem grandes alterações, deixando o nível de compatibilidade definindo reduzido. Quando estiver pronto para os comportamentos de tempo de execução mais recente, pode participar ao aumentar o nível de compatibilidade. 

## <a name="choose-a-compatibility-level"></a>Escolha um nível de compatibilidade

Nível de compatibilidade controla o comportamento de tempo de execução de uma tarefa do stream analytics. 

O Azure Stream Analytics suporta atualmente três níveis de compatibilidade:

* nível de predefinição 1.0-
* 1.1 - comportamento da versão atual
* 1.2 (pré-visualização) - comportamento mais recente com os mais recentes aprimoramentos no período de avaliação

O nível de compatibilidade de 1,0 original foi introduzido durante a disponibilidade geral do Azure Stream Analytics há vários anos atrás.

Quando cria uma nova tarefa de Stream Analytics, é melhor prática para criá-la utilizando o nível de compatibilidade mais recentes. Inicie o seu design de tarefa contando com os comportamentos mais recente, para evitar a alteração foi adicionada e a complexidade mais tarde.

## <a name="set-the-compatibility-level"></a>Definir o nível de compatibilidade

Pode definir o nível de compatibilidade de uma tarefa do Stream Analytics no portal do Azure ou utilizando o [criar a chamada de REST API de tarefa](/rest/api/streamanalytics/stream-analytics-job).

Para atualizar o nível de compatibilidade da tarefa no portal do Azure:

1. Utilize o [portal do Azure](https://portal.azure.com) localizar para a sua tarefa do Stream Analytics.
2. **Parar** a tarefa antes de atualizar o nível de compatibilidade. Não é possível atualizar o nível de compatibilidade, se o seu trabalho está num Estado em execução.
3. Sob o **configurar** cabeçalho, selecione **nível de compatibilidade**.
4. Escolha o valor de nível de compatibilidade que pretende.
5. Selecione **guardar** na parte inferior da página.

![Nível de compatibilidade do Stream Analytics no portal do Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Ao atualizar o nível de compatibilidade, o compilador de T-SQL valida a tarefa com a sintaxe que corresponde ao nível de compatibilidade selecionado.

## <a name="compatibility-level-12"></a>Nível de compatibilidade 1.2

As seguintes alterações principais foram introduzidas no nível de compatibilidade 1.2:

### <a name="geospatial-functions"></a>Funções geoespaciais

**Níveis de anteriores:** O Azure Stream Analytics utilizadas cálculos geográficos.

**nível de 1,2:** O Azure Stream Analytics permite que geométricos geo prevista coordenadas de computação. Não há nenhuma alteração na assinatura das funções geoespaciais. No entanto, sua semântica é ligeiramente diferente, permitindo que a computação mais precisa do que antes.

O Azure Stream Analytics oferece suporte a indexação de dados de referência do geoespacial. Dados de referência que contém os elementos de dados geoespaciais podem ser indexados para um cálculo de associação mais rápido.

As funções geoespaciais atualizado trazem a expressividade completa do formato de dados geoespaciais Well Known Text (well-). Pode especificar outros componentes de geoespaciais que não eram suportados anteriormente com GeoJson.

Para obter mais informações, consulte [atualiza para recursos geoespaciais no Azure Stream Analytics – Cloud e de IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Execução paralela da consulta para origens de entrada com várias partições

**Níveis de anteriores:** Consultas do Stream Analytics do Azure necessários a utilização da cláusula PARTITION BY para paralelizar o processamento de consultas entre partições de origem de entrada.

**nível de 1,2:** Se a lógica de consulta pode ser colocado em paralelo em partições de origem de entrada, o Azure Stream Analytics cria instâncias de consulta separada e executa os cálculos em paralelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integração nativa de API em massa com a saída do cosmos DB

**Níveis de anteriores:** O comportamento de upsert era *inserir ou intercalar*.

**nível de 1,2:** Integração nativa de API em massa com a saída do CosmosDB maximiza o débito e processa com eficiência a limitação de pedidos. Para obter mais informações, consulte [a saída de Azure Stream Analytics para a página do Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

É o comportamento de upsert *inserir ou substituir*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset ao escrever a saída SQL

**Níveis de anteriores:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) tipos foram ajustados para UTC.

**nível de 1,2:** Já não é ajustado DateTimeOffset.

### <a name="strict-validation-of-prefix-of-functions"></a>Validação estrita de prefixo de funções

**Níveis de anteriores:** Não ocorreu nenhuma validação estrita de prefixos de função.

**nível de 1,2:** O Azure Stream Analytics tem uma validação estrita de prefixos de função. Adicionar um prefixo para uma função incorporada causa um erro. Por exemplo,`myprefix.ABS(…)` não é suportada.

Adicionar um prefixo para os agregados internos também resulta num erro. Por exemplo, `myprefix.SUM(…)` não é suportada.

Usando o prefixo "system" para quaisquer resultados de funções definidas pelo utilizador em erro.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Não permitir matriz e objeto como propriedades chave no adaptador de saída do Cosmos DB

**Níveis de anteriores:** Os tipos de matriz e objeto foram suportados como uma propriedade de chave.

**nível de 1,2:** Tipos de matriz e objeto já não são suportados como uma propriedade de chave.

## <a name="compatibility-level-11"></a>Nível de compatibilidade 1.1

As seguintes alterações principais foram introduzidas no nível de compatibilidade 1.1:

### <a name="service-bus-xml-format"></a>Formato XML do barramento de serviço

**nível de 1.0:** O Azure Stream Analytics utilizadas DataContractSerializer, para que o conteúdo da mensagem incluído marcas XML. Por exemplo:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**nível de 1.1:** O conteúdo da mensagem contém o fluxo diretamente com nenhuma etiquetas adicionais. Por exemplo: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Sensibilidade persistente para nomes de campo

**nível de 1.0:** Os nomes de campo foram alterados em minúsculas quando processadas pelo motor de Azure Stream Analytics.

**nível 1.1:** sensibilidade é mantida para nomes de campo quando eles são processados pelo motor de Azure Stream Analytics.

> [!NOTE]
> Sensibilidade mantendo ainda não está disponível para tarefas de análise de Stream alojadas utilizando o ambiente do Edge. Como resultado, todos os nomes de campo são convertidos em minúsculas, se o seu trabalho está alojado no Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**nível de 1.0:** Comando CREATE TABLE não filtrar eventos com NaN (não numérico. Por exemplo, infinito, - Infinity) numa coluna de número de vírgula FLUTUANTE escreva por estarem fora do intervalo documentado para esses números.

**nível de 1.1:** CREATE TABLE permite-lhe especificar um forte esquema. O motor do Stream Analytics valida que os dados estão em conformidade com esse esquema. Com esse modelo, o comando pode filtrar eventos com valores de NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Desativar upcast automática para cadeias de caracteres de datetime em JSON

**nível de 1.0:** O analisador JSON seria valores de cadeia de caracteres automaticamente upcast com informações de data/hora/fuso para o tipo DateTime e, em seguida, convertê-lo para UTC. Este comportamento resultou na perda de informações de fuso horário.

**nível de 1.1:** Há não mais automaticamente upcast dos valores de cadeia de caracteres com informações de data/hora/fuso para o tipo DateTime. Como resultado, as informações de fuso horário são mantidas.

## <a name="next-steps"></a>Passos Seguintes

* [Resolver problemas de entradas do Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [O estado de funcionamento do Stream Analytics recursos](stream-analytics-resource-health.md)
