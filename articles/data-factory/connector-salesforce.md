---
title: Copiar dados de e para A Salesforce
description: Saiba como copiar dados da Salesforce para lojas de dados de sink suportadas ou de lojas de dados de origem suportada para a Salesforce, utilizando uma atividade de cópia num pipeline de fábrica de dados.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 7d380f34f849eac835abbd295cd1e2d8c17daaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153863"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Copiar dados de e para a Salesforce utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-salesforce-connector.md)
> * [Versão atual](connector-salesforce.md)

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para a Salesforce. Baseia-se no artigo de visão geral da [Copy Activity](copy-activity-overview.md) que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Salesforce é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Salesforce para qualquer loja de dados de sink suportado. Também pode copiar dados de qualquer loja de dados de origem suportada para a Salesforce. Para obter uma lista de lojas de dados que sejam suportadas como fontes ou afundados pela atividade copy, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Salesforce suporta:

- Salesforce Developer, Professional, Enterprise ou Edições Ilimitadas.
- Copiar dados de e para a produção, caixa de areia e domínio personalizado da Salesforce.

O conector Salesforce é construído em cima da Salesforce REST/Bulk API. Por predefinição, o conector utiliza [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) para copiar dados da Salesforce e utiliza [o v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) para copiar dados para a Salesforce. Também pode definir explicitamente a versão API [ `apiVersion` ](#linked-service-properties) utilizada para ler/escrever dados através de propriedade no serviço ligado.

## <a name="prerequisites"></a>Pré-requisitos

A permissão DaPI deve ser ativada na Salesforce. Para mais informações, consulte [Enable API acesso em Salesforce por conjunto de permissões](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites de pedido da Salesforce

A Salesforce tem limites tanto para os pedidos totais de API como para os pedidos simultâneos da API. Tenha em atenção os seguintes pontos:

- Se o número de pedidos simultâneos exceder o limite, o estrangulamento ocorre e verá falhas aleatórias.
- Se o número total de pedidos exceder o limite, a conta Salesforce fica bloqueada durante 24 horas.

Pode também receber a mensagem de erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Para mais informações, consulte a secção "Limites de pedidos da API" nos limites de desenvolvimento da [Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector Salesforce.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Salesforce.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para **Salesforce**. |Sim |
| ambienteUrl | Especifique o URL da instância Salesforce. <br> - O `"https://login.salesforce.com"`predefinido é . <br> - Para copiar dados da `"https://test.salesforce.com"`caixa de areia, especifique . <br> - Para copiar dados de domínio personalizado, especifique, por exemplo, `"https://[domain].my.salesforce.com"`. |Não |
| o nome de utilizador |Especifique um nome de utilizador para a conta de utilizador. |Sim |
| palavra-passe |Especifique uma palavra-passe para a conta de utilizador.<br/><br/>Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim |
| segurançaToken |Especifique um sinal de segurança para a conta de utilizador. <br/><br/>Para saber sobre fichas de segurança em geral, consulte [a Segurança e a API.](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) O token de segurança só pode ser ignorado se adicionar o IP do Tempo de Integração à [lista de endereços IP confiáveis](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) no Salesforce. Quando utilizar o Azure IR, consulte os endereços IP do Tempo de Execução de [Integração Azure](azure-integration-runtime-ip-addresses.md).<br/><br/>Para obter instruções sobre como obter e redefinir um símbolo de segurança, consulte [Obter um sinal de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Não |
| apiVersion | Especifique a versão Salesforce REST/Bulk API para utilizar, por exemplo. `48.0` Por predefinição, o conector utiliza [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) para copiar dados da Salesforce e utiliza [o v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) para copiar dados para a Salesforce. | Não |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | Não para fonte, Sim para afundar se o serviço ligado à fonte não tiver tempo de funcionação de integração |

>[!IMPORTANT]
>Quando copia dados para a Salesforce, o tempo de execução de integração azure padrão não pode ser usado para executar cópias. Por outras palavras, se o seu serviço ligado à fonte não tiver um tempo de funcionamento de integração especificado, crie explicitamente um Tempo de [Funcionamento de Integração Azure](create-azure-integration-runtime.md#create-azure-ir) com uma localização perto da sua instância Salesforce. Associar o serviço ligado salesforce como no exemplo seguinte.

**Exemplo: Armazenar credenciais na Fábrica de Dados**

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

**Exemplo: Armazenar credenciais no Cofre-Chave**

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Salesforce.

Para copiar dados de e para a Salesforce, detete a propriedade tipo do conjunto de dados para **SalesforceObject**. As seguintes propriedades são suportadas.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **SalesforceObject**.  | Sim |
| objectApiName | O nome do objeto Salesforce para recuperar dados de. | Não para a fonte, sim para afundar |

> [!IMPORTANT]
> A parte "__c" do **Nome API** é necessária para qualquer objeto personalizado.

![Data Factory Salesforce ligação Nome API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemplo:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>Para a retrocompatibilidade: Quando copia dados da Salesforce, se utilizar o conjunto de dados do tipo "RelationalTable" anterior, continua a funcionar enquanto vê uma sugestão para mudar para o novo tipo "SalesforceObject".

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para **RelacionalTable**. | Sim |
| tableName | Nome da mesa na Salesforce. | Não (se for especificada a "consulta" na fonte de atividade) |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte e pia da Salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce como um tipo de fonte

Para copiar dados da Salesforce, delineie o tipo de origem na atividade de cópia para **SalesforceSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **SalesforceSource**. | Sim |
| consulta |Use a consulta personalizada para ler dados. Pode utilizar a consulta de consulta de [objetos salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) ou consulta SQL-92. Veja mais dicas na secção de dicas de [consulta.](#query-tips) Se a consulta não for especificada, todos os dados do objeto Salesforce especificados no "objectApiName" no conjunto de dados serão recuperados. | Não (se for especificado "objectApiName" no conjunto de dados) |
| lerComportamento | Indica se deve consultar os registos existentes ou consultar todos os registos, incluindo os apagados. Se não especificado, o comportamento padrão é o primeiro. <br>Valores permitidos: **consulta** (padrão), **consultaAll**.  | Não |

> [!IMPORTANT]
> A parte "__c" do **Nome API** é necessária para qualquer objeto personalizado.

![Data Factory Salesforce ligação Lista de nomes API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>Para a retrocompatibilidade: Quando copia dados da Salesforce, se utilizar a cópia do tipo "RelationalSource" anterior, a fonte continua a funcionar enquanto vê uma sugestão para mudar para o novo tipo "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce como um tipo de pia

Para copiar dados para a Salesforce, delineie o tipo de pia na atividade de cópia para **SalesforceSink**. As seguintes propriedades são suportadas na secção de **sumidouro** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para **SalesforceSink**. | Sim |
| escreverComportamento | O comportamento de escrita para a operação.<br/>Os valores permitidos são **Insert** and **Upsert**. | Não (predefinido é Inserir) |
| externoIdFieldName | O nome do campo de identificação externo para a operação upsert. O campo especificado deve ser definido como "Campo de Identificação Externa" no objeto Salesforce. Não pode ter valores NULOs nos dados de entrada correspondentes. | Sim para "Upsert" |
| escreverBatchSize | A contagem de linhas de dados escritos à Salesforce em cada lote. | Não (o padrão é de 5.000) |
| ignorar Valores Nulos | Indica se deve ignorar os valores NULOs dos dados de entrada durante uma operação de escrita.<br/>Os valores permitidos são **verdadeiros** e **falsos.**<br>- **Verdade**: Deixe os dados no objeto de destino inalterados quando fizer uma operação de atualização ou atualização. Insira um valor predefinido definido quando fizer uma operação de inserção.<br/>- **Falso**: Atualize os dados no objeto de destino para NULL quando fizer uma operação de atualização ou atualização. Insira um valor NULO quando fizer uma operação de inserção. | Não (o padrão é falso) |

**Exemplo: Salesforce afunda numa atividade de cópia**

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

## <a name="query-tips"></a>Dicas de consulta

### <a name="retrieve-data-from-a-salesforce-report"></a>Recuperar dados de um relatório da Salesforce

Pode obter dados dos relatórios da Salesforce `{call "<report name>"}`especificando uma consulta como . Um exemplo é `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Recuperar registos apagados do Caixote do Reciclagem salesforce

Para consultar os registos apagados suaves da Caixa `readBehavior` de `queryAll`Reciclagem salesforce, pode especificar como . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Diferença entre sintaxe de consulta SOQL e SQL

Ao copiar dados da Salesforce, pode utilizar consulta SOQL ou consulta SQL. Note que estes dois têm diferentes sintaxe e suporte de funcionalidade, não a misture. É-lhe sugerido que utilize a consulta SOQL que é apoiada de forma nativa pela Salesforce. A tabela que se segue enumera as principais diferenças:

| Sintaxe | Modo SOQL | Modo SQL |
|:--- |:--- |:--- |
| Seleção de colunas | Precisa enumerar os campos a copiar na consulta, por exemplo.`SELECT field1, filed2 FROM objectname` | `SELECT *`é suportado para além da seleção da coluna. |
| Aspas | Os nomes arquivados/objetos não podem ser citados. | Os nomes de campo/objeto podem ser citados, por exemplo.`SELECT "id" FROM "Account"` |
| Formato data-data |  Consulte os detalhes [aqui](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) e amostras na próxima secção. | Consulte os detalhes [aqui](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) e amostras na próxima secção. |
| Valores booleanos | Representado como `False` `True`e, por `SELECT … WHERE IsDeleted=True`exemplo. | Representado como 0 ou 1, `SELECT … WHERE IsDeleted=1`por exemplo. |
| Coluna de renomeação | Não suportado. | Apoiado, por exemplo: `SELECT a AS b FROM …`. |
| Relação | Apoiado, por exemplo. `Account_vod__r.nvs_Country__c` | Não suportado. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recuperar dados utilizando uma cláusula onde na coluna DateTime

Quando especificar a consulta SOQL ou SQL, preste atenção à diferença de formato DateTime. Por exemplo:

* **Amostra SOQL:**`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Amostra SQL:**`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Erro de MALFORMED_QUERY:Truncado

Se tiver um erro de "MALFORMED_QUERY: Truncado", normalmente é devido ao facto de ter coluna do tipo JunctionIdList em dados e a Salesforce tem limitação em suportar esses dados com um grande número de linhas. Para mitigar, tente excluir a coluna JunctionIdList ou limitar o número de linhas para copiar (pode dividir para várias executações de atividade de cópia).

## <a name="data-type-mapping-for-salesforce"></a>Mapeamento de tipo de dados para Salesforce

Quando copia dados da Salesforce, os seguintes mapeamentos são utilizados desde tipos de dados salesforce a tipos de dados provisórios da Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

| Tipo de dados salesforce | Data Factory tipo de dados provisórios |
|:--- |:--- |
| Número automático |Cadeia |
| Caixa de verificação |Booleano |
| Moeda |Decimal |
| Date |DateTime |
| Date/Time |DateTime |
| Email |Cadeia |
| Id |Cadeia |
| Relação de procura |Cadeia |
| Lista de Picklist multi-selecionada |Cadeia |
| Número |Decimal |
| Percentagem |Decimal |
| Telefone |Cadeia |
| Picklist |Cadeia |
| Texto |Cadeia |
| Área de Texto |Cadeia |
| Área de texto (longa) |Cadeia |
| Área de Texto (Rica) |Cadeia |
| Texto (Encriptado) |Cadeia |
| do IdP |Cadeia |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
