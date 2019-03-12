---
title: Copiar dados de e para Salesforce com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados do Salesforce para arquivos de dados de sink suportado ou a partir de arquivos de dados de origem suportada para o Salesforce com uma atividade de cópia num pipeline de fábrica de dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: jingwang
ms.openlocfilehash: f06dd47a519d992e52ac0010c0ae7d81870a4842
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544533"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Copiar dados de e para Salesforce com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-salesforce-connector.md)
> * [Versão atual](connector-salesforce.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para o Salesforce. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do Salesforce para qualquer arquivo de dados de sink suportados. Também pode copiar dados de qualquer arquivo de dados de origem suportada para o Salesforce. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do Salesforce suporta:

- Edições de desenvolvedor do Salesforce, Professional, Enterprise ou ilimitado.
- Copiar dados de e para produção, o sandbox e o domínio personalizado de Salesforce.

O conector do Salesforce baseia-se da API de REST do Salesforce, com [v39](https://developer.salesforce.com/docs/atlas.en-us.206.0.api_rest.meta/api_rest/resources_list.htm) para copiar dados de e [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) para os dados de cópia.

## <a name="prerequisites"></a>Pré-requisitos

Permissão de API tem de estar ativada no Salesforce. Para obter mais informações, consulte [acesso ativar API no Salesforce por conjunto de permissões](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites de pedido de Salesforce

O Salesforce tem limites para total de pedidos de API e pedidos de API em simultâneo. Tenha em atenção os seguintes pontos:

- Se o número de pedidos simultâneos excede o limite, de limitação ocorre e ver falhas aleatórias.
- Se o número total de solicitações exceder o limite, a conta do Salesforce está bloqueada durante 24 horas.

Poderá também receber a mensagem de erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Para obter mais informações, consulte a seção "Limites de pedido de API" [limites de desenvolvedor do Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Salesforce.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Salesforce.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo deve ser definida como **Salesforce**. |Sim |
| environmentUrl | Especifique o URL da instância do Salesforce. <br> -Predefinição é `"https://login.salesforce.com"`. <br> -Para copiar dados de proteção de segurança, especifique `"https://test.salesforce.com"`. <br> -Para copiar dados de domínio personalizado, especifique, por exemplo, `"https://[domain].my.salesforce.com"`. |Não |
| nome do utilizador |Especifique um nome de utilizador para a conta de utilizador. |Sim |
| palavra-passe |Especifique uma palavra-passe da conta de utilizador.<br/><br/>Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| securityToken |Especifique um token de segurança da conta de utilizador. Para obter instruções sobre como repor e obter um token de segurança, consulte [obter um token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Para saber mais sobre os tokens de segurança em geral, veja [segurança e a API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não para a origem, Sim para sink se associada a origem de serviço não tem o runtime de integração |

>[!IMPORTANT]
>Quando copiar dados com o Salesforce, o padrão do Runtime de integração do Azure não pode ser utilizado para executar a cópia. Em outras palavras, se a sua origem associada serviço não tem um runtime de integração especificado explicitamente [criar um Runtime de integração do Azure](create-azure-integration-runtime.md#create-azure-ir) com uma localização perto de sua instância do Salesforce. Associe o serviço ligado do Salesforce como no exemplo seguinte.

**Exemplo: Credenciais de Store no Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

**Exemplo: Credenciais de Store no Cofre de chaves**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados do Salesforce.

Para copiar dados de e para o Salesforce, defina a propriedade de tipo de conjunto de dados para **SalesforceObject**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **SalesforceObject**.  | Sim |
| objectApiName | O nome de objeto do Salesforce para recuperar dados a partir de. | Não para a origem, Sim para o sink |

> [!IMPORTANT]
> A parte "__c" da **nome da API** é necessária para qualquer objeto personalizado.

![Nome da API de ligação de Salesforce da fábrica de dados](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemplo:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Para compatibilidade com versões anteriores: Quando copiar dados do Salesforce, se usar o conjunto de dados do tipo "RelationalTable" anterior, que mantém a funcionar, apesar de ver uma sugestão para mudar para o novo tipo de "SalesforceObject".

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida **RelationalTable**. | Sim |
| tableName | Nome da tabela no Salesforce. | Não (se for especificada "consulta" na origem de atividade) |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo Salesforce origem e sink.

### <a name="salesforce-as-a-source-type"></a>Salesforce como um tipo de origem

Para copiar dados do Salesforce, defina o tipo de origem na atividade de cópia para **SalesforceSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida **SalesforceSource**. | Sim |
| consulta |Utilize a consulta personalizada para ler dados. Pode usar [linguagem de consulta de objeto do Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) consulta ou consulta de SQL-92. Consulte mais sugestões na [sugestões de consulta](#query-tips) secção. Se a consulta não for especificada, serão possível obter todos os dados do objeto Salesforce especificada no "objectApiName" no conjunto de dados. | Não (se for especificado "objectApiName" no conjunto de dados) |
| readBehavior | Indica se deve consultar os registos existentes, ou consultar todos os registos, incluindo o que foi excluído. Se não for especificado, o comportamento padrão é o primeiro. <br>Valores permitidos: **consulta** (predefinição), **queryAll**.  | Não |

> [!IMPORTANT]
> A parte "__c" da **nome da API** é necessária para qualquer objeto personalizado.

![Lista de nomes de API de ligação de Salesforce de fábrica de dados](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Para compatibilidade com versões anteriores: Quando copiar dados do Salesforce, se utilizar a cópia do tipo "RelationalSource" anterior, a origem mantém a trabalhar, apesar de ver uma sugestão para mudar para o novo tipo de "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce como um tipo de sink

Para copiar dados para o Salesforce, defina o tipo de sink na atividade de cópia para **SalesforceSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida **SalesforceSink**. | Sim |
| WriteBehavior | O comportamento de escrita para a operação.<br/>Valores permitidos são **inserir** e **Upsert**. | Não (a predefinição é Insert) |
| externalIdFieldName | O nome do campo de ID externo para a operação de upsert. O campo especificado tem de ser definido como "Campo de Id externo" no objeto Salesforce. Ele não pode ter valores nulos os dados de entrada correspondentes. | Sim para "Upsert" |
| writeBatchSize | A contagem de linhas de dados escritos para o Salesforce em cada lote. | Não (a predefinição é 5.000) |
| ignoreNullValues | Indica se a ignorar valores NULL de dados de entrada durante uma operação de escrita.<br/>Valores permitidos são **true** e **falso**.<br>- **Verdadeiro**: Deixe os dados no objeto de destino inalterado quando faz uma operação de upsert ou atualização. Inserir um valor padrão definido quando o fizer uma operação de inserção.<br/>- **False**: Atualize os dados no objeto de destino como NULL quando o fizer uma operação de upsert ou atualização. Inserir um valor nulo ao fazer uma operação de inserção. | Não (a predefinição é falso) |

**Exemplo: Salesforce sink na atividade de cópia**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Sugestões de consulta

### <a name="retrieve-data-from-a-salesforce-report"></a>Obter dados a partir de um relatório de Salesforce

Pode recuperar dados de relatórios do Salesforce ao especificar uma consulta como `{call "<report name>"}`. Um exemplo é `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Obter registos eliminados do Salesforce lixeira

Para consultar os registos eliminados soft do Salesforce lixeira, pode especificar `readBehavior` como `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Diferença entre a sintaxe de consulta SOQL e SQL

Ao copiar dados do Salesforce, pode utilizar a consulta SOQL ou consulta SQL. Tenha em atenção que estas duas tem sintaxe diferente e suporte de funcionalidades, não misture-lo. São sugeridas para utilizar a consulta SOQL que é suportada nativamente pelo Salesforce. A tabela seguinte lista as principais diferenças:

| Sintaxe | Modo SOQL | Modo SQL |
|:--- |:--- |:--- |
| Seleção de coluna | Enumerar os campos a serem copiadas da consulta, por exemplo, é necessário `SELECT field1, filed2 FROM objectname` | `SELECT *` é suportada para além de seleção de coluna. |
| Entre aspas duplas | Nomes de campo/objeto não podem estar escritos entre aspas. | Nomes de campo/objetos podem estar escritos entre aspas, por exemplo `SELECT "id" FROM "Account"` |
| Formato DateTime |  Consulte os detalhes [aqui](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) e exemplos na secção seguinte. | Consulte os detalhes [aqui](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) e exemplos na secção seguinte. |
| Valores Boolianos | Representados como `False` e `True`, por exemplo, `SELECT … WHERE IsDeleted=True`. | Representados como 0 ou 1, por exemplo, `SELECT … WHERE IsDeleted=1`. |
| Mudar o nome de coluna | Não suportado. | Suportada, por exemplo: `SELECT a AS b FROM …`. |
| Relação | Suportada, por exemplo, `Account_vod__r.nvs_Country__c`. | Não suportado. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Obter dados através de um onde cláusula na coluna DateTime

Quando especificar a consulta SOQL ou SQL, preste atenção a diferença de formato DateTime. Por exemplo:

* **Exemplo SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Exemplo SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

## <a name="data-type-mapping-for-salesforce"></a>Tipo de dados de mapeamento para o Salesforce

Quando copia dados do Salesforce, os seguintes mapeamentos são utilizados entre tipos de dados do Salesforce para tipos de dados intermediárias do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink, veja [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do Salesforce | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
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

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
