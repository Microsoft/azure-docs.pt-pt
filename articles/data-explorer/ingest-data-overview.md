---
title: Ingestão de dados do Explorador de Dados azure
description: Saiba mais sobre as diferentes formas de ingerir (carregar) dados no Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 4846a19c403cce16bed704ed4e7c70499f3b5d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246399"
---
# <a name="azure-data-explorer-data-ingestion"></a>Ingestão de dados do Explorador de Dados azure

A ingestão de dados é o processo utilizado para carregar registos de dados de uma ou mais fontes para criar ou atualizar uma tabela no Azure Data Explorer. Uma vez ingeridos, os dados ficam disponíveis para consulta. O diagrama abaixo mostra o fluxo de ponta a ponta para trabalhar no Azure Data Explorer, incluindo a ingestão de dados.

![Fluxo de dados](media/ingest-data-overview/data-flow.png)

O serviço de gestão de dados do Azure Data Explorer, responsável pela ingestão de dados, fornece a seguinte funcionalidade:

1. **Recolha de dados**: Retire dados de fontes externas (Hubs de Eventos) ou leia pedidos de ingestão de uma fila Azure.

1. **Loteamento**: Os dados do lote que fluem para a mesma base de dados e tabela para otimizar a produção de ingestão.

1. **Validação**: Validação preliminar e conversão de formato, se necessário.

1. **Manipulação de dados**: Esquema sincronizado, organização, indexação, codificação e compressão dos dados.

1. Ponto de persistência no fluxo de **ingestão**: Gerir a carga de ingestão no motor e manusear as tentativas de retenção em falhas transitórias.

1. **Cometer a ingerir dados**: Disponibiliza os dados para consulta.

## <a name="ingestion-methods"></a>Métodos de ingestão

O Azure Data Explorer suporta vários métodos de ingestão, cada um com os seus próprios cenários, vantagens e desvantagens. O Azure Data Explorer oferece oleodutos e conectores a serviços comuns, ingestão programática utilizando SDKs e acesso direto ao motor para fins de exploração.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Ingestão utilizando oleodutos, conectores e plugins

O Azure Data Explorer suporta atualmente:

* Pipeline Event Grid, que pode ser gerido utilizando o assistente de gestão no portal Azure. Para mais informações, consulte [a Ingest Azure Blobs no Azure Data Explorer](ingest-data-event-grid.md).

* Pipeline Event Hub, que pode ser gerido usando o assistente de gestão no portal Azure. Para mais informações, consulte [os dados da Ingest do Event Hub para o Azure Data Explorer](ingest-data-event-hub.md).

* Logstash plugin, consulte [os dados da Ingest desde o Logstash até ao Azure Data Explorer](ingest-data-logstash.md).

* Ligação Kafka, veja [os dados de Ingest de Kafka para o Azure Data Explorer.](ingest-data-kafka.md)

### <a name="ingestion-using-integration-services"></a>Ingestão utilizando serviços de integração

* A Azure Data Factory (ADF), um serviço de integração de dados totalmente gerido para cargas de trabalho analíticas em Azure, para copiar dados de e para o Azure Data Explorer utilizando [lojas e formatos de dados suportados.](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) Para mais informações, consulte [os dados da Cópia da Azure Data Factory para o Azure Data Explorer](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Ingestão programática

O Azure Data Explorer fornece SDKs que podem ser usados para consulta e ingestão de dados. A ingestão programática é otimizada para reduzir os custos de ingestão (COGs), minimizando as transações de armazenamento durante e seguindo o processo de ingestão.

**SDKs disponíveis e projetos de código aberto:**

Kusto oferece sDK ao cliente que pode ser usado para ingerir e consultar dados com:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [SDK de Node](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Técnicas programáticas de ingestão:**

* Ingeque dados através do serviço de gestão de dados do Azure Data Explorer (ingestão de alto consumo e ingestão fiável):

    [**Ingestão de lotes**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (fornecido pela SDK): o cliente envia os dados para o armazenamento da Blob Azure (designado pelo serviço de gestão de dados do Azure Data Explorer) e publica uma notificação para uma fila Azure. A ingestão de lotes é a técnica recomendada para a ingestão de dados de alto volume, fiável e barata.

* Inserindo dados diretamente no motor Azure Data Explorer (mais adequado para exploração e prototipagem):

  * **Ingestão inline**: o comando de comando de comando de comando (.inline inline) contendo dados em banda destina-se a fins de ensaio ad hoc.

  * **Ingest de consulta**: comando de comando de controlo (.set, .set-or-append, .set-or-replace) que aponta para resultados de consulta é usado para gerar relatórios ou pequenas tabelas temporárias.

  * **Ingest a partir do armazenamento**: o comando de controlo (.inging ingerir) com dados armazenados externamente (por exemplo, Armazenamento De Blob Azure) permite uma ingestão eficiente a granel de dados.

**Latência de diferentes métodos:**

| Método | Latência |
| --- | --- |
| **Ingestão inline** | Imediato |
| **Ingest de consulta** | Tempo de consulta + tempo de processamento |
| **Ingestão a partir do armazenamento** | Tempo de descarregamento + tempo de processamento |
| **Ingestão em fila** | Tempo de loteamento + tempo de processamento |
| |

O tempo de processamento depende do tamanho dos dados, menos de alguns segundos. O tempo de loteamento não se aplica a 5 minutos.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Escolher o método de ingestão mais adequado

Antes de começar a ingerir dados, deve fazer as seguintes perguntas.

* Onde residem os meus dados? 
* Qual é o formato de dados, e pode ser alterado? 
* Quais são os campos necessários para serem consultados? 
* Qual é o volume e velocidade de dados esperados? 
* Quantos tipos de eventos são esperados (refletidos como o número de tabelas)? 
* Quantas vezes é esperado que o evento mude? 
* Quantos nós gerarão os dados? 
* Qual é a fonte do SO? 
* Quais são os requisitos de latência? 
* Pode ser utilizado um dos gasodutos de ingestão geridos existentes? 

Para organizações com uma infraestrutura existente que se baseiam num serviço de mensagens como o Event Hub e o IoT Hub, usar um conector é provavelmente a solução mais adequada. A ingestão em fila é adequada para grandes volumes de dados.

## <a name="supported-data-formats"></a>Formatos de dados suportados

Para todos os métodos de ingestão que não sejam ingerir a partir de consulta, forforme os dados para que o Azure Data Explorer possa analisá-lo. 
* Os formatos de dados suportados são: TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (separados de linha, multi-linhas), Avro, Orc e Parquet. 
* Suporta a compressão ZIP e GZIP.

> [!NOTE]
> Quando os dados estão a ser ingeridos, os tipos de dados são inferidos com base nas colunas da tabela-alvo. Se um registo estiver incompleto ou se um campo não puder ser analisado como o tipo de dados exigido, as colunas de tabela correspondentes serão povoadas com valores nulos.

## <a name="ingestion-recommendations-and-limitations"></a>Recomendações e limitações de ingestão

* A política eficaz de retenção dos dados ingeridos deriva da política de retenção da base de dados. Consulte a política de [retenção](/azure/kusto/concepts/retentionpolicy) para obter detalhes. A ingestão de dados requer **ingestor** de tabela ou Base de Dados em permissões **gestoras.**
* A ingestão suporta um tamanho máximo de ficheiro de 5 GB. A recomendação é ingerir ficheiros entre 100 MB e 1 GB.

## <a name="schema-mapping"></a>Mapeamento de schema

O mapeamento de schema ajuda a ligar os campos de dados de origem às colunas de tabelas de destino.

* [O Mapeamento CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (opcional) funciona com todos os formatos baseados em ordinal. Pode ser realizado utilizando o parâmetro de comando ingerir ou [pré-criado na mesa](/azure/kusto/management/create-ingestion-mapping-command) e referenciado a partir do parâmetro de comando ingerir.
* O [Mapeamento JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (obrigatório) e o [mapeamento Avro](/azure/kusto/management/mappings?branch=master#avro-mapping) (obrigatório) podem ser realizados utilizando o parâmetro de comando ingerir. Também podem ser [pré-criados na mesa](/azure/kusto/management/create-ingestion-mapping-command) e referenciados a partir do parâmetro de comando ingerir.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ingerir dados do Event Hub no Azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Ingerir dados usando a subscrição da Rede de Eventos no Azure Data Explorer](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Ingerir dados de Kafka para o Azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Ingerir dados utilizando a biblioteca Python do Explorador de Dados Azure](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingerir dados utilizando a biblioteca do Nó do Explorador de Dados Azure](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Dados de ingestão utilizando o Azure Data Explorer .NET Standard SDK (Pré-visualização)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingerir dados de Logstash para Azure Data Explorer](ingest-data-logstash.md)
