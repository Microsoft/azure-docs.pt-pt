---
title: Níveis de compatibilidade do Azure Stream Analytics
description: Saiba como configurar um nível de compatibilidade para uma tarefa do Azure Stream Analytics e as principais alterações no nível de compatibilidade mais recentes
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 14b04f7d6068863c79c7060d29b58232be1f40cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201803"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nível de compatibilidade para tarefas do Azure Stream Analytics

Este artigo descreve a opção de nível de compatibilidade no Azure Stream Analytics. O Stream Analytics é um serviço gerido, com atualizações regulares de funcionalidades e melhorias de desempenho. A maioria das atualizações de tempos de execução do serviço são automaticamente disponibilizadas aos utilizadores finais. 

No entanto, algumas novas funcionalidades no serviço podem introduzir uma grande mudança, como uma mudança no comportamento de um emprego existente, ou uma mudança na forma como os dados são consumidos em postos de trabalho. Pode manter os seus empregos de Stream Analytics existentes em funcionamento sem grandes alterações, deixando a definição do nível de compatibilidade reduzida. Quando estiver pronto para os mais recentes comportamentos de tempo de execução, pode optar por entrar aumentando o nível de compatibilidade. 

## <a name="choose-a-compatibility-level"></a>Escolha um nível de compatibilidade

Nível de compatibilidade controla o comportamento de tempo de execução de uma tarefa do stream analytics. 

O Azure Stream Analytics suporta atualmente três níveis de compatibilidade:

* 1.0 - Comportamento anterior
* 1.1 - Comportamento predefinido
* 1.2 - Comportamento mais recente com as mais recentes melhorias

O nível original de compatibilidade 1.0 foi introduzido durante a disponibilidade geral do Azure Stream Analytics há vários anos.

Quando cria um novo trabalho no Stream Analytics, é uma boa prática criá-lo usando o mais recente nível de compatibilidade. Inicie o seu design de trabalho contando com os mais recentes comportamentos, para evitar mudanças adicionais e complexidade mais tarde.

## <a name="set-the-compatibility-level"></a>Definir o nível de compatibilidade

Pode definir o nível de compatibilidade para um trabalho de Stream Analytics no portal Azure ou utilizando a [chamada de criação REST API](/rest/api/streamanalytics/stream-analytics-job).

Para atualizar o nível de compatibilidade do trabalho no portal Azure:

1. Utilize o [portal Azure](https://portal.azure.com) para localizar o seu trabalho em Stream Analytics.
2. **Pare** o trabalho antes de atualizar o nível de compatibilidade. Não é possível atualizar o nível de compatibilidade, se o seu trabalho está num Estado em execução.
3. Sob a rubrica **Configuração,** selecione **o nível**de compatibilidade .
4. Escolha o valor de nível de compatibilidade que deseja.
5. Selecione **Guardar** na parte inferior da página.

![Nível de compatibilidade do Stream Analytics no portal do Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Ao atualizar o nível de compatibilidade, o compilador T valida o trabalho com a sintaxe que corresponde ao nível de compatibilidade selecionado.

## <a name="compatibility-level-12"></a>Compatibilidade nível 1.2

As seguintes grandes alterações são introduzidas no nível de compatibilidade 1.2:

###  <a name="amqp-messaging-protocol"></a>Protocolo de mensagens AMQP

**Nível 1.2**: O Azure Stream Analytics utiliza o protocolo de mensagens [avançadas (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) para escrever às filas e tópicos de ônibus de serviço. A AMQP permite-lhe construir aplicações híbridas e de plataforma cruzada utilizando um protocolo padrão aberto.

### <a name="geospatial-functions"></a>Funções geoespaciais

**Níveis anteriores:** O Azure Stream Analytics utilizou cálculos de geografia.

**1.2 nível:** O Azure Stream Analytics permite-lhe calcular geocoordenadas geométricas projetadas. Não há alteração na assinatura das funções geoespaciais. No entanto, a sua semântica é ligeiramente diferente, permitindo uma computação mais precisa do que antes.

O Azure Stream Analytics suporta a indexação de dados de referência geoespacial. Os dados de referência que contêm elementos geoespaciais podem ser indexados para uma computação de adesão mais rápida.

As funções geoespaciais atualizadas trazem toda a expressividade do formato geoespacial do Texto Bem Conhecido (WKT). Pode especificar outros componentes geoespaciais que não foram previamente suportados com GeoJson.

Para mais informações, consulte [Atualizações para funcionalidades geoespaciais no Azure Stream Analytics – Cloud e IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Execução de consulta paralela para fontes de entrada com múltiplas divisórias

**Níveis anteriores:** As consultas do Azure Stream Analytics exigiram a utilização da cláusula PARTITION BY para paralelor o processamento de consultas através das divisórias de origem de entrada.

**1.2 nível:** Se a lógica da consulta pode ser paralela através de divisórias de origem de entrada, o Azure Stream Analytics cria instâncias de consulta separadas e executa computações em paralelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integração nativa da API a granel com saída cosmosDB

**Níveis anteriores:** O comportamento mais forte foi *inserir ou fundir.*

**1.2 nível:** A integração nativa da API a granel com a saída cosmosDB maximiza a produção e lida eficientemente com pedidos de estrangulamento. Para mais informações, consulte [a saída do Azure Stream Analytics para a página DD Do Azure Cosmos.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)

O comportamento de upsert é *inserir ou substituir*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DataTimeOffset ao escrever para a saída SQL

**Níveis anteriores:** Os tipos [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) foram ajustados à UTC.

**1.2 nível:** DateTimeOffset já não está ajustado.

### <a name="long-when-writing-to-sql-output"></a>Muito tempo ao escrever para a saída SQL

**Níveis anteriores:** Os valores foram truncados com base no tipo alvo.

**1.2 nível:** Os valores que não se enquadram no tipo-alvo são tratados de acordo com a política de erro de saída.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Registar e matriz de serialização ao escrever para a saída SQL

**Níveis anteriores:** Os discos foram escritos como "Record" e as matrizes foram escritas como "Array".

**1.2 nível:** Os registos e matrizes são serializados em formato JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Validação rigorosa do prefixo de funções

**Níveis anteriores:** Não houve validação rigorosa dos prefixos de função.

**1.2 nível:** O Azure Stream Analytics tem uma validação rigorosa dos prefixos de função. Adicionar um prefixo a uma função incorporada causa um erro. Por exemplo,`myprefix.ABS(…)` não é apoiado.

Adicionar um prefixo aos agregados incorporados também resulta em erro. Por exemplo, `myprefix.SUM(…)` não é apoiado.

A utilização do "sistema" prefixo para quaisquer funções definidas pelo utilizador resulta em erro.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Disallow Array e Objeto como propriedades-chave no adaptador de saída Cosmos DB

**Níveis anteriores:** Os tipos de matriz e objeto foram suportados como uma propriedade chave.

**1.2 nível:** Os tipos de matriz e objeto já não são suportados como uma propriedade chave.

## <a name="compatibility-level-11"></a>Compatibilidade nível 1.1

As seguintes alterações principais foram introduzidas no nível de compatibilidade 1.1:

### <a name="service-bus-xml-format"></a>Formato XML de ônibus de serviço

**Nível 1.0:** O Azure Stream Analytics utilizou dataContractSerializer, pelo que o conteúdo da mensagem incluía tags XML. Por exemplo:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 nível:** O conteúdo da mensagem contém o fluxo diretamente sem etiquetas adicionais. Por exemplo: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Sensibilidade de caso persistente para nomes de campo

**Nível 1.0:** Os nomes de campo foram alterados para minúsculas quando processados pelo motor Azure Stream Analytics.

**Nível 1.1:** persiste a sensibilidade à caixa para os nomes de campo quando são processados pelo motor Azure Stream Analytics.

> [!NOTE]
> Sensibilidade mantendo ainda não está disponível para tarefas de análise de Stream alojadas utilizando o ambiente do Edge. Como resultado, todos os nomes de campo são convertidos em minúsculas, se o seu trabalho está alojado no Edge.

### <a name="floatnandeserializationdisabled"></a>FlutuanaNDeserializaçãoDeficiente

**Nível 1.0:** O comando CREATE TABLE não filtrava eventos com NaN (Not-a-Number. Por exemplo, infinito, - Infinity) numa coluna de número de vírgula FLUTUANTE escreva por estarem fora do intervalo documentado para esses números.

**1.1 nível:** Tabela CREATE permite especificar um esquema forte. O motor do Stream Analytics valida que os dados estão em conformidade com esse esquema. Com esse modelo, o comando pode filtrar eventos com valores de NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Desative o upcast automático para cordas de data em JSON

**Nível 1.0:** O parser JSON aumentaria automaticamente os valores das cordas com informações de data/hora/zona para o tipo DateTime e, em seguida, convertê-lo-ia para UTC. Este comportamento resultou na perda da informação do fuso horário.

**1.1 nível:** Não existe mais automaticamente adifusão de valores de cordas com informações de data/hora/zona para o tipo DateTime. Como resultado, as informações de fuso horário são mantidas.

## <a name="next-steps"></a>Passos seguintes

* [Entradas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Saúde de recursos stream analytics](stream-analytics-resource-health.md)
