---
title: Mover dados do Sybase usando o Azure Data Factory
description: Saiba mais sobre como mover dados do banco de dados Sybase usando Azure Data Factory.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929027"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Mover dados do Sybase usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-onprem-sybase-connector.md)
> * [Versão 2 (versão atual)](../connector-sybase.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do Sybase na v2](../connector-sybase.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de um banco de dado Sybase local. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados local do Sybase para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Atualmente, o data Factory dá suporte apenas à movimentação de dados de um armazenamento de dados Sybase para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um armazenamento de dados Sybase. 

## <a name="prerequisites"></a>Pré-requisitos
Data Factory serviço dá suporte à conexão com fontes Sybase locais usando o gateway de Gerenciamento de Dados. Confira o artigo [movendo dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre gerenciamento de dados gateway e instruções passo a passo sobre como configurar o gateway.

O gateway é necessário mesmo que o banco de dados Sybase esteja hospedado em uma VM IaaS do Azure. Você pode instalar o gateway na mesma VM IaaS que o armazenamento de dados ou em uma VM diferente, contanto que o gateway possa se conectar ao banco de dado.

> [!NOTE]
> Consulte [solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre como solucionar problemas relacionados à conexão/gateway.

## <a name="supported-versions-and-installation"></a>Versões e instalação com suporte
Para Gerenciamento de Dados gateway para se conectar ao banco de dados Sybase, você precisa instalar o [provedor de data para Sybase iAnywhere. Data. SQLem qualquer lugar](https://go.microsoft.com/fwlink/?linkid=324846) 16 ou superior no mesmo sistema que o Gateway de gerenciamento de dados. 

Há suporte para o SAP Sybase SQL Anywhere (ASA) versão 16 e superior; IQ e ASE não têm suporte.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de um armazenamento de dados Cassandra local usando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados. 
- Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. 

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de um armazenamento de dados local do Sybase, confira a seção [exemplo de JSON: copiar dados do Sybase para o blob do Azure](#json-example-copy-data-from-sybase-to-azure-blob) deste artigo. 

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para um armazenamento de dados Sybase:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado do Sybase.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **OnPremisesSybase** |Sim |
| servidor |Nome do servidor Sybase. |Sim |
| base de dados |Nome do banco de dados Sybase. |Sim |
| schema |Nome do esquema no banco de dados. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados Sybase. Os valores possíveis são: Anonymous, Basic e Windows. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados Sybase local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção typeproperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção **typeproperties** para o conjunto de um do tipo **RelationalTable** (que inclui o conjunto de uma do Sybase) tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados Sybase à qual o serviço vinculado se refere. |Não (se a **consulta** de **RelationalSource** for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na seção typeproperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Quando a fonte é do tipo **RelationalSource** (que inclui o Sybase), as seguintes propriedades estão disponíveis na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: selecione * em MyTable. |Não (se **TableName** de **DataSet** for especificado) |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>Exemplo de JSON: copiar dados do Sybase para o blob do Azure
O exemplo a seguir fornece exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do banco de dado Sybase para o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.   

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties).
2. Um serviço curtido do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [RelationalTable](data-factory-onprem-sybase-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [RelationalSource](data-factory-onprem-sybase-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta no banco de dado Sybase para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [movendo dados entre os locais e a nuvem do local](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado do Sybase:**

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

**Serviço vinculado do armazenamento de BLOBs do Azure:**

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

**Conjunto de dados de entrada Sybase:**

O exemplo supõe que você criou uma tabela "MyTable" no Sybase e que ela contém uma coluna chamada "timestamp" para dados de série temporal.

A configuração "external": true informa ao serviço de Data Factory que esse conjunto de DataSet é externo ao data factory e não é produzido por uma atividade no data factory. Observe que o **tipo** do serviço vinculado está definido como: **RelationalTable**.

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

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada por hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **RelationalSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **Query** seleciona os dados do DBA. Tabela de pedidos no banco de dados.

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

## <a name="type-mapping-for-sybase"></a>Mapeamento de tipo para Sybase
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

O Sybase dá suporte a tipos T-SQL e T-SQL. Para uma tabela de mapeamento de tipos SQL para o tipo .NET, consulte o artigo [conector do SQL do Azure](data-factory-azure-sql-connector.md) .

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber mais sobre como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, confira [mapeando colunas Azure data Factory do conjunto de](data-factory-map-columns.md)informações

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
