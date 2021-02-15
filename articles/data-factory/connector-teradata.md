---
title: Copiar dados da Teradata Vantage utilizando a Azure Data Factory
description: O Conector Teradata do serviço Data Factory permite-lhe copiar dados de uma Teradata Vantage para lojas de dados suportadas pela Data Factory como pias.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: jingwang
ms.openlocfilehash: c65b295dca16a3453b2ee5472b9a3ee5e8d62df7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381771"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Copiar dados da Teradata Vantage utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versão atual](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados da Teradata Vantage. Baseia-se na [visão geral](copy-activity-overview.md)da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Teradata é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Teradata Vantage para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Teradata suporta:

- Teradata **versão 14.10, 15.0, 15.10, 16.0, 16.10 e 16.20**.
- Copiar dados utilizando a autenticação **Básica,** **Windows** ou **LDAP.**
- Cópia paralela de uma fonte teradata. Consulte a cópia paralela da secção [Teradata](#parallel-copy-from-teradata) para obter mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Se utilizar o Tempo de Execução de Integração Auto-hospedado, note que fornece um controlador Teradata incorporado a partir da versão 3.18. Não é necessário instalar manualmente nenhum controlador. O controlador requer "Visual C++ Redistributable Update 4" na máquina de tempo de funcionaamento de integração auto-hospedada. Se ainda não o tiver instalado, descarregue-o a partir [daqui.](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Teradata.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

O serviço ligado à Teradata suporta as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **Teradata.** | Yes |
| conexãoStragem | Especifica a informação necessária para se ligar à instância Teradata. Consulte as seguintes amostras.<br/>Também pode colocar uma palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte [as credenciais da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md) com mais detalhes. | Yes |
| nome de utilizador | Especifique um nome de utilizador para ligar ao Teradata. Aplica-se quando estiver a utilizar a autenticação do Windows. | No |
| palavra-passe | Especifique uma palavra-passe para a conta de utilizador especificada para o nome de utilizador. Também pode optar por fazer referência a [um segredo armazenado no Cofre da Chave Azure.](store-credentials-in-key-vault.md) <br>Aplica-se quando estiver a utilizar a autenticação do Windows ou a fazer referência a uma palavra-passe no Cofre de Chaves para autenticação básica. | No |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

Mais propriedades de conexão que pode definir em cadeia de ligação por sua caixa:

| Propriedade | Descrição | Valor predefinido |
|:--- |:--- |:--- |
| TdmstPortNumber | O número do porto usado para aceder à base de dados teradata.<br>Não altere este valor a menos que seja instruído a fazê-lo por Suporte Técnico. | 1025 |
| UtilizaçãoDataEncrypation | Especifica se encriptar todas as comunicações com a base de dados Teradata. Os valores permitidos são 0 ou 1.<br><br/>- **0 (desativado, predefinido)**: Encripta apenas informações de autenticação.<br/>- **1 (ativado)**: Encripta todos os dados que são transmitidos entre o controlador e a base de dados. | `0` |
| Conjunto de Caracteres | O conjunto de caracteres para usar para a sessão. Por exemplo, `CharacterSet=UTF16` .<br><br/>Este valor pode ser um conjunto de caracteres definido pelo utilizador, ou um dos seguintes conjuntos de caracteres pré-definidos: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS compatível, KANJISJIS_0S)<br/>- EUC (compatível com Unix, KANJIEC_0U)<br/>- Ibm Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | `ASCII` |
| MaxRespSize |O tamanho máximo do tampão de resposta para pedidos SQL, em kilobytes (KBs). Por exemplo, `MaxRespSize=‭10485760‬` .<br/><br/>Para a versão 16.00 ou posterior da Teradata Database, o valor máximo é de 7361536. Para ligações que usam versões anteriores, o valor máximo é 1048576. | `65536` |
| Nome do Mecanismo | Para utilizar o protocolo LDAP para autenticar a ligação, especifique `MechanismName=LDAP` . | N/D |

**Exemplo usando a autenticação básica**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo usando a autenticação do Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo usando a autenticação LDAP**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> A seguinte carga útil ainda é suportada. No entanto, deve usar o novo.

**Carga útil anterior:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Teradata. Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets](concepts-datasets-linked-services.md).

Para copiar dados da Teradata, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para `TeradataTable` . | Yes |
| base de dados | O nome do caso Teradata. | Não (se for especificada "consulta" na fonte de atividade) |
| table | O nome da mesa no caso Teradata. | Não (se for especificada "consulta" na fonte de atividade) |

**Exemplo:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable` conjunto de dados do tipo ainda é suportado. No entanto, recomendamos que utilize o novo conjunto de dados.

**Carga útil anterior:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Esta secção fornece uma lista de imóveis suportados por fonte teradata. Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades,](concepts-pipelines-activities.md)consulte Pipelines . 

### <a name="teradata-as-source"></a>Teradata como fonte

>[!TIP]
>Para carregar os dados da Teradata de forma eficiente utilizando a partição de dados, saiba mais a partir [da secção Paralelo da Teradata.](#parallel-copy-from-teradata)

Para copiar dados da Teradata, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para `TeradataSource` . | Yes |
| consulta | Utilize a consulta SQL personalizada para ler dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Quando ativar a carga partida, tem de ligar os parâmetros de partição incorporados correspondentes na sua consulta. Por exemplo, consulte a cópia paralela da secção [Teradata.](#parallel-copy-from-teradata) | Não (se a tabela no conjunto de dados for especificada) |
| partitionOptions | Especifica as opções de partição de dados utilizadas para carregar dados da Teradata. <br>Os valores de permitir são: **Nenhum** (padrão), **Hash** e **DynamicRange**.<br>Quando uma opção de partição é ativada (isto é, `None` não), o grau de paralelismo para carregar simultaneamente os dados de Teradata é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) definição da atividade da cópia. | No |
| divisóriasSas | Especificar o grupo das definições para a partilha de dados. <br>Aplicar quando a opção de partição não `None` for. | No |
| partitionColumnName | Especifique o nome da coluna de origem que será utilizada por partição de alcance ou partição hash para cópia paralela. Se não for especificado, o índice primário da tabela é autodetectado e utilizado como coluna de partição. <br>Aplicar quando a opção de partição for `Hash` ou `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem, o gancho `?AdfHashPartitionCondition` ou  `?AdfRangePartitionColumnName` a cláusula WHERE. Veja o exemplo na cópia paralela da secção [Teradata.](#parallel-copy-from-teradata) | No |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar a consulta para obter dados de origem, `?AdfRangePartitionUpbound` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Teradata.](#parallel-copy-from-teradata) | No |
| partitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem, `?AdfRangePartitionLowbound` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Teradata.](#parallel-copy-from-teradata) | No |

> [!NOTE]
>
> `RelationalSource` a fonte de cópia tipo ainda é suportada, mas não suporta a nova carga paralela incorporada a partir de Teradata (opções de partição). No entanto, recomendamos que utilize o novo conjunto de dados.

**Exemplo: copiar dados utilizando uma consulta básica sem partição**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Cópia paralela de Teradata

O conector Teradata da Fábrica de Dados fornece partição de dados incorporada para copiar dados da Teradata em paralelo. Pode encontrar opções de partição de dados na tabela **Fonte** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-teradata/connector-teradata-partition-options.png)

Quando ativa a cópia dividida, a Data Factory executa consultas paralelas contra a sua fonte Teradata para carregar dados por divisórias. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) regulação da atividade da cópia. Por exemplo, se definir `parallelCopies` para quatro, data factory simultaneamente gera e executa quatro consultas com base na sua opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados da sua Teradata.

Sugere-se que ative uma cópia paralela com a partilha de dados, especialmente quando carrega uma grande quantidade de dados a partir do seu Teradata. São sugeridas configurações para diferentes cenários. Ao copiar dados para a loja de dados baseada em ficheiros, é recomerado para escrever para uma pasta como vários ficheiros (especificar apenas o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

| Scenario                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa da mesa grande.                                   | **Opção de partição**: Haxixe. <br><br/>Durante a execução, a Data Factory deteta automaticamente a coluna de índice primário, aplica um haxixe contra ela e copia dados por partições. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada.                 | **Opção de partição**: Haxixe.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>` .<br>**Coluna de partição**: Especificar a coluna utilizada para aplicar a partição de haxixe. Se não for especificado, a Data Factory deteta automaticamente a coluna PK da tabela especificada no conjunto de dados Teradata.<br><br>Durante a execução, a Data Factory `?AdfHashPartitionCondition` substitui-se pela lógica de partição de haxixe e envia para Teradata. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, tendo uma coluna de inteiros com valor uniformemente distribuído para a divisão de gama. | **Opções de partição**: Partição dinâmica do alcance.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Coluna de partição**: Especificar a coluna utilizada para os dados de partição. Pode dividir-se contra a coluna com o tipo de dados inteiro.<br>**Limite superior** e **partição inferior**: Especifique se pretende filtrar contra a coluna de partição para obter dados apenas entre a gama inferior e superior.<br><br>Durante a execução, a Data Factory `?AdfRangePartitionColumnName` substitui, `?AdfRangePartitionUpbound` e com o nome `?AdfRangePartitionLowbound` e gamas de valor reais da coluna para cada partição, e envia para Teradata. <br>Por exemplo, se a sua coluna de partição "ID" definida com o limite inferior como 1 e o limite superior como 80, com conjunto de cópia paralela como 4, a Data Factory recupera dados por 4 divisórias. Os seus IDs estão entre [1,20], [21, 40], [41, 60], e [61, 80], respectivamente. |

**Exemplo: consulta com partição de haxixe**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Exemplo: consulta com partição de gama dinâmica**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Mapeamento do tipo de dados para Teradata

Quando copia dados da Teradata, aplicam-se os seguintes mapeamentos. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados teradata | Tipo de dados provisórios da Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Blobs |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Data |DateTime |
| Decimal |Decimal |
| Double (Duplo) |Double (Duplo) |
| Gráfico |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Número inteiro |Int32 |
| Dia do Intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Intervalo dia a hora |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Intervalo dia a minuto |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Dia de Intervalo para Segundo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Hora do Intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Hora do Intervalo ao Minuto |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Hora do Intervalo para Segundo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Minuto de Intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Minuto de intervalo para segundo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Mês de Intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Intervalo Segundo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Ano de Intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Intervalo ano a mês |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Número |Double (Duplo) |
| Período (Data) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Período (Tempo) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Período (Tempo com Fuso Horário) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Período (Timetamp) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Período (timetamp com fuso horário) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| PequenoInt |Int16 |
| Hora |TimeSpan |
| Tempo com fuso horário |TimeSpan |
| CarimboDeDataEHora |DateTime |
| Relógio com fuso horário |DateTime |
| Rio VarByte |Byte[] |
| Rio VarChar |String |
| VarGraphic |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Xml |Não suportado. Aplicar elenco explícito na consulta de origem. |


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
