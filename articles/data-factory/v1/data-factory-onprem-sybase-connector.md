---
title: Mover dados de Sybase com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados de base de dados Sybase com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: b379ee10-0ff5-4974-8c87-c95f82f1c5c6
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0de8d4145ff41b498149774af8ed74c56375dea9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605188"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Mover dados de Sybase com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-onprem-sybase-connector.md)
> * [Versão 2 (versão atual)](../connector-sybase.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de Sybase no V2](../connector-sybase.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de uma base de dados de Sybase no local. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados Sybase no local para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. O Data factory suporta, atualmente, apenas mover dados de um arquivo de dados Sybase para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um arquivo de dados Sybase. 

## <a name="prerequisites"></a>Pré-requisitos
Serviço do Data Factory suporta a ligação a origens de Sybase no local com o Data Management Gateway. Ver [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para saber mais sobre o Gateway de gestão de dados e instruções passo a passo sobre como configurar o gateway.

Gateway é necessário mesmo se a base de dados Sybase está hospedado numa VM de IaaS do Azure. Pode instalar o gateway na mesma VM de IaaS, como o arquivo de dados ou numa VM diferente, desde que o gateway consiga estabelecer ligação à base de dados.

> [!NOTE]
> Ver [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter sugestões sobre resolução de problemas do gateway de ligação/problemas relacionados com.

## <a name="supported-versions-and-installation"></a>Versões suportadas e instalação
Para o Data Management Gateway ligar à base de dados Sybase, tem de instalar o [fornecedor de dados para ianywhere. data de Sybase](https://go.microsoft.com/fwlink/?linkid=324846) 16 ou superior no mesmo sistema, como o Data Management Gateway. 

SAP Sybase SQL em qualquer lugar (ASA) versão 16 e acima é suportada; QI e ASE não são suportadas.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de um arquivo de dados do Cassandra no local através de APIs/ferramentas diferentes. 

- A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados. 
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados Sybase no local, consulte [exemplo de JSON: Copiar dados de Sybase para BLOBs do Azure](#json-example-copy-data-from-sybase-to-azure-blob) seção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para um arquivo de dados Sybase:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado do Sybase.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| type |A propriedade de tipo tem de ser definida como: **OnPremisesSybase** |Sim |
| server |Nome do servidor Sybase. |Sim |
| database |Nome da base de dados Sybase. |Sim |
| schema |Nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados Sybase. Os valores possíveis são: Anónimo, básico e Windows. |Sim |
| username |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| password |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados de Sybase no local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados Sybase) tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados Sybase pelo serviço ligado refere-se. |Não (se **consulta** dos **RelationalSource** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

Ao passo que, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Quando a origem é do tipo **RelationalSource** (que inclui Sybase), as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| query |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable. |Não (se **tableName** dos **conjunto de dados** for especificado) |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>Exemplo JSON: Copiar dados de Sybase para BLOBs do Azure
O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de base de dados Sybase para o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.   

O exemplo possui as seguintes entidades do data factory:

1. Um serviço ligado do tipo [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties).
2. Um serviço liked do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [RelationalTable](data-factory-onprem-sybase-connector.md#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [RelationalSource](data-factory-onprem-sybase-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta na base de dados Sybase para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

Como primeiro passo, configure o data management gateway. As instruções estão no [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado do Sybase:**

```JSON
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
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

**Serviço ligado do armazenamento de Blobs do Azure:**

```JSON
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

**Conjunto de dados entrado de Sybase:**

O exemplo assume que criou uma tabela "MyTable" Sybase e contém uma coluna chamada "timestamp" para dados de séries de tempo.

A definição "externo": true informa o serviço Data Factory, que este conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados. Tenha em atenção que o **tipo** do serviço ligado está definida como: **RelationalTable**.

```JSON
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
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

**Conjunto de dados de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

```JSON
{
    "name": "AzureBlobSybaseDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada uma vez por hora. No pipeline de definição de JSON, o **origem** tipo está definido como **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **consulta** propriedade seleciona os dados do DBA. Tabela Orders no banco de dados.

```JSON
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from DBA.Orders"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "SybaseDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobSybaseDataSet"
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
                "name": "SybaseToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-sybase"></a>Mapeamento do tipo de Sybase
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos com a seguinte abordagem de passo 2:

1. Converter entre tipos de origem nativas para o tipo de .NET
2. Converter de tipo de .NET para o tipo de sink nativo

Sybase suporta T-SQL e tipos de T-SQL. Para uma tabela de mapeamento de tipos de sql para o tipo .NET, consulte [conector do SQL Azure](data-factory-azure-sql-connector.md) artigo.

## <a name="map-source-to-sink-columns"></a>Origem do mapa para colunas de sink
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem para colunas no conjunto de dados de sink, veja [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura passível de repetição de fontes relacionais
Quando armazena a cópia de dados de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor será novamente executado de qualquer forma, terá de certificar-se de que os mesmos dados é de leitura não questão número de vezes que um setor é executado. Ver [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
