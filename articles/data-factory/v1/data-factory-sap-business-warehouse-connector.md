---
title: Mova dados do SAP Business Warehouse utilizando a Azure Data Factory
description: Saiba como mover dados do SAP Business Warehouse utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 108bdf057cd375e28b10a6838ec5c8c6f57749a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281057"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Mova dados do SAP Business Warehouse utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-sap-business-warehouse-connector.md)
> * [Versão 2 (versão atual)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector SAP Business Warehouse em V2](../connector-sap-business-warehouse.md).


Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de um Armazém De Negócios SAP (BW) no local. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de uma loja de dados sap business warehouse no local para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de um Armazém de Negócios SAP para outras lojas de dados, mas não para transferir dados de outras lojas de dados para um Armazém de Negócios SAP. 

## <a name="supported-versions-and-installation"></a>Versões e instalação suportadas
Este conector suporta a versão 7.x do SAP Business Warehouse. Suporta copiar dados de InfoCubes e QueryCubes (incluindo consultas BEx) utilizando consultas MDX.

Para permitir a conectividade com a instância SAP BW, instale os seguintes componentes:
- **Gateway de Gestão de Dados**: O serviço data Factory suporta a ligação a lojas de dados no local (incluindo o SAP Business Warehouse) utilizando um componente chamado Data Management Gateway. Para conhecer o Portal de Gestão de Dados e instruções passo a passo para a configuração do gateway, consulte a moving data store entre a loja de dados no local para o artigo da loja de [dados em nuvem.](data-factory-move-data-between-onprem-and-cloud.md) O gateway é necessário mesmo que o SAP Business Warehouse esteja hospedado numa máquina virtual Azure IaaS (VM). Pode instalar o portal no mesmo VM que o depósito de dados ou num VM diferente, desde que o portal possa ligar-se à base de dados.
- **Biblioteca SAP NetWeaver** na máquina de porta de entrada. Pode obter a biblioteca SAP Netweaver do seu administrador SAP, ou diretamente do Centro de [Descarregamento](https://support.sap.com/swdc)de Software SAP . Procure o **SAP Note #1025361** para obter o local de descarregamento para a versão mais recente. Certifique-se de que a arquitetura da biblioteca SAP NetWeaver (32-bits ou 64 bits) corresponde à instalação do gateway. Em seguida, instale todos os ficheiros incluídos no SAP NetWeaver RFC SDK de acordo com a Nota SAP. A biblioteca SAP NetWeaver também está incluída na instalação sap Client Tools.

> [!TIP]
> Coloque os dlls extraídos da pasta NetWeaver RFC SDK no sistema32.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que transfere dados de uma loja de dados Cassandra no local utilizando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy. 
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia. 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de um Armazém De Negócios SAP no local, consulte o [exemplo da JSON: Copiar dados do SAP Business Warehouse para a secção Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) deste artigo. 

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades data Factory específicas de uma loja de dados SAP BW:

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao SAP Business Warehouse (BW).

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor em que reside a instância SAP BW. | string | Sim
sistemaNúmero | Número do sistema do sistema SAP BW. | Número de cimacimal de dois dígitos representado como uma corda. | Sim
clientId | Identificação do cliente do cliente no sistema SAP W. | Número de cimacimal de três dígitos representado como uma corda. | Sim
o nome de utilizador | Nome do utilizador que tem acesso ao servidor SAP | string | Sim
palavra-passe | A palavra-passe do utilizador. | string | Sim
nome gateway | Nome do portal que o serviço Data Factory deve utilizar para ligar à instância SAP BW no local. | string | Sim
credenta encriptado | A corda credencial encriptada. | string | Não

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. Não existem propriedades específicas do tipo suportadas para o conjunto de dados SAP BW do tipo **RelationalTable**. 


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, são políticas disponíveis para todos os tipos de atividades.

Considerando que as propriedades disponíveis na secção **typeProperties** da atividade variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

Quando a fonte na atividade de cópia é do tipo **RelationalSource** (que inclui SAP BW), as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta MDX para ler os dados da instância SAP BW. | Consulta MDX. | Sim |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Exemplo jSON: Copiar dados do SAP Business Warehouse para O Blob Azure
O exemplo que se segue fornece definições jSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Esta amostra mostra como copiar dados de um Armazém De Negócios SAP no local para um Armazenamento De Blob Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.  

> [!IMPORTANT]
> Esta amostra fornece cortes JSON. Não inclui instruções passo a passo para a criação da fábrica de dados. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo.

A amostra tem as seguintes entidades fabris de dados:

1. Um serviço ligado do tipo [SapBw.](#linked-service-properties)
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de um caso do SAP Business Warehouse para uma bolha Azure de hora em hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, configurar o portal de gestão de dados. As instruções estão nos [dados em movimento entre os locais no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem.

### <a name="sap-business-warehouse-linked-service"></a>Serviço ligado ao Armazém De Negócios SAP
Este serviço ligado liga a sua instância SAP BW à fábrica de dados. A propriedade tipo está definida para **SapBw**. A secção TypeProperties fornece informações de ligação para a instância SAP BW. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Este serviço ligado liga a sua conta de Armazenamento Azure à fábrica de dados. A propriedade tipo está definida para **AzureStorage**. A secção TypeProperties fornece informações de ligação para a conta De armazenamento Azure.

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

### <a name="sap-bw-input-dataset"></a>Conjunto de dados de entrada SAP BW
Este conjunto de dados define o conjunto de dados do SAP Business Warehouse. Define o tipo de conjunto de dados da Fábrica de Dados para **Relacional .** Atualmente, não especifica quaisquer propriedades específicas do tipo para um conjunto de dados SAP BW. A consulta na definição de Atividade de Cópia especifica quais os dados a ler a partir da instância SAP BW. 

A fixação de propriedade externa a verdadeira informa o serviço Data Factory de que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

As propriedades de frequência e intervalo definem o horário. Neste caso, os dados são lidos a partir da instância SAP BW de hora a hora. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
Este conjunto de dados define o conjunto de dados de saída Azure Blob. A propriedade tipo está definida para AzureBlob. A secção TypeProperties fornece onde os dados copiados da instância SAP BW são armazenados. Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline com atividade de Cópia
O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **RelationalSource** (para fonte SAP BW) e o tipo de **pia** é definido para **BlobSink**. A consulta especificada para a propriedade de **consulta** seleciona os dados da última hora para copiar.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>Tipo de mapeamento para SAP BW
Conforme mencionado no artigo de atividades de movimento de [dados,](data-factory-data-movement-activities.md) a atividade de cópia realiza conversões automáticas de tipos de origem para tipos de sink com a seguinte abordagem em duas etapas:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter do tipo .NET para o tipo de pia nativa

Ao mover dados do SAP BW, os seguintes mapeamentos são utilizados de tipos De BW SAP para tipos .NET.

Tipo de dados no Dicionário ABAP | Tipo de dados .NET
-------------------------------- | --------------
ACCP |  int
CHAR | Cadeia
CLNT | Cadeia
CURR | Decimal
CUKY | Cadeia
DEZ | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | int
RIO LANG | Cadeia
LCHR | Cadeia
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
CRU | Byte[]
CADEIA BRUTA | Byte[]
CORDA | Cadeia
UNIDADE | Cadeia
DATS | Cadeia
NUMC | Cadeia
TIMS | Cadeia

> [!NOTE]
> Para mapear colunas desde o conjunto de dados de origem até colunas a partir de conjunto de dados de sumidouro, consulte [colunas de conjunto de dados de mapeamento na Fábrica](data-factory-map-columns.md)de Dados Azure .


## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para aprender sobre as colunas de mapeamento em conjunto de dados de origem para colunas em conjunto de dados de sumidouro, consulte [mapeando colunas](data-factory-map-columns.md)de conjunto de dados na Azure Data Factory .

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetível para evitar resultados não intencionais. Na Azure Data Factory, pode reproduzir uma fatia manualmente. Também pode configurar a política de retry para um conjunto de dados para que uma fatia seja reexecutada quando ocorre uma falha. Quando uma fatia é reexecutada de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [Leitura repetível a partir de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
