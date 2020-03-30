---
title: Mova dados da Sybase usando a Azure Data Factory
description: Saiba como mover dados da Base de Dados Sybase utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: b379ee10-0ff5-4974-8c87-c95f82f1c5c6
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cefa0c15dd50f95780034dcb63f888a2e1c6b65e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281252"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Mova dados da Sybase usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-sybase-connector.md)
> * [Versão 2 (versão atual)](../connector-sybase.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Sybase em V2](../connector-sybase.md).

Este artigo explica como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de uma base de dados sybase no local. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de uma loja de dados sybase no local para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de uma loja de dados Da Sybase para outras lojas de dados, mas não para transferir dados de outras lojas de dados para uma loja de dados Da Sybase. 

## <a name="prerequisites"></a>Pré-requisitos
O serviço Data Factory suporta a ligação a fontes sybase no local utilizando o Portal de Gestão de Dados. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para saber sobre o Gateway de Gestão de Dados e instruções passo a passo sobre a configuração do portal.

O gateway é necessário mesmo que a base de dados Sybase esteja hospedada num VM Azure IaaS. Pode instalar o portal no mesmo IaaS VM que o depósito de dados ou num VM diferente, desde que o portal possa ligar-se à base de dados.

> [!NOTE]
> Consulte problemas de [gateway de Troubleshoot](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre questões relacionadas com ligação a problemas/gateway.

## <a name="supported-versions-and-installation"></a>Versões e instalação suportadas
Para que o Portal de Gestão de Dados se conecte à Base de Dados Sybase, é necessário instalar o fornecedor de [dados para sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 ou superior no mesmo sistema que o Portal de Gestão de Dados. 

A versão 16 (ASA) sql (ASA) sap Sql (ASA) é suportada; O QI e a ASE não são suportados.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que transfere dados de uma loja de dados Cassandra no local utilizando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy. 
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia. 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados Sybase no local, consulte o [exemplo da JSON: Copiar dados da Sybase para a secção Azure Blob](#json-example-copy-data-from-sybase-to-azure-blob) deste artigo. 

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades data Factory específicas de uma loja de dados Sybase:

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado à Sybase.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesSybase** |Sim |
| servidor |Nome do servidor Sybase. |Sim |
| base de dados |Nome da base de dados da Base de Dados Sybase. |Sim |
| schema |Nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados da Sybase. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Basic ou Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar à base de dados sybase no local. |Sim |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **TypeProperties** para conjunto de dados do tipo **RelationalTable** (que inclui conjunto de dados Sybase) tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância base de dados sybase a que o serviço ligado se refere. |Não (se for especificada **a consulta** do **RelationalSource)** |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

Considerando que as propriedades disponíveis na secção typeProperties da atividade variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

Quando a fonte é do tipo **RelationalSource** (que inclui A Base Sybase), as seguintes propriedades estão disponíveis na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: selecione * do MyTable. |Não (se o nome do **conjunto** de **dados** for especificado) |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>Exemplo jSON: Copiar dados da Sybase para O Blob Azure
O exemplo que se segue fornece definições jSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados da base de dados da Sybase para o Armazenamento De Blob Azure. No entanto, os dados podem ser copiados para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.   

A amostra tem as seguintes entidades fabris de dados:

1. Um serviço de tipo [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties).
2. Um serviço apreciado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](data-factory-onprem-sybase-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [RelationalSource](data-factory-onprem-sybase-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de uma consulta resultam na base de dados da Sybase a uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, configurar o portal de gestão de dados. As instruções estão nos [dados em movimento entre os locais no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem.

**Serviço ligado à Base Sybase:**

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

**Serviço ligado ao armazenamento Azure Blob:**

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

**Conjunto de dados de entrada de base sybase:**

A amostra pressupõe que criou uma tabela "MyTable" em Sybase e contém uma coluna chamada "timestamp" para dados da série time.

Definição "externa": informa verdadeiramente o serviço Data Factory de que este conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados. Note que o **tipo** do serviço ligado está definido para: **RelationalTable**.

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

**Conjunto de dados de saída de Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

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

**Pipeline com atividade de Cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado de hora a hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **RelationalSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados do DBA. Tabela de encomendas na base de dados.

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

## <a name="type-mapping-for-sybase"></a>Tipo de mapeamento para Sybase
Conforme mencionado no artigo Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) a atividade copy realiza conversões automáticas de tipos de origem para tipos de sink com a seguinte abordagem de 2 passos:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter do tipo .NET para o tipo de pia nativa

A Base Sybase suporta os tipos T-SQL e T-SQL. Para uma tabela de mapeamento de tipos de sql para o tipo .NET, consulte o artigo [do Conector Azure SQL.](data-factory-azure-sql-connector.md)

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para aprender sobre as colunas de mapeamento em conjunto de dados de origem para colunas em conjunto de dados de sumidouro, consulte [mapeando colunas](data-factory-map-columns.md)de conjunto de dados na Azure Data Factory .

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetível para evitar resultados não intencionais. Na Azure Data Factory, pode reproduzir uma fatia manualmente. Também pode configurar a política de retry para um conjunto de dados para que uma fatia seja reexecutada quando ocorre uma falha. Quando uma fatia é reexecutada de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [Leitura repetível a partir de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
