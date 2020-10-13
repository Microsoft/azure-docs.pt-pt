---
title: Copiar dados da Amazon Redshift
description: Saiba como copiar dados da Amazon Redshift para lojas de dados de sumidouros suportados utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: a756a3cec5702570751e0bea09a4f59152accafc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484549"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copiar dados da Amazon Redshift usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-amazon-redshift-connector.md)
> * [Versão atual](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de um Redshift da Amazon. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Amazon Redshift é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Amazon Redshift para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Amazon Redshift suporta a obtenção de dados do Redshift usando consulta ou suporte de DESCARGA Redshift incorporado.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados da Redshift, considere usar o Redshift UNLOAD incorporado através do Amazon S3. Consulte [a Utilização DE DESCARGA para copiar dados da](#use-unload-to-copy-data-from-amazon-redshift) secção Redshift da Amazon para obter mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a copiar dados para uma loja de dados no local utilizando [o Tempo de Execução de Integração Auto-hospedado,](create-self-hosted-integration-runtime.md)conceda a Integração Runtime (use endereço IP da máquina) o acesso ao cluster Amazon Redshift. Consulte [o Acesso autorizado ao cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções.
* Se estiver a copiar dados para uma loja de dados Azure, consulte [os intervalos IP do Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653) para o endereço IP compute e as gamas SQL utilizadas pelos centros de dados Azure.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Amazon Redshift.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Amazon Redshift:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AmazonRedshift** | Sim |
| servidor |Endereço IP ou nome de anfitrião do servidor Amazon Redshift. |Sim |
| porta |O número da porta TCP que o servidor Amazon Redshift utiliza para ouvir as ligações dos clientes. |Não, o padrão é 5439 |
| base de dados |Nome da base de dados Amazon Redshift. |Sim |
| nome de utilizador |Nome do utilizador que tem acesso à base de dados. |Sim |
| palavra-passe |Senha para a conta de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |Não |

**Exemplo:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo Conjunto de Dados Redshift da Amazon.

Para copiar dados da Amazon Redshift, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **AmazonRedshiftTable** | Sim |
| esquema | O nome do esquema. |Não (se for especificada "consulta" na fonte de atividade)  |
| table | O nome da mesa. |Não (se for especificada "consulta" na fonte de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Uso `schema` e para nova carga de `table` trabalho. | Não (se for especificada "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se estiver a utilizar `RelationalTable` conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela Amazon Redshift source.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift como fonte

Para copiar dados da Amazon Redshift, desa um tipo de fonte na atividade de cópia para **o AmazonRedshiftSource.** As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **AmazonRedshiftSource** | Sim |
| consulta |Utilize a consulta personalizada para ler dados. Por exemplo: selecione * do MyTable. |Não (se for especificado "tableName" no conjunto de dados) |
| redshiftUnloadSettings | Grupo de propriedade ao usar Amazon Redshift UNLOAD. | Não |
| s3LinkedServiceName | Refere-se a um Amazon S3 a ser usado como uma loja provisória especificando um nome de serviço ligado do tipo "AmazonS3". | Sim, se usar DESCARREGAR |
| baldeName | Indique o balde S3 para armazenar os dados provisórios. Se não for fornecido, o serviço Data Factory gera-o automaticamente.  | Sim, se usar DESCARREGAR |

**Exemplo: Amazon Redshift fonte na atividade de cópia usando DESCARGA**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Saiba mais sobre como usar o UNLOAD para copiar dados da Amazon Redshift de forma eficiente a partir da próxima secção.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Use o UNLOAD para copiar dados da Amazon Redshift

[O UNLOAD](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) é um mecanismo fornecido pela Amazon Redshift, que pode descarregar os resultados de uma consulta a um ou mais ficheiros sobre o Amazon Simple Storage Service (Amazon S3). É a forma recomendada pela Amazon para copiar grandes conjuntos de dados da Redshift.

**Exemplo: copiar dados da Amazon Redshift para Azure Synapse Analytics (anteriormente SQL Data Warehouse) usando UNLOAD, cópia encenada e PolyBase**

Para este caso de utilização de amostras, a atividade de cópia descarrega dados da Amazon Redshift para o Amazon S3 configurados em "redshiftUnloadSettings", e depois copiar dados da Amazon S3 para Azure Blob conforme especificado em "stagingSettings", por último, usar o PolyBase para carregar dados em Azure Synapse Analytics (ex-SQL Data Warehouse). Todo o formato provisório é manuseado corretamente pela atividade de cópia.

![Redshift para Azure Synapse Analytics copy workflow](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapeamento do tipo de dados para Amazon Redshift

Ao copiar dados da Amazon Redshift, os seguintes mapeamentos são usados desde os tipos de dados da Amazon Redshift até aos tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de schema e tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados redshift da Amazon | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEANA |Cadeia |
| CHAR |Cadeia |
| DATE |DateTime |
| DECIMAL |Decimal |
| DUPLA PRECISÃO |Double (Duplo) |
| INTEGER |Int32 |
| REAL |Único |
| SMALLINT |Int16 |
| TEXT |Cadeia |
| TIMETAMP |DateTime |
| RIO VARCHAR |Cadeia |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
