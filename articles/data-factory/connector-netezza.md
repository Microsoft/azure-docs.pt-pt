---
title: Copiar dados do Netezza com o Azure Data Factory
description: Saiba como copiar dados de Netezza para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c51469997af23be7a5e1b88677ecadb37e10ac64
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358430"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiar dados do Netezza com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Netezza. O artigo baseia-se [na Copy Activity na Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

>[!TIP]
>Para o cenário de migração de dados de Netezza para Azure, saiba mais com [a Use Azure Data Factory para migrar dados do servidor Netezza para o Azure.](data-migration-guidance-netezza-azure-sqldw.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Netezza é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)


Pode copiar dados de Netezza para qualquer arquivo de dados de sink suportados. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

O conector Netezza suporta cópias paralelas a partir da fonte. Consulte a cópia paralela da secção [Netezza](#parallel-copy-from-netezza) para obter mais detalhes.

O Azure Data Factory fornece um driver incorporado para permitir a conectividade. Não precisa de instalar manualmente a qualquer driver para utilizar este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

Pode criar um pipeline que utiliza uma atividade de cópia com o SDK de .NET, o SDK de Python, Azure PowerShell, a API REST ou um modelo Azure Resource Manager. Consulte o [tutorial copy activity](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que pode usar para definir entidades do Data Factory que são específicas para o conector de Netezza.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de Netezza ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **Netezza**. | Sim |
| connectionString | Uma cadeia de ligação de ODBC para estabelecer ligação à Netezza. <br/>Também pode colocar palavra-passe no Cofre de Chaves Azure e retirar a configuração `pwd` da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo do Cofre chave Azure com mais detalhes. | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) para utilizar para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, é utilizada a predefinição de Runtime de integração do Azure. |Não |

Uma corda de ligação típica é `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. A tabela seguinte descreve mais propriedades que pode definir:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| SecurityLevel | O nível de segurança (SSL/TLS), que o driver usa para a ligação ao arquivo de dados. Exemplo: `SecurityLevel=preferredSecured`. Os valores suportados são:<br/>- **Only unsecured** (**apenas UnSecured**): O condutor não utiliza SSL.<br/>- **Preferred unsecured (preferredUnSecured) (predefinição)** : Se o servidor fornecer uma escolha, o controlador não utiliza SSL. <br/>- **Preferred secured (preferredSecured)** : Se o servidor fornecer uma escolha, o controlador utiliza SSL. <br/>- **Apenas seguro (apenas protegido)** : O controlador não se liga a menos que esteja disponível uma ligação SSL. | Não |
| CaCertFile | O caminho completo para o certificado SSL que é utilizado pelo servidor. Exemplo: `CaCertFile=<cert path>;`| Sim, se o SSL esteja ativado |

**Exemplo**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: palavra-passe de loja no Cofre de Chaves Azure**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets](concepts-datasets-linked-services.md).

Para copiar dados do Netezza, detete a propriedade **do tipo** do conjunto de dados para **NetezzaTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **NetezzaTable** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| table | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Use `schema` e `table` para uma nova carga de trabalho. | Não (se for especificada "query" na origem de atividade) |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza como origem

>[!TIP]
>Para carregar os dados da Netezza de forma eficiente utilizando a partilha de dados, saiba mais com a cópia paralela da secção [Netezza.](#parallel-copy-from-netezza)

Para copiar dados do Netezza, delineie o tipo de **origem** na Atividade de Cópia para **o NetezzaSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da Atividade de Cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** da fonte de atividade de cópia deve ser definida para **NetezzaSource**. | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Exemplo: `"SELECT * FROM MyTable"` | Não (se for especificado "tableName" no conjunto de dados) |
| partitionOptions | Especifica as opções de partilha de dados utilizadas para carregar dados da Netezza. <br>Os valores de adesão são: **Nenhum** (padrão), **DataSlice,** e **DynamicRange**.<br>Quando uma opção de partição é ativada (isto é, não `None`), o grau de paralelismo para carregar simultaneamente dados de uma base de dados Netezza é controlado por [`parallelCopies`](copy-activity-performance.md#parallel-copy) definição da atividade da cópia. | Não |
| partiçãoDefinições | Especifique o grupo das definições para a partilha de dados. <br>Aplicar quando a opção de partição não é `None`. | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo inteiro** que será utilizado por divisórias de alcance para cópia paralela. Se não especificada, a chave principal da tabela é detetada automaticamente e utilizada como coluna de partição. <br>Aplicar quando a opção de partição for `DynamicRange`. Se utilizar uma consulta para recuperar os dados de origem, prenda `?AdfRangePartitionColumnName` na cláusula WHERE. Consulte o exemplo na cópia paralela da secção [Netezza.](#parallel-copy-from-netezza) | Não |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange`. Se utilizar a consulta para recuperar dados de origem, prenda `?AdfRangePartitionUpbound` na cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Netezza.](#parallel-copy-from-netezza) | Não |
| partitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange`. Se utilizar uma consulta para recuperar os dados de origem, prenda `?AdfRangePartitionLowbound` na cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Netezza.](#parallel-copy-from-netezza) | Não |

**Exemplo:**

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

## <a name="parallel-copy-from-netezza"></a>Cópia paralela de Netezza

O conector Netezza data Factory fornece partição de dados incorporados para copiar dados da Netezza em paralelo. Pode encontrar opções de partilha de dados na tabela **Fonte** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-netezza/connector-netezza-partition-options.png)

Quando ativa a cópia dividida, a Data Factory executa consultas paralelas contra a sua fonte Netezza para carregar dados por divisórias. O grau paralelo é controlado pela [definição`parallelCopies`](copy-activity-performance.md#parallel-copy) da atividade da cópia. Por exemplo, se definir `parallelCopies` a quatro, a Data Factory gera e executa simultaneamente quatro consultas com base na sua opção e definições especificadas de partição, e cada consulta recupera uma parte dos dados da sua base de dados Netezza.

É sugerido que permita cópias paralelas com partilha de dados especialmente quando carrega uma grande quantidade de dados da sua base de dados Netezza. As seguintes são configurações sugeridas para diferentes cenários. Ao copiar dados para uma loja de dados baseada em ficheiros, é re-ordenado para escrever para uma pasta como múltiplos ficheiros (apenas especificar o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de mesa grande.                                   | **Opção de partição**: Data Slice. <br><br/>Durante a execução, a Data Factory partilha automaticamente os dados com base nas [fatias de dados incorporadas da Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)e copia dados por divisórias. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada.                 | **Opção de partição**: Data Slice.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Durante a execução, a Data Factory substitui `?AdfPartitionCount` (com número de cópia paralelo definido na atividade de cópia) e `?AdfDataSliceCondition` com a lógica da partilha de fatias de dados, e envia para netezza. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, tendo uma coluna inteiro com valor uniformemente distribuído para a partilha de alcance. | **Opções de partição**: Partição dinâmica.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna utilizada para os dados da partilha. Pode dividir-se contra a coluna com o tipo de dados inteiros.<br>**Divisória superior ligada** e **divisória inferior:** Especifique se pretende filtrar contra a coluna de divisórias para recuperar dados apenas entre a gama inferior e superior.<br><br>Durante a execução, a Data Factory substitui `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` com o nome e os intervalos de valor reais para cada partição, e envia para netezza. <br>Por exemplo, se a sua coluna de partição "ID" definida com o limite inferior como 1 e o limite superior como 80, com cópia paralela definida como 4, data Factory recupera dados por 4 divisórias. As suas identificações estão entre [1,20], [21, 40], [41, 60], e [61, 80], respectivamente. |

**Exemplo: consulta com partição de fatias de dados**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Exemplo: consulta com partição dinâmica de gama**

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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
