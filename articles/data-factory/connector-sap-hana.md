---
title: Copiar dados do SAP HANA
description: Saiba como copiar dados da SAP HANA para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: 74462b68bea38e4d84219adeedb7c3bb0893bbb4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417226"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiar dados do SAP HANA utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-sap-hana-connector.md)
> * [Versão atual](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados SAP HANA. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para conhecer o suporte global da ADF no cenário de integração de dados DoSAP, consulte a integração de [dados do SAP utilizando o livro branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SAP HANA é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da base de dados SAP HANA para qualquer loja de dados suportada. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SAP HANA suporta:

- Copiar dados de qualquer versão da base de dados SAP HANA.
- Copiar dados de modelos de **informação HANA** (tais como vistas analíticas e de cálculo) e **tabelas Row/Column**.
- Copiar dados utilizando a autenticação **Básica** ou **Windows.**
- Cópia paralela de uma fonte da SAP HANA. Consulte a cópia paralela da secção [SAP HANA](#parallel-copy-from-sap-hana) para obter mais detalhes.

> [!TIP]
> Para copiar dados **para** a loja de dados SAP HANA, utilize conector genérico ODBC. Veja [a pia sap HANA](connector-odbc.md#sap-hana-sink) com detalhes. Note que os serviços ligados para o conector SAP HANA e o conector ODBC estão com um tipo diferente, pelo que não podem ser reutilizados.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector SAP HANA, é necessário:

- Criar um Tempo de Integração Auto-hospedado. Consulte o artigo [De Integração Autónoma](create-self-hosted-integration-runtime.md) para obter mais detalhes.
- Instale o controlador SAP HANA ODBC na máquina de tempo de execução de integração. Pode transferir o controlador ODBC do SAP HANA a partir do [Centro de Transferências de Software SAP](https://support.sap.com/swdc). Procure com a palavra-chave **SAP HANA CLIENT para Windows**.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector SAP HANA.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado sAP HANA:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapHana** | Sim |
| conexãoString | Especifique as informações necessárias para se ligar ao SAP HANA utilizando a **autenticação básica** ou a **autenticação do Windows**. Consulte as seguintes amostras.<br>Na cadeia de ligação, o servidor/porta é obrigatório (a porta predefinida é 30015), e o nome de utilizador e a palavra-passe são obrigatórios quando se utiliza a autenticação básica. Para configurações avançadas adicionais, consulte as propriedades de [conexão SAP HANA ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Também pode colocar palavra-passe no Cofre de Chaves Azure e retirar a configuração da palavra-passe da cadeia de ligação. Consulte [as credenciais da Loja no](store-credentials-in-key-vault.md) artigo do Cofre de Chaves Azure com mais detalhes. | Sim |
| userName | Especifique o nome do utilizador ao utilizar a autenticação do Windows. Exemplo: `user@domain.com` | Não |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Não |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. É necessário um tempo de execução de integração auto-hospedado, tal como mencionado nos [pré-requisitos.](#prerequisites) |Sim |

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

Se estava a utilizar o serviço ligado sAP HANA com a seguinte carga útil, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados SAP HANA.

Para copiar dados do SAP HANA, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **SapHanaTable** | Sim |
| schema | Nome do esquema na base de dados SAP HANA. | Não (se for especificada a "consulta" na fonte de atividade) |
| tabela | Nome da tabela na base de dados SAP HANA. | Não (se for especificada a "consulta" na fonte de atividade) |

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

Se estiver `RelationalTable` a utilizar o conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte sap HANA.

### <a name="sap-hana-as-source"></a>SAP HANA como fonte

>[!TIP]
>Para ingerir dados do SAP HANA de forma eficiente utilizando a partilha de dados, saiba mais com a cópia paralela da secção [SAP HANA.](#parallel-copy-from-sap-hana)

Para copiar dados do SAP HANA, as seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **SapHanaSource** | Sim |
| consulta | Especifica a consulta SQL para ler os dados da instância SAP HANA. | Sim |
| partilhaOpOp | Especifica as opções de partilha de dados utilizadas para ingerir dados do SAP HANA. Saiba mais na cópia paralela da secção [SAP HANA.](#parallel-copy-from-sap-hana)<br>Os valores de adstão: **Nenhum** (predefinido), **PhysicalPartitionsOfTable,** **SapHanaDynamicRange**. Saiba mais na cópia paralela da secção [SAP HANA.](#parallel-copy-from-sap-hana) `PhysicalPartitionsOfTable`Só pode ser utilizado quando copia dados de uma tabela, mas não consulta. <br>Quando uma opção de partição `None`é ativada (isto é, não), o grau de paralelismo para carregar simultaneamente os dados do SAP HANA é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) definição da atividade da cópia. | Falso |
| partiçãoDefinições | Especifique o grupo das definições para a partilha de dados.<br>Aplicar quando a `SapHanaDynamicRange`opção de partição é . | Falso |
| partitionColumnName | Especifique o nome da coluna de origem que será utilizada por partição para cópia paralela. Se não especificado, o índice ou a chave primária da tabela é detetado automaticamente e utilizado como coluna de partição.<br>Aplicar quando a `SapHanaDynamicRange`opção de partição for . Se utilizar uma consulta para recuperar os `?AdfHanaDynamicRangePartitionCondition` dados de origem, ligue-se à cláusula ONDE. Consulte o exemplo na cópia paralela da secção [SAP HANA.](#parallel-copy-from-sap-hana) | Sim, `SapHanaDynamicRange` quando se usa a partição. |
| pacoteTamanho | Especifica o tamanho do pacote de rede (em Kilobytes) para dividir dados em vários blocos. Se tiver uma grande quantidade de dados para copiar, aumentar o tamanho do pacote pode aumentar a velocidade de leitura do SAP HANA na maioria dos casos. Recomenda-se o teste de desempenho ao ajustar o tamanho do pacote. | Não.<br>O valor predefinido é de 2048 (2MB). |

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

Se estiver `RelationalSource` a utilizar a fonte de cópia dactilografada, continua a ser suportada como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="parallel-copy-from-sap-hana"></a>Cópia paralela do SAP HANA

O conector Data Factory SAP HANA fornece a partilha de dados incorporados para copiar dados do SAP HANA em paralelo. Pode encontrar opções de partilha de dados na tabela **Fonte** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Quando ativa a cópia dividida, a Data Factory executa consultas paralelas contra a sua fonte SAP HANA para recuperar dados por divisórias. O grau paralelo é [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) controlado pela regulação da atividade da cópia. Por exemplo, se `parallelCopies` definir para quatro, data Factory gera simultaneamente e executa quatro consultas com base na sua opção e configurações especificadas de partição, e cada consulta recupera uma parte dos dados do seu SAP HANA.

É sugerido que permita cópias paralelas com partilha de dados especialmente quando ingere uma grande quantidade de dados do seu SAP HANA. As seguintes são configurações sugeridas para diferentes cenários. Ao copiar dados para uma loja de dados baseada em ficheiros, recomenda-se escrever para uma pasta como múltiplos ficheiros (apenas especificar o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

| Cenário                                           | Definições sugeridas                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Carga completa de mesa grande.                        | **Opção de partição**: Divisórias físicas da mesa. <br><br/>Durante a execução, a Data Factory deteta automaticamente o tipo de partição física da tabela SAP HANA especificada e escolhe a estratégia de partição correspondente:<br>- **Partilha de alcance**: Obtenha as gamas de colunas de partição e divisórias definidas para a tabela e, em seguida, copie os dados por alcance. <br>- **Divisão hash**: Utilize a chave de partição de haxixe como coluna de partição, em seguida, partição e cópia dos dados com base em intervalos calculados aDf. <br>- **Partição de robin redondo** ou **sem partição:** Utilize a chave primária como coluna de partição, em seguida, partição e copie os dados com base em intervalos calculados da ADF. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada. | **Opção de partição**: Partição dinâmica.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna utilizada para aplicar divisória dinâmica. <br><br>Durante a execução, a Data Factory calcula em primeiro lugar as gamas de valor da coluna de partição especificada, distribuindo uniformemente as `?AdfHanaDynamicRangePartitionCondition` linhas em vários baldes de acordo com o número de valores distintos da coluna de divisórias e a definição paralela de cópias ADF, substituindo-a por filtrar a gama de valores da coluna de divisórias para cada partição, e envia para o SAP HANA.<br><br>Se pretender utilizar várias colunas como coluna de partição, pode concatenar os valores de cada coluna como `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`uma coluna na consulta e especificá-la como coluna de partição em ADF, como . |

**Exemplo: consulta com divisórias físicas de uma mesa**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemplo: consulta com partição dinâmica de gama**

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

## <a name="data-type-mapping-for-sap-hana"></a>Mapeamento de tipo de dados para SAP HANA

Ao copiar dados do SAP HANA, os seguintes mapeamentos são utilizados desde tipos de dados SAP HANA para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados SAP HANA | Tipo de dados provisórios da fábrica de dados |
| ------------------ | ------------------------------ |
| ALFANUM           | String                         |
| BIGINT             | Int64                          |
| BINÁRIO             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATA               | DateTime                       |
| DECIMAL            | Decimal                        |
| DUPLO             | Double                         |
| BOIA              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Único                         |
| SEGUNDO ENCONTRO         | DateTime                       |
| TEXTO CURTO          | String                         |
| PEQUENADECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| HORA               | TimeSpan                       |
| TINYINT            | Byte                           |
| RIO VARCHAR            | String                         |
| CARIMBO TEMPORAL          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
