---
title: Copiar dados da MongoDB usando o legado
description: Saiba como copiar dados do Mongo DB para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 803e34a93e8019cfc2577bfaab3ba13c409c6b01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418172"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados da MongoDB utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Versão atual](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados mongoDB. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!IMPORTANT]
>A ADF lança um novo conector MongoDB que fornece um melhor suporte nativo mongoDB em comparação com esta implementação baseada em ODBC, consulte o artigo do [conector MongoDB](connector-mongodb.md) sobre detalhes. Este conector MongoDB legado é mantido suportado como é para a compbilidade retrógrada, enquanto para qualquer nova carga de trabalho, por favor use o novo conector.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados do MongoDB para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector MongoDB suporta:

- Versões MongoDB **2.4, 2.6, 3.0, 3.2, 3.4 e 3.6**.
- Copiar dados utilizando autenticação **Básica** ou **Anónima.**

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Tempo de Funcionamento de Integração fornece um controlador MongoDB incorporado, pelo que não precisa de instalar manualmente qualquer controlador ao copiar dados da MongoDB.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector MongoDB.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **MongoDb** |Sim |
| servidor |Endereço IP ou nome de anfitrião do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor MongoDB usa para ouvir ligações ao cliente. |Não (padrão é 27017) |
| nome da base de dados |Nome da base de dados MongoDB a que pretende aceder. |Sim |
| authenticationType | Tipo de autenticação utilizada para ligar à base de dados MongoDB.<br/>Os valores permitidos são: **Básico,** e **Anónimo.** |Sim |
| o nome de utilizador |Conta de utilizador para aceder ao MongoDB. |Sim (se for utilizada a autenticação básica). |
| palavra-passe |A palavra-passe do utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim (se for utilizada a autenticação básica). |
| authSource |Nome da base de dados MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Não. Para a autenticação básica, a predefinição é utilizar a conta de administração e a base de dados especificada utilizando a propriedade databaseName. |
| enableSsl | Especifica se as ligações ao servidor estão encriptadas utilizando TLS. O valor predefinido é false.  | Não |
| permitir AutoSignedServerCert | Especifica se permite certificados auto-assinados a partir do servidor. O valor predefinido é false.  | Não |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

**Exemplo:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados.](concepts-datasets-linked-services.md) As seguintes propriedades são suportadas para dataset MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **MongoDbCollection** | Sim |
| coleçãoNome |Nome da coleção na base de dados mongoDB. |Sim |

**Exemplo:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte mongoDB.

### <a name="mongodb-as-source"></a>MongoDB como fonte

As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **MongoDbSource** | Sim |
| consulta |Utilize a consulta Personalizada SQL-92 para ler dados. Por exemplo: selecione * do MyTable. |Não (se for especificado "collectionName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Quando especificar a consulta SQL, preste atenção ao formato DateTime. Por exemplo: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` ou utilizar parâmetros`SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schema by Data Factory

O serviço Azure Data Factory infere o esquema de uma coleção mongoDB utilizando os **últimos 100 documentos** da coleção. Se estes 100 documentos não contiverem esquema completo, algumas colunas podem ser ignoradas durante a operação de cópia.

## <a name="data-type-mapping-for-mongodb"></a>Mapeamento de tipo de dados para MongoDB

Ao copiar dados do MongoDB, os seguintes mapeamentos são utilizados desde tipos de dados MongoDB para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados mongoDB | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| Binário |Byte[] |
| Booleano |Booleano |
| Date |DateTime |
| Número Duplo |Double |
| NúmeroInt |Int32 |
| Número longo |Int64 |
| ObjectID |String |
| String |String |
| UUID |GUID |
| Objeto |Renormalizado em colunas achatadas com "_" como separador aninhada |

> [!NOTE]
> Para aprender sobre o suporte para matrizes usando tabelas virtuais, consulte o Suporte para tipos complexos usando a secção [de tabelas virtuais.](#support-for-complex-types-using-virtual-tables)
>
> Atualmente, os seguintes tipos de dados MongoDB não são suportados: DBPointer, JavaScript, Max/Min key, Regular Expression, Symbol, Timestamp, Undefined.

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos que usam tabelas virtuais

A Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados da sua base de dados MongoDB. Para tipos complexos como matrizes ou objetos com diferentes tipos em todos os documentos, o condutor renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver tais colunas, o condutor gera as seguintes tabelas virtuais:

* Uma **tabela base,** que contém os mesmos dados que a tabela real, exceto as colunas de tipo complexos. A tabela base usa o mesmo nome que a verdadeira tabela que representa.
* Uma **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da mesa real, um separador "_" e o nome da matriz ou objeto.

As tabelas virtuais referem-se aos dados na tabela real, permitindo ao condutor aceder aos dados desnormalizados. Você pode aceder ao conteúdo das matrizes MongoDB consultando e juntando as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, o ExampleTable aqui é uma tabela MongoDB que tem uma coluna com um conjunto de Objetos em cada célula – Faturas e uma coluna com uma matriz de tipos Scalar – Classificações.

| _id | Nome do cliente | Faturas | Nível de Serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"torradeira", preço:"456", desconto:"0.2"}, {invoice_id:"124", item:"forno", preço: "1235", desconto: "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"frigorífico", preço: "12543", desconto: "0.0"}] |Gold |[1,2] |

O condutor geraria várias tabelas virtuais para representar esta única tabela. A primeira tabela virtual é a tabela base chamada "ExampleTable", mostrada no exemplo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e são expandidos nas tabelas virtuais.

| _id | Nome do cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas seguintes mostram as tabelas virtuais que representam as matrizes originais no exemplo. Estas tabelas contêm o seguinte:

* Uma referência de volta à coluna principal original correspondente à linha da matriz original (através da coluna _id)
* Uma indicação da posição dos dados dentro da matriz original
* Os dados expandidos para cada elemento dentro da matriz

**Tabela "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |torradeira |456 |0,2 |
| 1111 |1 |124 |forno |1235 |0,2 |
| 2222 |0 |135 |frigorífico |12543 |0,0 |

**Tabela "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
