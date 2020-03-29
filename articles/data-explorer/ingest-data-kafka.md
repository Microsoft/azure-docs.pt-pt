---
title: Ingerir dados de Kafka para o Azure Data Explorer
description: Neste artigo, aprende-se a ingerir (carregar) dados no Azure Data Explorer a partir de Kafka.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 03b46ff50683149a22c71ccb155480a0f08455bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66497269"
---
# <a name="ingest-data-from-kafka-into-azure-data-explorer"></a>Ingerir dados de Kafka para o Azure Data Explorer
 
O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer oferece ingestão (carregamento de dados) de Kafka. Kafka é uma plataforma de streaming distribuída que permite a construção de pipelines de dados de streaming em tempo real que movem dados de forma fiável entre sistemas ou aplicações.
 
## <a name="prerequisites"></a>Pré-requisitos
 
* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar. 
 
* [Um cluster de teste e base de dados.](create-cluster-database-portal.md)
 
* [Uma aplicação de amostra](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) que gera dados e envia para Kafka.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) para executar a aplicação de amostras.
 
## <a name="kafka-connector-setup"></a>Configuração do conector Kafka

Kafka Connect é uma ferramenta para o streaming escalável e fiável de dados entre Apache Kafka e outros sistemas. Torna simples definir rapidamente conectores que movem grandes coleções de dados para dentro e para fora de Kafka. O ADX Kafka Sink serve como conector de Kafka.
 
### <a name="bundle"></a>Pacote

Kafka pode `.jar` carregar um plugin que funcionará como um conector personalizado. Para produzir `.jar`tal a, clonamos o código localmente e construiremos usando Maven. 

#### <a name="clone"></a>Clone

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Compilação

Construa localmente com Maven para produzir um `.jar` completo com dependências.

* JDK >= 1,8 [download](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Download [de](https://maven.apache.org/install.html) Maven
 

Dentro do diretório raiz *kafka-sink-azure-kusto,* corra:

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Implementação 

Carregue o plugin em Kafka. Um exemplo de implantação usando estivador pode ser encontrado em [kafka-sink-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Documentação detalhada sobre conectores kafka e como implantá-los pode ser encontrada no [Kafka Connect](https://kafka.apache.org/documentation/#connect) 

### <a name="example-configuration"></a>Configuração de exemplo 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>Criar uma tabela de alvos no ADX
 
Crie uma tabela em ADX para a qual Kafka possa enviar dados. Criar a tabela no cluster e na base de dados aprovisionada nos **pré-requisitos.**
 
1. No portal Azure, navegue para o seu cluster e selecione **Consulta**.
 
    ![Ligação da aplicação de consulta](media/ingest-data-event-hub/query-explorer-link.png)
 
1. Copie o comando seguinte para a janela e selecione **Executar**.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![Executar criação da consulta](media/ingest-data-event-hub/run-create-query.png)
 
1. Copie o comando seguinte para a janela e selecione **Executar**.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    Este comando mapeia os dados JSON recebidos para os nomes de coluna e tipos de dados da tabela (TestTable).


## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora que o cluster Kafka está ligado ao ADX, utilize a [aplicação de amostra](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que descarregou para gerar dados.

### <a name="clone"></a>Clone

Clone a aplicação de amostra localmente:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>Executar a aplicação

1. Abra a solução de aplicação de exemplo no Visual Studio.

1. No `Program.cs` ficheiro, atualize a `connectionString` constante para a sua cadeia de ligação Kafka.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Compile e execute a aplicação. A aplicação envia mensagens para o cluster Kafka, e imprime o seu estado a cada 10 segundos.

1. Depois de a aplicação ter enviado algumas mensagens, passe para o próximo passo.
 
## <a name="query-and-review-the-data"></a>Consulta e revisão dos dados

1. Para garantir que não ocorreram erros durante a ingestão:

    ```Kusto
    .show ingestion failures
    ```

1. Para ver os dados recentemente ingeridos:

    ```Kusto
    TestTable 
    | count
    ```

1. Para ver o conteúdo das mensagens:
 
    ```Kusto
    TestTable
    ```
 
    O conjunto de resultados deve parecer o seguinte:
 
    ![Conjunto de resultados das mensagens](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>Passos seguintes
 
* [Dados de consulta no Explorador de Dados do Azure](web-query-data.md)
