---
title: Copiar dados da SAP HANA
description: Saiba como copiar dados da SAP HANA para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/22/2020
ms.openlocfilehash: ce3c1e22dd030c0730bf4d9859591c00860908a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382281"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiar dados da SAP HANA utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-sap-hana-connector.md)
> * [Versão atual](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados SAP HANA. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

>[!TIP]
>Para obter o apoio global da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP utilizando o papel branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada em cada conector SAP, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SAP HANA é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da base de dados SAP HANA para qualquer loja de dados de sumidouro suportado. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SAP HANA suporta:

- Copiar dados de qualquer versão da base de dados SAP HANA.
- Copiar dados de modelos de **informação HANA** (tais como vistas analíticas e de cálculo) e **tabelas linha/coluna**.
- Copiar dados utilizando a autenticação **Básica** ou **Windows.**
- Cópia paralela de uma fonte SAP HANA. Consulte a cópia paralela da secção [SAP HANA](#parallel-copy-from-sap-hana) para obter mais detalhes.

> [!TIP]
> Para copiar dados **na** loja de dados SAP HANA, utilize o conector ODBC genérico. Consulte a secção [de pia SAP HANA](#sap-hana-sink) com detalhes. Note que os serviços ligados para o conector SAP HANA e o conector ODBC são com um tipo diferente, portanto não pode ser reutilizado.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector SAP HANA, tem de:

- Crie um tempo de integração auto-hospedado. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instale o controlador SAP HANA ODBC na máquina de intervalo de integração. Pode transferir o controlador ODBC do SAP HANA a partir do [Centro de Transferências de Software SAP](https://support.sap.com/swdc). Procure com a palavra-chave **SAP HANA CLIENT para windows**.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector SAP HANA.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à SAP HANA:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapHana** | Yes |
| conexãoStragem | Especifique as informações necessárias para se ligar ao SAP HANA utilizando a **autenticação básica** ou **a autenticação do Windows.** Consulte as seguintes amostras.<br>Na cadeia de ligação, o servidor/porta é obrigatório (a porta predefinida é 30015), e o nome de utilizador e a palavra-passe são obrigatórios quando se utiliza a autenticação básica. Para configurações avançadas adicionais, consulte as [Propriedades de Ligação SAP HANA ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Também pode colocar a palavra-passe no Cofre da Chave Azure e retirar a configuração da palavra-passe da cadeia de ligação. Consulte [as credenciais da Loja no artigo do Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Yes |
| userName | Especifique o nome do utilizador ao utilizar a autenticação do Windows. Exemplo: `user@domain.com` | No |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | No |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. É necessário um tempo de integração auto-organizado, tal como mencionado nos [Pré-Requisitos](#prerequisites). |Yes |

**Exemplo: utilizar a autenticação básica**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: utilizar a autenticação do Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Se você estava usando o serviço ligado SAP HANA com a seguinte carga útil, ele ainda é suportado como está, enquanto você é sugerido para usar o novo para a frente.

**Exemplo:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados SAP HANA.

Para copiar dados da SAP HANA, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **SapHanaTable** | Yes |
| esquema | Nome do esquema na base de dados SAP HANA. | Não (se for especificada "consulta" na fonte de atividade) |
| table | Nome da tabela na base de dados SAP HANA. | Não (se for especificada "consulta" na fonte de atividade) |

**Exemplo:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se estiver a utilizar `RelationalTable` conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA como fonte

>[!TIP]
>Para ingerir dados da SAP HANA de forma eficiente utilizando a partilha de dados, saiba mais com cópia paralela da secção [SAP HANA.](#parallel-copy-from-sap-hana)

Para copiar dados da SAP HANA, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **SapHanaSource** | Yes |
| consulta | Especifica a consulta SQL para ler dados a partir da instância SAP HANA. | Yes |
| partitionOptions | Especifica as opções de partição de dados utilizadas para ingerir dados da SAP HANA. Saiba mais a partir [da secção SAP HANA.](#parallel-copy-from-sap-hana)<br>Os valores de permitir são: **Nenhum**   (padrão), **PhysicalPartitionsOfTable,** **SapHanaDynamicRange**. Saiba mais a partir [da secção SAP HANA.](#parallel-copy-from-sap-hana) `PhysicalPartitionsOfTable` só podem ser utilizados ao copiar dados de uma tabela, mas não consultar. <br>Quando uma opção de partição é ativada (isto é, `None` não), o grau de paralelismo para carregar simultaneamente os dados do SAP HANA é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) definição da atividade da cópia. | Falso |
| divisóriasSas | Especificar o grupo das definições para a partilha de dados.<br>Aplicar quando a opção de partição for `SapHanaDynamicRange` . | Falso |
| partitionColumnName | Especificar o nome da coluna de origem que será utilizada por partição para cópia paralela. Se não for especificado, o índice ou a chave primária da tabela é detetado automaticamente e utilizado como coluna de partição.<br>Aplicar quando a opção de partição for  `SapHanaDynamicRange` . Se utilizar uma consulta para recuperar os dados de origem,  `?AdfHanaDynamicRangePartitionCondition` ligue-se à cláusula WHERE. Veja o exemplo na cópia paralela da secção [SAP HANA.](#parallel-copy-from-sap-hana) | Sim, quando se usa `SapHanaDynamicRange` a partição. |
| pacoteSize | Especifica o tamanho do pacote de rede (em Kilobytes) para dividir dados em vários blocos. Se tiver uma grande quantidade de dados para copiar, o aumento do tamanho do pacote pode aumentar a velocidade de leitura do SAP HANA na maioria dos casos. Recomenda-se o teste de desempenho ao ajustar o tamanho do pacote. | N.º<br>O valor predefinido é de 2048 (2MB). |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se estava a usar `RelationalSource` uma fonte de cópia dactilografada, ainda é suportada como está, enquanto é sugerido que utilize a nova que vai para a frente.

## <a name="parallel-copy-from-sap-hana"></a>Cópia paralela da SAP HANA

O conector SAP HANA da Data Factory fornece partição de dados incorporada para copiar dados da SAP HANA em paralelo. Pode encontrar opções de partição de dados na tabela **Fonte** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Quando ativa a cópia dividida, a Data Factory executa consultas paralelas contra a sua fonte SAP HANA para obter dados por divisórias. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) regulação da atividade da cópia. Por exemplo, se definir `parallelCopies` para quatro, data factory simultaneamente gera e executa quatro consultas com base na sua opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados do seu SAP HANA.

Sugere-se que ative uma cópia paralela com a partilha de dados, especialmente quando ingerir uma grande quantidade de dados do seu SAP HANA. São sugeridas configurações para diferentes cenários. Ao copiar dados na loja de dados baseada em ficheiros, recomenda-se escrever para uma pasta como vários ficheiros (especificar apenas o nome da pasta), caso em que o desempenho é melhor do que escrever num único ficheiro.

| Scenario                                           | Definições sugeridas                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Carga completa da mesa grande.                        | **Opção de partição**: Divisórias físicas da tabela. <br><br/>Durante a execução, a Data Factory deteta automaticamente o tipo de partição física da tabela SAP HANA especificada e escolhe a estratégia de partição correspondente:<br>- **Divisão de gama**: Obter a coluna de partição e os intervalos de partição definidos para a tabela e, em seguida, copiar os dados por intervalo. <br>- **Partição de haxixe**: Utilize a tecla de partição de haxixe como coluna de partição, em seguida, partição e cópia dos dados com base em intervalos calculados ADF. <br>- **Partição redonda-robin** ou **não partição**: Use a chave primária como coluna de partição, em seguida, partição e copie os dados com base em intervalos calculados ADF. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada. | **Opção de partição**: Partição dinâmica do alcance.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Coluna de partição**: Especificar a coluna utilizada para aplicar partição dinâmica de alcance. <br><br>Durante a execução, a Data Factory calcula em primeiro lugar as gamas de valor da coluna de partição especificada, distribuindo uniformemente as linhas em vários baldes de acordo com o número de valores distintos da coluna de partição e a definição paralela de cópia ADF, `?AdfHanaDynamicRangePartitionCondition` substituindo-as pela filtragem da gama de valor da coluna de partição para cada partição, e envia para SAP HANA.<br><br>Se pretender utilizar várias colunas como coluna de partição, pode concatenar os valores de cada coluna como uma coluna na consulta e especificá-la como coluna de partição em ADF, como `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition` . |

**Exemplo: consulta com divisórias físicas de uma tabela**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemplo: consulta com partição de gama dinâmica**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapeamento do tipo de dados para SAP HANA

Ao copiar dados da SAP HANA, os seguintes mapeamentos são utilizados desde os tipos de dados SAP HANA até aos tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de schema e tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados SAP HANA | Tipo de dados provisórios da fábrica de dados |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| BIGINT             | Int64                          |
| BINÁRIO             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| RIO BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DUPLO             | Double (Duplo)                         |
| FLUTUAR              | Double (Duplo)                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Único                         |
| SEGUNDADATA         | DateTime                       |
| CURTOTEXTO          | String                         |
| PEQUENODECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| ESTRIASMETRIA     | Byte[]                         |
| ESTADÓTIPO        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| RIO VARCHAR            | String                         |
| CARIMBO DE DATA/HORA          | DateTime                       |
| VARBINÁRIA          | Byte[]                         |

## <a name="sap-hana-sink"></a>Pia SAP HANA

Atualmente, o conector SAP HANA não é suportado como pia, enquanto pode utilizar o conector ODBC genérico com o controlador SAP HANA para escrever dados em SAP HANA. 

Siga os [Pré-requisitos](#prerequisites) para configurar o Tempo de Execução de Integração Auto-hospedado e instale primeiro o controlador SAP HANA ODBC. Crie um serviço ligado ao ODBC para ligar à sua loja de dados SAP HANA, como mostrado no exemplo seguinte, em seguida, crie conjunto de dados e a atividade de cópia afunde com o tipo ODBC em conformidade. Saiba mais a partir do artigo do [conector ODBC.](connector-odbc.md)

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
