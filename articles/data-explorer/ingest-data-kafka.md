---
title: 'Início rápido: Ingerir dados do Kafka no Explorador de dados do Azure'
description: Neste início rápido, irá aprender a ingerir dados de (carga) no Explorador de dados do Azure do Kafka.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/19/2018
ms.openlocfilehash: 180053aa2146d8ab80df832044ac3ee4d45970da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445641"
---
# <a name="quickstart-ingest-data-from-kafka-into-azure-data-explorer"></a>Início rápido: Ingerir dados do Kafka no Explorador de dados do Azure
 
O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Explorador de dados do Azure oferece ingestão (carregamento de dados) do Kafka. O Kafka é uma plataforma de transmissão em fluxo distribuída que permite a criação de pipelines em tempo real de dados transmissão em fluxo que fiável mover dados entre sistemas ou aplicativos.
 
## <a name="prerequisites"></a>Pré-requisitos
 
* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar. 
 
* [Um cluster e uma base de dados de teste](create-cluster-database-portal.md)
 
* [Uma aplicação de exemplo](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) que gera dados e envia-os para o Kafka

* [Visual Studio 2017 Versão 15.3.2 ou superior](https://www.visualstudio.com/vs/) para executar a aplicação de exemplo
 
## <a name="kafka-connector-setup"></a>Configuração do conector de Kafka

Ligar o Kafka é uma ferramenta para escalável e fiável de transmissão em fluxo de dados entre o Apache Kafka e outros sistemas. Ela torna simples definir rapidamente os conectores que mover grandes coleções de dados para dentro e fora do Kafka. O Sink de Kafka ADX serve como o conector do Kafka.
 
### <a name="bundle"></a>Pacote

Kafka pode carregar um `.jar` como um plug-in que funcionará como um conector personalizado. Para produzir como, por exemplo um `.jar`, iremos clonar o código localmente e criar com o Maven. 

#### <a name="clone"></a>Clone

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Compilação

Criar localmente com o Maven para produzir um `.jar` completo com dependências.

* JDK > = 1.8 [transferir](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven [transferir](https://maven.apache.org/install.html)
 

Dentro do diretório de raiz *kafka-sink do azure kusto*, execute:

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Implementação 

Carregar o plug-in para o Kafka. Um exemplo de implementação com o docker pode ser encontrado em [kafka-sink do azure kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Documentação em conectores de Kafka detalhada e como implementá-las pode ser encontrado em [ligar do Kafka](https://kafka.apache.org/documentation/#connect) 

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
 
## <a name="create-a-target-table-in-adx"></a>Criar uma tabela de destino no ADX
 
Crie uma tabela no ADX para o qual Kafka pode enviar dados. Criar a tabela no cluster e a base de dados aprovisionada no **pré-requisitos**.
 
1. No portal do Azure, navegue para o cluster e selecione **consulta**.
 
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

Agora que o cluster de Kafka está ligado à ADX, utilizar o [aplicação de exemplo](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que transferiu para gerar dados.

### <a name="clone"></a>Clone

Clone a aplicação de exemplo localmente:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>Executar a aplicação

1. Abra a solução de aplicação de exemplo no Visual Studio.

1. Na `Program.cs` de ficheiros, atualize o `connectionString` constante para a cadeia de ligação do Kafka.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Compile e execute a aplicação. A aplicação envia mensagens para o cluster de Kafka e exibe o estado de cada 10 segundos.

1. Depois da aplicação enviou algumas mensagens, avançar para o passo seguinte.
 
## <a name="query-and-review-the-data"></a>Consultar e analisar os dados

1. Para garantir que nenhum erro ocorreu durante a ingestão:

    ```Kusto
    .show ingestion failures
    ```

1. Para ver os dados ingeridos recentemente:

    ```Kusto
    TestTable 
    | count
    ```

1. Para ver o conteúdo das mensagens:
 
    ```Kusto
    TestTable
    ```
 
    O conjunto de resultados deve ter um aspeto semelhante ao seguinte:
 
    ![Conjunto de resultados das mensagens](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>Passos Seguintes
 
> [!div class="nextstepaction"]
> [Quickstart: Consultar dados no Explorador de dados do Azure](web-query-data.md)
