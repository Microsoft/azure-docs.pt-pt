---
title: Copiar dados da Netezza utilizando a Azure Data Factory
description: Saiba como copiar dados da Netezza para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
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
ms.openlocfilehash: 89efa8dc9989f693964415741299042c63f93780
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418121"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiar dados da Netezza utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Netezza. O artigo baseia-se [na Copy Activity na Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

>[!TIP]
>Para o cenário de migração de dados de Netezza para Azure, saiba mais com [a Use Azure Data Factory para migrar dados do servidor Netezza para o Azure.](data-migration-guidance-netezza-azure-sqldw.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Netezza é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)


Pode copiar dados da Netezza para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

O conector Netezza suporta cópias paralelas a partir da fonte. Consulte a cópia paralela da secção [Netezza](#parallel-copy-from-netezza) para obter mais detalhes.

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade. Não é necessário instalar manualmente qualquer controlador para utilizar este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

Pode criar um pipeline que utilize uma atividade de cópia utilizando o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou um modelo de Gestor de Recursos Azure. Consulte o [tutorial copy activity](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia.

As seguintes secções fornecem detalhes sobre propriedades que pode utilizar para definir entidades da Fábrica de Dados específicas do conector Netezza.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Netezza:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **Netezza**. | Sim |
| conexãoString | Uma cadeia de ligação ODBC para ligar a Netezza. <br/>Também pode colocar palavra-passe no Cofre `pwd` de Chaves Azure e retirar a configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo do Cofre chave Azure com mais detalhes. | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) para utilizar para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, é utilizado o tempo de execução de integração azure padrão. |Não |

Uma corda de `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`ligação típica é . A tabela a seguir descreve mais propriedades que pode definir:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| Nível de Segurança | O nível de segurança (SSL/TLS) que o condutor utiliza para a ligação ao armazém de dados. Exemplo: `SecurityLevel=preferredSecured`. Os valores suportados são:<br/>- **Apenas não seguro** (**apenas UnSecured**): O condutor não utiliza TLS.<br/>- **Preferencialmente não seguro (preferencialMente UnSecured) (predefinição)**: Se o servidor fornecer uma escolha, o controlador não utiliza TLS. <br/>- **Preferência segura (preferredSecured)**: Se o servidor fornecer uma escolha, o controlador utiliza TLS. <br/>- **Apenas seguro (apenas Protegido)**: O controlador não se liga a menos que esteja disponível uma ligação TLS. | Não |
| CaCertFile | O caminho completo para o certificado TLS/SSL que é usado pelo servidor. Exemplo: `CaCertFile=<cert path>;`| Sim, se o TLS estiver ativado |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Esta secção fornece uma lista de propriedades que o conjunto de dados Netezza suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets](concepts-datasets-linked-services.md).

Para copiar dados do Netezza, detete a propriedade **do tipo** do conjunto de dados para **NetezzaTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **NetezzaTable** | Sim |
| schema | Nome do esquema. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tabela | Nome da mesa. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Uso `schema` `table` e para nova carga de trabalho. | Não (se for especificada a "consulta" na fonte de atividade) |

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

## <a name="copy-activity-properties"></a>Propriedades de Copy Activity

Esta secção fornece uma lista de propriedades que a fonte Netezza suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza como fonte

>[!TIP]
>Para carregar os dados da Netezza de forma eficiente utilizando a partilha de dados, saiba mais com a cópia paralela da secção [Netezza.](#parallel-copy-from-netezza)

Para copiar dados do Netezza, delineie o tipo de **origem** na Atividade de Cópia para **o NetezzaSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da Atividade de Cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** da fonte de atividade de cópia deve ser definida para **NetezzaSource**. | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Exemplo: `"SELECT * FROM MyTable"` | Não (se for especificado "tableName" no conjunto de dados) |
| partilhaOpOp | Especifica as opções de partilha de dados utilizadas para carregar dados da Netezza. <br>Os valores de adesão são: **Nenhum** (padrão), **DataSlice,** e **DynamicRange**.<br>Quando uma opção de partição `None`é ativada (isto é, não), o grau de paralelismo para carregar simultaneamente dados de uma base de dados netezza é controlado por [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) regulação da atividade da cópia. | Não |
| partiçãoDefinições | Especifique o grupo das definições para a partilha de dados. <br>Aplicar quando a opção de partição não `None`é . | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo inteiro** que será utilizado por divisórias de alcance para cópia paralela. Se não especificada, a chave principal da tabela é detetada automaticamente e utilizada como coluna de partição. <br>Aplicar quando a `DynamicRange`opção de partição for . Se utilizar uma consulta para recuperar os `?AdfRangePartitionColumnName` dados de origem, ligue-se à cláusula ONDE. Consulte o exemplo na cópia paralela da secção [Netezza.](#parallel-copy-from-netezza) | Não |
| partiçãoUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição é . Se utilizar a consulta para recuperar `?AdfRangePartitionUpbound` dados de origem, ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Netezza.](#parallel-copy-from-netezza) | Não |
| partiçãoLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição for . Se utilizar uma consulta para recuperar os `?AdfRangePartitionLowbound` dados de origem, ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Netezza.](#parallel-copy-from-netezza) | Não |

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

Quando ativa a cópia dividida, a Data Factory executa consultas paralelas contra a sua fonte Netezza para carregar dados por divisórias. O grau paralelo é [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) controlado pela regulação da atividade da cópia. Por exemplo, se `parallelCopies` definir para quatro, data Factory gera simultaneamente e executa quatro consultas com base na sua opção e configurações especificadas de partição, e cada consulta recupera uma parte dos dados da sua base de dados Netezza.

É sugerido que permita cópias paralelas com partilha de dados especialmente quando carrega uma grande quantidade de dados da sua base de dados Netezza. As seguintes são configurações sugeridas para diferentes cenários. Ao copiar dados para uma loja de dados baseada em ficheiros, é re-ordenado para escrever para uma pasta como múltiplos ficheiros (apenas especificar o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

| Cenário                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de mesa grande.                                   | **Opção de partição**: Data Slice. <br><br/>Durante a execução, a Data Factory partilha automaticamente os dados com base nas [fatias de dados incorporadas da Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)e copia dados por divisórias. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada.                 | **Opção de partição**: Data Slice.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Durante a execução, `?AdfPartitionCount` a Data Factory substitui (com `?AdfDataSliceCondition` número de cópia paralela definido na atividade de cópia) e com a lógica da partilha de fatias de dados, e envia para netezza. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, tendo uma coluna inteiro com valor uniformemente distribuído para a partilha de alcance. | **Opções de partição**: Partição dinâmica.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna utilizada para os dados da partilha. Pode dividir-se contra a coluna com o tipo de dados inteiros.<br>**Divisória superior ligada** e **divisória inferior:** Especifique se pretende filtrar contra a coluna de divisórias para recuperar dados apenas entre a gama inferior e superior.<br><br>Durante a execução, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` Data Factory substitui, e com o nome real da coluna e gamas de valor para cada partição, e envia para Netezza. <br>Por exemplo, se a sua coluna de partição "ID" definida com o limite inferior como 1 e o limite superior como 80, com cópia paralela definida como 4, data Factory recupera dados por 4 divisórias. As suas identificações estão entre [1,20], [21, 40], [41, 60], e [61, 80], respectivamente. |

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
