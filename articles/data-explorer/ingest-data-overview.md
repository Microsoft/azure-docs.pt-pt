---
title: Ingestão de dados do Azure Data Explorer
description: Saiba mais sobre as diferentes formas, pode ingerir (carregar) de dados no Explorador de dados do Azure
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 2/18/2019
ms.openlocfilehash: c4db658db3d32905368390826e5531ca31525486
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58133132"
---
# <a name="azure-data-explorer-data-ingestion"></a>Ingestão de dados do Azure Data Explorer

Ingestão de dados é o processo usado para carregar os registos de dados a partir de uma ou mais origens para criar ou atualizar uma tabela no Explorador de dados do Azure. Uma vez ingeridos, os dados ficam disponíveis para consulta. O diagrama abaixo mostra o fluxo de ponto-a-ponto para trabalhar no Explorador de dados do Azure, incluindo a ingestão de dados.

![Fluxo de dados](media/ingest-data-overview/data-flow.png)

O serviço de gestão de dados do Explorador de dados do Azure, que é responsável por ingestão de dados, fornece as seguintes funcionalidades:

1. **Extração de dados**: Extrair dados de fontes externas (os Hubs de eventos) ou pedidos de ingestão de leitura da fila do Azure.

1. **Criação de batches**: Fluxo para a mesma base de dados e tabela para otimizar o débito de ingestão de dados de batch.

1. **Validação**: Preliminar validação e o formato de conversão se necessário.

1. **Manipulação de dados**: Correspondência de esquema, organizar, a indexação, a codificação e a compressão dos dados.

1. **Ponto de persistência no fluxo de ingestão**: Gerir a carga de ingestão no motor e processar várias repetições após falhas transitórias.

1. **Consolidar os dados de ingestão**: Disponibiliza os dados para consulta.

## <a name="ingestion-methods"></a>Métodos de ingestão

O Explorador de dados do Azure suporta vários métodos de ingestão, cada um com seus próprios cenários de destino, as vantagens e desvantagens. O Explorador de dados do Azure oferece os pipelines e dos conectores para serviços comuns, ingestão programática utilizar SDKs e acesso direto para o mecanismo para fins de exploração.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Com o plug-ins, conectores e pipelines de ingestão

O Explorador de dados do Azure suporta atualmente:

* Pipeline grelha de eventos, que pode ser gerido com o Assistente de gestão no portal do Azure. Para obter mais informações, consulte [ingerir os Blobs do Azure no Explorador de dados do Azure](ingest-data-event-grid.md).

* Pipeline Hub de eventos, que pode ser gerido com o Assistente de gestão no portal do Azure. Para obter mais informações, consulte [ingerir dados de Hub de eventos no Explorador de dados do Azure](ingest-data-event-hub.md).

* Plug-in do Logstash, consulte [ingerir dados a partir do Logstash ao Azure Data Explorer](ingest-data-logstash.md).

* Conector do Kafka, consulte [ingerir dados do Kafka no Explorador de dados do Azure](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Ingestão de uso do integration services

* O Azure Data Factory (ADF), um serviço de integração de dados totalmente gerido para análise cargas de trabalho no Azure, para copiar dados de e para utilizar o Explorador de dados do Azure [arquivos de dados e formatos suportados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats). Para obter mais informações, consulte [copiar dados para ou a partir do Explorador de dados do Azure com o Azure Data Factory](/azure/data-factory/connector-azure-data-explorer).

### <a name="programmatic-ingestion"></a>Ingestão programática

O Explorador de dados do Azure disponibiliza SDKs que podem ser utilizadas para ingestão de dados e de consultas. Ingestão programático é otimizada para reduzir os custos de ingestão (COGs), minimizando as transações de armazenamento durante e após o processo de ingestão.

**SDKs disponíveis e projetos de código-fonte aberto**:

Kusto oferece o cliente SDK que pode ser utilizado para ingerir e consultar dados com:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [SDK do .NET](/azure/kusto/api/netfx/about-the-sdk)

* [SDK Java](/azure/kusto/api/java/kusto-java-client-library)

* [Nó de SDK](/azure/kusto/api/node/kusto-node-client-library)

* [API REST](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Técnicas de ingestão programática**:

* Ingestão de dados através do serviço de gestão de dados do Azure Data Explorer (ingestão de alto débito e fiável):

    [**Ingestão de lote** ](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (fornecido pelo SDK): o cliente carrega os dados para o armazenamento de Blobs do Azure (designado pelo serviço de gestão de dados do Explorador de dados do Azure) e envia uma notificação para uma fila do Azure. Ingestão de batch é a técnica recomendada para elevado volume, confiável e a ingestão de dados barato.

* Ingestão de dados diretamente para o mecanismo de Explorador de dados do Azure (mais adequado para a exploração e a criação de protótipos):

  * **Ingestão de inline**: controlo de comando (.ingest inline) que contém os dados em banda servem para fins de testes ad hoc.

  * **Ingestão de consulta**: comandos de controle (.set, .set ou acrescentar, .set ou substituir) que aponta para os resultados da consulta é utilizado para a geração de relatórios ou pequenas tabelas temporárias.

  * **Ingestão de armazenamento**: o comando de controlo (.ingest em) com os dados armazenados externamente (por exemplo, armazenamento de Blobs do Azure) permite a ingestão em massa eficiente de dados.

**Latência de métodos diferentes**:

| Método | Latência |
| --- | --- |
| **Ingestão de inline** | Imediato |
| **Ingestão de consulta** | Hora de consulta + tempo de processamento |
| **Ingestão de armazenamento** | Tempo de transferência + tempo de processamento |
| **Ingestão em fila** | Hora de criação de batches + tempo de processamento |
| |

Tempo de processamento depende do tamanho de dados, menos do que alguns segundos. Criação de batches tempo a predefinição é 5 minutos.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Escolher o método mais apropriado de ingestão

Antes de começar a ingestão de dados, deve se fazer as perguntas seguintes.

* Onde residem os meus dados? 
* O que é o formato de dados e pode ser alterado? 
* Quais são os campos necessários para ser consultado? 
* O que é o volume de dados esperado e a velocidade? 
* Quantos tipos de eventos são esperados (refletida como o número de tabelas)? 
* A frequência com que o esquema de eventos é esperado para alterar? 
* Quantos nós irão gerar os dados? 
* O que é a origem do sistema operacional? 
* Quais são os requisitos de latência? 
* Um dos pipelines de ingestão gerido existente sirva? 

Para organizações com uma infraestrutura existente que se baseiam num serviço de mensagens, como o Hub de eventos, através de um conector é provavelmente a solução mais apropriada. Ingestão em fila é adequada para grandes volumes de dados.

## <a name="supported-data-formats"></a>Formatos de dados suportados

Para a ingestão de todos os métodos diferentes de ingestão de consulta, formate os dados para que o Explorador de dados do Azure pode analisá-lo. Os formatos de dados suportados são:

* CSV, TSV, PSV, SCSV, SOH
* Avro (separados por linha, com várias linha), de JSON
* ZIP e GZIP 

> [!NOTE]
> Quando está a ser ingeridos dados, os tipos de dados são inferidos com base nas colunas da tabela de destino. Se um registo está incompleto ou um campo não pode ser analisado como o tipo de dados necessários, as colunas da tabela correspondente serão preenchidas com valores nulos.

## <a name="ingestion-recommendations-and-limitations"></a>Recomendações de ingestão e limitações

* A política de retenção em vigor a partir de dados ingeridos é derivada de política de retenção da base de dados. Ver [política de retenção](/azure/kusto/concepts/retentionpolicy) para obter detalhes. Ingestão de dados requer **ingestor de tabela** ou **ingestor de base de dados** permissões.
* Ingestão suporta um tamanho de ficheiro máximo de 5 GB. Recomenda-se para a ingestão de ficheiros entre 100 MB e 1 GB.

## <a name="schema-mapping"></a>Mapeamento de esquema

Mapeamento de esquema ajuda a vincular os campos de dados de origem a colunas da tabela de destino.

* [Mapeamento de CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (opcional) funciona com todos os formatos baseados em ordinal. Isso pode ser executado usando o parâmetro de comando de ingestão ou [criados previamente na tabela](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) e referenciado do parâmetro de comando de ingestão.
* [Mapeamento de JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (obrigatório) e [Avro mapeamento](/azure/kusto/management/mappings?branch=master#avro-mapping) (obrigatório) pode ser efetuado utilizando o parâmetro de comando de ingestão. Eles também podem ser [criados previamente na tabela](/azure/kusto/management/tables#create-ingestion-mapping) e referenciado do parâmetro de comando de ingestão.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ingerir dados de Hub de eventos no Explorador de dados do Azure](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Ingestão de dados com a subscrição do Event Grid no Explorador de dados do Azure](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Ingerir dados do Kafka no Explorador de dados do Azure](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Ingestão de dados usando a biblioteca de Python de Explorador de dados do Azure](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestão de dados usando a biblioteca de nó de Explorador de dados do Azure](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestão de dados com o SDK do Azure Data Explorer .NET Standard (pré-visualização)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingestão de dados do Logstash para o Explorador de dados do Azure](ingest-data-logstash.md)
