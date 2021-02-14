---
title: Mover dados da DB2 utilizando a Azure Data Factory
description: Saiba como mover dados a partir de uma base de dados DB2 no local, utilizando a atividade de cópia da fábrica de dados Azure
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bcd1e6f9f9be1765e76399707ae36776a9f1987d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387364"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Mover dados do DB2 utilizando a atividade de cópia da fábrica de dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-db2-connector.md)
> * [Versão 2 (versão atual)](../connector-db2.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector DB2 em V2](../connector-db2.md).


Este artigo descreve como pode utilizar a Copy Activity na Azure Data Factory para copiar dados de uma base de dados DB2 no local para uma loja de dados. Pode copiar dados para qualquer loja que esteja listada como um lavatório suportado no artigo de atividades de [movimento de dados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) da Data Factory. Este tópico baseia-se no artigo da Data Factory, que apresenta uma visão geral do movimento de dados utilizando a Copy Activity e lista as combinações de data store suportadas. 

A Data Factory suporta atualmente apenas a transferência de dados de uma base de dados DB2 para uma [loja de dados de sumidouro suportado.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) A transferência de dados de outras lojas de dados para uma base de dados DB2 não é suportada.

## <a name="prerequisites"></a>Pré-requisitos
A Data Factory suporta a ligação a uma base de dados DB2 no local, utilizando o [gateway de gestão de dados.](data-factory-data-management-gateway.md) Para obter instruções passo a passo para configurar o pipeline de dados gateway para mover os seus dados, consulte os [dados do Movimento de informações no local para o](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem.

É necessário um gateway mesmo que o DB2 esteja hospedado no Azure IaaS VM. Pode instalar o gateway no mesmo IaaS VM que a loja de dados. Se o gateway puder ligar-se à base de dados, pode instalar o gateway num VM diferente.

O gateway de gestão de dados fornece um controlador DB2 incorporado, por isso não é necessário instalar manualmente um controlador para copiar dados da DB2.

> [!NOTE]
> Para obter dicas sobre a ligação de resolução de problemas e problemas de gateway, consulte o artigo [de gateway troubleshoot.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)


## <a name="supported-versions"></a>Versões suportadas
O conector DB2 da Data Factory suporta as seguintes plataformas e versões IBM DB2 com as versões 9, 10 e 11 do Gestor de Acesso Sql (DRDA) distribuídos.

* IBM DB2 para versão z/OS 11.1
* IBM DB2 para versão z/OS 10.1
* IBM DB2 para i (AS400) versão 7.2
* IBM DB2 para i (AS400) versão 7.1
* IBM DB2 para Linux, UNIX e Windows (LUW) versão 11
* IBM DB2 para a versão LUW 10.5
* IBM DB2 para luw versão 10.1

> [!TIP]
> Se receber a mensagem de erro "O pacote correspondente a um pedido de execução de declaração SQL não foi encontrado. SQLSTATE=51002 SQLCODE=-805", a razão é que um pacote necessário não é criado para o utilizador normal no SISTEMA. Para resolver este problema, siga estas instruções para o seu tipo de servidor DB2:
> - DB2 para i (AS400): Deixe um utilizador de energia criar a coleção para o utilizador normal antes de executar a Atividade de Cópia. Para criar a coleção, utilize o comando: `create collection <username>`
> - DB2 para z/OS ou LUW: Utilize uma conta de alto privilégio -- um utilizador de energia ou administrador que tenha autoridades de pacotes e BIND, BINDADD, GRANT EXECUTE TO PUBLIC permissões -- para executar a cópia uma vez. A embalagem necessária é criada automaticamente durante a cópia. Depois, pode voltar para o utilizador normal para as suas execuções de cópias subsequentes.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia para mover dados de uma loja de dados DB2 no local, utilizando diferentes ferramentas e APIs: 

- A forma mais fácil de criar um oleoduto é utilizar o Assistente de Cópia da Fábrica de Dados Azure. Para uma rápida passagem pela criação de um oleoduto utilizando o Copy Wizard, consulte o [Tutorial: Crie um oleoduto utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md). 
- Também pode utilizar ferramentas para criar um pipeline, incluindo Visual Studio, Azure PowerShell, um modelo de Gestor de Recursos Azure, a API .NET e a API REST. Para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia, consulte o tutorial de [Atividade de Cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie serviços ligados para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie conjuntos de dados para representar dados de entrada e saída para a operação de cópia. 
3. Crie um pipeline com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o Copy Wizard, as definições JSON para os serviços ligados à Data Factory, conjuntos de dados e entidades de pipeline são automaticamente criadas para si. Quando utiliza ferramentas ou APIs (exceto a API .NET), define as entidades da Data Factory utilizando o formato JSON. O exemplo JSON: Copiar dados de DB2 para Azure Blob mostra as definições JSON para as entidades da Data Factory que são usadas para copiar dados de uma loja de dados DB2 no local.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são usadas para definir as entidades da Data Factory que são específicas de uma loja de dados DB2.

## <a name="db2-linked-service-properties"></a>Propriedades de serviço ligadas DB2
A tabela a seguir lista as propriedades JSON específicas de um serviço ligado DB2.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **tipo** |Esta propriedade deve ser definida para **OnPremisesDb2**. |Yes |
| **servidor** |O nome do servidor DB2. |Yes |
| **base de dados** |O nome da base de dados DB2. |Yes |
| **esquema** |O nome do esquema na base de dados DB2. Esta propriedade é sensível a casos. |No |
| **authenticationType** |O tipo de autenticação que é utilizado para ligar à base de dados DB2. Os valores possíveis são: Anónimo, Básico e Windows. |Yes |
| **nome de utilizador** |O nome da conta de utilizador se utilizar a autenticação Basic ou Windows. |No |
| **palavra-passe** |A palavra-passe para a conta de utilizador. |No |
| **gatewayName** |O nome do gateway que o serviço data factory deve usar para ligar à base de dados DB2 no local. |Yes |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções, como **estrutura,** **disponibilidade,** e **a política** para um conjunto de dados JSON, são semelhantes para todos os tipos de conjunto de dados (Azure SQL, armazenamento Azure Blob, armazenamento de mesa Azure, e assim por diante).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **de tipoproperties** para um conjunto de dados do tipo **RelationalTable,** que inclui o conjunto de dados DB2, tem a seguinte propriedade:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **tableName** |O nome do quadro na placa de base de dados DB2 a que o serviço ligado se refere. Esta propriedade é sensível a casos. |Não (se for especificada a propriedade de **consulta** de uma atividade de cópia do tipo **RelationalSource)** |

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia
Para obter uma lista das secções e propriedades disponíveis para definir atividades de cópia, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) As propriedades da Copy Activity, tais como **nome,** **descrição,** tabela **de entradas,** tabela **de saídas** e **política,** estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na secção **de tipoProperties** da atividade variam para cada tipo de atividade. Para a Atividade de Cópia, as propriedades variam consoante os tipos de fontes de dados e pias.

Para a Atividade de Cópia, quando a fonte é do tipo **RelationalSource** (que inclui DB2), as seguintes propriedades estão disponíveis na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **consulta** |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""` |Não (se for especificada a propriedade do nome de **tabela** de um conjunto de dados) |

> [!NOTE]
> Schema e nomes de mesa são sensíveis a casos. Na declaração de consulta, encerra os nomes dos imóveis utilizando "" (cotações duplas).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Exemplo JSON: Copiar dados do armazenamento DB2 para Azure Blob
Este exemplo fornece definições JSON de amostra que pode usar para criar um oleoduto utilizando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). O exemplo mostra como copiar dados de uma base de dados DB2 para o armazenamento blob. No entanto, os dados podem ser copiados para qualquer tipo de pia de loja de [dados suportado,](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a Atividade de Cópia da Fábrica de Dados Azure.

A amostra tem as seguintes entidades da Data Factory:

- Um serviço DB2 ligado do tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Um serviço de armazenamento Azure Blob ligado ao tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelacionalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- Um [oleoduto](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza as propriedades [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

A amostra copia dados de uma consulta resulta numa base de dados DB2 para uma bolha Azure por hora. As propriedades JSON que são utilizadas na amostra são descritas nas secções que seguem as definições da entidade.

Como primeiro passo, instale e configuure um gateway de dados. As instruções estão nos [dados de deslocação entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem.

**Serviço ligado DB2**

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

A amostra assume que criou uma tabela em DB2 chamada "MyTable" que tem uma coluna rotulada de "timetamp" para os dados da série de tempo.

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

Os dados são escritos para uma nova bolha a cada hora, definindo a propriedade **de frequência** para "Hora" e a propriedade de **intervalo** para 1. A **propriedade pastaPath** para o blob é avaliada dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza as partes do ano, mês, dia e hora da hora de início.

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

O pipeline contém uma atividade de cópia configurada para utilizar conjuntos de dados de entrada e saída especificados e que está programado para ser executado a cada hora. Na definição JSON para o pipeline, o tipo **de fonte** é definido para **RelationalSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **de consulta** seleciona os dados da tabela "Encomendas".

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
Conforme mencionado no artigo de [atividades](data-factory-data-movement-activities.md) de movimento de dados, copy Activity realiza conversões automáticas de tipo fonte para tipo de pia, utilizando a seguinte abordagem em duas etapas:

1. Converter de um tipo de origem nativa para um tipo .NET
2. Converter de um tipo .NET para um tipo de pia nativa

Os seguintes mapeamentos são utilizados quando a Copy Activity converte os dados de um tipo DB2 para um tipo .NET:

| Tipo de base de dados DB2 | .NET Tipo de quadro |
| --- | --- |
| PequenoInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Único |
| Double (Duplo) |Double (Duplo) |
| Float |Double (Duplo) |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Operador numérico |Decimal |
| Data |Data e Hora |
| Hora |TimeSpan |
| CarimboDeDataEHora |DateTime |
| Xml |Byte[] |
| Char |String |
| Rio VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binário |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Gráfico |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blobs |Byte[] |
| DbClob |String |
| PequenoInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Único |
| Double (Duplo) |Double (Duplo) |
| Float |Double (Duplo) |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Operador numérico |Decimal |
| Data |Data e Hora |
| Hora |TimeSpan |
| CarimboDeDataEHora |DateTime |
| Xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para aprender a mapear colunas no conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetíveis de fontes relacionais
Ao copiar dados de uma loja de dados relacional, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a propriedade **da política** de retry para um conjunto de dados para refazer uma fatia quando ocorre uma falha. Certifique-se de que os mesmos dados são lidos independentemente do número de repetições da fatia, e independentemente da forma como reexecute a fatia. Para obter mais informações, consulte [leituras repetíveis de fontes relacionais.](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Desempenho e otimização
Conheça os factores-chave que afetam o desempenho da Copy Activity e formas de otimizar o desempenho no [Copy Activity Performance and Afinando Guide](data-factory-copy-activity-performance.md).
