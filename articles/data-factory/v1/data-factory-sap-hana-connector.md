---
title: Mover dados do SAP HANA com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados do SAP HANA com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 96d16552cfadca9b345d0f0cd0a344249897f571
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258441"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Mover dados do SAP HANA com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-sap-hana-connector.md)
> * [Versão 2 (versão atual)](../connector-sap-hana.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector do SAP HANA no V2](../connector-sap-business-warehouse.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados do SAP HANA no local. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados do SAP HANA no local para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. O Data factory suporta, atualmente, apenas mover dados de um SAP HANA para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um SAP HANA.

## <a name="supported-versions-and-installation"></a>Versões suportadas e instalação
Este conector suporta qualquer versão da base de dados do SAP HANA. Ele oferece suporte a cópia de dados de modelos de informação HANA (como as vistas análise e cálculo) e tabelas de linha/coluna usando consultas SQL.

Para ativar a conectividade à instância do SAP HANA, instale os seguintes componentes:
- **O Data Management Gateway**: Serviço do Data Factory suporta a conexão com fontes de dados no local (incluindo SAP HANA) usando um componente chamado Data Management Gateway. Para saber mais sobre o Gateway de gestão de dados e instruções passo a passo sobre como configurar o gateway, veja [arquivo de dados de migração entre dados no local ao arquivo de dados da cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo. Gateway é necessário, mesmo que o SAP HANA está alojado numa máquina virtual (VM) IaaS do Azure. Pode instalar o gateway na mesma VM como o arquivo de dados ou numa VM diferente, desde que o gateway consiga estabelecer ligação à base de dados.
- **Controlador ODBC do SAP HANA** no computador gateway. Pode transferir o controlador ODBC do SAP HANA do [SAP Software Download Center](https://support.sap.com/swdc). Pesquisa com a palavra-chave **SAP HANA cliente para Windows**. 

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de um arquivo de dados do SAP HANA no local através de APIs/ferramentas diferentes. 

- A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados. 
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados a partir do SAP HANA no local, consulte [exemplo de JSON: Copiar dados do SAP HANA para BLOBs do Azure](#json-example-copy-data-from-sap-hana-to-azure-blob) seção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para um arquivo de dados do SAP HANA:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado do SAP HANA.

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor no qual reside a instância do SAP HANA. Se o servidor estiver a utilizar uma porta personalizada, especifique `server:port`. | string | Sim
authenticationType | Tipo de autenticação. | cadeia de caracteres. "Básico" ou "Windows" | Sim 
o nome de utilizador | Nome de utilizador que tem acesso ao servidor SAP | string | Sim
palavra-passe | A palavra-passe do utilizador. | string | Sim
gatewayName | Nome do gateway que o serviço Data Factory deve utilizar para ligar à instância de SAP HANA no local. | string | Sim
encryptedCredential | A cadeia de credencial encriptada. | string | Não

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Não há específicos do tipo propriedades suportados para o conjunto de dados do SAP HANA typu **RelationalTable**. 


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, tabelas de entrada e saídas, são as políticas estão disponíveis para todos os tipos de atividades.

Ao passo que, propriedades disponíveis no **typeProperties** secção da atividade varia com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Quando a origem na atividade de cópia é do tipo **RelationalSource** (que inclui o SAP HANA), as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta SQL para ler dados a partir da instância do SAP HANA. | Consulta SQL. | Sim |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Exemplo JSON: Copiar dados do SAP HANA para BLOBs do Azure
O exemplo a seguir fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Este exemplo mostra como copiar dados do SAP HANA no local para um armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** a qualquer uma dos sinks listados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.  

> [!IMPORTANT]
> Este exemplo fornece trechos JSON. Não inclui instruções passo a passo para criar a fábrica de dados. Ver [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo.

O exemplo possui as seguintes entidades do data factory:

1. Um serviço ligado do tipo [SapHana](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [RelationalTable](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma instância do SAP HANA para um blob do Azure à hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

Como primeiro passo, configure o data management gateway. As instruções estão no [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo.

### <a name="sap-hana-linked-service"></a>Serviço ligado do SAP HANA
Este serviço ligado liga a sua instância do SAP HANA para a fábrica de dados. A propriedade de tipo está definida como **SapHana**. A secção typeProperties fornece informações de ligação para a instância do SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Este serviço ligado liga a conta de armazenamento do Azure à fábrica de dados. A propriedade de tipo está definida como **AzureStorage**. A secção typeProperties fornece informações de ligação para a conta de armazenamento do Azure.

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="sap-hana-input-dataset"></a>Conjunto de dados de entrada de SAP HANA

Este conjunto de dados define o conjunto de dados do SAP HANA. Definir o tipo de conjunto de dados de fábrica de dados para **RelationalTable**. Atualmente, não especifique quaisquer propriedades de tipo específicas para um conjunto de dados do SAP HANA. A consulta na definição da atividade de cópia Especifica quais os dados para ler a partir da instância do SAP HANA. 

A definição de propriedade externa como true informa o serviço Data Factory, que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

Propriedades de frequências e intervalos define a agenda. Neste caso, os dados são lidos a partir da instância do SAP HANA à hora. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure
Este conjunto de dados define o conjunto de dados de Blobs do Azure de saída. A propriedade de tipo está definida para AzureBlob. A secção typeProperties fornece onde estão armazenados os dados copiados a partir da instância do SAP HANA. Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline com atividade de cópia

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **RelationalSource** (para a origem do SAP HANA) e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **consulta** propriedade seleciona os dados na hora anterior para copiar.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Mapeamento de tipo para o SAP HANA
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos com a abordagem de dois passos seguintes:

1. Converter entre tipos de origem nativas para o tipo de .NET
2. Converter de tipo de .NET para o tipo de sink nativo

Ao mover dados do SAP HANA, os seguintes mapeamentos de servem de tipos de SAP HANA para tipos .NET.

Tipo do SAP HANA | .NET com base em tipo
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | Single
VALOR DE DUPLO | Single
DECIMAL | Decimal
VALOR BOOLEANO | Byte
VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
DATA | DateTime
HORA | TimeSpan
CARIMBO DE DATA/HORA | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Limitações conhecidas
Existem algumas limitações conhecidas quando se copiam dados a partir do SAP HANA:

- As cadeias NVARCHAR são truncadas para o comprimento máximo de 4000 carateres Unicode
- SMALLDECIMAL não é suportado
- VARBINARY não é suportada
- Datas válidas estão entre 1899/12/30 e 9999/12/31

## <a name="map-source-to-sink-columns"></a>Origem do mapa para colunas de sink
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem para colunas no conjunto de dados de sink, veja [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura passível de repetição de fontes relacionais
Quando armazena a cópia de dados de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor será novamente executado de qualquer forma, terá de certificar-se de que os mesmos dados é de leitura não questão número de vezes que um setor é executado. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
