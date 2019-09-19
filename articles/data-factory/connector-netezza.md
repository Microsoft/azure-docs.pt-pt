---
title: Copiar dados do Netezza com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de Netezza para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 5d0c09c9cff2fefcc2eee20b9fd2f93dd375115f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090010"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiar dados do Netezza com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Netezza. O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para o cenário de migração de dados do Netezza para o Azure, saiba mais em [usar Azure data Factory para migrar dados do servidor Netezza local para o Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector do Netezza tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)


Pode copiar dados de Netezza para qualquer arquivo de dados de sink suportados. Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

O conector do Netezza dá suporte à cópia paralela da origem. Consulte a seção [cópia paralela de Netezza](#parallel-copy-from-netezza) para obter detalhes.

O Azure Data Factory fornece um driver incorporado para permitir a conectividade. Não precisa de instalar manualmente a qualquer driver para utilizar este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

Pode criar um pipeline que utiliza uma atividade de cópia com o SDK de .NET, o SDK de Python, Azure PowerShell, a API REST ou um modelo Azure Resource Manager. Consulte a [tutorial da atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre como criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que pode usar para definir entidades do Data Factory que são específicas para o conector de Netezza.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de Netezza ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade tem de ser definida como **Netezza**. | Sim |
| connectionString | Uma cadeia de ligação de ODBC para estabelecer ligação à Netezza. <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar a senha em Azure Key Vault e efetuar pull da `pwd` configuração da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, é utilizada a predefinição de Runtime de integração do Azure. |Não |

É uma cadeia de ligação típica `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. A tabela seguinte descreve mais propriedades que pode definir:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| SecurityLevel | O nível de segurança (SSL/TLS), que o driver usa para a ligação ao arquivo de dados. Exemplo: `SecurityLevel=preferredSecured`. Os valores suportados são:<br/>- **Somente não seguro** (**onlyUnSecured**): O driver não usa SSL.<br/>- **Preferencial não seguro (preferredUnSecured) (padrão)** : Se o servidor fornecer uma opção, o driver não usará SSL. <br/>- **Seguro preferencial (preferredSecured)** : Se o servidor fornecer uma opção, o driver usará SSL. <br/>- **Somente protegido (onlySecured)** : O driver não se conecta, a menos que uma conexão SSL esteja disponível. | Não |
| CaCertFile | O caminho completo para o certificado SSL que é utilizado pelo servidor. Exemplo: `CaCertFile=<cert path>;`| Sim, se o SSL esteja ativado |

**Exemplo**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a senha no Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Esta seção fornece uma lista de propriedades que suporta o conjunto de dados Netezza.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados](concepts-datasets-linked-services.md).

Para copiar dados de Netezza, defina o **tipo** propriedade do conjunto de dados para **NetezzaTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **NetezzaTable** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| table | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da tabela com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Use `schema` e`table` para uma nova carga de trabalho. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista de propriedades que suporta a origem de Netezza.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza como origem

>[!TIP]
>Para carregar dados do Netezza com eficiência usando o particionamento de dados, saiba mais na seção [cópia paralela da Netezza](#parallel-copy-from-netezza) .

Para copiar dados de Netezza, defina o **origem** tipo de atividade de cópia para **NetezzaSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **NetezzaSource**. | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Exemplo: `"SELECT * FROM MyTable"` | Não (se for especificado "tableName" no conjunto de dados) |
| partitionOptions | Especifica as opções de particionamento de dados usadas para carregar dados do Netezza. <br>Os valores permitidos são: **Nenhum** (padrão), **dataslice**e **DynamicRange**.<br>Quando uma opção de partição é habilitada (ou seja `None`, não), o grau de paralelismo para carregar dados simultaneamente de um banco de Netezza é controlado pela [`parallelCopies`](copy-activity-performance.md#parallel-copy) configuração na atividade de cópia. | Não |
| partitionSettings | Especifique o grupo de configurações para o particionamento de dados. <br>Aplicar quando a opção de `None`partição não for. | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo inteiro** que será usado pelo particionamento de intervalo para cópia paralela. Se não for especificado, a chave primária da tabela será detectada automaticamente e usada como a coluna de partição. <br>Aplicar quando a opção de partição `DynamicRange`for. Se você usar uma consulta para recuperar os dados de origem, `?AdfRangePartitionColumnName` Conecte-se à cláusula WHERE. Consulte o exemplo em [cópia paralela da seção Netezza](#parallel-copy-from-netezza) . | Não |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de `DynamicRange`partição for. Se você usar a consulta para recuperar dados de origem `?AdfRangePartitionUpbound` , conecte a cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela de Netezza](#parallel-copy-from-netezza) . | Não |
| partitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição `DynamicRange`for. Se você usar uma consulta para recuperar os dados de origem, `?AdfRangePartitionLowbound` Conecte-se à cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela de Netezza](#parallel-copy-from-netezza) . | Não |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Cópia paralela do Netezza

O conector do Netezza Data Factory fornece particionamento de dados interno para copiar dados do Netezza em paralelo. Você pode encontrar opções de particionamento de dados na tabela de **origem** da atividade de cópia.

![Captura de tela das opções de partição](./media/connector-netezza/connector-netezza-partition-options.png)

Quando você habilita a cópia particionada, o Data Factory executa consultas paralelas em sua fonte Netezza para carregar dados por partições. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance.md#parallel-copy) configuração na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, data Factory gera e executa quatro consultas de maneira simultânea com base em sua opção de partição especificada e nas configurações, e cada consulta recupera uma parte dos dados do Netezza.

É recomendável habilitar a cópia paralela com o particionamento de dados, especialmente quando você carrega grandes quantidades de dados de seu banco de Netezza. Veja a seguir as configurações sugeridas para cenários diferentes. Ao copiar dados para o armazenamento de dados baseado em arquivo, ele é redirecionado para gravar em uma pasta como vários arquivos (apenas especifique o nome da pasta). nesse caso, o desempenho é melhor do que gravar em um único arquivo.

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de uma tabela grande.                                   | **Opção de partição**: Fatia de dados. <br><br/>Durante a execução, Data Factory particiona automaticamente os dados com base nas [fatias de dados internas do Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)e copia os dados por partições. |
| Carregar uma grande quantidade de dados usando uma consulta personalizada.                 | **Opção de partição**: Fatia de dados.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Durante a execução, data Factory `?AdfPartitionCount` substitui (com o número de cópia paralela definido na atividade `?AdfDataSliceCondition` de cópia) e pela lógica de partição da fatia de dados e envia ao Netezza. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, tendo uma coluna de inteiros com valor distribuído uniformemente para o particionamento de intervalo. | **Opções de partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna usada para particionar dados. Você pode particionar em relação à coluna com tipo de dados Integer.<br>Limite **superior** da partição e limite **inferior**da partição: Especifique se deseja filtrar a coluna de partição para recuperar dados somente entre o intervalo inferior e superior.<br><br>Durante a execução, data Factory `?AdfRangePartitionColumnName`substitui `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` pelo nome real da coluna e os intervalos de valores para cada partição e envia para Netezza. <br>Por exemplo, se a sua coluna de partição "ID" estiver definida com o limite inferior como 1 e o limite superior como 80, com conjunto de cópia paralela como 4, Data Factory recuperará dados por 4 partições. Suas IDs estão entre [1, 20], [21, 40], [41, 60] e [61, 80], respectivamente. |

**Exemplo: consultar com a partição de fatia de dados**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Exemplo: consulta com partição de intervalo dinâmico**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).
