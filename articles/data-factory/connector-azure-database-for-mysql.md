---
title: Copiar e transformar dados na Base de Dados Azure para o MySQL
description: ganhar como copiar e transformar dados em Azure Database para o MySQL utilizando a Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 4d13f6f435a21b467cae1b8e14211a001792787f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012614"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Copiar e transformar dados na Base de Dados Azure para o MySQL utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity in Azure Data Factory para copiar dados de e para Azure Database para o MySQL, e utilizar o Fluxo de Dados para transformar dados na Base de Dados Azure para o MySQL. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

Este conector é especializado para [a Azure Database para o serviço MySQL](../mysql/overview.md). Para copiar dados da base de dados genérica do MySQL localizada no local ou na nuvem, utilize o [conector MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Esta Base de Dados Azure para o conector MySQL é suportada para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas da Base de Dados Azure para o conector MySQL.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para a Base de Dados Azure para o serviço ligado ao MySQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzureMySql** | Yes |
| conexãoStragem | Especifique as informações necessárias para ligar à Base de Dados Azure para a ocorrência do MySQL. <br/> Também pode colocar a palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no artigo do Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

Uma cadeia de ligação típica é `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Mais propriedades que pode definir por seu caso:

| Propriedade | Descrição | Opções | Necessário |
|:--- |:--- |:--- |:--- |
| SSLMode | Esta opção especifica se o controlador utiliza encriptação e verificação TLS ao ligar-se ao MySQL. Por exemplo, `SSLMode=<0/1/2/3/4>`| DESATIVADO (0) / PREFERIDO (1) **(Predefinido)** / OBRIGATÓRIO (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | No |
| UseSystemTrustStore | Esta opção especifica se deve utilizar um certificado de CA da loja de fidedignidade do sistema ou de um ficheiro PEM especificado. Por exemplo, `UseSystemTrustStore=<0/1>;`| Ativado (1) / Desativado (0) **(Predefinição)** | No |

**Exemplo:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar senha em Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pela Base de Dados Azure para conjunto de dados MySQL.

Para copiar os dados da Base de Dados Azure para o MySQL, defina a propriedade tipo do conjunto de dados para **AzureMySqlTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **AzureMySqlTable** | Yes |
| tableName | O nome da tabela na base de dados MySQL. | Não (se for especificada "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela Base de Dados Azure para fonte mySQL e pia.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database para MySQL como fonte

Para copiar dados da Base de Dados Azure para o MySQL, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **AzureMySqlSource** | Yes |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |
| consultaCommandTimeout | O tempo de espera antes do pedido de consulta. O padrão é de 120 minutos (02:00:00) | No |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database para MySQL como pia

Para copiar dados para a Base de Dados Azure para o MySQL, as seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do lavatório de atividade de cópia deve ser definida para: **AzureMySqlSink** | Yes |
| preCopyScript | Especifique uma consulta SQL para a atividade da cópia a executar antes de escrever dados na Base de Dados Azure para o MySQL em cada execução. Pode utilizar esta propriedade para limpar os dados pré-carregados. | No |
| escreverBatchSize | Insere dados na Base de Dados Azure para a tabela MySQL quando o tamanho do tampão atinge o writeBatchSize.<br>Valor permitido é número inteiro representando o número de linhas. | Não (o padrão é 10.000) |
| escreverBatchTimeout | Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo.<br>Os valores permitidos são o Timespan. Um exemplo é 00:30:00 (30 minutos). | Não (padrão é 00:00:30) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever para tabelas a partir da Base de Dados Azure para o MySQL. Para obter mais informações, consulte a [transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) nos fluxos de dados de mapeamento. Pode optar por utilizar uma Base de Dados Azure para conjunto de dados Do MySQL ou um [conjunto de dados inline](data-flow-source.md#inline-datasets) como fonte e tipo de pia.

### <a name="source-transformation"></a>Transformação de origem

A tabela abaixo lista as propriedades suportadas pela Base de Dados Azure para a fonte MySQL. Pode editar estas propriedades no separador **Opções Fonte.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabela | Se selecionar tabela como entrada, o fluxo de dados recolhe todos os dados da tabela especificada no conjunto de dados. | No | - |*(apenas para conjunto de dados em linha)*<br>tableName |
| Consulta | Se selecionar a Consulta como entrada, especifique uma consulta SQL para obter dados da fonte, que substitui qualquer tabela que especifique no conjunto de dados. Usar consultas é uma ótima maneira de reduzir linhas para testes ou análises.<br><br>**A** cláusula Por cláusula não é suportada, mas pode definir uma declaração completa SELECT FROM. Também pode utilizar funções de tabela definidas pelo utilizador. **selecionar * do udfGetData()** é um UDF em SQL que devolve uma tabela que pode usar no fluxo de dados.<br>Exemplo de consulta: `select * from mytable where customerId > 1000 and customerId < 2000` ou `select * from "MyTable"` . .| Não | String | consulta |
| Tamanho do lote | Especifique o tamanho do lote para colar dados grandes em lotes. | No | Número inteiro | batchSize |
| Nível de Isolamento | Escolha um dos seguintes níveis de isolamento:<br>- Ler Comprometido<br>- Ler Não Comprometido (padrão)<br>- Leitura Repetível<br>- Serializável<br>- Nenhum (ignorar o nível de isolamento) | No | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZÁVEL<br/>NENHUMA</small> |isolamentoLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Azure Database para o exemplo do script de origem MySQL

Quando utiliza a Base de Dados Azure para o MySQL como tipo de origem, o script de fluxo de dados associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Transformação do sumidouro

A tabela abaixo lista as propriedades suportadas pela Base de Dados Azure para o lavatório MySQL. Pode editar estas propriedades no **separador Opções De Sumidouro.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Método de atualização | Especifique quais as operações permitidas no seu destino de base de dados. O padrão é apenas permitir inserções.<br>Para atualizar, intensificar ou apagar linhas, é necessária uma [transformação de linha Alter](data-flow-alter-row.md) para marcar linhas para essas ações. | Yes | `true` ou `false` | deletable <br/>inserível <br/>atualizável <br/>upsertable |
| Colunas-chave | Para atualizações, atualizações e eliminações, as colunas-chave devem ser definidas para determinar qual a linha a alterar.<br>O nome da coluna que escolher como chave será utilizado como parte da atualização subsequente, atualizar, eliminar. Portanto, deve escolher uma coluna que exista no mapeamento da Pia. | No | Matriz | keys |
| Salte a escrita de colunas-chave | Se não pretender não escrever o valor na coluna-chave, selecione "Salte as colunas-chave de escrita". | No | `true` ou `false` | skipKeyWrites |
| Ação de mesa |Determina se deve recriar ou remover todas as linhas da tabela de destino antes de escrever.<br>- **Nenhuma:** nenhuma ação será feita à mesa.<br>- **Recriar:** A mesa será largada e recriada. Necessário se criar uma nova tabela dinamicamente.<br>- **Truncato**: Todas as linhas da mesa-alvo serão removidas. | No | `true` ou `false` | recriar<br/>truncato |
| Tamanho do lote | Especifique quantas linhas estão a ser escritas em cada lote. Tamanhos maiores do lote melhoram a compressão e a otimização da memória, mas arriscam-se a sair das exceções de memória ao caching dados. | No | Número inteiro | batchSize |
| Scripts pré e post SQL | Especifique scripts SQL de várias linhas que serão executados antes (pré-processamento) e depois (pós-processamento) os dados são escritos na sua base de dados de Sink. | Não | String | pré-QLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Azure Database para o exemplo do script do pia MySQL

Quando utiliza a Base de Dados Azure para o MySQL como tipo de pia, o script de fluxo de dados associado é:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapeamento do tipo de dados para Azure Database para MySQL

Ao copiar dados da Azure Database para o MySQL, os seguintes mapeamentos são utilizados desde os tipos de dados do MySQL até aos tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de schema e tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Base de Dados Azure para o tipo de dados MySQL | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
