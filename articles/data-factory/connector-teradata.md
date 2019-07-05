---
title: Copiar dados do Teradata com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre Teradata conector do serviço Data Factory que lhe permite copiar dados de base de dados Teradata armazenamentos de dados suportado pelo Data Factory como sinks.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561435"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Copiar dados do Teradata com o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versão atual](connector-teradata.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados Teradata. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de base de dados Teradata para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de Teradata suporta:

- Teradata **versão 14.10, 15.0, 15.10, 16.0, 16.10 e 16.20**.
- Copiar dados utilizando **básica** ou **Windows** autenticação.
- Cópia paralela da origem de Teradata. Ver [paralela cópia a partir de Teradata](#parallel-copy-from-teradata) secção com detalhes.

> [!NOTE]
>
> O Azure Data Factory atualizado o conector de Teradata desde v3.18 de Runtime de integração autoalojado, que é dado o poder por um controlador ODBC incorporado e oferece opções de conexão flexível, bem como cópia paralela de out-of-box para aumentar o desempenho. Qualquer carga de trabalho existente com o conector de Teradata anterior capacitados pelo fornecedor de dados do .NET para Teradata ainda é suportado como-é, ao passo que são sugeridas para utilizar o novo daqui em diante. Tenha em atenção de que o novo caminho requer um conjunto diferente de origem de serviço/conjunto de dados/copiar vinculado. Consulte a secção de respectiva detalhes de configuração.

## <a name="prerequisites"></a>Pré-requisitos

Se sua Teradata não está acessível ao público, terá de configurar um Runtime de integração autoalojado. Para obter mais informações sobre o runtime de integração, consulte [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md). O Runtime de integração fornece um driver de Teradata incorporado a partir da versão 3.18, portanto não precisa de instalar manualmente a qualquer driver. O controlador necessitar de "Visual C++ atualização 4 2012 Redistributable" da máquina do Runtime de integração autoalojado, transferi a partir [aqui](https://www.microsoft.com/en-sg/download/details.aspx?id=30679) se ainda não tiver instalado.

Para obter a versão de Runtime de integração autoalojado inferior 3.18, tem de instalar o [fornecedor de dados do .NET para Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) versão 14 ou superior no computador Runtime de integração. 

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de Teradata.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de Teradata ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **Teradata** | Sim |
| connectionString | Especifica as informações necessárias para ligar à instância de base de dados Teradata. Consulte os exemplos seguintes.<br/>Também pode colocar a palavra-passe no Azure Key Vault e obter o `password` configuração fora de cadeia de ligação. Consulte a [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. | Sim |
| username | Especifique o nome de utilizador para ligar à base de dados Teradata. Aplica-se de que ao utilizar a autenticação do Windows. | Não |
| password | Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. Também pode optar por [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). <br>Aplica-se ao utilizar a autenticação do Windows, ou uma referência a palavra-passe no Cofre de chaves para a autenticação básica. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Runtime de integração autoalojado é necessário, conforme mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo: usando a autenticação básica**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: utilizar a autenticação do Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Se estivesse usando Teradata capacitado do serviço ligado pelo fornecedor de dados do .NET para Teradata com a carga seguinte, ainda é suportado como-é, ao passo que são sugeridas para utilizar o novo daqui em diante.

**Payload anterior:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados Teradata.

Para copiar dados da Teradata, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **TeradataTable** | Sim |
| database | Nome da base de dados Teradata. | Não (se for especificada "consulta" na origem de atividade) |
| table | Nome da tabela na base de dados Teradata. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> Se estivesse usando o conjunto de dados de tipo de "RelationalTable" como o seguinte, ainda é suportado como-é, ao passo que são sugeridas para utilizar o novo daqui em diante.

**Payload anterior:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem de Teradata.

### <a name="teradata-as-source"></a>Teradata como origem

> [!TIP]
>
> Saiba mais a partir da [paralela cópia a partir de Teradata](#parallel-copy-from-teradata) secção sobre como carregar dados do Teradata eficiente, utilizando a criação de partições de dados.

Para copiar dados da Teradata, as seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **TeradataSource** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`.<br>Quando ativa a carga particionada, tem de vincular correspondente de partição incorporada de parâmetro (s) na sua consulta. Veja exemplos [paralela cópia a partir de Teradata](#parallel-copy-from-teradata) secção. | Não (se for especificada tabela no conjunto de dados) |
| partitionOptions | Especifica os dados de criação de partições as opções usadas para carregar dados da Teradata. <br>Permitir que os valores são: **NONE** (predefinição), **Hash** e **DynamicRange**.<br>Quando a opção de partição está ativada (não ' None'), também configure **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** definir na atividade de cópia, por exemplo, como 4, que determina o grau de paralelo para carregar ao mesmo tempo os dados da Teradata base de dados. | Não |
| partitionSettings | Especifique o grupo de definições para a criação de partições de dados. <br>Aplicam-se quando a opção de partição não está `None`. | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo de número inteiro** que será utilizada pelo particionamento por intervalos de cópia em paralela. Se não for especificado, a chave primária da tabela será automaticamente detetado e utilizado como coluna de partição. <br>Aplicar se a opção de partição `Hash` ou `DynamicRange`. Se utilizar a consulta para recuperar dados de origem, conectar `?AdfHashPartitionCondition` ou `?AdfRangePartitionColumnName` na cláusula WHERE. Veja o exemplo na [paralela cópia a partir de Teradata](#parallel-copy-from-teradata) secção. | Não |
| partitionUpperBound | Valor máximo da coluna de partição para copiar dados. <br>Aplicam-se quando a opção de partição é `DynamicRange`. Se utilizar a consulta para recuperar dados de origem, conectar `?AdfRangePartitionUpbound` na cláusula WHERE. Veja o exemplo na [paralela cópia a partir de Teradata](#parallel-copy-from-teradata) secção. | Não |
| PartitionLowerBound | Valor mínimo da coluna de partição para copiar dados. <br>Aplicam-se quando a opção de partição é `DynamicRange`. Se utilizar a consulta para recuperar dados de origem, conectar `?AdfRangePartitionLowbound` na cláusula WHERE. Veja o exemplo na [paralela cópia a partir de Teradata](#parallel-copy-from-teradata) secção. | Não |

> [!NOTE]
>
> Se estivesse usando a origem de cópia do tipo "RelationalSource", ainda é suportado como-é, mas não suporta o novo carregue em paralelo incorporado do Teradata (opções de partição). São sugeridas para usar essa nova associação daqui em diante.

**Exemplo: copiar dados através da consulta básica sem partição**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Copiar paralela de Teradata

Conector de Teradata de fábrica de dados fornece dados internos, criação de partições para copiar dados do Teradata em paralelo com um desempenho excelente. Pode encontrar a origem de Teradata de -> Opções de criação de partições de dados na atividade de cópia:

![Opções de partição](./media/connector-teradata/connector-teradata-partition-options.png)

Quando ativa a cópia particionada, fábrica de dados executa consultas paralelas em relação a sua origem de Teradata carregar dados, as partições. O grau de paralelo é configurado e controlado através da **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** definição numa atividade de cópia. Por exemplo, se definir `parallelCopies` como quatro, fábrica de dados em simultâneo gera e executa quatro consultas com base na sua opção de partição especificado e definições, cada parte ao obter dados da sua base de dados Teradata.

São sugeridas para ativar a cópia paralela com dados de criação de partições especialmente quando carrega grande quantidade de dados da base de dados Teradata. Seguem-se as configurações sugeridas para diferentes cenários:

| Cenário                                                     | Definições de sugerida                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carregamento completo da tabela grande                                   | **Opção de partição**: Hash. <br><br/>Durante a execução, o data Factory automaticamente detetar coluna de PK, aplica hash em relação a ele e copiar os dados por partições. |
| Carregar a grande quantidade de dados através de consultas personalizado                 | **Opção de partição**: Hash.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna utilizada para a partição de hash de aplicar. Se não for especificado, o ADF detectará automaticamente a coluna de PK da tabela especificada no conjunto de dados Teradata.<br><br>Durante a execução, substituição de fábrica de dados `?AdfHashPartitionCondition` com a lógica de partição de hash e enviá-las para Teradata. |
| Carregar a grande quantidade de dados usando uma consulta personalizada, ter uma coluna de número inteiro com o valor uniformemente distribuída para a criação de partições de intervalo | **Opções de partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna utilizada para criar partições de dados. Pode particionar em relação a coluna com o tipo de dados de número inteiro.<br>**Limite superior de partição** e **limite inferior de partição**: Especifique se pretende filtrar em relação a coluna de partição para obter apenas os dados entre o intervalo mínimo e máximo.<br><br>Durante a execução, substituição de fábrica de dados `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` com o nome da coluna real e o valor intervalos para cada partição e enviar para Teradata. <br>Por exemplo, se a coluna "ID de partição" definido com o limite inferior como 1 e o limite superior, como 80, com o conjunto de cópia paralela como 4, o ADF recuperar dados através de 4 partições com o ID entre [1,20], [21, 40], [41, 60] e [61, 80]. |

**Exemplo: consulta com a partição de hash**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Exemplo: consulta com a partição de intervalo dinâmico**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Tipo de dados de mapeamento para Teradata

Ao copiar dados da Teradata, os seguintes mapeamentos são utilizados entre tipos de dados Teradata aos tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados Teradata | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Integer |Int32 |
| Interval Day |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Day To Hour |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Day To Minute |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Day To Second |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Hour |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Hour To Minute |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Hour To Second |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Minute |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Minute To Second |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Month |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Second |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Year |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Interval Year To Month |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Number |Double |
| Período (data) |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Período (hora) |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Período (hora com fuso horário) |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Período (Timestamp) |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Período (Timestamp com o fuso horário) |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |
| Xml |Não suportado. Aplicam-se a conversão explícita na consulta de origem. |


## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
