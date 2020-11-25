---
title: Copiar dados de e para Azure Databricks Delta Lake
description: Saiba como copiar dados de e para o Lago Delta da Azure Databricks utilizando uma atividade de cópia num oleoduto da Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/24/2020
ms.openlocfilehash: 3eb43c98ae2697ece5ded8ae0df451a6cf5f272d
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96007210"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Copiar dados de e para a Azure Databricks Delta Lake usando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade copy na Azure Data Factory para copiar dados de e para Azure Databricks Delta Lake. Baseia-se na atividade Copy no artigo [da Azure Data Factory,](copy-activity-overview.md) que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Databricks Delta Lake é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com uma tabela [de matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Em geral, a Azure Data Factory suporta o Delta Lake com as seguintes capacidades para atender às suas várias necessidades.

- A atividade de cópia suporta o conector Azure Databricks Delta Lake para copiar dados de qualquer loja de dados de origem suportada para a tabela do lago delta Azure Databricks, e da tabela delta lake para qualquer loja de dados de pia suportada. Aproveita o seu cluster Databricks para realizar o movimento de dados, ver detalhes na [secção Pré-Requisitos](#prerequisites).
- [Mapping Data Flow](concepts-data-flow-overview.md) suporta [o formato Delta](format-delta.md) genérico no Azure Storage como fonte e afundar para ler e escrever ficheiros Delta para ETL sem código, e executa em runtime gerido de integração Azure.
- [As atividades da Databricks](transform-data-databricks-notebook.md) suportam orquestrar o seu ETL centrado em código ou a carga de trabalho de aprendizagem automática em cima do lago Delta.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector Azure Databricks Delta Lake, você precisa configurar um cluster em Azure Databricks.

- Para copiar dados para o lago Delta, a atividade copy invoca o cluster Azure Databricks para ler dados de um Azure Storage, que é a sua fonte original ou uma área de preparação para onde a Data Factory escreve primeiro os dados de origem através de uma cópia encenada incorporada. Saiba mais a partir do [Lago Delta como fonte.](#delta-lake-as-source)
- Da mesma forma, para copiar dados do Lago Delta, a atividade copy invoca o cluster Azure Databricks para escrever dados para um Azure Storage, que é o seu lavatório original ou uma área de preparação de onde a Data Factory continua a escrever dados para a pia final através de uma cópia encenada incorporada. Saiba mais com [o Lago Delta como a pia.](#delta-lake-as-sink)

O cluster Databricks precisa de ter acesso à conta Azure Blob ou Azure Data Lake Storage Gen2, tanto o sistema de contentores/ficheiros de armazenamento utilizados para a origem/pia/encenação e o sistema de contentores/ficheiros onde pretende escrever as tabelas Delta Lake.

- Para utilizar **o Azure Data Lake Storage Gen2,** pode configurar uma chave de acesso principal ou **de armazenamento** de um **serviço** no cluster Databricks como parte da configuração Apache Spark. Siga os passos no [Access diretamente com o titular do serviço](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) ou o Access [diretamente utilizando a chave de acesso à conta de armazenamento.](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key)

- Para utilizar **o armazenamento Azure Blob,** pode configurar uma chave de acesso à **conta de armazenamento** ou um **símbolo SAS** no cluster Databricks como parte da configuração Apache Spark. Siga os passos no [armazenamento Access Azure Blob utilizando a API RDD](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api).

Durante a execução da atividade da cópia, se o cluster configurado tiver sido encerrado, a Data Factory inicia-o automaticamente. Se você autorizá-lo usando a Data Factory autoria UI, para operações como pré-visualização de dados, você precisa ter um cluster ao vivo, Data Factory não iniciará o cluster em seu nome.

#### <a name="specify-the-cluster-configuration"></a>Especificar a configuração do cluster

1. No **modo cluster** drop-down, selecione **Standard**.

2. Na versão de runtime de **Databricks,** selecione uma versão de tempo de execução databricks.

3. Ligue [o Auto Otimize](/azure/databricks/delta/optimizations/auto-optimize) adicionando as seguintes propriedades à sua [configuração Spark](/azure/databricks/clusters/configure#spark-config):

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Configure o seu cluster dependendo das suas necessidades de integração e escala.

Para obter detalhes de configuração do cluster, consulte [os clusters Configure](/azure/databricks/clusters/configure).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que definem entidades da Data Factory específicas de um conector Azure Databricks Delta Lake.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para um serviço ligado ao Lago Delta Azure Databricks.

| Propriedade    | Descrição                                                  | Obrigatório |
| :---------- | :----------------------------------------------------------- | :------- |
| tipo        | A propriedade tipo deve ser definida para **AzureDatabricksDeltaLake**. | Sim      |
| domínio      | Especificar o URL do espaço de trabalho Azure Databricks, por `https://adb-xxxxxxxxx.xx.azuredatabricks.net` exemplo. |          |
| clusterId   | Especifique o ID do cluster de um cluster existente. Deve ser um Cluster Interativo já criado. <br>Pode encontrar o ID do Cluster de um Cluster Interativo no espaço de trabalho databricks -> Clusters - > Configuração de > Configuração ->. [Saiba mais](/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | O token de acesso é necessário para que a Data Factory autente para a Azure Databricks. O token de acesso precisa de ser gerado a partir do espaço de trabalho dos dados. Passos mais detalhados para encontrar o token de acesso podem ser encontrados [aqui.](/azure/databricks/dev-tools/api/latest/authentication#generate-token) |          |
| connectVia  | O [tempo de integração](concepts-integration-runtime.md) que é usado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou um tempo de integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | Não       |

**Exemplo:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) 

As seguintes propriedades são suportadas para o conjunto de dados Azure Databricks Delta Lake.

| Propriedade  | Descrição                                                  | Obrigatório                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| tipo      | A propriedade do tipo do conjunto de dados deve ser definida para **AzureDatabricksDeltaLakeDataset**. | Sim                         |
| base de dados | O nome da base de dados. |Não para a fonte, sim para a pia.  |
| mesa | Nome da mesa delta. |Não para a fonte, sim para a pia.  |

**Exemplo:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia do Lago Delta Azure Databricks.

### <a name="delta-lake-as-source"></a>Lago Delta como fonte

Para copiar dados do Lago Delta da Azure Databricks, as seguintes propriedades são suportadas na secção **de origem** da atividade copy.

| Propriedade                     | Descrição                                                  | Obrigatório |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| tipo                         | A propriedade tipo da fonte de atividade copy deve ser definida para **AzureDatabricksDeltaLakeSource**. | Sim      |
| consulta          | Especifique a consulta SQL para ler dados. Para o controlo de viagem no tempo, siga o padrão abaixo:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | Não       |
| exportaçõesSettings | Configurações avançadas usadas para recuperar dados da tabela delta. | Não       |
| ***Em: `exportSettings` _** |  |  |
| tipo | O tipo de comando de exportação, definido para _*AzureDatabricksDeltaLakeExportCommand*** | Sim |
| dataFormat | Tipo de data de formato para cadeia com um formato de data. Os formatos de data personalizada seguem os formatos no [padrão de data.](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html) Se não for especificado, utiliza o valor predefinido `yyyy-MM-dd` . | Não |
| timetampFormat | Tipo de calibre tempo de formato para corda com um formato de horatamp. Os formatos de data personalizada seguem os formatos no [padrão de data.](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html) Se não for especificado, utiliza o valor predefinido `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | Não |

#### <a name="direct-copy-from-delta-lake"></a>Cópia direta do lago delta

Se a sua loja de dados e formato de sumidouro satisfaçam os critérios descritos nesta secção, pode utilizar a atividade Copy para copiar diretamente da tabela Delta da Azure Databricks para afundar. A Data Factory verifica as definições e falha a atividade copy executada se os seguintes critérios não forem cumpridos:

- O **serviço ligado à pia** é o armazenamento [Azure Blob](connector-azure-blob-storage.md) ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). A credencial de conta deve ser pré-configurada na configuração do cluster Azure Databricks, aprender mais com [pré-requisitos](#prerequisites).

- O formato de **dados** da pia é de **Parquet**, **texto delimitado,** ou **Avro** com as seguintes configurações, e aponta para uma pasta em vez de ficheiro.

    - Para o formato **Parquet,** o codec de compressão não é **nenhum,** **snappy,** ou **gzip**.
    - Para formato **de texto delimitado:**
        - `rowDelimiter` é qualquer personagem.
        - `compression` pode ser **nenhum,** **bzip2,** **gzip**.
        - `encodingName` UTF-7 não é apoiado.
    - Para o formato **Avro,** o codec de compressão não é **nenhum,** **esvaziar** ou **snappy**.

- Na fonte de atividade copy, `additionalColumns` não é especificado.
- Se copiar dados para textolimitado, na atividade de cópia afundar, `fileExtension` deve ser ".csv".
- No mapeamento da atividade copy, a conversão do tipo não está ativada.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Cópia encenada do lago delta

Quando a sua loja ou formato de dados de lavatório não corresponder aos critérios de cópia direta, como mencionado na última secção, ative a cópia encenada incorporada utilizando uma instância de armazenamento Azure provisória. A funcionalidade de cópia encenada também lhe proporciona uma melhor produção. A Data Factory exporta dados da Azure Databricks Delta Lake para o armazenamento de produção, depois copia os dados para afundar e, finalmente, limpa os seus dados temporários do armazenamento da encenação. Consulte [a cópia encenada](copy-activity-performance-features.md#staged-copy) para obter mais informações sobre a cópia dos dados utilizando a encenação.

Para utilizar esta funcionalidade, crie um [serviço ligado ao armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties) ou um serviço ligado ao [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) que se refere à conta de armazenamento como a encenação provisória. Em seguida, especifique as `enableStaging` propriedades e propriedades na atividade `stagingSettings` Copy.

>[!NOTE]
>A credencial de conta de armazenamento de encenação deve ser pré-configurada na configuração do cluster Azure Databricks, aprender mais com [pré-requisitos](#prerequisites).

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Lago Delta como pia

Para copiar dados para a Azure Databricks Delta Lake, as seguintes propriedades são suportadas na secção de **lavatório** de atividades Copy.

| Propriedade      | Descrição                                                  | Obrigatório |
| :------------ | :----------------------------------------------------------- | :------- |
| tipo          | A propriedade tipo do lavatório de atividade copy, definido para **AzureDatabricksDeltaLakeSink**. | Sim      |
| preCopyScript | Especifique uma consulta SQL para a atividade copy para executar antes de escrever dados na tabela delta databricks em cada execução. Pode utilizar esta propriedade para limpar os dados pré-carregados ou adicionar uma tabela truncada ou declaração de Vácuo. | Não       |
| importaçõesS | Definições avançadas usadas para escrever dados na tabela delta. | Não |
| **_Em: `importSettings` __* |                                                              |  |
| tipo | O tipo de comando de importação, definido para _*AzureDatabricksDeltaLakeImportCommand*** | Sim |
| dataFormat | Cadeia de formato até à data tipo com um formato de data. Os formatos de data personalizada seguem os formatos no [padrão de data.](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html) Se não for especificado, utiliza o valor predefinido `yyyy-MM-dd` . | Não |
| timetampFormat | Cadeia de formato para tipo de estamp de tempo com um formato de horatamp. Os formatos de data personalizada seguem os formatos no [padrão de data.](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html) Se não for especificado, utiliza o valor predefinido `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | Não |

#### <a name="direct-copy-to-delta-lake"></a>Cópia direta para o lago delta

Se a sua loja de dados de origem e formato satisfaçam os critérios descritos nesta secção, pode utilizar a atividade Copy para copiar diretamente da fonte para o Lago Delta de Dados Azure. A Azure Data Factory verifica as definições e falha a atividade copy executada se os seguintes critérios não forem cumpridos:

- O **serviço ligado à fonte** é o armazenamento [Azure Blob](connector-azure-blob-storage.md) ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). A credencial de conta deve ser pré-configurada na configuração do cluster Azure Databricks, aprender mais com [pré-requisitos](#prerequisites).

- O formato de dados de **origem** é de **Parquet**, **texto delimitado,** ou **Avro** com as seguintes configurações, e aponta para uma pasta em vez de ficheiro.

    - Para o formato **Parquet,** o codec de compressão não é **nenhum,** **snappy,** ou **gzip**.
    - Para formato **de texto delimitado:**
        - `rowDelimiter` é padrão, ou qualquer personagem único.
        - `compression` pode ser **nenhum,** **bzip2,** **gzip**.
        - `encodingName` UTF-7 não é apoiado.
    - Para o formato **Avro,** o codec de compressão não é **nenhum,** **esvaziar** ou **snappy**.

- Na fonte de atividade copy: 

    - `wildcardFileName` apenas contém `*` wildcard, mas não `?` , e não é `wildcardFolderName` especificado.
    - `prefix`, `modifiedDateTimeStart` `modifiedDateTimeEnd` e `enablePartitionDiscovery` não estão especificados.
    - `additionalColumns` não é especificado.

- No mapeamento da atividade copy, a conversão do tipo não está ativada.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Cópia encenada para o lago delta

Quando a sua loja de dados de origem ou formato não corresponder aos critérios de cópia direta, como mencionado na última secção, ative a cópia encenada incorporada utilizando uma instância de armazenamento Azure provisória. A funcionalidade de cópia encenada também lhe proporciona uma melhor produção. A Data Factory converte automaticamente os dados para satisfazer os requisitos do formato de dados em armazenamento de paragem e, em seguida, carregue os dados em delta lake a partir daí. Finalmente, limpa os seus dados temporários do armazenamento. Consulte [a cópia encenada](copy-activity-performance-features.md#staged-copy) para obter mais informações sobre a cópia dos dados utilizando a encenação.

Para utilizar esta funcionalidade, crie um [serviço ligado ao armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties) ou um serviço ligado ao [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) que se refere à conta de armazenamento como a encenação provisória. Em seguida, especifique as `enableStaging` propriedades e propriedades na atividade `stagingSettings` Copy.

>[!NOTE]
>A credencial de conta de armazenamento de encenação deve ser pré-configurada na configuração do cluster Azure Databricks, aprender mais com [pré-requisitos](#prerequisites).

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>Monitorização

A Azure Data Factory fornece a mesma [experiência de monitorização da atividade de cópia](copy-activity-monitoring.md) que outros conectores. Além disso, uma vez que os dados de carregamento de/para o lago delta estão a funcionar no seu cluster Azure Databricks, pode [visualizar registos de cluster detalhados](/azure/databricks/clusters/clusters-manage#--view-cluster-logs) e [monitorizar o desempenho](/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter mais informações sobre as propriedades, consulte [a atividade da Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes

Para uma lista de lojas de dados suportadas como fontes e pias por copy atividade na Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)