---
title: Copiar e transformar dados na Base de Dados Azure para PostgreSQL
description: Saiba como copiar e transformar dados na Base de Dados Azure para PostgreSQL utilizando a Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: ec4ea645e325ef48d4cb5951cd39fd4e9cbe1617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738060"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copiar e transformar dados na Base de Dados Azure para PostgreSQL utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity in Azure Data Factory para copiar dados de e para a Base de Dados Azure para PostgreSQL, e utilizar o Fluxo de Dados para transformar dados na Base de Dados Azure para PostgreSQL. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

Este conector é especializado para o [serviço Azure Database for PostgreSQL](../postgresql/overview.md). Para copiar dados de uma base de dados pós-QL genérica localizada no local ou na nuvem, utilize o [conector PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Esta Base de Dados Azure para conector PostgreSQL é suportada para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com uma [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Atualmente, o fluxo de dados suporta a base de dados Azure para o PostgreSQL Single Server, mas não o Flexible Server ou Hyperscale (Citus).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções oferecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas da Base de Dados Azure para conector PostgreSQL.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para a Base de Dados Azure para o serviço ligado a PostgreSQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzurePostgreSql**. | Sim |
| conexãoStragem | Um fio de ligação ODBC para ligar à Base de Dados Azure para PostgreSQL.<br/>Também pode colocar uma palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no Cofre da Chave Azure](store-credentials-in-key-vault.md) para obter mais detalhes. | Sim |
| connectVia | Esta propriedade representa o [tempo de integração](concepts-integration-runtime.md) a ser usado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |Não |

Uma cadeia de ligação típica é `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Aqui estão mais propriedades que pode definir por seu caso:

| Propriedade | Descrição | Opções | Necessário |
|:--- |:--- |:--- |:--- |
| Método de Encriptação (EM)| O método que o condutor utiliza para encriptar os dados enviados entre o controlador e o servidor de base de dados. Por exemplo  `EncryptionMethod=<0/1/6>;`| 0 (Sem encriptação) **(Padrão)** / 1 (SSL) / 6 (RequestSSL) | Não |
| ValidaçãoServerCertificato (VSC) | Determina se o controlador valida o certificado enviado pelo servidor de base de dados quando a encriptação SSL está ativada (Método de Encriptação=1). Por exemplo  `ValidateServerCertificate=<0/1>;`| 0 (Desativado) **(Predefinição)** / 1 (Habilitado) | Não |

**Exemplo:**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Exemplo:**

***Guarde a palavra-passe no Cofre da Chave Azure***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [datasets na Azure Data Factory](concepts-datasets-linked-services.md). Esta secção fornece uma lista de propriedades que a Base de Dados Azure para PostgreSQL suporta em conjuntos de dados.

Para copiar os dados da Base de Dados Azure para PostgreSQL, defina a propriedade tipo do conjunto de dados para **AzurePostgreSqlTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para **AzurePostgreSqlTable** | Sim |
| tableName | Nome da mesa | Não (se for especificada "consulta" na fonte de atividade) |

**Exemplo:**

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades, consulte Pipelines e atividades na Azure Data Factory.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por uma Base de Dados Azure para fonte PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Base de Dados Azure para PostgreSql como fonte

Para copiar os dados da Base de Dados Azure para PostgreSQL, deslote o tipo de origem na atividade da cópia para **AzurePostgreSqlSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para **AzurePostgreSqlSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `SELECT * FROM mytable` ou `SELECT * FROM "MyTable"` . . Nota em PostgreSQL, o nome da entidade é tratado como caso-insensível se não for citado. | Não (se a propriedade tableName no conjunto de dados for especificada) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Base de Dados Azure para PostgreSQL como pia

Para copiar dados para a Base de Dados Azure para PostgreSQL, as seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do lavatório de atividade de cópia deve ser definida para **AzurePostgreSQLSink**. | Sim |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia a executar antes de escrever dados na Base de Dados Azure para PostgreSQL em cada execução. Pode utilizar esta propriedade para limpar os dados pré-carregados. | Não |
| escreverMethod | O método usado para escrever dados na Base de Dados Azure para PostgreSQL.<br>Os valores permitidos são: **CopyCommand** (pré-visualização, que é mais performante), **BulkInsert** (predefinição). | Não |
| escreverBatchSize | O número de linhas carregadas na Base de Dados Azure para PostgreSQL por lote.<br>Valor permitido é um número inteiro que representa o número de linhas. | Não (o padrão é 1.000.000) |
| escreverBatchTimeout | Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo.<br>Os valores permitidos são as cordas timespan. Um exemplo é 00:30:00 (30 minutos). | Não (padrão é 00:30:00) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever para tabelas a partir da Base de Dados Azure para PostgreSQL. Para obter mais informações, consulte a [transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) nos fluxos de dados de mapeamento. Pode optar por utilizar uma Base de Dados Azure para conjunto de dados PostgreSQL ou um [conjunto de dados inline](data-flow-source.md#inline-datasets) como fonte e tipo de pia.

### <a name="source-transformation"></a>Transformação de origem

A tabela abaixo lista as propriedades suportadas pela Base de Dados Azure para fonte PostgreSQL. Pode editar estas propriedades no separador **Opções Fonte.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabela | Se selecionar tabela como entrada, o fluxo de dados recolhe todos os dados da tabela especificada no conjunto de dados. | Não | - |*(apenas para conjunto de dados em linha)*<br>tableName |
| Consulta | Se selecionar a Consulta como entrada, especifique uma consulta SQL para obter dados da fonte, que substitui qualquer tabela que especifique no conjunto de dados. Usar consultas é uma ótima maneira de reduzir linhas para testes ou análises.<br><br>**A** cláusula Por cláusula não é suportada, mas pode definir uma declaração completa SELECT FROM. Também pode utilizar funções de tabela definidas pelo utilizador. **selecionar * do udfGetData()** é um UDF em SQL que devolve uma tabela que pode usar no fluxo de dados.<br>Exemplo de consulta: `select * from mytable where customerId > 1000 and customerId < 2000` ou `select * from "MyTable"` . . Nota em PostgreSQL, o nome da entidade é tratado como caso-insensível se não for citado.| Não | String | consulta |
| Tamanho do lote | Especifique o tamanho do lote para colar dados grandes em lotes. | Não | Número inteiro | batchSize |
| Nível de Isolamento | Escolha um dos seguintes níveis de isolamento:<br>- Ler Comprometido<br>- Ler Não Comprometido (padrão)<br>- Leitura Repetível<br>- Serializável<br>- Nenhum (ignorar o nível de isolamento) | Não | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZÁVEL<br/>NENHUMA</small> |isolamentoLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Azure Database para postgreSQL exemplo de script de origem

Quando utiliza a Base de Dados Azure para PostgreSQL como tipo de origem, o script de fluxo de dados associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Transformação do sumidouro

A tabela abaixo lista as propriedades suportadas pela Base de Dados Azure para o lavatório PostgreSQL. Pode editar estas propriedades no **separador Opções De Sumidouro.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Método de atualização | Especifique quais as operações permitidas no seu destino de base de dados. O padrão é apenas permitir inserções.<br>Para atualizar, intensificar ou apagar linhas, é necessária uma [transformação de linha Alter](data-flow-alter-row.md) para marcar linhas para essas ações. | Sim | `true` ou `false` | deletable <br/>inserível <br/>atualizável <br/>upsertable |
| Colunas-chave | Para atualizações, atualizações e eliminações, as colunas-chave devem ser definidas para determinar qual a linha a alterar.<br>O nome da coluna que escolher como chave será utilizado como parte da atualização subsequente, atualizar, eliminar. Portanto, deve escolher uma coluna que exista no mapeamento da Pia. | Não | Matriz | keys |
| Salte a escrita de colunas-chave | Se não pretender não escrever o valor na coluna-chave, selecione "Salte as colunas-chave de escrita". | Não | `true` ou `false` | skipKeyWrites |
| Ação de mesa |Determina se deve recriar ou remover todas as linhas da tabela de destino antes de escrever.<br>- **Nenhuma:** nenhuma ação será feita à mesa.<br>- **Recriar:** A mesa será largada e recriada. Necessário se criar uma nova tabela dinamicamente.<br>- **Truncato**: Todas as linhas da mesa-alvo serão removidas. | Não | `true` ou `false` | recriar<br/>truncato |
| Tamanho do lote | Especifique quantas linhas estão a ser escritas em cada lote. Tamanhos maiores do lote melhoram a compressão e a otimização da memória, mas arriscam-se a sair das exceções de memória ao caching dados. | Não | Número inteiro | batchSize |
| Scripts pré e post SQL | Especifique scripts SQL de várias linhas que serão executados antes (pré-processamento) e depois (pós-processamento) os dados são escritos na sua base de dados de Sink. | Não | String | pré-QLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Azure Database para postgreSQL exemplo de script de pia

Quando utiliza a Base de Dados Azure para postgreSQL como tipo de pia, o script de fluxo de dados associado é:

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
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter mais informações sobre as propriedades, consulte [a atividade da Lookup na Azure Data Factory.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte [lojas de dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
