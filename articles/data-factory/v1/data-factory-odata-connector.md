---
title: Mover dados de fontes OData
description: Saiba como mover dados de fontes OData utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 95f92d4e5616d7754c355610685701a8e089b84e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265912"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Mova dados De uma fonte OData utilizando a Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-odata-connector.md)
> * [Versão 2 (versão atual)](../connector-odata.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector OData em V2](../connector-odata.md).


Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de uma fonte oData. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de uma fonte OData para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de uma fonte OData para outras lojas de dados, mas não para a transferência de dados de outras lojas de dados para uma fonte OData.

## <a name="supported-versions-and-authentication-types"></a>Versões suportadas e tipos de autenticação
Este conector OData suporta as versões OData 3.0 e 4.0, e pode copiar dados tanto de fontes OData cloud como no local. Para este último, é necessário instalar o Portal de Gestão de Dados. Consulte [os dados entre as instalações e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter detalhes sobre gateway de gestão de dados.

Abaixo são suportados os tipos de autenticação:

* Para aceder ao feed OData **em nuvem,** pode utilizar a autenticação anónima, básica (nome de utilizador e palavra-passe) ou Azure Ative Directory baseada em OAuth.
* Para aceder ao feed OData **no local,** pode utilizar a autenticação anónima, básica (nome do utilizador e palavra-passe) ou Windows.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados a partir de uma fonte OData utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON.  Para uma amostra com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de uma fonte oData, consulte o [exemplo da JSON: Copiar dados de OData fonte da secção Azure Blob](#json-example-copy-data-from-odata-source-to-azure-blob) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades data Factory específicas à fonte OData:

## <a name="linked-service-properties"></a>Propriedades do Serviço Linked
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao OData.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OData** |Sim |
| url |Url do serviço OData. |Sim |
| authenticationType |Tipo de autenticação utilizada para ligar à fonte OData. <br/><br/> Para o OData em nuvem, os valores possíveis são Anónimos, Básicos e OAuth (nota Azure Data Factory atualmente apenas suporta o OAuth baseado em Diretório Ativo Azure). <br/><br/> Para o OData no local, os valores possíveis são Anónimos, Básicos e Windows. |Sim |
| o nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. |Sim (apenas se estiver a usar a autenticação básica) |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Sim (apenas se estiver a usar a autenticação básica) |
| credenciadoCredential |Se estiver a utilizar o OAuth, clique em **autorizar** o botão no Data Factory Copy Wizard ou Editor e introduza a sua credencial, então o valor desta propriedade será gerado automaticamente. |Sim (apenas se estiver a usar a autenticação OAuth) |
| nome gateway |Nome do portal que o serviço Data Factory deve utilizar para ligar ao serviço OData no local. Especificar apenas se estiver a copiar dados a partir de origem OData no local. |Não |

### <a name="using-basic-authentication"></a>Utilização da autenticação básica
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

### <a name="using-anonymous-authentication"></a>Utilização da autenticação anónima
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

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Utilização de autenticação do Windows a aceder a fonte oData no local
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

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Utilização da autenticação OAuth a aceder à nuvem DeData fonte
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção typeProperties para conjunto de dados do tipo **ODataResource** (que inclui dataset OData) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| path |Caminho para o recurso OData |Não |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção tipoPropriedades da atividade por outro lado variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

Quando a fonte é do tipo **RelationalSource** (que inclui OData) as seguintes propriedades estão disponíveis na secção TypeProperties:

| Propriedade | Descrição | Exemplo | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |"?$select=Nome, Descrição&$top=5" |Não |

## <a name="type-mapping-for-odata"></a>Tipo de mapeamento para OData
Conforme mencionado no artigo de atividades de movimento de [dados,](data-factory-data-movement-activities.md) a atividade de cópia realiza conversões automáticas de tipos de origem para tipos de sink com a seguinte abordagem de dois passos.

1. Converter de tipos de origem nativa para .NET tipo
2. Converter do tipo .NET para o tipo de pia nativa

Ao mover dados do OData, os seguintes mapeamentos são utilizados dos tipos OData para o tipo .NET.

| Tipo de Dados OData | Tipo .NET |
| --- | --- |
| Edm.Binary |Byte[] |
| Edm.Boolean |Booleano |
| Edm.Byte |Byte[] |
| Edm.DateTime |DateTime |
| Edm.Decimal |Decimal |
| Edm.Double |Double |
| Edm.Single |Único |
| Edm.Guid |GUID |
| Edm.Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte |Int16 |
| Edm.String |String |
| Edm.Tempo |TimeSpan |
| Edm.DateTimeOffset |DataTimeOffset |

> [!Note]
> Os tipos de dados complexos OData, por exemplo, o objeto não são suportados.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Exemplo jSON: Copiar dados da fonte oData para OE Blob
Este exemplo fornece definições jSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados de uma fonte OData para um Armazenamento De Blob Azure. No entanto, os dados podem ser copiados para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure. A amostra tem as seguintes entidades data Factory:

1. Um serviço ligado do tipo [OData.](#linked-service-properties)
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [ODataResource](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de consulta contra uma fonte OData para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao OData:** Este exemplo utiliza a autenticação Anónima. Consulte a secção de serviço ligada ao [OData](#linked-service-properties) para obter diferentes tipos de autenticação que possa utilizar.

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

**Serviço ligado ao Armazenamento Azure:**

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

Definição "externa": "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Especificar **o caminho** na definição de conjunto de dados é opcional.

**Conjunto de dados de saída de Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

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

**Copiar atividade num oleoduto com fonte OData e pia Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **RelationalSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados mais recentes (mais recentes) da fonte OData.

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

Especificar **a consulta** na definição do gasoduto é opcional. O **URL** que o serviço Data Factory utiliza para recuperar dados é: URL especificado no serviço ligado (obrigatório) + caminho especificado no conjunto de dados (opcional) + consulta no pipeline (opcional).

### <a name="type-mapping-for-odata"></a>Tipo de mapeamento para OData
Conforme mencionado no artigo de atividades de movimento de [dados,](data-factory-data-movement-activities.md) a atividade de cópia realiza conversões automáticas de tipos de origem para tipos de sink com a seguinte abordagem de 2 passos:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter do tipo .NET para o tipo de pia nativa

Ao mover dados das lojas de dados OData, os tipos de dados oData são mapeados para tipos .NET.

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para aprender sobre as colunas de mapeamento em conjunto de dados de origem para colunas em conjunto de dados de sumidouro, consulte [mapeando colunas](data-factory-map-columns.md)de conjunto de dados na Azure Data Factory .

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetível para evitar resultados não intencionais. Na Azure Data Factory, pode reproduzir uma fatia manualmente. Também pode configurar a política de retry para um conjunto de dados para que uma fatia seja reexecutada quando ocorre uma falha. Quando uma fatia é reexecutada de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [Leitura repetível a partir de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
