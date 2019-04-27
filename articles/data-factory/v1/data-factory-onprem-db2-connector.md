---
title: Mover dados de DB2 através do Azure Data Factory | Documentos da Microsoft
description: Aprenda a mover dados de uma base de dados de DB2 no local ao utilizar a atividade de cópia de fábrica de dados do Azure
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 72c88ef10bf1df217ec6e24ac744d0b30386b4a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824019"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Mover dados de DB2 usando a atividade de cópia de fábrica de dados do Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-onprem-db2-connector.md)
> * [Versão 2 (versão atual)](../connector-db2.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de DB2 no V2](../connector-db2.md).


Este artigo descreve como pode utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados de DB2 no local para um arquivo de dados. Pode copiar dados para qualquer armazenamento que está listado como um sink suportado na [atividades de movimento de dados do Data Factory](data-factory-data-movement-activities.md#supported-data-stores-and-formats) artigo. Este tópico tem por base o artigo do Data Factory, que apresenta uma visão geral do movimento de dados através da atividade de cópia e lista as combinações de loja de dados suportados. 

Atualmente, o Data Factory suporta apenas mover dados de uma base de dados DB2 para um [arquivo de dados sink suportado](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Mover dados de outros dados armazena para uma base de dados não é suportado de DB2.

## <a name="prerequisites"></a>Pré-requisitos
Ligar a uma base de dados de DB2 no local com o Data Factory suporta o [gateway de gestão de dados](data-factory-data-management-gateway.md). Para obter instruções passo a passo configurar o pipeline de dados do gateway para mover seus dados, consulte a [mover dados do local para a cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo.

É necessário um gateway, mesmo que o DB2 está alojado numa VM de IaaS do Azure. Pode instalar o gateway na mesma VM de IaaS, como o arquivo de dados. Se o gateway consiga estabelecer ligação à base de dados, pode instalar o gateway numa VM diferente.

O gateway de gestão de dados fornece um driver de DB2 incorporado, pelo que não precisa de instalar manualmente um driver para copiar dados de DB2.

> [!NOTE]
> Para obter dicas sobre como resolver problemas de gateway e de ligação, veja a [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) artigo.


## <a name="supported-versions"></a>Versões suportadas
O conector de DB2 da fábrica de dados suporta as seguintes plataformas de IBM DB2 e as versões com versões de Gestor de acesso de SQL distribuídos relacional da base de dados arquitetura (DRDA) 9, 10 e 11:

* IBM DB2 para a versão 11.1 de z/OS
* IBM DB2 para z/OS, versão 10.1
* IBM DB2 para a versão de i (AS400) 7.2
* IBM DB2 para a versão de i (AS400) 7.1
* IBM DB2 para Linux, UNIX e Windows (LUW) versão 11
* IBM DB2 para LUW versão 10.5
* IBM DB2 para LUW versão 10.1

> [!TIP]
> Se receber a mensagem de erro "o pacote correspondente a uma solicitação de execução de instrução SQL não foi encontrado. SQLSTATE = 51002 SQLCODE =-805, "o motivo é um pacote necessário não foi criado para o usuário normal no sistema operacional. Para resolver este problema, siga estas instruções para o tipo de servidor do DB2:
> - DB2 para i (AS400): Permitir que um usuário de poder criar a coleção para o usuário normal, antes de executar a atividade de cópia. Para criar a coleção, utilize o comando: `create collection <username>`
> - DB2 para z/OS ou LUW: Utilize uma conta de alto privilégio – um utilizador avançado ou administrador de que tem as autoridades de pacote e BIND, BINDADD, GRANT executar para permissões públicas, para executar a cópia de uma vez. O pacote necessário é criado automaticamente durante a cópia. Em seguida, pode mudar para o usuário normal para suas execuções de cópia subsequentes.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia para mover dados de um arquivo de dados DB2 no local através de APIs e ferramentas diferentes: 

- A maneira mais fácil para criar um pipeline é usar o Assistente de cópia de fábrica de dados do Azure. Para uma passo a passo rápido sobre como criar um pipeline com o Assistente de cópia, consulte o [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md). 
- Também pode utilizar ferramentas para criar um pipeline, incluindo o portal do Azure, Visual Studio, Azure PowerShell, um modelo Azure Resource Manager, a API de .NET e a API REST. Para obter instruções passo a passo Criar um pipeline com uma atividade de cópia, consulte a [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie serviços ligados para ligar a entrada e saída de arquivos de dados à fábrica de dados.
2. Crie conjuntos de dados para representar os dados de entrada e saídos da operação de cópia. 
3. Crie um pipeline com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o Assistente de cópia, definições de JSON da fábrica de dados ligado de serviços, datasets e entidades de pipeline são criadas automaticamente para. Ao utilizar ferramentas ou APIs (exceto a API .NET), define as entidades da fábrica de dados utilizando o formato JSON. O exemplo JSON: Copiar dados de DB2 para o armazenamento de Blobs do Azure mostram as definições de JSON para as entidades da fábrica de dados que são utilizadas para copiar dados de um arquivo de dados DB2 no local.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizadas para definir as entidades da fábrica de dados que são específicas para um arquivo de dados DB2.

## <a name="db2-linked-service-properties"></a>Propriedades do serviço ligado de DB2
A tabela seguinte lista as propriedades JSON que são específicas para um serviço ligado de DB2.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tipo** |Esta propriedade tem de ser definida **OnPremisesDb2**. |Sim |
| **server** |O nome do servidor DB2. |Sim |
| **database** |O nome da base de dados DB2. |Sim |
| **schema** |O nome do esquema na base de dados DB2. Esta propriedade diferencia maiúsculas de minúsculas. |Não |
| **authenticationType** |O tipo de autenticação que é utilizada para ligar à base de dados DB2. Os valores possíveis são: Anónimo, básico e Windows. |Sim |
| **username** |O nome da conta de utilizador, se usar a autenticação básica ou do Windows. |Não |
| **password** |A palavra-passe da conta de utilizador. |Não |
| **gatewayName** |O nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados de DB2 no local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista das seções e as propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Secções, tal como **estrutura**, **disponibilidade**e o **política** para um conjunto de dados JSON, são semelhantes para todos os tipos de conjunto de dados (Azure SQL, armazenamento de Blobs do Azure, armazenamento de tabelas do Azure e assim por diante).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para um conjunto de dados do tipo **RelationalTable**, que inclui o conjunto de dados DB2, tem a seguinte propriedade:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tableName** |O nome da tabela na instância de base de dados DB2, que o serviço ligado refere-se a. Esta propriedade diferencia maiúsculas de minúsculas. |Não (se o **consulta** propriedade de uma atividade de cópia do tipo **RelationalSource** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade copy
Para obter uma lista das seções e as propriedades disponíveis para a definição de atividades de cópia, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Copiar as propriedades da atividade, tal como **name**, **Descrição**, **entradas** tabela, **produz** tabela, e **política**, estão disponíveis para todos os tipos de atividades. As propriedades que estão disponíveis no **typeProperties** variam de seção da atividade para cada tipo de atividade. Para a atividade de cópia, as propriedades variam consoante os tipos de origens de dados e sinks.

Para a atividade de cópia, quando a origem é do tipo **RelationalSource** (que inclui DB2), as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **query** |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""` |Não (se o **tableName** é especificada a propriedade de um conjunto de dados) |

> [!NOTE]
> Nomes de tabela e esquema diferenciam maiúsculas de minúsculas. A instrução de consulta, coloque os nomes das propriedades utilizando "" (as aspas duplas).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Exemplo JSON: Copiar dados de DB2 para armazenamento de Blobs do Azure
Este exemplo fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). O exemplo mostra como copiar dados de uma base de dados DB2 no armazenamento de Blobs. No entanto, os dados podem ser copiados para [tipo de sink de armazenar todos os dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia de fábrica de dados do Azure.

O exemplo possui as seguintes entidades do Data Factory:

- Serviço do tipo de ligado um DB2 [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Serviço do tipo de ligado um armazenamento de Blobs do Azure [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- R [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza o [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) propriedades

O exemplo copia dados de um resultado de consulta numa base de dados DB2 para um blob do Azure à hora. As propriedades JSON utilizadas no exemplo são descritas nas secções que se seguem as definições de entidade.

Como primeiro passo, instalar e configurar um gateway de dados. As instruções estão no [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado de DB2**

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

**Serviço de ligado de armazenamento de Blobs do Azure**

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

**Conjunto de dados de entrada de DB2**

O exemplo parte do princípio de que criou uma tabela em DB2 com o nome "MyTable" que tem uma coluna intitulada "timestamp" para os dados de séries de tempo.

O **externo** propriedade está definida como "true". Esta definição informa o serviço Data Factory que este conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados. Tenha em atenção que o **tipo** estiver definida como **RelationalTable**.


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

Dados são escritos para um blob novo a cada hora, definindo a **frequência** propriedade como "Hour" e o **intervalo** propriedade como 1. O **folderPath** propriedade para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e partes de hora da hora de início.

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

O pipeline contém uma atividade de cópia que está configurado para utilizar a entrada especificada e conjuntos de dados de saída e que está agendado para ser executado a cada hora. Na definição de JSON do pipeline, o **origem** tipo está definido como **RelationalSource** e o **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **consulta** propriedade seleciona os dados da tabela "Pedidos".

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

## <a name="type-mapping-for-db2"></a>Mapeamento do tipo de DB2
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, atividade de cópia executa conversões de tipo automática do tipo de origem para o tipo de sink usando a abordagem de dois passos seguintes:

1. Converter a partir de um tipo de origem nativas para um tipo de .NET
2. Converter de um tipo .NET para um tipo de sink nativo

Os seguintes mapeamentos são utilizados quando a atividade de cópia converte os dados de um tipo de DB2 para um tipo .NET:

| Tipo de base de dados de DB2 | Tipo de .NET framework |
| --- | --- |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |DateTime |
| Hora |TimeSpan |
| Carimbo de data/hora |DateTime |
| Xml |Byte[] |
| char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binário |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Gráfico |String |
| VarGraphic |String |
| LongVarGraphic |String |
| CLOB |String |
| Blobs |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |DateTime |
| Hora |TimeSpan |
| Carimbo de data/hora |DateTime |
| Xml |Byte[] |
| char |String |

## <a name="map-source-to-sink-columns"></a>Origem do mapa para colunas de sink
Para saber como mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink, veja [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetíveis de fontes relacionais
Quando copia dados a partir de um arquivo de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a repetição **política** propriedade para um conjunto de dados voltar a executar um setor quando ocorre uma falha. Certifique-se de que os mesmos dados é de leitura não importa quantas vezes o setor será novamente executado e, independentemente de como voltar a executar o setor. Para obter mais informações, consulte [Repeatable lê a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Saiba mais sobre os principais fatores que afetam o desempenho de atividade de cópia e formas de otimizar o desempenho na [guia de ajuste e de desempenho de atividade de cópia](data-factory-copy-activity-performance.md).
