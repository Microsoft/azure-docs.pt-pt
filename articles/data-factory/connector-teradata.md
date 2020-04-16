---
title: Copiar dados da Teradata Vantage utilizando a Azure Data Factory
description: O Conector Teradata do serviço Data Factory permite copiar dados de um Teradata Vantage para lojas de dados suportadas pela Data Factory como pias.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 4eed79210e3e39f82b892ac0681e161ebb59597e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418036"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Copiar dados da Teradata Vantage utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versão atual](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia na Azure Data Factory para copiar dados da Teradata Vantage. Baseia-se na visão geral da atividade da [cópia.](copy-activity-overview.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Teradata é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Teradata Vantage para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Teradata suporta:

- Vereção Teradata **14.10, 15.0, 15.10, 16.0, 16.10 e 16.20**.
- Copiar dados utilizando a autenticação **Básica** ou **Windows.**
- Cópia paralela de uma fonte teradata. Consulte a cópia paralela da secção [Teradata](#parallel-copy-from-teradata) para obter mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Se utilizar o Tempo de Funcionamento de Integração Auto-hospedado, note que fornece um controlador Teradata incorporado a partir da versão 3.18. Não precisa instalar manualmente nenhum controlador. O condutor requer "Visual C++ Redistribuable Update 4" na máquina de tempo de execução de integração auto-hospedada. Se ainda não o tiver instalado, descarregue-o a partir [daqui.](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector Teradata.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

O serviço ligado à Teradata suporta as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **Teradata**. | Sim |
| conexãoString | Especifica as informações necessárias para se ligar à instância Teradata. Consulte as seguintes amostras.<br/>Também pode colocar uma palavra-passe no Cofre `password` de Chaves Azure e retirar a configuração da cadeia de ligação. Consulte [as credenciais da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| o nome de utilizador | Especifique um nome de utilizador para ligar à Teradata. Aplica-se quando está a utilizar a autenticação do Windows. | Não |
| palavra-passe | Especifique uma palavra-passe para a conta de utilizador especificada para o nome de utilizador. Também pode optar por fazer referência a [um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). <br>Aplica-se quando está a utilizar a autenticação do Windows ou a fazer referência a uma palavra-passe no Cofre chave para autenticação básica. | Não |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

Mais propriedades de ligação que pode definir na cadeia de ligação por seu caso:

| Propriedade | Descrição | Valor predefinido |
|:--- |:--- |:--- |
| Conjunto de caracteres | O personagem está pronto para usar para a sessão. Por exemplo, `CharacterSet=UTF16`.<br><br/>Este valor pode ser um conjunto de caracteres definido pelo utilizador, ou um dos seguintes conjuntos de caracteres pré-definidos: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>LATIN1252_0A<br/>LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS compatível, KANJISJIS_0S)<br/>- EUC (Unix compatível, KANJIEC_0U)<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>HANGUL949_7R0<br/>ARABIC1256_6A0<br/>CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | O valor `ASCII`predefinido é . |
| MaxRespSize |O tamanho máximo do tampão de resposta para pedidos DeQL, em kilobytes (KBs). Por exemplo, `MaxRespSize=‭10485760‬`.<br/><br/>Para a Base de Dados Teradata versão 16.00 ou posterior, o valor máximo é 7361536. Para ligações que utilizam versões anteriores, o valor máximo é 1048576. | O valor `65536`predefinido é . |

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

> [!NOTE]
>
> A seguinte carga ainda é suportada. No entanto, para a frente, deve usar o novo.

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

Para copiar dados da Teradata, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de `TeradataTable`dados deve ser definida para . | Sim |
| base de dados | O nome da instância Teradata. | Não (se for especificada a "consulta" na fonte de atividade) |
| tabela | O nome da tabela na instância Teradata. | Não (se for especificada a "consulta" na fonte de atividade) |

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
> `RelationalTable`tipo conjunto de dados ainda é suportado. No entanto, recomendamos que utilize o novo conjunto de dados.

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

Esta secção fornece uma lista de propriedades suportadas por fonte da Teradata. Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata como fonte

>[!TIP]
>Para carregar os dados da Teradata de forma eficiente utilizando a partilha de dados, saiba mais com cópia paralela da secção [Teradata.](#parallel-copy-from-teradata)

Para copiar dados da Teradata, as seguintes propriedades são suportadas na secção **de origem** de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de `TeradataSource`atividade de cópia deve ser definida para . | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Quando ativa a carga divisória, tem de ligar os parâmetros correspondentes de partição incorporados na sua consulta. Por exemplo, consulte a cópia paralela da secção [Teradata.](#parallel-copy-from-teradata) | Não (se for especificado o quadro no conjunto de dados) |
| partilhaOpOp | Especifica as opções de partilha de dados utilizadas para carregar dados da Teradata. <br>Os valores de aditar são: **Nenhum** (padrão), **Hash** e **DynamicRange**.<br>Quando uma opção de partição `None`é ativada (isto é, não), o grau de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) paralelismo para carregar simultaneamente dados da Teradata é controlado pela definição da atividade da cópia. | Não |
| partiçãoDefinições | Especifique o grupo das definições para a partilha de dados. <br>Aplicar quando a opção de partição não `None`é . | Não |
| partitionColumnName | Especifique o nome da coluna de origem que será utilizada por divisória de alcance ou partição de Hash para cópia paralela. Se não especificado, o índice primário da tabela é detetado automaticamente e utilizado como coluna de partição. <br>Aplicar quando a `Hash` opção de partição for ou `DynamicRange`. Se utilizar uma consulta para recuperar os `?AdfHashPartitionCondition` `?AdfRangePartitionColumnName` dados de origem, ligue ou em que cláusula DE ONDE. Consulte o exemplo na cópia paralela da secção [Teradata.](#parallel-copy-from-teradata) | Não |
| partiçãoUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição é . Se utilizar a consulta para recuperar `?AdfRangePartitionUpbound` dados de origem, ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Teradata.](#parallel-copy-from-teradata) | Não |
| partiçãoLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição for . Se utilizar uma consulta para recuperar os `?AdfRangePartitionLowbound` dados de origem, ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Teradata.](#parallel-copy-from-teradata) | Não |

> [!NOTE]
>
> `RelationalSource`a fonte de cópia do tipo ainda é suportada, mas não suporta a nova carga paralela incorporada da Teradata (opções de partição). No entanto, recomendamos que utilize o novo conjunto de dados.

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

## <a name="parallel-copy-from-teradata"></a>Cópia paralela da Teradata

O conector Data Factory Teradata fornece a partilha de dados incorporados para copiar dados da Teradata em paralelo. Pode encontrar opções de partilha de dados na tabela **Fonte** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-teradata/connector-teradata-partition-options.png)

Quando ativa a cópia dividida, a Data Factory executa consultas paralelas contra a sua fonte Teradata para carregar dados por divisórias. O grau paralelo é [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) controlado pela regulação da atividade da cópia. Por exemplo, se `parallelCopies` definir para quatro, data Factory gera simultaneamente e executa quatro consultas com base na sua opção e configurações especificadas de partição, e cada consulta recupera uma parte dos dados do seu Teradata.

É sugerido que permita cópias paralelas com partilha de dados especialmente quando carrega uma grande quantidade de dados a partir do seu Teradata. As seguintes são configurações sugeridas para diferentes cenários. Ao copiar dados para uma loja de dados baseada em ficheiros, é re-ordenado para escrever para uma pasta como múltiplos ficheiros (apenas especificar o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

| Cenário                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de mesa grande.                                   | **Opção de partição**: Hash. <br><br/>Durante a execução, a Data Factory deteta automaticamente a coluna de índice primário, aplica um hash contra ela e copia dados por divisórias. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada.                 | **Opção de partição**: Hash.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna utilizada para a aplicação da partição de hash. Se não especificado, a Data Factory deteta automaticamente a coluna PK da tabela especificada no conjunto de dados teradata.<br><br>Durante a execução, `?AdfHashPartitionCondition` data Factory substitui com a lógica da partição de haxixe, e envia para a Teradata. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, tendo uma coluna inteiro com valor uniformemente distribuído para a partilha de alcance. | **Opções de partição**: Partição dinâmica.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna utilizada para os dados da partilha. Pode dividir-se contra a coluna com o tipo de dados inteiros.<br>**Divisória superior ligada** e **divisória inferior:** Especifique se pretende filtrar contra a coluna de divisórias para recuperar dados apenas entre a gama inferior e superior.<br><br>Durante a execução, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` Data Factory substitui, e com o nome real da coluna e gamas de valor para cada partição, e envia para a Teradata. <br>Por exemplo, se a sua coluna de partição "ID" definida com o limite inferior como 1 e o limite superior como 80, com cópia paralela definida como 4, data Factory recupera dados por 4 divisórias. As suas identificações estão entre [1,20], [21, 40], [41, 60], e [61, 80], respectivamente. |

**Exemplo: consulta com partição de hash**

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

**Exemplo: consulta com partição dinâmica de gama**

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

## <a name="data-type-mapping-for-teradata"></a>Mapeamento de tipo de dados para Teradata

Quando copia dados da Teradata, aplicam-se os seguintes mapeamentos. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

| Tipo de dados teradata | Data Factory tipo de dados provisórios |
|:--- |:--- |
| BigInt |Int64 |
| Blobs |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Estação Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Gráfico |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Número inteiro |Int32 |
| Dia do Intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Intervalo dia a hora |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Intervalo dia a minuto |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Dia de intervalo para segundo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Hora de Intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Hora de intervalo ao minuto |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Hora de intervalo para segunda |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Minuto de intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Intervalo minuto para segundo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Mês de Intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Intervalo Segundo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Ano de Intervalo |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Intervalo de ano para mês |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Número |Double |
| Período (Data) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Período (Hora) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Período (Hora com Fuso Horário) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Período (carimbo de tempo) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Período (carimbo de tempo com fuso horário) |Não suportado. Aplicar elenco explícito na consulta de origem. |
| SmallInt |Int16 |
| Hora |TimeSpan |
| Tempo com fuso horário |TimeSpan |
| Carimbo de data/hora |DateTime |
| Carimbo de tempo com fuso horário |DateTime |
| Rio VarByte |Byte[] |
| Rio Varchar |String |
| Vargraphic |Não suportado. Aplicar elenco explícito na consulta de origem. |
| Xml |Não suportado. Aplicar elenco explícito na consulta de origem. |


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
