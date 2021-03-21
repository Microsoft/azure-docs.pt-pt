---
title: Copiar dados da MongoDB utilizando o legado
description: Saiba como copiar dados da Mongo DB para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num oleoduto legado da Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: e13a1a5a939d314bdf4500c0827fa13201505016
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368851"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory-legacy"></a>Copiar dados da MongoDB utilizando a Azure Data Factory (legado)

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Versão atual](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados mongoDB. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

>[!IMPORTANT]
>A ADF lança um novo conector MongoDB que fornece um melhor suporte nativo do MongoDB em comparação com esta implementação baseada em ODBC, consulte o artigo [do conector MongoDB](connector-mongodb.md) sobre detalhes. Este conector MongoDB legado é mantido suportado como é para compabilidade retrógrada, enquanto para qualquer nova carga de trabalho, por favor, use o novo conector.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados mongoDB para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector MongoDB suporta:

- Versões MongoDB **2.4, 2.6, 3.0, 3.2, 3.4 e 3.6**.
- Copiar dados utilizando a autenticação **básica** ou **anónima.**

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Tempo de Execução de Integração fornece um controlador MongoDB incorporado, pelo que não precisa de instalar manualmente nenhum controlador ao copiar dados da MongoDB.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector MongoDB.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado a MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **MongoDb** |Yes |
| servidor |Endereço IP ou nome de anfitrião do servidor MongoDB. |Yes |
| porta |Porta TCP que o servidor MongoDB utiliza para ouvir as ligações do cliente. |Não (padrão é 27017) |
| base de dados Nome |Nome da base de dados mongoDB a que pretende aceder. |Yes |
| authenticationType | Tipo de autenticação usada para ligar à base de dados mongoDB.<br/>Os valores permitidos são: **Básico,** e **Anónimo.** |Yes |
| nome de utilizador |Conta de utilizador para aceder a MongoDB. |Sim (se for utilizada a autenticação básica). |
| palavra-passe |A palavra-passe do utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). |Sim (se for utilizada a autenticação básica). |
| authSource |Nome da base de dados MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |N.º Para a autenticação básica, o predefinido é utilizar a conta de administração e a base de dados especificada através da base de dados PropriedadeName. |
| ativarSl | Especifica se as ligações ao servidor são encriptadas utilizando O S.TLS. O valor predefinido é false.  | No |
| permitirSelfSignedServerCert | Especifica se permite certificados auto-assinados a partir do servidor. O valor predefinido é false.  | No |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md). As seguintes propriedades são suportadas para conjunto de dados MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para: **MongoDbCollection** | Yes |
| coleçãoName |Nome da coleção na base de dados mongoDB. |Yes |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte mongoDB.

### <a name="mongodb-as-source"></a>MongoDB como fonte

As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **MongoDbSource** | Yes |
| consulta |Utilize a consulta personalizada SQL-92 para ler dados. Por exemplo: selecione * do MyTable. |Não (se for especificado "nome de recolha" no conjunto de dados) |

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
> Quando especificar a consulta SQL, preste atenção ao formato DateTime. Por exemplo: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` ou para usar o parâmetro `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schema by Data Factory

O serviço Azure Data Factory infere o esquema de uma coleção mongoDB utilizando os **mais recentes 100 documentos** da coleção. Se estes 100 documentos não contiverem esquema completo, algumas colunas podem ser ignoradas durante a operação da cópia.

## <a name="data-type-mapping-for-mongodb"></a>Mapeamento do tipo de dados para MongoDB

Ao copiar dados da MongoDB, os seguintes mapeamentos são usados desde os tipos de dados mongoDB até aos tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de schema e tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados MongoDB | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| Binário |Byte[] |
| Booleano |Booleano |
| Date |DateTime |
| NúmeroDouble |Double (Duplo) |
| NúmeroInt |Int32 |
| NúmeroLong |Int64 |
| ObjectID |Cadeia |
| Cadeia |Cadeia |
| UUID |GUID |
| Objeto |Renormalizado em colunas planas com "_" como separador aninhado |

> [!NOTE]
> Para saber mais sobre o suporte a matrizes que utilizem tabelas virtuais, consulte o Suporte para tipos complexos utilizando a secção [de tabelas virtuais.](#support-for-complex-types-using-virtual-tables)
>
> Atualmente, os seguintes tipos de dados MongoDB não são suportados: DBPointer, JavaScript, tecla Max/Min, Expressão Regular, Símbolo, Relógio, Indefinido.

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos que usam tabelas virtuais

A Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados da sua base de dados MongoDB. Para tipos complexos como matrizes ou objetos com diferentes tipos nos documentos, o condutor re normaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver tais colunas, o controlador gera as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados que a tabela real, exceto para as colunas tipo complexas. A tabela base usa o mesmo nome que a mesa real que representa.
* Uma **tabela virtual** para cada coluna tipo complexo, que expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da mesa real, um separador "_" e o nome da matriz ou objeto.

As tabelas virtuais referem-se aos dados na tabela real, permitindo ao condutor aceder aos dados denormalizados. Pode aceder ao conteúdo das matrizes mongoDB consultando e juntando as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, o ExemploTable aqui é uma tabela MongoDB que tem uma coluna com uma matriz de Objetos em cada célula – Faturas, e uma coluna com uma matriz de tipos Scalar – Classificações.

| _id | Nome do Cliente | Faturas | Nível de Serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"torradeira", preço:"456", desconto:"0,2"}, {invoice_id:"124", item:"forno", preço: "1235", desconto: "0,2"}} |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"frigorífico", preço: "12543", desconto: "0,0"}] |Gold |[1,2] |

O condutor geraria várias tabelas virtuais para representar esta única tabela. A primeira tabela virtual é a tabela base denominada "ExemploTable", mostrada no exemplo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e expandidos nas tabelas virtuais.

| _id | Nome do Cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas seguintes mostram as tabelas virtuais que representam as matrizes originais no exemplo. Estas tabelas contêm os seguintes:

* Uma referência à coluna de chave primária original correspondente à linha da matriz original (através da coluna _id)
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
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
