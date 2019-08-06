---
title: Copiar dados do MongoDB com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de Mongo DB para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 86dcd39ad7b9f1e207e9254ec72698db3998bbd6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61400479"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados do MongoDB com o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Versão atual](connector-mongodb.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados do MongoDB. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

>[!IMPORTANT]
>Versão do ADF um novo conector do MongoDB que fornece melhor MongoDB nativo suportam o comparando com essa implementação baseado em ODBC, consulte [conector do MongoDB](connector-mongodb.md) artigo em detalhes. Este conector herdado do MongoDB é mantida suportado como-é para compatibilidade com versões anteriores, enquanto para qualquer carga de trabalho novo, utilize o novo conector.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados do MongoDB para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do MongoDB suporta:

- MongoDB **versões 2.4, 2.6, 3.0, 3.2, 3.4 e 3.6**.
- Copiar dados utilizando **básica** ou **anónimo** autenticação.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de uma base de dados do MongoDB que não está acessível ao público, terá de configurar um Runtime de integração autoalojado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para saber mais detalhes. O Runtime de integração fornece um driver de MongoDB incorporado, portanto não precisa de instalar manualmente a qualquer driver quando copiar dados do MongoDB.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type |A propriedade de tipo tem de ser definida como: **MongoDb** |Sim |
| server |Nome anfitrião ou endereço IP do servidor do MongoDB. |Sim |
| port |Porta TCP que o servidor do MongoDB utiliza para escutar ligações de cliente. |Não (a predefinição é 27017) |
| databaseName |Nome da base de dados do MongoDB que pretende aceder. |Sim |
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados do MongoDB.<br/>Valores permitidos são: **Básica**, e **anônimo**. |Sim |
| username |Conta de utilizador para aceder a MongoDB. |Sim (se for utilizada autenticação básica). |
| password |A palavra-passe do utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim (se for utilizada autenticação básica). |
| authSource |Nome da base de dados do MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Não. Para a autenticação básica, a predefinição é utilizar a conta de administrador e a base de dados especificada, utilizando a propriedade databaseName. |
| enableSsl | Especifica se as ligações ao servidor são encriptadas com SSL. O valor predefinido é false.  | Não |
| allowSelfSignedServerCert | Especifica se pretende permitir que os certificados autoassinados do servidor. O valor predefinido é false.  | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados e serviços ligados](concepts-datasets-linked-services.md). As seguintes propriedades são suportadas para o conjunto de dados do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **MongoDbCollection** | Sim |
| collectionName |Nome da coleção na base de dados do MongoDB. |Sim |

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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do MongoDB.

### <a name="mongodb-as-source"></a>MongoDB como origem

As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **MongoDbSource** | Sim |
| query |Utilize a consulta de SQL-92 personalizada para ler os dados. Por exemplo: selecionar * de MyTable. |Não (se for especificado "collectionName" no conjunto de dados) |

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
> Quando especificar a consulta SQL, preste atenção para o formato de DateTime. Por exemplo: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` ou para utilizar o parâmetro `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Esquema pelo Data Factory

O serviço do Azure Data Factory infere o esquema de uma coleção do MongoDB ao utilizar o **mais recente 100 documentos** na coleção. Se esses 100 documentos não contêm esquema completo, algumas colunas podem ser ignoradas durante a operação de cópia.

## <a name="data-type-mapping-for-mongodb"></a>Tipo de dados de mapeamento para o MongoDB

Ao copiar dados do MongoDB, os seguintes mapeamentos são utilizados entre tipos de dados do MongoDB para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do MongoDB | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| Binary |Byte[] |
| Boolean |Boolean |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| Object |Renormalized em colunas com "_" como separador aninhada de nivelamento |

> [!NOTE]
> Para saber mais sobre o suporte para matrizes com tabelas virtuais, consulte [suporte para tipos complexos usando tabelas virtuais](#support-for-complex-types-using-virtual-tables) secção.
>
> Atualmente, não são suportados os seguintes tipos de dados do MongoDB: Máx. por minuto de DBPointer, JavaScript, chave, expressões regulares, símbolo, Timestamp, indefinido.

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos usando tabelas virtuais

O Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados de sua base de dados do MongoDB. Para obter tipos complexos, como matrizes ou objetos com diferentes tipos de documentos, o driver normaliza novamente dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contém este tipo de colunas, o driver gera as tabelas virtuais seguintes:

* R **tabela base**, que contém os mesmos dados que a tabela real, exceto as colunas de tipo complexo. A tabela base utiliza o mesmo nome, como a tabela real que representa.
* R **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da tabela real, um separador de "_" e o nome do objeto ou matriz.

Tabelas virtuais referem-se aos dados na tabela real, permitindo que o driver acessar os dados desnormalizados. Pode acessar o conteúdo das matrizes de MongoDB consultando e associar as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, ExampleTable aqui é uma tabela do MongoDB que tem uma coluna com uma matriz de objetos em cada célula – notas fiscais e uma coluna com uma matriz de tipos escalares – classificações.

| _id | Nome do cliente | Faturas | Nível de Serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] |Prateado |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] |Dourado |[1,2] |

O controlador irá gerar várias tabelas virtuais para representar nesta única tabela. A primeira tabela virtual é a tabela de base com o nome "ExampleTable", mostrado no exemplo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e são expandidos nas tabelas virtuais.

| _id | Nome do cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Prateado |
| 2222 |XYZ |Dourado |

As tabelas seguintes mostram as tabelas virtuais que representam as matrizes originais no exemplo. Estas tabelas contenham o seguinte:

* Uma referência para a coluna de chave primária original correspondente para a linha da matriz original (através da coluna de ID)
* Uma indicação de que a posição dos dados dentro da matriz original
* Os dados expandidos para cada elemento dentro da matriz

**Tabela de "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |torradeira |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |frigorífico |12543 |0.0 |

**Table “ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
