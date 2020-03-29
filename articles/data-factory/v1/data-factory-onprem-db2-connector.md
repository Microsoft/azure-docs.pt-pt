---
title: Mova dados do DB2 utilizando a Azure Data Factory
description: Saiba como mover dados de uma base de dados DB2 no local utilizando a Atividade de Cópia de Cópia de Fábrica de Dados do Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931794"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Mova dados do DB2 utilizando a Atividade de Cópia da Fábrica de Dados Do Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-db2-connector.md)
> * [Versão 2 (versão atual)](../connector-db2.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector DB2 em V2](../connector-db2.md).


Este artigo descreve como pode utilizar a Copy Activity na Azure Data Factory para copiar dados de uma base de dados DB2 no local para uma loja de dados. Pode copiar dados para qualquer loja que esteja listada como um sumidouro suportado no artigo de atividades de movimento de dados da Fábrica de [Dados.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Este tópico baseia-se no artigo data Factory, que apresenta uma visão geral do movimento de dados utilizando a Atividade de Cópia e lista as combinações de lojas de dados suportadas. 

A Data Factory suporta atualmente apenas a transferência de dados de uma base de dados DB2 para uma loja de [dados de sink suportado.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Não é suportado o deslocação de dados de outras lojas de dados para uma base de dados DB2.

## <a name="prerequisites"></a>Pré-requisitos
Data Factory suporta a ligação a uma base de dados DB2 no local utilizando o gateway de [gestão](data-factory-data-management-gateway.md)de dados . Para obter instruções passo a passo para configurar o pipeline de dados de gateway para mover os seus dados, consulte os dados de Move do local para o artigo [em nuvem.](data-factory-move-data-between-onprem-and-cloud.md)

É necessária uma porta de entrada mesmo que o DB2 esteja hospedado no Azure IaaS VM. Pode instalar o portal no mesmo IaaS VM que o depósito de dados. Se o portal puder ligar-se à base de dados, pode instalar o portal num VM diferente.

O portal de gestão de dados fornece um controlador DB2 incorporado, pelo que não é necessário instalar manualmente um controlador para copiar dados do DB2.

> [!NOTE]
> Para obter dicas sobre questões relacionadas com a resolução de problemas e problemas de gateway, consulte o artigo problemas de [gateway.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)


## <a name="supported-versions"></a>Versões suportadas
O conector DB2 data Factory suporta as seguintes plataformas e versões IBM DB2 com arquitetura de base de dados relacional distribuída (DRDA) SQL Access Manager versões 9, 10 e 11:

* IBM DB2 para z/OS versão 11.1
* IBM DB2 para z/OS versão 10.1
* IBM DB2 para i (AS400) versão 7.2
* IBM DB2 para i (AS400) versão 7.1
* IBM DB2 para Linux, UNIX e Windows (LUW) versão 11
* IBM DB2 para a versão LUW 10.5
* IBM DB2 para a versão LUW 10.1

> [!TIP]
> Se receber a mensagem de erro "O pacote correspondente a um pedido de execução de declaração SQL não foi encontrado. SQLSTATE=51002 SQLCODE=-805", a razão é que um pacote necessário não é criado para o utilizador normal no SISTEMA. Para resolver este problema, siga estas instruções para o seu tipo de servidor DB2:
> - DB2 para i (AS400): Deixe um utilizador de energia criar a coleção para o utilizador normal antes de executar a Atividade de Cópia. Para criar a coleção, utilize o comando:`create collection <username>`
> - DB2 para z/OS ou LUW: Utilize uma conta de alto privilégio -- um utilizador ou administrador que tenha autoridades de pacotes e BIND, BINDADD, GRANT EXECUTE TO PUBLIC permissions -- para executar a cópia uma vez. O pacote necessário é automaticamente criado durante a cópia. Depois, pode voltar ao utilizador normal para as suas executações posteriores.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia para mover dados de uma loja de dados DB2 no local utilizando diferentes ferramentas e APIs: 

- A maneira mais fácil de criar um pipeline é usar o Assistente de Cópia da Fábrica de Dados Azure. Para uma rápida passagem pela criação de um pipeline utilizando o Assistente de Cópia, consulte o [Tutorial: Crie um pipeline utilizando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md). 
- Também pode usar ferramentas para criar um pipeline, incluindo Visual Studio, Azure PowerShell, um modelo de Gestor de Recursos Azure, a API .NET e a API REST. Para obter instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte o [tutorial copy activity](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie serviços ligados para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie conjuntos de dados para representar dados de entrada e saída para a operação de cópia. 
3. Crie um pipeline com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o Copy Wizard, as definições jSON para os serviços ligados à Fábrica de Dados, conjuntos de dados e entidades de pipeline são automaticamente criadas para si. Quando utiliza ferramentas ou APIs (exceto a API .NET), define as entidades da Fábrica de Dados utilizando o formato JSON. O exemplo jSON: Copiar dados de DB2 para armazenamento De Blob mostra as definições JSON para as entidades data Factory que são usadas para copiar dados de uma loja de dados DB2 no local.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir as entidades data Factory que são específicas de uma loja de dados DB2.

## <a name="db2-linked-service-properties"></a>Propriedades de serviço seletos db2
A tabela seguinte lista as propriedades JSON específicas de um serviço ligado ao DB2.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tipo** |Esta propriedade deve ser definida para **OnPremisesDb2**. |Sim |
| **servidor** |O nome do servidor DB2. |Sim |
| **base de dados** |O nome da base de dados DB2. |Sim |
| **schema** |O nome do esquema na base de dados DB2. Esta propriedade é sensível a casos. |Não |
| **authenticationType** |O tipo de autenticação que é usado para ligar à base de dados DB2. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| **nome de utilizador** |O nome da conta de utilizador se utilizar a autenticação Basic ou Windows. |Não |
| **palavra-passe** |A palavra-passe para a conta de utilizador. |Não |
| **nome gateway** |O nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados DB2 no local. |Sim |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções, tais como **estrutura,** **disponibilidade,** e **a política** de um conjunto de dados JSON, são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, armazenamento Azure Blob, armazenamento de mesa Azure, e assim por diante).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **TypeProperties** para um conjunto de dados do tipo **RelationalTable,** que inclui o conjunto de dados DB2, tem a seguinte propriedade:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tableName** |O nome da tabela na instância de base de dados DB2 a que o serviço ligado se refere. Esta propriedade é sensível a casos. |Não (se for especificada a propriedade **de consulta** de uma atividade de cópia do tipo **RelationalSource)** |

## <a name="copy-activity-properties"></a>Propriedades de Copy Activity
Para obter uma lista das secções e propriedades disponíveis para definir atividades de cópia, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades de Copy Activity, tais como **nome,** **descrição,** tabela **de inputs,** tabela de **saídas** e **política,** estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na secção **typeProperties** da atividade variam para cada tipo de atividade. No que diz matéria de cópia, as propriedades variam consoante os tipos de fontes de dados e pias.

Para a Atividade de Cópia, quando a fonte é do tipo **RelationalSource** (que inclui DB2), as seguintes propriedades estão disponíveis na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **consulta** |Use a consulta personalizada para ler os dados. |Fio de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""` |Não (se for especificada a propriedade **tableName** de um conjunto de dados) |

> [!NOTE]
> O schema e os nomes das mesas são sensíveis aos casos. Na declaração de consulta, encerre os nomes de propriedade utilizando "" (orçamentos duplos).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Exemplo jSON: Copiar dados de DB2 para armazenamento De Blob Azure
Este exemplo fornece definições jSON de amostra que pode usar para criar um pipeline utilizando o [Estúdio Visual](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). O exemplo mostra como copiar dados de uma base de dados DB2 para o armazenamento blob. No entanto, os dados podem ser copiados para qualquer tipo de pia de loja de [dados suportado](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a Atividade de Cópia de Cópia de Fábrica de Dados Azure.

A amostra tem as seguintes entidades data Factory:

- Um serviço ligado db2 do tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Um serviço ligado ao armazenamento Azure Blob do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza as propriedades [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

A amostra copia dados de uma consulta resultam numa base de dados DB2 para uma bolha Azure de hora a hora. As propriedades JSON que são utilizadas na amostra são descritas nas secções que seguem as definições da entidade.

Como primeiro passo, instale e configure um portal de dados. As instruções estão nos [dados de deslocação entre os locais no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem.

**Serviço ligado db2**

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

**Serviço ligado ao armazenamento Azure Blob**

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

**Conjunto de dados de entrada DB2**

A amostra pressupõe que criou uma tabela em DB2 chamada "MyTable" que tem uma coluna com a etiqueta "timestamp" para os dados da série time.

A propriedade **externa** está definida como "verdadeira". Esta definição informa o serviço Data Factory de que este conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados. Note que a propriedade **tipo** está definida para **RelationalTable**.


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

Os dados são escritos para uma nova bolha a cada hora, definindo a propriedade de **frequência** para "Hora" e a propriedade de **intervalo** para 1. A propriedade **pastaPath** para a bolha é avaliada dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza as partes do ano, mês, dia e hora da hora de início.

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

**Gasoduto para a atividade de cópia**

O pipeline contém uma atividade de cópia configurada para utilizar conjuntos de dados de entrada e saída especificados e que está programado para funcionar a cada hora. Na definição JSON para o gasoduto, o tipo de **origem** é definido para **RelationalSource** e o tipo de **pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados da tabela "Encomendas".

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

## <a name="type-mapping-for-db2"></a>Tipo de mapeamento para DB2
Conforme mencionado no artigo de atividades de movimento de [dados,](data-factory-data-movement-activities.md) a Atividade de Cópia realiza conversões automáticas de tipo de origem para tipo de pia utilizando a seguinte abordagem em duas etapas:

1. Converter de um tipo de fonte nativa para um tipo .NET
2. Converter de um tipo .NET para um tipo de pia nativa

Os seguintes mapeamentos são utilizados quando a Copy Activity converte os dados de um tipo DB2 para um tipo .NET:

| Tipo de base de dados DB2 | Tipo de quadro .NET |
| --- | --- |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Único |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numérico |Decimal |
| Date |DateTime |
| Hora |TimeSpan |
| Carimbo de data/hora |DateTime |
| Xml |Byte[] |
| Char |Cadeia |
| Rio Varchar |Cadeia |
| LongVarchar |Cadeia |
| DB2DynArray |Cadeia |
| Binário |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Gráfico |Cadeia |
| Vargraphic |Cadeia |
| Longvargraphic |Cadeia |
| Estação Clob |Cadeia |
| Blobs |Byte[] |
| DbClob |Cadeia |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Único |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numérico |Decimal |
| Date |DateTime |
| Hora |TimeSpan |
| Carimbo de data/hora |DateTime |
| Xml |Byte[] |
| Char |Cadeia |

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para aprender a mapear colunas no conjunto de dados de origem para colunas no conjunto de dados do lavatório, consulte colunas de conjunto de [dados de mapeamento na Fábrica](data-factory-map-columns.md)de Dados Azure .

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetíveis de fontes relacionais
Quando copiar dados de uma loja de dados relacional, tenha em mente a repetível para evitar resultados não intencionais. Na Azure Data Factory, pode reproduzir uma fatia manualmente. Também pode configurar a propriedade da **política** de retry para um conjunto de dados para reexecutar uma fatia quando ocorre uma falha. Certifique-se de que os mesmos dados são lidos independentemente do número de vezes que a fatia é reexecutada e independentemente de como reexecutar a fatia. Para mais informações, consulte [leituras repetíveis a partir de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Conheça os factores-chave que afetam o desempenho da Atividade de Cópia e formas de otimizar o desempenho no Guia de [Desempenho e Afinação](data-factory-copy-activity-performance.md)da Atividade de Cópia.
