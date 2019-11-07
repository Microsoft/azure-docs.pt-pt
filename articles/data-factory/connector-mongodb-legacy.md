---
title: Copiar dados do MongoDB usando o Azure Data Factory
description: Saiba como copiar dados do Mongo DB para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0c2c2d9ad78bb09a37faaa5825f8dae3e27370ea
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680679"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados do MongoDB usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Versão atual](connector-mongodb.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de um banco de dados MongoDB. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!IMPORTANT]
>Versão do ADF um novo conector do MongoDB que fornece melhor suporte nativo do MongoDB para comparação com essa implementação baseada em ODBC, consulte o artigo [conector do MongoDB](connector-mongodb.md) em detalhes. Esse conector do MongoDB herdado é mantido com suporte como está para a compatibilidade com versões anteriores, enquanto para qualquer nova carga de trabalho, use o novo conector.

## <a name="supported-capabilities"></a>Recursos com suporte

Você pode copiar dados de um banco de dados MongoDB para qualquer armazenamento de dado de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector do MongoDB dá suporte a:

- MongoDB **versões 2,4, 2,6, 3,0, 3,2, 3,4 e 3,6**.
- Copiar dados usando a autenticação **básica** ou **anônima** .

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Integration Runtime fornece um driver MongoDB interno, portanto, você não precisa instalar manualmente nenhum driver ao copiar dados do MongoDB.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade Type deve ser definida como: **MongoDB** |Sim |
| servidor |Endereço IP ou nome do host do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor MongoDB usa para escutar conexões de cliente. |Não (o padrão é 27017) |
| NomeDoBancoDeDados |Nome do banco de dados MongoDB que você deseja acessar. |Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados MongoDB.<br/>Os valores permitidos são: **básico**e **anônimo**. |Sim |
| o nome de utilizador |Conta de usuário para acessar o MongoDB. |Sim (se a autenticação básica for usada). |
| palavra-passe |A palavra-passe do utilizador. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim (se a autenticação básica for usada). |
| AuthName |Nome do banco de dados MongoDB que você deseja usar para verificar suas credenciais para autenticação. |Não. Para a autenticação básica, o padrão é usar a conta de administrador e o banco de dados especificado usando a propriedade databaseName. |
| enableSsl | Especifica se as conexões com o servidor são criptografadas usando SSL. O valor padrão é false.  | Não |
| allowSelfSignedServerCert | Especifica se os certificados autoassinados devem ser permitidos do servidor. O valor padrão é false.  | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [conjuntos de valores e serviços vinculados](concepts-datasets-linked-services.md). As propriedades a seguir têm suporte para o conjunto de conjuntos do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **mongodbcollection** | Sim |
| collectionName |Nome da coleção no banco de dados MongoDB. |Sim |

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do MongoDB.

### <a name="mongodb-as-source"></a>MongoDB como fonte

As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **MongoDB** | Sim |
| consulta |Use a consulta SQL-92 personalizada para ler os dados. Por exemplo: selecione * em MyTable. |Não (se "CollectionName" no DataSet for especificado) |

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
> Ao especificar a consulta SQL, preste atenção ao formato DateTime. Por exemplo: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` ou para usar o parâmetro `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Esquema por Data Factory

Azure Data Factory Service infere o esquema de uma coleção do MongoDB usando os **últimos 100 documentos** na coleção. Se esses 100 documentos não contiverem esquema completo, algumas colunas poderão ser ignoradas durante a operação de cópia.

## <a name="data-type-mapping-for-mongodb"></a>Mapeamento de tipo de dados para o MongoDB

Ao copiar dados do MongoDB, os seguintes mapeamentos são usados de tipos de dados do MongoDB para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e o esquema de origem para o coletor.

| Tipo de dados do MongoDB | Tipo de dados provisório do data Factory |
|:--- |:--- |
| binário |Byte [] |
| Booleano |Booleano |
| Date |DateTime |
| NumberDouble |Clique |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| PERSONALIZADO |GUID |
| Object |Renormalizado para colunas achatadas com "_" como separador aninhado |

> [!NOTE]
> Para saber mais sobre o suporte para matrizes usando tabelas virtuais, consulte a seção [suporte para tipos complexos usando tabelas virtuais](#support-for-complex-types-using-virtual-tables) .
>
> Atualmente, não há suporte para os seguintes tipos de dados do MongoDB: DBPointer, JavaScript, Max/min Key, expressão regular, símbolo, carimbo de data/hora, indefinido.

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos usando tabelas virtuais

Azure Data Factory usa um driver ODBC interno para se conectar e copiar dados de seu banco de dados MongoDB. Para tipos complexos, como matrizes ou objetos com diferentes tipos em todos os documentos, o driver renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver essas colunas, o driver gerará as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados que a tabela real, exceto para as colunas de tipo complexo. A tabela base usa o mesmo nome que a tabela real que ela representa.
* Uma **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da tabela real, um separador "_" e o nome da matriz ou objeto.

As tabelas virtuais referem-se aos dados na tabela real, permitindo que o driver acesse os dados desnormalizados. Você pode acessar o conteúdo de matrizes do MongoDB consultando e unindo as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, o exemplo aqui é uma tabela do MongoDB que tem uma coluna com uma matriz de objetos em cada célula – faturas e uma coluna com uma matriz de tipos escalares – classificações.

| _id | Nome do cliente | Faturas | Nível de Serviço | As |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", item: "torradeira", Price: "456", Discount: "0.2"}, {invoice_id: "124", item: "forno", Price: "1235", Discount: "0,2"}] |Silver |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135", item: "geladeira", preço: "12543", desconto: "0,0"}] |Gold |[1, 2] |

O driver geraria várias tabelas virtuais para representar essa única tabela. A primeira tabela virtual é a tabela base chamada "ExampleTable", mostrada no exemplo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e expandidos nas tabelas virtuais.

| _id | Nome do cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas a seguir mostram as tabelas virtuais que representam as matrizes originais no exemplo. Essas tabelas contêm o seguinte:

* Uma referência de volta à coluna de chave primária original correspondente à linha da matriz original (por meio da coluna _id)
* Uma indicação da posição dos dados na matriz original
* Os dados expandidos para cada elemento dentro da matriz

**Tabela "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |torradeira |456 |0,2 |
| 1111 |1 |124 |forno |1235 |0,2 |
| 2222 |0 |135 |geladeira |12543 |0,0 |

**Tabela "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
