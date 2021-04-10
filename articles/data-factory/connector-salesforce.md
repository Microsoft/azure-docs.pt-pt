---
title: Copiar dados de e para a Salesforce
description: Saiba como copiar dados da Salesforce para lojas de dados de sumidouros suportados ou de lojas de dados de origem suportadas para a Salesforce utilizando uma atividade de cópia num oleoduto de fábrica de dados.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 5b49e62330c789d6d5cbe2af2edb28a2c3e1238f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583109"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Copiar dados de e para a Salesforce utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-salesforce-connector.md)
> * [Versão atual](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity na Azure Data Factory para copiar dados de e para a Salesforce. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da Copy Activity que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Salesforce é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Salesforce para qualquer loja de dados de lavatórios suportados. Também pode copiar dados de qualquer loja de dados de origem suportada para a Salesforce. Para obter uma lista de lojas de dados que são suportadas como fontes ou sumidouros pela atividade Copy, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Salesforce suporta:

- Salesforce Developer, Professional, Enterprise ou Edições Ilimitadas.
- Copiar dados de e para a produção da Salesforce, caixa de areia e domínio personalizado.

O conector Salesforce é construído em cima da Salesforce REST/API a granel. Por predefinição, ao copiar dados da Salesforce, o conector utiliza [o V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) e escolhe automaticamente entre APIs REST e Bulk com base no tamanho dos dados – quando o conjunto de resultados é grande, a API a granel é utilizada para um melhor desempenho; ao escrever dados à Salesforce, o conector utiliza [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) de API a granel. Também pode definir explicitamente a versão API usada para ler/escrever dados através de [ `apiVersion` propriedade](#linked-service-properties) em serviço ligado.

## <a name="prerequisites"></a>Pré-requisitos

A permissão da API deve ser ativada na Salesforce. Para obter mais informações, consulte [o acesso da API ao Salesforce por autorização](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites de pedido da Salesforce

A Salesforce tem limites tanto para os pedidos totais de API como para os pedidos simultâneos de API. Tenha em atenção os seguintes pontos:

- Se o número de pedidos simultâneos exceder o limite, ocorre estrangulamento e vê falhas aleatórias.
- Se o número total de pedidos exceder o limite, a conta Salesforce fica bloqueada por 24 horas.

Pode também receber a mensagem de erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Para obter mais informações, consulte a secção "Limites de pedido de API" nos [limites de desenvolvimento da Salesforce](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Salesforce.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Salesforce.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para **Salesforce.** |Yes |
| ambienteUrl | Especifique o URL da instância Salesforce. <br> - O padrão é `"https://login.salesforce.com"` . <br> - Para copiar dados da caixa de areia, especifique `"https://test.salesforce.com"` . <br> - Para copiar dados do domínio personalizado, especifique, por exemplo, `"https://[domain].my.salesforce.com"` . |No |
| nome de utilizador |Especifique um nome de utilizador para a conta de utilizador. |Yes |
| palavra-passe |Especifique uma palavra-passe para a conta de utilizador.<br/><br/>Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). |Yes |
| securityToken |Especifique um sinal de segurança para a conta de utilizador. <br/><br/>Para conhecer os tokens de segurança em geral, consulte [a Segurança e a API.](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) O token de segurança só pode ser ignorado se adicionar o IP do Tempo de Integração à [lista de endereços IP fidedignas](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) no Salesforce. Ao utilizar o Azure IR, consulte os [endereços IP do tempo de execução da integração Azure](azure-integration-runtime-ip-addresses.md).<br/><br/>Para obter instruções sobre como obter e redefinir um sinal de segurança, consulte [obter um token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). |No |
| apiVersion | Especifique a versão API salesforce/granel para utilizar, por `48.0` exemplo. Por predefinição, o conector utiliza [o V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) para copiar dados da Salesforce e utiliza [o V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) para copiar dados para a Salesforce. | No |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | No |

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

**Exemplo: Armazenar credenciais no Cofre de Chaves**

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

Para copiar dados de e para Salesforce, defina o tipo de propriedade do conjunto de dados para **SalesforceObject**. As seguintes propriedades são suportadas.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **SalesforceObject**.  | Yes |
| objectApiName | O nome do objeto Salesforce para obter dados de. | Não para a fonte, sim para a pia |

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
>Para retrocompatibilidade: Quando copiar dados da Salesforce, se utilizar o conjunto de dados do tipo "RelationalTable" anterior, ele continua a funcionar enquanto vê uma sugestão para mudar para o novo tipo "SalesforceObject".

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida como **RelationalTable**. | Yes |
| tableName | O nome da mesa na Salesforce. | Não (se for especificada "consulta" na fonte de atividade) |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de imóveis suportados pela fonte e pia salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce como tipo de origem

Para copiar dados da Salesforce, deteta o tipo de origem na atividade de cópia para **SalesforceSource**. As seguintes propriedades são suportadas na secção **de origem** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para **SalesforceSource**. | Yes |
| consulta |Utilize a consulta personalizada para ler dados. Pode utilizar a consulta [de idioma de consulta de objetos salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) ou consulta SQL-92. Veja mais dicas na secção [de dicas de consulta.](#query-tips) Se a consulta não for especificada, todos os dados do objeto Salesforce especificados em "objectApiName" no conjunto de dados serão recuperados. | Não (se for especificado "objectApiName" no conjunto de dados) |
| readOportur-se | Indica se deve consultar os registos existentes ou consultar todos os registos, incluindo os eliminados. Se não for especificado, o comportamento padrão é o primeiro. <br>Valores permitidos: **consulta** (predefinição), **consultaTo.**  | No |

> [!IMPORTANT]
> A parte "__c" do **Nome API** é necessária para qualquer objeto personalizado.

![Lista de nomes API de conexão de vendas de fábrica de dados](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>Para retrocompatibilidade: Quando copiar dados da Salesforce, se utilizar a cópia do tipo "RelationalSource" anterior, a fonte continua a funcionar enquanto vê uma sugestão para mudar para o novo tipo "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce como um tipo de pia

Para copiar dados para a Salesforce, desaperte o tipo de pia na atividade de cópia para **SalesforceSink**. As seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do lavatório de atividade de cópia deve ser definida para **SalesforceSink**. | Yes |
| escrever Comportamento | O comportamento de escrita para a operação.<br/>Os valores permitidos são **Insert** e **Upsert**. | Não (predefinição é Inserir) |
| nome externoIdField | O nome do campo de identificação externo para a operação de upsert. O campo especificado deve ser definido como "Campo de Identificação Externa" no objeto Salesforce. Não pode ter valores NULOS nos dados de entrada correspondentes. | Sim para "Upsert" |
| escreverBatchSize | A contagem de dados escrita à Salesforce em cada lote. | Não (o padrão é 5.000) |
| ignoreNullValues | Indica se deve ignorar os valores NUS dos dados de entrada durante uma operação de escrita.<br/>Os valores permitidos são **verdadeiros** e **falsos.**<br>- **Verdade**: Deixe os dados no objeto de destino inalterados quando fizer uma operação de atualização ou atualização. Insira um valor predefinido definido quando fizer uma operação de inserção.<br/>- **Falso**: Atualize os dados no objeto de destino para NU QUANDO fizer uma operação de atualização ou atualização. Insira um valor NULO quando fizer uma operação de inserção. | Não (o padrão é falso) |
| maxConcurrentConnections |O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas.| No |

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

Pode obter dados dos relatórios da Salesforce especificando uma consulta como `{call "<report name>"}` . Um exemplo é `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Recuperar registos apagados do Caixote do Reciclagem da Salesforce

Para consultar os registos suaves eliminados do Caixote do Lixo de Reciclagem da Salesforce, pode especificar `readBehavior` como `queryAll` . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Diferença entre sintaxe de consulta SOQL e SQL

Ao copiar dados da Salesforce, pode utilizar consulta SOQL ou consulta SQL. Note que estes dois têm diferentes sintaxe e suporte de funcionalidade, não misture. Sugere-se que utilize a consulta SOQL, que é apoiada de forma nativa pela Salesforce. A tabela a seguir enumera as principais diferenças:

| Syntax | Modo SOQL | Modo SQL |
|:--- |:--- |:--- |
| Seleção de colunas | É necessário enumerar os campos a serem copiados na consulta, por exemplo. `SELECT field1, filed2 FROM objectname` | `SELECT *` é suportado para além da seleção de colunas. |
| Marcas de aspas | Os nomes arquivados/objetos não podem ser citados. | Os nomes de campo/objeto podem ser citados, por exemplo. `SELECT "id" FROM "Account"` |
| Formato de data |  Consulte os detalhes [aqui](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) e as amostras na secção seguinte. | Consulte os detalhes [aqui](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals) e as amostras na secção seguinte. |
| Valores booleano | Representado como `False` `True` e, por `SELECT … WHERE IsDeleted=True` exemplo. | Representado como 0 ou 1, por `SELECT … WHERE IsDeleted=1` exemplo. |
| Renomeação de coluna | Não suportado. | Apoiado, por exemplo: `SELECT a AS b FROM …` . |
| Relação | Apoiado, por `Account_vod__r.nvs_Country__c` exemplo. | Não suportado. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recupere os dados utilizando uma cláusula em que a cláusula da coluna DateTime

Quando especificar a consulta SOQL ou SQL, preste atenção à diferença do formato DateTime. Por exemplo:

* **Amostra SOQL:**`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Amostra SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>Erro de MALFORMED_QUERY: Truncado

Se atingir o erro de "MALFORMED_QUERY: Truncated", normalmente é devido a ter coluna tipo JunctionIdList em dados e a Salesforce tem limitação em suportar esses dados com um grande número de linhas. Para atenuar, tente excluir a coluna JunctionIdList ou limitar o número de linhas a copiar (pode dividir-se a múltiplas operações de cópia).

## <a name="data-type-mapping-for-salesforce"></a>Mapeamento do tipo de dados para Salesforce

Ao copiar dados da Salesforce, os seguintes mapeamentos são usados desde os tipos de dados da Salesforce até aos tipos de dados provisórios da Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados salesforce | Tipo de dados provisórios da Data Factory |
|:--- |:--- |
| Número automático |String |
| Caixa de verificação |Booleano |
| Moeda |Decimal |
| Data |DateTime |
| Data/Hora |DateTime |
| E-mail |String |
| ID |String |
| Relação de procura |String |
| Lista de escolhas multi-selecionadas |String |
| Número |Decimal |
| Percentagem |Decimal |
| Telefone |String |
| Picklist |String |
| Texto |Cadeia |
| Área de Texto |String |
| Área de texto (longo) |String |
| Área de texto (Rico) |String |
| Texto (Encriptado) |String |
| URL |String |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)