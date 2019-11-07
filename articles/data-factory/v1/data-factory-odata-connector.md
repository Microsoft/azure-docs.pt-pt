---
title: Mover dados de fontes OData
description: Saiba mais sobre como mover dados de fontes OData usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ea751a18f8a5e5423b3199919ccf440c41595091
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666673"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Mover dados de uma fonte OData usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-odata-connector.md)
> * [Versão 2 (versão atual)](../connector-odata.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector OData na v2](../connector-odata.md).


Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de uma fonte OData. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de uma fonte OData para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Atualmente, o data Factory dá suporte apenas à movimentação de dados de uma fonte OData para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para uma fonte OData.

## <a name="supported-versions-and-authentication-types"></a>Tipos de autenticação e versões com suporte
Este conector OData dá suporte a OData versão 3,0 e 4,0, e você pode copiar dados do OData de nuvem e de fontes OData locais. Para o último, você precisa instalar o gateway de Gerenciamento de Dados. Confira o artigo [mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre gerenciamento de dados gateway.

Há suporte para os tipos de autenticação abaixo:

* Para acessar o feed OData de **nuvem** , você pode usar a autenticação anônima, básica (nome de usuário e senha) ou Azure Active Directory com base no OAuth.
* Para acessar o feed OData **local** , você pode usar a autenticação anônima, básica (nome de usuário e senha) ou Windows.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de uma fonte OData usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de uma fonte OData, confira a seção [exemplo de JSON: copiar dados do OData Source para o blob do Azure](#json-example-copy-data-from-odata-source-to-azure-blob) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para a fonte OData:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do OData.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **OData** |Sim |
| url |URL do serviço OData. |Sim |
| authenticationType |Tipo de autenticação usado para se conectar à fonte OData. <br/><br/> Para o Cloud OData, os valores possíveis são Anonymous, Basic e OAuth (Observe Azure Data Factory atualmente só dão suporte ao OAuth com base em Azure Active Directory). <br/><br/> Para o OData local, os valores possíveis são Anonymous, Basic e Windows. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação básica. |Sim (somente se você estiver usando a autenticação básica) |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Sim (somente se você estiver usando a autenticação básica) |
| authorizedCredential |Se você estiver usando o OAuth, clique no botão **autorizar** no assistente de data Factory de cópia ou no editor e insira sua credencial, então o valor dessa propriedade será gerado automaticamente. |Sim (somente se você estiver usando a autenticação OAuth) |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao serviço OData local. Especifique somente se você estiver copiando dados da fonte OData local. |Não |

### <a name="using-basic-authentication"></a>Usando a autenticação básica
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Usando a autenticação do Windows para acessar a fonte OData local
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Usando a autenticação OAuth acessando a origem do Cloud OData
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeproperties para o conjunto de um do tipo **ODataResource** (que inclui o conjunto de texto do OData) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Multi-Path |Caminho para o recurso OData |Não |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeproperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Quando a fonte é do tipo **RelationalSource** (que inclui o OData), as seguintes propriedades estão disponíveis na seção typeproperties:

| Propriedade | Descrição | Exemplo | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |"? $select = name, Description & $top = 5" |Não |

## <a name="type-mapping-for-odata"></a>Mapeamento de tipo para OData
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas.

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados do OData, os seguintes mapeamentos são usados de tipos OData para o tipo .NET.

| Tipo de dados OData | Tipo .NET |
| --- | --- |
| EDM. Binary |Byte [] |
| Edm.Boolean |bool |
| EDM. byte |Byte [] |
| EDM. DateTime |DateTime |
| EDM. decimal |Vírgula |
| Edm.Double |Clique |
| EDM. single |Único |
| EDM. GUID |GUID |
| EDM. Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| EDM. SByte |Int16 |
| Edm.String |String |
| EDM.Time |Período |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> Não há suporte para tipos de dados complexos OData, por exemplo, objeto.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Exemplo de JSON: copiar dados da origem do OData para o blob do Azure
Este exemplo fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de uma fonte OData para um armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory. O exemplo tem as seguintes entidades de Data Factory:

1. Um serviço vinculado do tipo [OData](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [ODataResource](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados da consulta em uma fonte OData para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do OData:** Este exemplo usa a autenticação anônima. Consulte a seção [serviço vinculado OData](#linked-service-properties) para diferentes tipos de autenticação que você pode usar.

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Serviço vinculado do armazenamento do Azure:**

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

**Conjunto de dados de entrada OData:**

A configuração "external": "true" informa ao serviço de Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

A especificação do **caminho** na definição do conjunto de conjuntos é opcional.

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Atividade de cópia em um pipeline com origem OData e coletor de blob:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **RelationalSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **Query** seleciona os mais recentes (mais recentes) dados da fonte OData.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
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
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

A especificação da **consulta** na definição de pipeline é opcional. A **URL** que o serviço de data Factory usa para recuperar dados é: URL especificada no serviço vinculado (obrigatório) + caminho especificado no conjunto de dados (opcional) + consulta no pipeline (opcional).

### <a name="type-mapping-for-odata"></a>Mapeamento de tipo para OData
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados de repositórios de dados OData, os tipos de dados OData são mapeados para tipos .NET.

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber mais sobre como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, confira [mapeando colunas Azure data Factory do conjunto de](data-factory-map-columns.md)informações

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
