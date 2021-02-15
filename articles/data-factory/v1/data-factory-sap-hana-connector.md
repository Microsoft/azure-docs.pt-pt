---
title: Mover dados da SAP HANA usando a Azure Data Factory
description: Saiba como mover dados da SAP HANA usando a Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 9c192a5f84f36fbdea0dd1d280928aa40186d4ee
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386990"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Mover dados da SAP HANA usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-sap-hana-connector.md)
> * [Versão 2 (versão atual)](../connector-sap-hana.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector SAP HANA em V2](../connector-sap-business-warehouse.md).

Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de um SAP HANA no local. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados de uma loja de dados SAP HANA no local para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de um SAP HANA para outras lojas de dados, mas não para transferir dados de outras lojas de dados para um SAP HANA.

## <a name="supported-versions-and-installation"></a>Versões e instalação suportadas
Este conector suporta qualquer versão da base de dados SAP HANA. Suporta a cópia de dados de modelos de informação HANA (tais como vistas analíticas e de cálculo) e tabelas de linha/coluna usando consultas SQL.

Para permitir a conectividade à instância SAP HANA, instale os seguintes componentes:
- **Data Management Gateway**: O serviço data factory suporta a ligação a lojas de dados no local (incluindo SAP HANA) utilizando um componente chamado Data Management Gateway. Para saber mais sobre o Gateway de Gestão de Dados e instruções passo a passo para configurar o gateway, consulte [dados de deslocação entre a loja de dados no local para o artigo da loja de dados na nuvem.](data-factory-move-data-between-onprem-and-cloud.md) O gateway é necessário mesmo que o SAP HANA esteja hospedado numa máquina virtual Azure IaaS (VM). Pode instalar o gateway no mesmo VM que a loja de dados ou num VM diferente, desde que o gateway possa ligar-se à base de dados.
- **Condutor SAP HANA ODBC** na máquina de porta de entrada. Pode transferir o controlador ODBC do SAP HANA a partir do [Centro de Transferências de Software SAP](https://support.sap.com/swdc). Procure com a palavra-chave **SAP HANA CLIENT para windows**. 

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma loja de dados SAP HANA no local utilizando diferentes ferramentas/APIs. 

- A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy. 
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia. 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de um SAP HANA no local, consulte [o exemplo JSON: Copiar dados da secção SAP HANA para Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) deste artigo. 

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas para uma loja de dados SAP HANA:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela a seguir fornece descrição para elementos JSON específicos do serviço ligado à SAP HANA.

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor em que reside a instância SAP HANA. Se o seu servidor estiver a utilizar uma porta personalizada, especifique `server:port` . | string | Yes
authenticationType | Tipo de autenticação. | corda. "Básico" ou "Windows" | Yes 
nome de utilizador | Nome do utilizador que tem acesso ao servidor SAP | string | Yes
palavra-passe | A palavra-passe do utilizador. | string | Yes
gatewayName | Nome do gateway que o serviço data factory deve utilizar para ligar ao local de entrada SAP HANA. | string | Yes
criptografadoCredential | A cadeia de credencial encriptada. | cadeia (de carateres) | No

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. Não existem propriedades específicas do tipo suportadas para o conjunto de dados SAP HANA do tipo **RelationalTable**. 


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, entradas e tabelas de saída, são políticas disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na secção **de tipos de atividade** variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

Quando a origem na atividade de cópia é do tipo **RelationalSource** (que inclui o SAP HANA), as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta SQL para ler dados a partir da instância SAP HANA. | Consulta SQL. | Yes |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Exemplo JSON: Copiar dados da SAP HANA para a Azure Blob
A amostra que se segue fornece definições JSON de amostra que pode utilizar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Esta amostra mostra como copiar dados de um SAP HANA no local para um Azure Blob Storage. No entanto, os dados podem ser copiados **diretamente** para qualquer um dos lavatórios [listados aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia na Fábrica de Dados Azure.  

> [!IMPORTANT]
> Esta amostra fornece snippets JSON. Não inclui instruções passo a passo para a criação da fábrica de dados. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo.

A amostra tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [SapHana.](#linked-service-properties)
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelacionalTable](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de um caso SAP HANA para uma bolha Azure por hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, configurar o portal de gestão de dados. As instruções estão nos [dados móveis entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem.

### <a name="sap-hana-linked-service"></a>Serviço ligado SAP HANA
Este serviço ligado liga a sua instância SAP HANA à fábrica de dados. A propriedade tipo está definida para **SapHana.** A secção typeProperties fornece informações de ligação para a instância SAP HANA.

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
Este serviço ligado liga a sua conta de Armazenamento Azure à fábrica de dados. A propriedade tipo está definida para **AzureStorage**. A secção typeProperties fornece informações de ligação para a conta de Armazenamento Azure.

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

### <a name="sap-hana-input-dataset"></a>Conjunto de dados de entrada SAP HANA

Este conjunto de dados define o conjunto de dados SAP HANA. Define o tipo de conjunto de dados da Data Factory para **RelationalTable**. Atualmente, não especifica quaisquer propriedades específicas do tipo para um conjunto de dados SAP HANA. A consulta na definição de Atividade de Cópia especifica quais os dados a ler a partir da instância SAP HANA. 

A fixação de propriedade externa a verdadeiro informa o serviço Data Factory de que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

As propriedades de frequência e intervalo definem o horário. Neste caso, os dados são lidos a partir do caso SAP HANA de hora a hora. 

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
Este conjunto de dados define o conjunto de dados Azure Blob de saída. A propriedade tipo está definida para AzureBlob. A secção typeProperties fornece onde os dados copiados da instância SAP HANA são armazenados. Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

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


### <a name="pipeline-with-copy-activity"></a>Pipeline com atividade de Copy

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição de JSON do gasoduto, o tipo **de fonte** é definido para **RelationalSource** (para fonte SAP HANA) e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **de consulta** seleciona os dados na hora passada para copiar.

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


### <a name="type-mapping-for-sap-hana"></a>Tipo de mapeamento para SAP HANA
Conforme mencionado no artigo [de atividades](data-factory-data-movement-activities.md) de movimento de dados, a atividade copy realiza conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem em duas etapas:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter de tipo .NET para tipo de pia nativa

Ao mover dados do SAP HANA, os seguintes mapeamentos são utilizados dos tipos SAP HANA para os tipos .NET.

Tipo SAP HANA | .NET Tipo de base
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | Único
DUPLO | Único
DECIMAL | Decimal
BOOLEANA | Byte
RIO VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
DATE | DateTime
TIME | TimeSpan
CARIMBO DE DATA/HORA | DateTime
SEGUNDADATA | DateTime

## <a name="known-limitations"></a>Limitações conhecidas
Existem algumas limitações conhecidas ao copiar dados da SAP HANA:

- As cadeias NVARCHAR são truncadas para o comprimento máximo de 4000 carateres Unicode
- SMALLDECIMAL não é suportado
- VARBINARY não é suportado
- As Datas Válidas estão entre 30/12/1899 e 31/12/9999

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para obter informações sobre as colunas de mapeamento em conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.
