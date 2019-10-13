---
title: Ingestão de dados do Azure Data Explorer
description: Saiba mais sobre as diferentes maneiras que você pode ingerir (carregar) dados no Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 35d3451327a0ce7bcaf567f93c48d532842b4f25
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285912"
---
# <a name="azure-data-explorer-data-ingestion"></a>Ingestão de dados do Azure Data Explorer

A ingestão de dados é o processo usado para carregar registros de dados de uma ou mais fontes para criar ou atualizar uma tabela no Azure Data Explorer. Depois de ingerido, os dados ficam disponíveis para consulta. O diagrama a seguir mostra o fluxo de ponta a ponta para trabalhar no Azure Data Explorer, incluindo a ingestão de dados.

![Fluxo de dados](media/ingest-data-overview/data-flow.png)

O serviço de gerenciamento de dados do Azure Data Explorer, que é responsável pela ingestão de dados, fornece a seguinte funcionalidade:

1. **Pull de dados**: efetua pull de dados de fontes externas (hubs de eventos) ou de solicitações de ingestão de leitura de uma fila do Azure.

1. **Envio em lote**: dados de lote que fluem para o mesmo banco de dado e tabela para otimizar a taxa de transferência de ingestão.

1. **Validação**: validação preliminar e conversão de formato, se necessário.

1. **Manipulação de dados**: esquema de correspondência, organização, indexação, codificação e compactação dos dados.

1. **Ponto de persistência no fluxo de ingestão**: gerencie a carga de ingestão no mecanismo e manipule novas tentativas em caso de falhas transitórias.

1. **Confirmar a ingestão de dados**: torna os dados disponíveis para consulta.

## <a name="ingestion-methods"></a>Métodos de ingestão

O Azure Data Explorer dá suporte a vários métodos de ingestão, cada um com seus próprios cenários de destino, vantagens e desvantagens. O Azure Data Explorer oferece pipelines e conectores para serviços comuns, ingestão programática usando SDKs e acesso direto ao mecanismo para fins de exploração.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Ingestão usando pipelines, conectores e plug-ins

Atualmente, o Azure Data Explorer dá suporte a:

* Pipeline de grade de eventos, que pode ser gerenciado usando o assistente de gerenciamento no portal do Azure. Para obter mais informações, consulte [ingerir BLOBs do Azure no azure data Explorer](ingest-data-event-grid.md).

* Pipeline do hub de eventos, que pode ser gerenciado usando o assistente de gerenciamento no portal do Azure. Para obter mais informações, consulte [ingerir dados do hub de eventos para data Explorer do Azure](ingest-data-event-hub.md).

* Plug-in Logstash, consulte [dados de ingestão do Logstash para o Azure data Explorer](ingest-data-logstash.md).

* Conector do Kafka, consulte [ingerir dados do Kafka para o Azure data Explorer](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Ingestão usando o Integration Services

* Azure Data Factory (ADF), um serviço de integração de dados totalmente gerenciado para cargas de trabalho analíticas no Azure, para copiar dados de e para o Azure Data Explorer usando [formatos e armazenamentos de dados com suporte](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats). Para obter mais informações, consulte [copiar dados de Azure data Factory para o Azure data Explorer](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Ingestão programática

O Azure Data Explorer fornece SDKs que podem ser usados para consulta e ingestão de dados. A ingestão programática é otimizada para reduzir os custos de ingestão (COGs), minimizando as transações de armazenamento durante e seguindo o processo de ingestão.

**SDKs disponíveis e projetos de código-fonte aberto**:

O Kusto oferece o SDK do cliente que pode ser usado para ingerir e consultar dados com:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [SDK do .NET](/azure/kusto/api/netfx/about-the-sdk)

* [SDK Java](/azure/kusto/api/java/kusto-java-client-library)

* [SDK do node](/azure/kusto/api/node/kusto-node-client-library)

* [API REST](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Técnicas de ingestão programática**:

* Ingestão de dados por meio do serviço de gerenciamento de dados do Azure Data Explorer (alta taxa de transferência e ingestão confiável):

    [**Ingestão de lote**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (fornecida pelo SDK): o cliente carrega os dados no armazenamento de BLOBs do Azure (designado pelo serviço de gerenciamento de dados do Azure data Explorer) e posta uma notificação em uma fila do Azure. A ingestão de lote é a técnica recomendada para ingestão de dados de alto volume, confiável e barato.

* Ingerir dados diretamente no mecanismo de Data Explorer do Azure (mais apropriado para exploração e protótipos):

  * **Ingestão embutida**: o comando Control (. ingestão embutida) contendo dados em banda destina-se a fins de teste ad hoc.

  * **Ingestão da consulta**: o comando de controle (. set,. set-ou-Append,. set-ou-replace) que aponta para os resultados da consulta é usado para gerar relatórios ou pequenas tabelas temporárias.

  * **Ingestão de armazenamento**: o comando de controle (. ingestão) com dados armazenados externamente (por exemplo, armazenamento de BLOBs do Azure) permite a ingestão eficiente de dados em massa.

**Latência de métodos diferentes**:

| Método | Latência |
| --- | --- |
| **Ingestão embutida** | Implantação |
| **Ingerir da consulta** | Tempo de consulta + tempo de processamento |
| **Ingestão do armazenamento** | Tempo de download + tempo de processamento |
| **Ingestão na fila** | Tempo em lote + tempo de processamento |
| |

O tempo de processamento depende do tamanho dos dados, menos de alguns segundos. O tempo de envio em lote tem como padrão 5 minutos.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Escolhendo o método de ingestão mais apropriado

Antes de começar a ingerir dados, você deve fazer a si mesmo as perguntas a seguir.

* Onde meus dados residem? 
* Qual é o formato de dados e pode ser alterado? 
* Quais são os campos obrigatórios a serem consultados? 
* Qual é o volume de dados e a velocidade esperados? 
* Quantos tipos de evento são esperados (refletidos como o número de tabelas)? 
* Com que frequência o esquema de evento deve ser alterado? 
* Quantos nós irão gerar os dados? 
* O que é o sistema operacional de origem? 
* Quais são os requisitos de latência? 
* Um dos pipelines de ingestão gerenciados existentes pode ser usado? 

Para organizações com uma infraestrutura existente baseada em um serviço de mensagens como hub de eventos e Hub IoT, o uso de um conector é provavelmente a solução mais apropriada. A ingestão na fila é apropriada para grandes volumes de dados.

## <a name="supported-data-formats"></a>Formatos de dados com suporte

Para todos os métodos de ingestão que não sejam ingeridos na consulta, formate os dados para que o Azure Data Explorer possa analisá-lo. Os formatos de dados com suporte são:

* TXT, CSV, TSV, TSVE, PSV, SCSV, SOH
* JSON (separado por linha, várias linhas), Avro
* ZIP e GZIP 

> [!NOTE]
> Quando os dados estão sendo ingeridos, os tipos de dados são inferidos com base nas colunas da tabela de destino. Se um registro estiver incompleto ou um campo não puder ser analisado como o tipo de dados necessário, as colunas da tabela correspondente serão preenchidas com valores nulos.

## <a name="ingestion-recommendations-and-limitations"></a>Recomendações e limitações de ingestão

* A política de retenção efetiva de dados ingeridos é derivada da política de retenção do banco de dado. Consulte [política de retenção](/azure/kusto/concepts/retentionpolicy) para obter detalhes. A ingestão de dados exige permissões de ingestão de **tabela** ou de **ingestão de banco** .
* A ingestão dá suporte a um tamanho máximo de arquivo de 5 GB. A recomendação é ingerir arquivos entre 100 MB e 1 GB.

## <a name="schema-mapping"></a>Mapeamento de esquema

O mapeamento de esquema ajuda a associar campos de dados de origem às colunas da tabela de destino.

* O [mapeamento de CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (opcional) funciona com todos os formatos baseados em ordinais. Ele pode ser executado usando o parâmetro de comando de ingestão ou [previamente criado na tabela](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) e referenciado do parâmetro de comando de ingestão.
* O [mapeamento JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (obrigatório) e o [mapeamento Avro](/azure/kusto/management/mappings?branch=master#avro-mapping) (obrigatório) podem ser executados usando o parâmetro de comando de ingestão. Elas também podem ser [criadas previamente na tabela](/azure/kusto/management/tables#create-ingestion-mapping) e referenciadas do parâmetro de comando de ingestão.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ingerir dados do hub de eventos para o Azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Ingerir dados usando a assinatura da grade de eventos no Azure Data Explorer](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Ingerir dados do Kafka para o Azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Ingerir dados usando a biblioteca do Azure Data Explorer Python](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingerir dados usando a biblioteca de nós do Azure Data Explorer](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingerir dados usando o SDK do Azure Data Explorer .NET Standard (versão prévia)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingerir dados do Logstash para o Azure Data Explorer](ingest-data-logstash.md)
