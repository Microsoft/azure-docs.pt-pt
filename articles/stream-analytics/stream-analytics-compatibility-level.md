---
title: Níveis de compatibilidade do Azure Stream Analytics
description: Saiba como definir um nível de compatibilidade para um trabalho do Azure Stream Analytics e grandes mudanças no último nível de compatibilidade
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 11014c5a5c5cd0cabae1b62083bd5e662be2c6b7
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348938"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nível de compatibilidade para empregos Azure Stream Analytics

Este artigo descreve a opção de nível de compatibilidade no Azure Stream Analytics. O Stream Analytics é um serviço gerido, com atualizações regulares de funcionalidades e melhorias de desempenho. A maioria das atualizações de tempo de execução do serviço são automaticamente disponibilizadas aos utilizadores finais. 

No entanto, algumas novas funcionalidades no serviço podem introduzir uma grande mudança, como uma mudança no comportamento de um emprego existente, ou uma mudança na forma como os dados são consumidos na gestão de empregos. Pode manter os seus empregos stream analytics existentes em funcionamento sem grandes alterações, deixando a definição de nível de compatibilidade reduzida. Quando estiver pronto para os últimos comportamentos de tempo de execução, pode optar aumentando o nível de compatibilidade. 

## <a name="choose-a-compatibility-level"></a>Escolha um nível de compatibilidade

O nível de compatibilidade controla o comportamento de um trabalho de análise de fluxo. 

A Azure Stream Analytics suporta atualmente três níveis de compatibilidade:

* 1.0 - Nível de compatibilidade original, introduzido durante a disponibilidade geral do Azure Stream Analytics há vários anos.
* 1.1 - Comportamento anterior
* 1.2 - Comportamento mais recente com melhorias mais recentes

Quando se cria um novo trabalho stream Analytics, é uma boa prática criá-lo usando o mais recente nível de compatibilidade. Inicie o seu design de trabalho contando com os comportamentos mais recentes, para evitar mudanças adicionais e complexidade mais tarde.

## <a name="set-the-compatibility-level"></a>Definir o nível de compatibilidade

Pode definir o nível de compatibilidade para um trabalho stream Analytics no portal Azure ou utilizando a [chamada de API de emprego de criação](/rest/api/streamanalytics/2016-03-01/streamingjobs/createorreplace#compatibilitylevel).

Para atualizar o nível de compatibilidade do trabalho no portal Azure:

1. Utilize o [portal Azure](https://portal.azure.com) para localizar o seu trabalho stream Analytics.
2. **Pare** o trabalho antes de atualizar o nível de compatibilidade. Não pode atualizar o nível de compatibilidade se o seu trabalho estiver em funcionamento.
3. No título **Configure,** selecione **o nível de compatibilidade**.
4. Escolha o valor de nível de compatibilidade que deseja.
5. **Selecione Guarde** na parte inferior da página.

![Nível de compatibilidade do Stream Analytics no portal Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Ao atualizar o nível de compatibilidade, o compilador T valida o trabalho com a sintaxe que corresponde ao nível de compatibilidade selecionado.

## <a name="compatibility-level-12"></a>Compatibilidade nível 1.2

Introduzem-se as seguintes grandes alterações no nível de compatibilidade 1.2:

###  <a name="amqp-messaging-protocol"></a>Protocolo de mensagens AMQP

**1.2 nível** : A azure Stream Analytics utiliza o protocolo de mensagens [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) para escrever para Service Bus Queues and Topics. A AMQP permite-lhe construir aplicações híbridas e inter-plataforma usando um protocolo padrão aberto.

### <a name="geospatial-functions"></a>Funções geoespaciais

**Níveis anteriores:** Azure Stream Analytics usou cálculos de Geografia.

**1.2 nível:** O Azure Stream Analytics permite-lhe calcular coordenadas geométricas projetadas. Não há alteração na assinatura das funções geoespacial. No entanto, a sua semântica é ligeiramente diferente, permitindo uma computação mais precisa do que antes.

O Azure Stream Analytics suporta a indexação de dados geoespacial de referência. Os dados de referência que contêm elementos geoespaciais podem ser indexados para uma computação de junção mais rápida.

As funções geoespaciais atualizadas trazem a expressividade total do formato geoespacial do Texto Bem Conhecido (WKT). Pode especificar outros componentes geoespaciais que não foram previamente suportados com GeoJson.

Para obter mais informações, consulte [Atualizações de funcionalidades geoespaciais em Azure Stream Analytics – Cloud e IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Execução de consulta paralela para fontes de entrada com múltiplas divisórias

**Níveis anteriores:** As consultas Azure Stream Analytics exigiram a utilização de partição por cláusula para paralelizar o processamento de consultas através de divisórias de fonte de entrada.

**1.2 nível:** Se a lógica de consulta pode ser paralelamente através de divisições de fonte de entrada, o Azure Stream Analytics cria instâncias de consulta separadas e executa computações em paralelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integração API a granel nativa com a produção cosmosDB

**Níveis anteriores:** O comportamento de upsert foi *inserir ou fundir.*

**1.2 nível:** A integração nativa da API a granel com a saída cosmosDB maximiza a produção e lida eficientemente com pedidos de estrangulamento. Para obter mais informações, consulte [a saída Azure Stream Analytics para a página DB do Azure Cosmos](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12).

O comportamento de upsert é *inserir ou substituir*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DataTimeOffset ao escrever para a saída SQL

**Níveis anteriores:** Os tipos [de DataTimeOffset](/sql/t-sql/data-types/datetimeoffset-transact-sql) foram ajustados para UTC.

**1.2 nível:** O DateTimeOffset já não está ajustado.

### <a name="long-when-writing-to-sql-output"></a>Longa escrita para a saída SQL

**Níveis anteriores:** Os valores foram truncados com base no tipo alvo.

**1.2 nível:** Os valores que não se enquadram no tipo alvo são tratados de acordo com a política de erro de saída.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Serialização de registo e matriz ao escrever para a saída SQL

**Níveis anteriores:** Os discos foram escritos como "Record" e as matrizes foram escritas como "Array".

**1.2 nível:** Os registos e matrizes são serializados no formato JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Validação estrita do prefixo de funções

**Níveis anteriores:** Não houve validação estrita dos prefixos da função.

**1.2 nível:** A Azure Stream Analytics tem uma validação rigorosa dos prefixos de função. A adição de um prefixo a uma função incorporada provoca um erro. Por exemplo, `myprefix.ABS(…)` não é apoiado.

A adição de um prefixo aos agregados incorporados também resulta em erro. Por exemplo, `myprefix.SUM(…)` não é apoiado.

A utilização do "sistema" do prefixo para quaisquer funções definidas pelo utilizador resulta em erro.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Desaplice o Array e o Object como propriedades-chave no adaptador de saída Do Cosmos DB

**Níveis anteriores:** Os tipos de Matriz e Objeto foram suportados como uma propriedade chave.

**1.2 nível:** Os tipos de Matriz e Objetos já não são suportados como uma propriedade chave.

## <a name="compatibility-level-11"></a>Compatibilidade nível 1.1

Introduzem-se as seguintes grandes alterações no nível de compatibilidade 1.1:

### <a name="service-bus-xml-format"></a>Formato Service Bus XML

**Nível 1.0:** O Azure Stream Analytics utilizou o DataContractSerializer, pelo que o conteúdo da mensagem incluía tags XML. Por exemplo:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**Nível 1.1:** O conteúdo da mensagem contém o fluxo diretamente sem etiquetas adicionais. Por exemplo: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Sensibilidade a casos persistentes para nomes de campo

**Nível 1.0:** Os nomes de campo foram alterados para minúsculas quando processados pelo motor Azure Stream Analytics.

**1.1 nível:** a sensibilidade ao caso é persistiu para os nomes de campo quando são processados pelo motor Azure Stream Analytics.

> [!NOTE]
> A sensibilidade a caso persistente ainda não está disponível para trabalhos analíticos stream hospedados usando o ambiente Edge. Como resultado, todos os nomes de campo são convertidos para minúsculas se o seu trabalho estiver hospedado no Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializaçãoDisabled

**Nível 1.0:** O comando CREATE TABLE não filtrava eventos com NaN (Not-a-Number. Por exemplo, Infinito, -Infinito) num tipo de coluna FLOAT porque estão fora do intervalo documentado para estes números.

**Nível 1.1:** CRIAR TABELA permite especificar um esquema forte. O motor Stream Analytics valida que os dados estão em conformidade com este esquema. Com este modelo, o comando pode filtrar eventos com valores NaN.

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>Desativar a conversão automática de cadeias de datas para o tipo DateTime na entrada para JSON

**Nível 1.0:** O analisador JSON converteria automaticamente os valores de cadeia com informações de data/hora/fuso para o tipo DATETIME na entrada, de modo a que o valor perca imediatamente a sua informação original de formatação e fuso horário. Porque isto é feito em entrada, mesmo que esse campo não tenha sido usado na consulta, é convertido em UTC DateTime.

**Nível 1.1:** Não existe conversão automática de valores de cadeia com informação de data/hora/fuso para o tipo DATETIME. Como resultado, as informações sobre o azono e a formatação original são mantidas. No entanto, se o campo NVARCHAR(MAX) for utilizado na consulta como parte de uma expressão DATETIME (função DATEADD, por exemplo), é convertido para tipo DATETIME para executar o cálculo e perde a sua forma original.

## <a name="next-steps"></a>Próximos passos

* [Resolução de problemas Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Saúde do recurso stream Analytics](./stream-analytics-troubleshoot-query.md)