---
title: Mover dados do Teradata com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre o conector de Teradata para o serviço Data Factory, que lhe permite mover dados de base de dados Teradata
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 98eb76d8-5f3d-4667-b76e-e59ed3eea3ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d22318f4d9e233a57d521fe36f0827b9fc3af3e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610748"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Mover dados do Teradata com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-onprem-teradata-connector.md)
> * [Versão 2 (versão atual)](../connector-teradata.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de Teradata no V2](../connector-teradata.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de uma base de dados do Teradata no local. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados Teradata no local para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. O Data factory suporta, atualmente, apenas mover dados de um arquivo de dados Teradata para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um arquivo de dados Teradata.

## <a name="prerequisites"></a>Pré-requisitos
O Data factory suporta a ligação a origens de Teradata no local através do Gateway de gestão de dados. Ver [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para saber mais sobre o Gateway de gestão de dados e instruções passo a passo sobre como configurar o gateway.

Gateway é necessário mesmo se a Teradata está hospedado numa VM de IaaS do Azure. Pode instalar o gateway na mesma VM de IaaS, como o arquivo de dados ou numa VM diferente, desde que o gateway consiga estabelecer ligação à base de dados.

> [!NOTE]
> Ver [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter sugestões sobre resolução de problemas do gateway de ligação/problemas relacionados com.

## <a name="supported-versions-and-installation"></a>Versões suportadas e instalação
Para o Data Management Gateway ligar à base de dados Teradata, tem de instalar o [fornecedor de dados do .NET para Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) versão 14 ou superior no mesmo sistema, como o Data Management Gateway. Teradata versão 12 e acima é suportada.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de um arquivo de dados do Cassandra no local através de APIs/ferramentas diferentes.

- A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados Teradata no local, consulte [exemplo de JSON: Copiar dados do Teradata para BLOBs do Azure](#json-example-copy-data-from-teradata-to-azure-blob) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para um arquivo de dados Teradata:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado de Teradata.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| type |A propriedade de tipo tem de ser definida como: **OnPremisesTeradata** |Sim |
| server |Nome do servidor de Teradata. |Sim |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados Teradata. Os valores possíveis são: Anónimo, básico e Windows. |Sim |
| username |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| password |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do Teradata no local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Atualmente, não há nenhuma propriedade de tipo suportada para o conjunto de dados Teradata.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e as políticas estão disponíveis para todos os tipos de atividades.

Ao passo que, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Quando a origem é do tipo **RelationalSource** (que inclui a Teradata), as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| query |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable. |Sim |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>Exemplo JSON: Copiar dados do Teradata para BLOBs do Azure
O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do Teradata para armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

O exemplo possui as seguintes entidades do data factory:

1. Um serviço ligado do tipo [OnPremisesTeradata](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [RelationalTable](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta na base de dados Teradata para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

Como primeiro passo, configure o data management gateway. As instruções estão no [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado do Teradata:**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Serviço ligado do armazenamento de Blobs do Azure:**

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

**Conjunto de dados entrado de Teradata:**

O exemplo assume que criou uma tabela "MyTable" Teradata e contém uma coluna chamada "timestamp" para dados de séries de tempo.

A definição "externo": true informa o serviço Data Factory, que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
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

**Conjunto de dados de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada uma vez por hora. No pipeline de definição de JSON, o **origem** tipo está definido como **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **consulta** propriedade seleciona os dados na hora anterior para copiar.

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
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
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Mapeamento de tipo para Teradata
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos com a seguinte abordagem de passo 2:

1. Converter entre tipos de origem nativas para o tipo de .NET
2. Converter de tipo de .NET para o tipo de sink nativo

Ao mover dados para Teradata, os seguintes mapeamentos de servem de Teradata tipo para tipo .NET.

| Tipo de base de dados Teradata | Tipo de .NET framework |
| --- | --- |
| char |String |
| CLOB |String |
| Gráfico |String |
| VarChar |String |
| VarGraphic |String |
| Blobs |Byte[] |
| Byte |Byte[] |
| VarByte |Byte[] |
| BigInt |Int64 |
| ByteInt |Int16 |
| Decimal |Decimal |
| Double |Double |
| Número inteiro |Int32 |
| Number |Double |
| SmallInt |Int16 |
| Date |DateTime |
| Hora |TimeSpan |
| Hora com fuso horário |String |
| Carimbo de data/hora |DateTime |
| Timestamp com o fuso horário |DateTimeOffset |
| Dia do intervalo |TimeSpan |
| Dia de intervalo para a hora |TimeSpan |
| Dia de intervalo de minuto |TimeSpan |
| Dia do intervalo como segundo |TimeSpan |
| Hora do intervalo |TimeSpan |
| Intervalo de hora para minuto |TimeSpan |
| Hora do intervalo como segundo |TimeSpan |
| Minuto do intervalo |TimeSpan |
| Intervalo de minuto para segundo |TimeSpan |
| Intervalo de segundo |TimeSpan |
| Ano de intervalo |String |
| Intervalo de ano para mês |String |
| Mês de intervalo |String |
| Period(Date) |String |
| Period(Time) |String |
| Período (hora com fuso horário) |String |
| Period(Timestamp) |String |
| Período (Timestamp com o fuso horário) |String |
| Xml |String |

## <a name="map-source-to-sink-columns"></a>Origem do mapa para colunas de sink
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem para colunas no conjunto de dados de sink, veja [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura passível de repetição de fontes relacionais
Quando armazena a cópia de dados de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor será novamente executado de qualquer forma, terá de certificar-se de que os mesmos dados é de leitura não questão número de vezes que um setor é executado. Ver [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
