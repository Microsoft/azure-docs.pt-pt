---
title: Mover dados do DB2 usando Azure Data Factory
description: Saiba como mover dados de um banco de dado DB2 local usando Azure Data Factory atividade de cópia
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5d2c6b0460c3a7566adb17601aceb57e57f4d0b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931794"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Mover dados do DB2 usando Azure Data Factory atividade de cópia
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-onprem-db2-connector.md)
> * [Versão 2 (versão atual)](../connector-db2.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do DB2 na v2](../connector-db2.md).


Este artigo descreve como você pode usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados DB2 local para um armazenamento de dado. Você pode copiar dados para qualquer repositório listado como um coletor com suporte no artigo [Data Factory atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Este tópico se baseia no artigo Data Factory, que apresenta uma visão geral da movimentação de dados usando a atividade de cópia e lista as combinações de armazenamento de dados com suporte. 

Atualmente, o Data Factory dá suporte apenas à movimentação de dados de um banco de dados DB2 para um [armazenamento de dado de coletor com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Não há suporte para a movimentação de dados de outros armazenamentos de dados para um banco de dado DB2.

## <a name="prerequisites"></a>Pré-requisitos
O Data Factory dá suporte à conexão a um banco de dados DB2 local usando o [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md). Para obter as instruções passo a passo para configurar o pipeline de dados do gateway para mover seus dados, consulte o artigo [mover dados do local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

Um gateway é necessário mesmo se o DB2 estiver hospedado na VM IaaS do Azure. Você pode instalar o gateway na mesma VM IaaS que o armazenamento de dados. Se o gateway puder se conectar ao banco de dados, você poderá instalar o gateway em uma VM diferente.

O gateway de gerenciamento de dados fornece um driver DB2 interno, portanto, você não precisa instalar manualmente um driver para copiar dados do DB2.

> [!NOTE]
> Para obter dicas sobre como solucionar problemas de conexão e gateway, consulte o artigo [solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .


## <a name="supported-versions"></a>Versões suportadas
O conector do Data Factory DB2 dá suporte às seguintes plataformas e versões do IBM DB2 com o Gerenciador de acesso (DRDA) do SQL Access Manager versões 9, 10 e 11.

* IBM DB2 para z/OS versão 11,1
* IBM DB2 para z/OS versão 10,1
* IBM DB2 para i (AS400) versão 7,2
* IBM DB2 para i (AS400) versão 7,1
* IBM DB2 para Linux, UNIX e Windows (LUW) versão 11
* IBM DB2 para LUW versão 10,5
* IBM DB2 para LUW versão 10,1

> [!TIP]
> Se você receber a mensagem de erro "o pacote correspondente a uma solicitação de execução de instrução SQL não foi encontrado. SQLSTATE = 51002 SQLCODE =-805, "o motivo é que um pacote necessário não é criado para o usuário normal no sistema operacional. Para resolver esse problema, siga estas instruções para o tipo de servidor DB2:
> - DB2 para i (AS400): permita que um usuário avançado crie a coleção para o usuário normal antes de executar a atividade de cópia. Para criar a coleção, use o comando: `create collection <username>`
> - DB2 para z/OS ou LUW: Use uma conta de alto privilégio – um usuário avançado ou administrador que tenha autoridades de pacote e BIND, BINDADD, CONCEda permissões EXECUTE TO PUBLIC--para executar a cópia uma vez. O pacote necessário é criado automaticamente durante a cópia. Depois disso, você pode alternar de volta para o usuário normal para suas execuções de cópia subsequentes.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia para mover dados de um armazenamento de dados do DB2 local usando diferentes ferramentas e APIs: 

- A maneira mais fácil de criar um pipeline é usar o assistente de Azure Data Factory Copy. Para obter instruções rápidas sobre como criar um pipeline usando o assistente de cópia, consulte o [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md). 
- Você também pode usar ferramentas para criar um pipeline, incluindo o Visual Studio, Azure PowerShell, um modelo de Azure Resource Manager, a API do .NET e a API REST. Para obter instruções detalhadas sobre como criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie serviços vinculados para vincular armazenamentos de dados de entrada e saída ao seu data factory.
2. Crie conjuntos de dados para representar o dado de entrada e saída para a operação de cópia. 
3. Crie um pipeline com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. 

Quando você usa o assistente de cópia, as definições de JSON para os Data Factory serviços vinculados, conjuntos de valores e entidades de pipeline são criadas automaticamente para você. Ao usar ferramentas ou APIs (exceto a API do .NET), você define as entidades de Data Factory usando o formato JSON. O exemplo de JSON: copiar dados do DB2 para o armazenamento de BLOBs do Azure mostra as definições de JSON para as entidades de Data Factory que são usadas para copiar dados de um armazenamento de dados do DB2 local.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir as entidades de Data Factory que são específicas para um armazenamento de dados DB2.

## <a name="db2-linked-service-properties"></a>Propriedades do serviço vinculado do DB2
A tabela a seguir lista as propriedades JSON que são específicas para um serviço vinculado do DB2.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **tipo** |Essa propriedade deve ser definida como **OnPremisesDb2**. |Sim |
| **servidor** |O nome do servidor DB2. |Sim |
| **database** |O nome do banco de dados DB2. |Sim |
| **schema** |O nome do esquema no banco de dados DB2. Essa propriedade diferencia maiúsculas de minúsculas. |Não |
| **authenticationType** |O tipo de autenticação usado para se conectar ao banco de dados DB2. Os valores possíveis são: Anonymous, Basic e Windows. |Sim |
| **username** |O nome da conta de usuário se você usar a autenticação básica ou do Windows. |Não |
| **password** |A senha da conta de usuário. |Não |
| **gatewayName** |O nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados DB2 local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções, como **estrutura**, **disponibilidade**e a **política** de um conjunto de dados JSON, são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, armazenamento de BLOBs do Azure, armazenamento de tabelas do Azure e assim por diante).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção **typeproperties** para um conjunto de um DataSet do tipo **RelationalTable**, que inclui o conjunto de um DB2, tem a seguinte propriedade:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **tableName** |O nome da tabela na instância do banco de dados DB2 à qual o serviço vinculado se refere. Essa propriedade diferencia maiúsculas de minúsculas. |Não (se a propriedade de **consulta** de uma atividade de cópia do tipo **RelationalSource** for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da atividade copy
Para obter uma lista das seções e propriedades que estão disponíveis para definir atividades de cópia, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . As propriedades da atividade de cópia, como **nome**, **Descrição**, tabela de **entradas** , tabela de **saídas** e **política**, estão disponíveis para todos os tipos de atividades. As propriedades que estão disponíveis na seção **typeproperties** da atividade variam para cada tipo de atividade. Para a atividade de cópia, as propriedades variam de acordo com os tipos de fontes de dados e coletores.

Para a atividade de cópia, quando a fonte é do tipo **RelationalSource** (que inclui DB2), as seguintes propriedades estão disponíveis na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| **query** |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""` |Não (se a propriedade **TableName** de um conjunto de um DataSet for especificada) |

> [!NOTE]
> Os nomes de esquema e tabela diferenciam maiúsculas de minúsculas. Na instrução de consulta, coloque os nomes de propriedade usando "" (aspas duplas).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Exemplo de JSON: copiar dados do DB2 para o armazenamento de BLOBs do Azure
Este exemplo fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). O exemplo mostra como copiar dados de um banco de dados DB2 para o armazenamento de BLOBs. No entanto, os dados podem ser copiados para [qualquer tipo de coletor de armazenamento de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando Azure data Factory atividade de cópia.

O exemplo tem as seguintes entidades de Data Factory:

- Um serviço vinculado do DB2 do tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Um serviço vinculado do armazenamento de BLOBs do Azure do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa as propriedades [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

O exemplo copia dados de um resultado de consulta em um banco de dado DB2 para um blob do Azure a cada hora. As propriedades JSON que são usadas no exemplo são descritas nas seções que seguem as definições de entidade.

Como uma primeira etapa, instale e configure um gateway de dados. As instruções estão no artigo [movendo dados entre os locais e a nuvem local](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado do DB2**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Serviço vinculado do armazenamento de BLOBs do Azure**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Conjunto de dados de entrada do DB2**

O exemplo supõe que você criou uma tabela no DB2 denominada "MyTable" que tem uma coluna rotulada "timestamp" para os dados de série temporal.

A propriedade **external** é definida como "true". Essa configuração informa ao serviço de Data Factory que esse conjunto de DataSet é externo ao data factory e não é produzido por uma atividade no data factory. Observe que a propriedade **Type** está definida como **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Conjunto de dados dos Blobs do Azure**

Os dados são gravados em um novo BLOB a cada hora, definindo a propriedade **Frequency** como "Hour" e a propriedade **Interval** como 1. A propriedade **FolderPath** do blob é avaliada dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes de ano, mês, dia e hora da hora de início.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline para a atividade de cópia**

O pipeline contém uma atividade de cópia configurada para usar conjuntos de dados de entrada e saída especificados e que está agendado para ser executado a cada hora. Na definição de JSON para o pipeline, o tipo de **origem** é definido como **RelationalSource** e o tipo de **coletor** é definido como **BlobSink**. A consulta SQL especificada para a propriedade **Query** seleciona os dados da tabela "Orders".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Mapeamento de tipo para DB2
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas do tipo de origem para o tipo de coletor usando a seguinte abordagem de duas etapas:

1. Converter de um tipo de fonte nativa em um tipo .NET
2. Converter de um tipo .NET em um tipo de coletor nativo

Os seguintes mapeamentos são usados quando a atividade de cópia converte os dados de um tipo DB2 para um tipo .NET:

| Tipo de banco de dados DB2 | Tipo de .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Único |
| Double |Double |
| Flutuante |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numérico |Decimal |
| Date |DateTime |
| Tempo |TimeSpan |
| Carimbo de data/hora |DateTime |
| Xml |Byte[] |
| char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Único |
| Double |Double |
| Flutuante |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numérico |Decimal |
| Date |DateTime |
| Tempo |TimeSpan |
| Carimbo de data/hora |DateTime |
| Xml |Byte[] |
| char |String |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, consulte [mapeando colunas do conjunto de informações no Azure data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetidas de fontes relacionais
Ao copiar dados de um repositório de dados relacional, mantenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a propriedade **política** de repetição de um conjunto de um para executar novamente uma fatia quando ocorrer uma falha. Verifique se os mesmos dados são lidos independentemente de quantas vezes a fatia é executada novamente e, independentemente de como você executa novamente a fatia. Para obter mais informações, consulte [leituras repetidas de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Saiba mais sobre os principais fatores que afetam o desempenho da atividade de cópia e maneiras de otimizar o desempenho no [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).
