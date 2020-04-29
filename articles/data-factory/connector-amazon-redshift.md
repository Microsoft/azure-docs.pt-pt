---
title: Copiar dados da Amazon Redshift
description: Saiba como copiar dados da Amazon Redshift para lojas de dados de sink suportadas utilizando a Azure Data Factory.
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
ms.openlocfilehash: ce63da745fb84ebccd57b246fc934f595dd7cda1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418257"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copiar dados da Amazon Redshift usando a Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-amazon-redshift-connector.md)
> * [Versão atual](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de um Redshift Amazon. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Amazon Redshift é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Amazon Redshift para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Amazon Redshift suporta a recuperação de dados da Redshift utilizando o suporte redshift unload incorporado.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados da Redshift, considere usar o REDShift UNLOAD incorporado através da Amazon S3. Consulte o Use UNLOAD para copiar dados da secção [Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) para obter mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a copiar dados para uma loja de dados no local utilizando o Tempo de [Integração Auto-hospedado,](create-self-hosted-integration-runtime.md)conceda o Tempo de Integração (use o endereço IP da máquina) o acesso ao cluster Amazon Redshift. Consulte [autorizar o acesso ao cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções.
* Se estiver a copiar dados para uma loja de dados Azure, consulte [o Azure Data Center IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) para o endereço IP computacional e as gamas SQL utilizadas pelos centros de dados Azure.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector Amazon Redshift.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Amazon Redshift:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AmazonRedshift** | Sim |
| servidor |Endereço IP ou nome anfitrião do servidor Amazon Redshift. |Sim |
| porta |O número da porta TCP que o servidor Amazon Redshift usa para ouvir as ligações dos clientes. |Não, o padrão é 5439. |
| base de dados |Nome da base de dados Amazon Redshift. |Sim |
| o nome de utilizador |Nome do utilizador que tenha acesso à base de dados. |Sim |
| palavra-passe |Palavra-passe para a conta de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Amazon Redshift.

Para copiar dados da Amazon Redshift, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **AmazonRedshiftTable** | Sim |
| schema | Nome do esquema. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tabela | Nome da mesa. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Uso `schema` `table` e para nova carga de trabalho. | Não (se for especificada a "consulta" na fonte de atividade) |

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

Se estiver `RelationalTable` a utilizar o conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte da Amazon Redshift.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift como fonte

Para copiar dados da Amazon Redshift, delineie o tipo de origem na atividade de cópia para **AmazonRedshiftSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **AmazonRedshiftSource** | Sim |
| consulta |Use a consulta personalizada para ler dados. Por exemplo: selecione * do MyTable. |Não (se for especificado "tableName" no conjunto de dados) |
| redshiftUnloadDefinis | Grupo imobiliário ao usar a Amazon Redshift UNLOAD. | Não |
| s3LinkedServiceName | Refere-se a um Amazon S3 a ser usado como uma loja provisória, especificando um nome de serviço ligado do tipo "AmazonS3". | Sim, se usar o UNLOAD |
| bucketName | Indique o balde S3 para armazenar os dados provisórios. Caso não seja fornecido, o serviço Data Factory gera-o automaticamente.  | Sim, se usar o UNLOAD |

**Exemplo: Fonte da Amazon Redshift na atividade de cópia usando UNLOAD**

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

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Use UNLOAD para copiar dados da Amazon Redshift

[Unload](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) é um mecanismo fornecido pela Amazon Redshift, que pode descarregar os resultados de uma consulta a um ou mais ficheiros sobre o Serviço de Armazenamento Simples da Amazon (Amazon S3). É a forma recomendada pela Amazon para copiar grandes conjuntos de dados da Redshift.

**Exemplo: copiar dados da Amazon Redshift para O Armazém de Dados Azure SQL utilizando UNLOAD, cópia encenada e PolyBase**

Para este caso de utilização da amostra, a atividade de cópia descarrega dados da Amazon Redshift para a Amazon S3 configuradas em "redshiftUnloadSettings", e depois copia dados da Amazon S3 para o Azure Blob, conforme especificado em "stagingSettings", utilizar por último a PolyBase para carregar dados no SQL Data Warehouse. Todo o formato provisório é manuseado corretamente pela atividade da cópia.

![Redshift para SQL DW copy workflow](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapeamento de tipo de dados para Amazon Redshift

Ao copiar dados da Amazon Redshift, os seguintes mapeamentos são usados desde tipos de dados da Amazon Redshift para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados da Amazon Redshift | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |String |
| CHAR |String |
| DATA |DateTime |
| DECIMAL |Decimal |
| PRECISÃO DUPLA |Double |
| INTEGER |Int32 |
| REAL |Único |
| SMALLINT |Int16 |
| TEXT |String |
| CARIMBO TEMPORAL |DateTime |
| RIO VARCHAR |String |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
