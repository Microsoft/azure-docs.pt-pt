---
title: Mover dados do Salesforce usando Data Factory
description: Saiba mais sobre como mover dados do Salesforce usando Azure Data Factory.
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
ms.openlocfilehash: 71201efeb56ffda2dfbf82ca19b3bacb773c7c3d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666153"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Mover dados do Salesforce usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-salesforce-connector.md)
> * [Versão 2 (versão atual)](../connector-salesforce.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do Salesforce na v2](../connector-salesforce.md).

Este artigo descreve como você pode usar a atividade de cópia em uma data factory do Azure para copiar dados do Salesforce para qualquer armazenamento de dados listado na coluna coletor na tabela [fontes e coletores com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

Atualmente, Azure Data Factory dá suporte apenas à movimentação de dados do Salesforce para [armazenamentos de dados de coletor com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats), mas não oferece suporte à movimentação de dados de outros armazenamentos de dados para o Salesforce.

## <a name="supported-versions"></a>Versões suportadas
Este conector dá suporte às seguintes edições do Salesforce: Developer Edition, Professional Edition, Enterprise Edition ou Unlimited Edition. E ele dá suporte à cópia de produção, área restrita e domínio personalizado do Salesforce.

## <a name="prerequisites"></a>Pré-requisitos
* A permissão de API deve ser habilitada. Consulte [como fazer habilitar o acesso à API no Salesforce por conjunto de permissões?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Para copiar dados do Salesforce para armazenamentos de dados locais, você deve ter pelo menos Gerenciamento de Dados gateway 2,0 instalado em seu ambiente local.

## <a name="salesforce-request-limits"></a>Limites de solicitação do Salesforce
O Salesforce tem limites para solicitações de API total e solicitações de API simultâneas. Tenha em atenção os seguintes pontos:

- Se o número de solicitações simultâneas exceder o limite, a limitação ocorrerá e você verá falhas aleatórias.
- Se o número total de solicitações exceder o limite, a conta do Salesforce será bloqueada por 24 horas.

Você também pode receber o erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Consulte a seção "limites de solicitação de API" no artigo [limites de desenvolvedor do Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) para obter detalhes.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados do Salesforce usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados do Salesforce, confira a seção [exemplo de JSON: copiar dados do Salesforce para o blob do Azure](#json-example-copy-data-from-salesforce-to-azure-blob) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para o Salesforce:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece descrições para elementos JSON que são específicos para o serviço vinculado do Salesforce.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **Salesforce**. |Sim |
| environmentUrl | Especifique a URL da instância do Salesforce. <br><br> -O padrão é "https:\//login.salesforce.com". <br> -Para copiar dados da área restrita, especifique "https://test.salesforce.com". <br> -Para copiar dados do domínio personalizado, especifique, por exemplo, "https://[domínio]. My. Salesforce. com". |Não |
| o nome de utilizador |Especifique um nome de usuário para a conta de usuário. |Sim |
| palavra-passe |Especifique uma senha para a conta de usuário. |Sim |
| securityToken |Especifique um token de segurança para a conta de usuário. Consulte [obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como redefinir/obter um token de segurança. Para saber mais sobre os tokens de segurança em geral, consulte [segurança e a API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Sim |

## <a name="dataset-properties"></a>Propriedades de DataSet
Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de os. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure e assim por diante).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeproperties para um DataSet do tipo **RelationalTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no Salesforce. |Não (se uma **consulta** de **RelationalSource** for especificada) |

> [!IMPORTANT]
> A parte "__c" do nome da API é necessária para qualquer objeto personalizado.

![Data Factory-conexão do Salesforce-nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e várias políticas estão disponíveis para todos os tipos de atividades.

As propriedades que estão disponíveis na seção typeproperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Na atividade de cópia, quando a fonte é do tipo **RelationalSource** (que inclui o Salesforce), as seguintes propriedades estão disponíveis na seção typeproperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Uma consulta SQL-92 ou consulta [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Por exemplo: `select * from MyTable__c`. |Não (se **TableName** do **DataSet** for especificado) |

> [!IMPORTANT]
> A parte "__c" do nome da API é necessária para qualquer objeto personalizado.

![Data Factory-conexão do Salesforce-nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Dicas de consulta
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Recuperando dados usando a cláusula WHERE na coluna DateTime
Ao especificar a consulta SOQL ou SQL, preste atenção à diferença de formato DateTime. Por exemplo:

* **Exemplo de SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Exemplo de SQL**:
    * **Usando o assistente de cópia para especificar a consulta:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Usando a edição JSON para especificar a consulta (escape Char corretamente):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Recuperando dados do relatório do Salesforce
Você pode recuperar dados de relatórios do Salesforce especificando a consulta como `{call "<report name>"}`, por exemplo,. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Recuperando registros excluídos da lixeira do Salesforce
Para consultar os registros com exclusão reversível da lixeira do Salesforce, você pode especificar **"IsDeleted = 1"** em sua consulta. Por exemplo,

* Para consultar somente os registros excluídos, especifique "select * from MyTable__c **onde IsDeleted = 1**"
* Para consultar todos os registros, incluindo os existentes e excluídos, especifique "select * from MyTable__c **onde IsDeleted = 0 ou IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Exemplo de JSON: copiar dados do Salesforce para o blob do Azure
O exemplo a seguir fornece exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do Salesforce para o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

Aqui estão os artefatos Data Factory que você precisará criar para implementar o cenário. As seções que seguem a lista fornecem detalhes sobre essas etapas.

* Um serviço vinculado do tipo [Salesforce](#linked-service-properties)
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [RelationalTable](#dataset-properties)
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Serviço vinculado do Salesforce**

Este exemplo usa o serviço vinculado do **Salesforce** . Consulte a seção [serviço vinculado do Salesforce](#linked-service-properties) para obter as propriedades que são suportadas por esse serviço vinculado. Consulte [obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como redefinir/obter o token de segurança.

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
**Conjunto de dados de entrada do Salesforce**

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

A configuração de **external** como **true** informa ao serviço de data Factory que o dataset é externo ao data Factory e não é produzido por uma atividade no data Factory.

> [!IMPORTANT]
> A parte "__c" do nome da API é necessária para qualquer objeto personalizado.

![Data Factory-conexão do Salesforce-nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Conjunto de dados de saída do blob do Azure**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1).

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

O pipeline contém a atividade de cópia, que é configurada para usar os conjuntos de dados de entrada e saída, e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **RelationalSource**e o tipo de **coletor** é definido como **BlobSink**.

Consulte [Propriedades do tipo RelationalSource](#copy-activity-properties) para obter a lista de propriedades com suporte pelo RelationalSource.

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
> A parte "__c" do nome da API é necessária para qualquer objeto personalizado.

![Data Factory-conexão do Salesforce-nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapeamento de tipo para Salesforce

| Tipo do Salesforce | . Tipo baseado em rede |
| --- | --- |
| Número automático |String |
| verificação |Booleano |
| Moeda |Vírgula |
| Date |DateTime |
| Data/hora |DateTime |
| Email |String |
| Id |String |
| Relação de pesquisa |String |
| Lista de seleção de seleção múltipla |String |
| Número |Vírgula |
| Percentagem |Vírgula |
| Telefone |String |
| Seleção |String |
| Texto |String |
| Área de texto |String |
| Área de texto (longa) |String |
| Área de texto (rica) |String |
| Texto (criptografado) |String |
| do IdP |String |

> [!NOTE]
> Para mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta, consulte [mapeando colunas do conjunto de linhas no Azure data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
