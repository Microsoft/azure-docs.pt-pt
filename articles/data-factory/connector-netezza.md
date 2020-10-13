---
title: Copiar dados da Netezza utilizando a Azure Data Factory
description: Saiba como copiar dados da Netezza para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jingwang
ms.openlocfilehash: 7f98fee687fca6a2b6e746b24ca582671e28391f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84216393"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiar dados da Netezza utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity na Azure Data Factory para copiar dados da Netezza. O artigo baseia-se [na Copy Activity in Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

>[!TIP]
>Para cenário de migração de dados de Netezza para Azure, saiba mais com [a Use Azure Data Factory para migrar dados do servidor Netezza para Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Netezza é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)


Pode copiar dados da Netezza para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

O conector Netezza suporta cópia paralela da fonte. Consulte a cópia paralela da secção [Netezza](#parallel-copy-from-netezza) para obter mais detalhes.

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade. Não é necessário instalar manualmente nenhum controlador para utilizar este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

Pode criar um pipeline que utilize uma atividade de cópia utilizando o .NET SDK, o Python SDK, a Azure PowerShell, o REST API ou um modelo Azure Resource Manager. Consulte o tutorial de [Atividade de Cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia.

As secções seguintes fornecem detalhes sobre propriedades que pode usar para definir entidades da Data Factory que são específicas do conector Netezza.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço netezza ligado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **Netezza**. | Sim |
| conexãoStragem | Uma cadeia de ligação ODBC para ligar ao Netezza. <br/>Também pode colocar a palavra-passe no Cofre da Chave Azure e retirar a `pwd` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no artigo do Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [Tempo de Execução de Integração](concepts-integration-runtime.md) para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza-se o tempo de execução de integração Azure predefinido. |Não |

Uma cadeia de ligação típica é `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` . A tabela a seguir descreve mais propriedades que pode definir:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| SegurançaLevel | O nível de segurança que o condutor utiliza para a ligação à loja de dados. O controlador suporta ligações SSL com autenticação unidirecionais utilizando a versão SSL 3. <br>Exemplo: `SecurityLevel=preferredSecured`. Os valores suportados são:<br/>- **Apenas não protegido** **(apenas Não**Seguro): O condutor não utiliza SSL.<br/>- **Preferencialmente não protegido (preferencialmente Não Seguro) (predefinição) :** Se o servidor fornecer uma escolha, o controlador não utiliza SSL. <br/>- **Garantia preferida (preferencialmente Assegurada)**: Se o servidor fornecer uma escolha, o controlador utiliza SSL. <br/>- **Apenas protegido (apenas Em segurança)**: O controlador não se liga a menos que esteja disponível uma ligação SSL. | Não |
| CaCertFile | O caminho completo para o certificado SSL que é usado pelo servidor. Exemplo: `CaCertFile=<cert path>;`| Sim, se o SSL estiver ativado |

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

**Exemplo: armazenar senha em Azure Key Vault**

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

Para copiar dados da Netezza, defina a propriedade **tipo** do conjunto de dados para **o NetezzaTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **NetezzaTable** | Sim |
| esquema | O nome do esquema. |Não (se for especificada "consulta" na fonte de atividade)  |
| table | O nome da mesa. |Não (se for especificada "consulta" na fonte de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Uso `schema` e para nova carga de `table` trabalho. | Não (se for especificada "consulta" na fonte de atividade) |

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

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Esta secção fornece uma lista de propriedades que a fonte Netezza suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza como fonte

>[!TIP]
>Para carregar os dados do Netezza de forma eficiente utilizando a partilha de dados, saiba mais a partir [da secção Paralelo da Netezza.](#parallel-copy-from-netezza)

Para copiar dados do Netezza, desagrafe o tipo **de origem** em Copy Activity para **NetezzaSource**. As seguintes propriedades são suportadas na secção **origem** da Atividade de Cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de Atividade de Cópia deve ser definida para **NetezzaSource**. | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Exemplo: `"SELECT * FROM MyTable"` | Não (se for especificado "tableName" no conjunto de dados) |
| partitionOptions | Especifica as opções de partição de dados utilizadas para carregar dados do Netezza. <br>Os valores de permitir são: **Nenhum** (padrão), **DataSlice**e **DynamicRange**.<br>Quando uma opção de partição é ativada (isto é, `None` não), o grau de paralelismo para carregar simultaneamente dados de uma base de dados Netezza é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) definição da atividade da cópia. | Não |
| divisóriasSas | Especificar o grupo das definições para a partilha de dados. <br>Aplicar quando a opção de partição não `None` for. | Não |
| partitionColumnName | Especificar o nome da coluna de origem **no tipo inteiro** que será utilizado por partição de gama para cópia paralela. Se não for especificada, a chave primária da tabela é autodetectada e utilizada como coluna de partição. <br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem, `?AdfRangePartitionColumnName` ligue-se à cláusula WHERE. Veja o exemplo na cópia paralela da secção [Netezza.](#parallel-copy-from-netezza) | Não |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar a consulta para obter dados de origem, `?AdfRangePartitionUpbound` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Netezza.](#parallel-copy-from-netezza) | Não |
| partitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem, `?AdfRangePartitionLowbound` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Netezza.](#parallel-copy-from-netezza) | Não |

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

## <a name="parallel-copy-from-netezza"></a>Cópia paralela da Netezza

O conector Netezza da Data Factory fornece partição de dados incorporada para copiar dados da Netezza em paralelo. Pode encontrar opções de partição de dados na tabela **Fonte** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-netezza/connector-netezza-partition-options.png)

Quando ativa a cópia dividida, a Data Factory executa consultas paralelas contra a sua fonte Netezza para carregar dados por divisórias. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) regulação da atividade da cópia. Por exemplo, se definir `parallelCopies` para quatro, data factory simultaneamente gera e executa quatro consultas com base na sua opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados da sua base de dados Netezza.

Sugere-se que ative uma cópia paralela com a partilha de dados, especialmente quando carrega uma grande quantidade de dados a partir da sua base de dados Netezza. São sugeridas configurações para diferentes cenários. Ao copiar dados para a loja de dados baseada em ficheiros, é recomcomcomerado para escrever para uma pasta como vários ficheiros (especificar apenas o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

| Cenário                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa da mesa grande.                                   | **Opção de partição**: Fatia de Dados. <br><br/>Durante a execução, a Data Factory partilha automaticamente os dados com base [nas fatias de dados incorporadas da Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)e copia dados por partições. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada.                 | **Opção de partição**: Fatia de Dados.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>` .<br>Durante a execução, a Data Factory substitui `?AdfPartitionCount` (por um número de cópia paralelo definido na atividade da cópia) e `?AdfDataSliceCondition` pela lógica de partição da fatia de dados, e envia para o Netezza. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, tendo uma coluna de inteiros com valor uniformemente distribuído para a divisão de gama. | **Opções de partição**: Partição dinâmica do alcance.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Coluna de partição**: Especificar a coluna utilizada para os dados de partição. Pode dividir-se contra a coluna com o tipo de dados inteiro.<br>**Limite superior** e **partição inferior**: Especifique se pretende filtrar contra a coluna de partição para obter dados apenas entre a gama inferior e superior.<br><br>Durante a execução, a Data Factory `?AdfRangePartitionColumnName` substitui, `?AdfRangePartitionUpbound` e com o nome `?AdfRangePartitionLowbound` e gamas de valor reais da coluna para cada partição, e envia para Netezza. <br>Por exemplo, se a sua coluna de partição "ID" definida com o limite inferior como 1 e o limite superior como 80, com conjunto de cópia paralela como 4, a Data Factory recupera dados por 4 divisórias. Os seus IDs estão entre [1,20], [21, 40], [41, 60], e [61, 80], respectivamente. |

**Exemplo: consulta com partição de fatia de dados**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Exemplo: consulta com partição de gama dinâmica**

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

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
