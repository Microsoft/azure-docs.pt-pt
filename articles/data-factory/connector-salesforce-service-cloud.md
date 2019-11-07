---
title: Copiar dados de e para a nuvem do serviço Salesforce usando Azure Data Factory
description: Saiba como copiar dados da nuvem do serviço Salesforce para armazenamentos de dados de coletor com suporte ou de armazenamentos de dados de origem com suporte para a nuvem do serviço Salesforce usando uma atividade de cópia em um pipeline data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 5b98e11d4b8d820c87dfb6ffc5e98b46f2095ace
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680359"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Copiar dados de e para a nuvem do serviço Salesforce usando Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para a nuvem do serviço Salesforce. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector de nuvem do serviço do Salesforce tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados da nuvem do serviço Salesforce para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer armazenamento de dados de origem com suporte para a nuvem do serviço Salesforce. Para obter uma lista de armazenamentos de dados com suporte como fontes ou coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector de nuvem do serviço Salesforce dá suporte a:

- Edições do Salesforce Developer, Professional, Enterprise ou Unlimited.
- Copiar dados de e para a produção, a área restrita e o domínio personalizado do Salesforce.

O conector de nuvem de serviço do Salesforce é criado sobre a API REST do Salesforce/em massa, com [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) para copiar dados de e [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) para copiar dados para o.

## <a name="prerequisites"></a>Pré-requisitos

A permissão de API deve ser habilitada no Salesforce. Para obter mais informações, consulte [habilitar o acesso à API no Salesforce por conjunto de permissões](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites de solicitação do Salesforce

O Salesforce tem limites para solicitações de API total e solicitações de API simultâneas. Tenha em atenção os seguintes pontos:

- Se o número de solicitações simultâneas exceder o limite, a limitação ocorrerá e você verá falhas aleatórias.
- Se o número total de solicitações exceder o limite, a conta do Salesforce será bloqueada por 24 horas.

Você também pode receber a mensagem de erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Para obter mais informações, consulte a seção "limites de solicitação de API" nos [limites de desenvolvedor do Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector de nuvem do serviço do Salesforce.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Salesforce.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade Type deve ser definida como **SalesforceServiceCloud**. |Sim |
| environmentUrl | Especifique a URL da instância de nuvem do serviço do Salesforce. <br> -O padrão é `"https://login.salesforce.com"`. <br> -Para copiar dados da área restrita, especifique `"https://test.salesforce.com"`. <br> -Para copiar dados de um domínio personalizado, especifique, por exemplo, `"https://[domain].my.salesforce.com"`. |Não |
| o nome de utilizador |Especifique um nome de usuário para a conta de usuário. |Sim |
| palavra-passe |Especifique uma senha para a conta de usuário.<br/><br/>Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| securityToken |Especifique um token de segurança para a conta de usuário. Para obter instruções sobre como redefinir e obter um token de segurança, consulte [obter um token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Para saber mais sobre os tokens de segurança em geral, consulte [segurança e a API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usará o Azure Integration Runtime padrão. | Não para fonte, sim para o coletor se o serviço vinculado de origem não tiver o tempo de execução de integração |

>[!IMPORTANT]
>Quando você copia dados para a nuvem do serviço Salesforce, o Azure Integration Runtime padrão não pode ser usado para executar a cópia. Em outras palavras, se o serviço vinculado de origem não tiver um tempo de execução de integração especificado, [crie explicitamente um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com um local perto da instância de nuvem do serviço Salesforce. Associe o serviço vinculado da nuvem do serviço do Salesforce como no exemplo a seguir.

**Exemplo: armazenar credenciais no Data Factory**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar credenciais no Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de serviços de nuvem do serviço Salesforce.

Para copiar dados de e para a nuvem do serviço Salesforce, há suporte para as propriedades a seguir.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **SalesforceServiceCloudObject**.  | Sim |
| objectApiName | O nome do objeto do Salesforce do qual recuperar dados. | Não para fonte, sim para coletor |

> [!IMPORTANT]
> A parte "__c" do **nome da API** é necessária para qualquer objeto personalizado.

![Nome da API de conexão Data Factory Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemplo:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como **RelationalTable**. | Sim |
| tableName | Nome da tabela na nuvem do serviço Salesforce. | Não (se "Query" na origem da atividade for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pelo coletor e pela fonte de nuvem do serviço Salesforce.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Nuvem do serviço Salesforce como um tipo de origem

Para copiar dados da nuvem do serviço Salesforce, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como **SalesforceServiceCloudSource**. | Sim |
| consulta |Use a consulta personalizada para ler os dados. Você pode usar a consulta [SOQL (Salesforce Object Querying Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) ou a consulta SQL-92. Veja mais dicas na seção [dicas de consulta](#query-tips) . Se a consulta não for especificada, todos os dados do objeto de nuvem do serviço do Salesforce especificados em "objectApiName" no conjunto serão recuperados. | Não (se "objectApiName" no DataSet for especificado) |
| readBehavior | Indica se é para consultar os registros existentes ou consultar todos os registros, incluindo aqueles excluídos. Se não for especificado, o comportamento padrão será o primeiro. <br>Valores permitidos: **Query** (padrão), **queryall**.  | Não |

> [!IMPORTANT]
> A parte "__c" do **nome da API** é necessária para qualquer objeto personalizado.

![Lista de nome da API de conexão do Salesforce Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Nuvem do serviço Salesforce como um tipo de coletor

Para copiar dados para a nuvem do serviço Salesforce, as propriedades a seguir têm suporte na seção **coletor** de atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do coletor da atividade de cópia deve ser definida como **SalesforceServiceCloudSink**. | Sim |
| WriteBehavior | O comportamento de gravação para a operação.<br/>Os valores permitidos são **Insert** e **Upsert**. | Não (o padrão é inserir) |
| externalIdFieldName | O nome do campo de ID externa para a operação Upsert. O campo especificado deve ser definido como "campo de ID externa" no objeto de nuvem do serviço Salesforce. Ele não pode ter valores nulos nos dados de entrada correspondentes. | Sim para "Upsert" |
| writeBatchSize | A contagem de linhas de dados gravados na nuvem do serviço Salesforce em cada lote. | Não (o padrão é 5.000) |
| ignoreNullValues | Indica se os valores nulos devem ser ignorados de dados de entrada durante uma operação de gravação.<br/>Os valores permitidos são **true** e **false**.<br>- **true**: Deixe os dados no objeto de destino inalterados quando você fizer uma operação de Upsert ou atualização. Insira um valor padrão definido quando você fizer uma operação de inserção.<br/>- **false**: atualizar os dados no objeto de destino para NULL quando você fizer uma operação de Upsert ou atualização. Insira um valor nulo ao fazer uma operação de inserção. | Não (o padrão é false) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Dicas de consulta

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Recuperar dados de um relatório de nuvem do serviço Salesforce

Você pode recuperar dados de relatórios de nuvem do serviço do Salesforce especificando uma consulta como `{call "<report name>"}`. Um exemplo é `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Recuperar registros excluídos da lixeira da nuvem do serviço Salesforce

Para consultar os registros com exclusão reversível da lixeira da nuvem do serviço do Salesforce, você pode especificar `readBehavior` como `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Diferença entre SOQL e sintaxe de consulta SQL

Ao copiar dados da nuvem do serviço Salesforce, você pode usar a consulta SOQL ou a consulta SQL. Observe que esses dois têm suporte de sintaxe e funcionalidade diferentes, não os misturam. Você deve usar a consulta SOQL que tem suporte nativo pela nuvem do serviço Salesforce. A tabela a seguir lista as principais diferenças:

| Sintaxe | Modo de SOQL | Modo SQL |
|:--- |:--- |:--- |
| Seleção de coluna | É necessário enumerar os campos a serem copiados na consulta, por exemplo, `SELECT field1, filed2 FROM objectname` | `SELECT *` tem suporte além da seleção de coluna. |
| Aspas | Nomes de objetos/arquivados não podem ser colocados entre aspas. | Os nomes de campo/objeto podem ser colocados entre aspas, por exemplo, `SELECT "id" FROM "Account"` |
| Formato DateTime |  Consulte os detalhes [aqui](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) e os exemplos na próxima seção. | Consulte os detalhes [aqui](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) e os exemplos na próxima seção. |
| Valores Boolianos | Representado como `False` e `True`, por exemplo, `SELECT … WHERE IsDeleted=True`. | Representado como 0 ou 1, por exemplo, `SELECT … WHERE IsDeleted=1`. |
| Renomeação de coluna | Não suportado. | Com suporte, por exemplo: `SELECT a AS b FROM …`. |
| Inter-relações | Com suporte, por exemplo, `Account_vod__r.nvs_Country__c`. | Não suportado. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recuperar dados usando uma cláusula WHERE na coluna DateTime

Quando você especifica a consulta SQL ou SOQL, preste atenção à diferença de formato DateTime. Por exemplo:

* **Exemplo de SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Exemplo de SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Erro de MALFORMED_QUERY: truncado

Se você encontrar o erro de "MALFORMED_QUERY: TRUNCATE", normalmente, é devido a você ter uma coluna de tipo JunctionIdList nos dados e o Salesforce tem a limitação de dar suporte a esses dados com um grande número de linhas. Para mitigar, tente excluir a coluna JunctionIdList ou limitar o número de linhas a serem copiadas (você pode particionar várias execuções da atividade de cópia).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mapeamento de tipo de dados para a nuvem do serviço Salesforce

Quando você copia dados da nuvem do serviço Salesforce, os seguintes mapeamentos são usados de tipos de dados de nuvem do serviço do Salesforce para Data Factory tipos de dados provisórios. Para saber mais sobre como a atividade de cópia mapeia o esquema de origem e o tipo de dados para o coletor, consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados de nuvem do serviço Salesforce | Data Factory tipo de dados provisório |
|:--- |:--- |
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

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
