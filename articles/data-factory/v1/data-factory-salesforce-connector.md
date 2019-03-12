---
title: Mover dados do Salesforce com o Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados do Salesforce com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3689c93c392d910d6b3979291bc5a6c81080418c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551811"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Mover dados do Salesforce com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-salesforce-connector.md)
> * [Versão 2 (versão atual)](../connector-salesforce.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector do Salesforce no V2](../connector-salesforce.md).

Este artigo descreve como pode utilizar a atividade de cópia de uma fábrica de dados do Azure para copiar dados do Salesforce para qualquer arquivo de dados que está listado na coluna de Sink na [suportado origens e sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Este artigo baseia-se a [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral de movimento de dados com a atividade de cópia e combinações de loja de dados suportados.

O Azure Data Factory suporta atualmente apenas mover dados do Salesforce para [arquivos de dados de sink suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats), mas não suporte a mover dados de outros dados armazena ao Salesforce.

## <a name="supported-versions"></a>Versões suportadas
Este conector suporta as seguintes edições do Salesforce: Developer Edition, Professional Edition, Enterprise Edition ou edição ilimitada. E ele suporta copiar do Salesforce produção, área de segurança e o domínio personalizado.

## <a name="prerequisites"></a>Pré-requisitos
* Permissão de API tem de estar ativada. Consulte [como ativar o acesso à API no Salesforce por conjunto de permissões?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Para copiar dados do Salesforce para arquivos de dados no local, tem de ter, pelo menos, Data Management Gateway 2.0 instalado no seu ambiente no local.

## <a name="salesforce-request-limits"></a>Limites de pedido de Salesforce
O Salesforce tem limites para total de pedidos de API e pedidos de API em simultâneo. Tenha em atenção os seguintes pontos:

- Se o número de pedidos simultâneos excede o limite, de limitação ocorre e verá falhas aleatórias.
- Se o número total de solicitações exceder o limite, a conta do Salesforce será bloqueada durante 24 horas.

Também poderá receber o erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Consulte a secção "Limites de pedido de API" a [limites de desenvolvedor do Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) artigo para obter detalhes.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados do Salesforce ao utilizar ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON. Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados do Salesforce, consulte [exemplo de JSON: Copiar dados do Salesforce para BLOBs do Azure](#json-example-copy-data-from-salesforce-to-azure-blob) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas ao Salesforce:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece descrições para os elementos JSON que são específicas para o serviço ligado do Salesforce.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida como: **Salesforce**. |Sim |
| environmentUrl | Especifique a instância de URL do Salesforce. <br><br> -Predefinição é "https://login.salesforce.com". <br> -Para copiar dados de proteção de segurança, especifique "https://test.salesforce.com". <br> -Para copiar dados de domínio personalizado, especifique, por exemplo, "https://[domain].my.salesforce.com". |Não |
| nome do utilizador |Especifique um nome de utilizador para a conta de utilizador. |Sim |
| palavra-passe |Especifique uma palavra-passe da conta de utilizador. |Sim |
| securityToken |Especifique um token de segurança da conta de utilizador. Ver [obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como repor/obter um token de segurança. Para saber mais sobre os tokens de segurança em geral, veja [segurança e a API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOBs do Azure, tabela do Azure e assim por diante).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para um conjunto de dados do tipo **RelationalTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no Salesforce. |Não (se um **consulta** dos **RelationalSource** for especificado) |

> [!IMPORTANT]
> A parte de "__c" o nome da API é necessário para qualquer objeto personalizado.

![Nome da fábrica - ligação de Salesforce - API de dados](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte a [Criar pipelines](data-factory-create-pipelines.md) artigo. Propriedades, como tabelas de nome, descrição, entrada e saída, e várias políticas estão disponíveis para todos os tipos de atividades.

As propriedades que estão disponíveis na secção typeProperties da atividade, por outro lado, variar de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Na atividade de cópia, quando a origem é do tipo **RelationalSource** (que inclui o Salesforce), as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Uma consulta de SQL-92 ou [linguagem de consulta de objeto do Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) consulta. Por exemplo: `select * from MyTable__c`. |Não (se o **tableName** da **conjunto de dados** for especificado) |

> [!IMPORTANT]
> A parte de "__c" o nome da API é necessário para qualquer objeto personalizado.

![Nome da fábrica - ligação de Salesforce - API de dados](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Sugestões de consulta
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Obter dados com a onde cláusula na coluna DateTime
Quando especificar a consulta SOQL ou SQL, preste atenção à diferença de formato DateTime. Por exemplo:

* **Exemplo SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Exemplo SQL**:
    * **Com o Assistente para copiar para especificar a consulta:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Usar o JSON para especificar a consulta de edição (escape char corretamente):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Recuperar dados do relatório de Salesforce
Pode recuperar dados de relatórios do Salesforce ao especificar a consulta como `{call "<report name>"}`, por exemplo,. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>A obter eliminados registos da Reciclagem de Salesforce
Para consultar registos soft eliminados da Reciclagem Salesforce, pode especificar **"IsDeleted = 1"** na sua consulta. Por exemplo,

* Para consultar apenas os registos eliminados, especificar "selecionar * de MyTable__c **onde IsDeleted = 1**"
* Para consultar todos os registos, incluindo o existente e o eliminado, especificar "selecionar * de MyTable__c **onde IsDeleted = 0 ou IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Exemplo JSON: Copiar dados do Salesforce para BLOBs do Azure
O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do Salesforce para o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

Aqui estão os artefactos do Data Factory que terá de criar para implementar o cenário. As seções a seguir lista fornecem detalhes sobre estes passos.

* Um serviço ligado do tipo [Salesforce](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [RelationalTable](#dataset-properties)
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Serviço ligado do Salesforce**

Este exemplo utiliza a **Salesforce** serviço ligado. Consulte a [serviço ligado do Salesforce](#linked-service-properties) secção para as propriedades que são suportados por este serviço ligado. Ver [obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como repor/obter o token de segurança.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Serviço ligado do Armazenamento do Azure**

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
**Conjunto de dados de entrada de Salesforce**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
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

A definição **externo** ao **verdadeiro** informa o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

> [!IMPORTANT]
> A parte de "__c" o nome da API é necessário para qualquer objeto personalizado.

![Nome da fábrica - ligação de Salesforce - API de dados](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Conjunto de dados de saída do blob do Azure**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia, que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **RelationalSource**e o **sink** tipo está definido como **BlobSink**.

Ver [propriedades do tipo RelationalSource](#copy-activity-properties) para obter a lista de propriedades que são suportadas pelo RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> A parte de "__c" o nome da API é necessário para qualquer objeto personalizado.

![Nome da fábrica - ligação de Salesforce - API de dados](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapeamento do tipo de Salesforce
| Tipo de Salesforce | . Tipo de NET |
| --- | --- |
| Número de automática |String |
| Caixa de verificação |Booleano |
| Moeda |Decimal |
| Data |DateTime |
| Data/Hora |DateTime |
| E-mail |String |
| ID |String |
| Relação de referência |String |
| Lista de opções de seleção múltipla |String |
| Número |Decimal |
| Percentagem |Decimal |
| Telefone |String |
| Lista de opções |String |
| Texto |String |
| Área de texto |String |
| Área de texto (longa) |String |
| Área de texto (avançado) |String |
| Texto (encriptado) |String |
| URL |String |

> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte a [guia de sintonização de desempenho de atividade de cópia e](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
