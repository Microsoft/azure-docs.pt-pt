---
title: Schema e mapeamento do tipo de dados na atividade da cópia
description: Saiba como a atividade de cópia no Azure Data Factory mapeia esquemas e tipos de dados, desde dados de origem até dados de sumidouros.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: ce7c0cba4a231fbdb33679f8cdac7d57c79845f5
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968879"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>Schema e mapeamento do tipo de dados na atividade da cópia
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como a atividade de cópia da Azure Data Factory executa o mapeamento de esquemas e mapeamento do tipo de dados de origem para os dados de sumidouro.

## <a name="schema-mapping"></a>Mapeamento de Schema

### <a name="default-mapping"></a>Mapeamento padrão

Por predefinição, a atividade de cópia mapeia dados de origem para afundar **por nomes** de colunas de forma sensível ao caso. Se não existirem pias, por exemplo, escrevendo para ficheiros, os nomes de campo de origem serão persistidos como nomes de pia. Este mapeamento predefinido suporta esquemas flexíveis e o esquema deriva da fonte para afundar da execução para a execução - todos os dados devolvidos pela loja de dados de origem podem ser copiados para afundar.

Se a sua fonte for ficheiro de texto sem linha de cabeçalho, é necessário [um mapeamento explícito,](#explicit-mapping) uma vez que a fonte não contém nomes de colunas.

### <a name="explicit-mapping"></a>Mapeamento explícito

Também pode especificar mapeamento explícito para personalizar o mapeamento coluna/campo de origem para afundar com base na sua necessidade. Com o mapeamento explícito, só é possível copiar dados parciais de origem para afundar, ou mapear dados de origem para afundar com nomes diferentes, ou reformular dados tabulares/hierárquicos. Atividade de cópia:

1. Lê os dados da fonte e determina o esquema de origem.
2. Aplica o seu mapeamento definido.
3. Escreve os dados para afundar.

Saiba mais sobre:

- [Fonte tabular para pia tabular](#tabular-source-to-tabular-sink)
- [Fonte hierárquica para afundar tabular](#hierarchical-source-to-tabular-sink)
- [Fonte tabular/hierárquica para afundar hierárquico](#tabularhierarchical-source-to-hierarchical-sink)

Pode configurar o mapeamento na Data Factory autoriando uI -> copy activity -> mapeamento ou especificar programáticamente o mapeamento na atividade de cópia -> `translator` propriedade. As seguintes propriedades são suportadas em `translator`  ->  `mappings` objetos de > matriz -> `source` e , que aponta para a `sink` coluna/campo específico para mapear dados.

| Propriedade | Descrição                                                  | Obrigatório |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nome da coluna/campo da fonte ou da pia. Solicite fonte tabular e pia. | Yes      |
| ordinal  | Índice de coluna. Começa a partir de 1. <br>Aplicar e ser necessário quando utilizar textolimitado sem linha de cabeçalho. | No       |
| caminho     | Expressão do caminho JSON para cada campo para extrair ou mapear. Solicite fonte hierárquica e afunde, por exemplo, Conectores Cosmos DB, MongoDB ou REST.<br>Para campos sob o objeto raiz, o caminho JSON começa com `$` raiz; para campos dentro da matriz escolhida por `collectionReference` propriedade, o caminho JSON começa a partir do elemento matriz sem `$` . | No       |
| tipo     | Data Factory tipo de dados provisórios da coluna de origem ou pia. Em geral, não precisa de especificar ou alterar esta propriedade. Saiba mais sobre [o mapeamento do tipo de dados.](#data-type-mapping) | No       |
| cultura  | Cultura da origem ou coluna de pia. Aplicar quando o tipo é `Datetime` ou `Datetimeoffset` . . A predefinição é `en-us`.<br>Em geral, não precisa de especificar ou alterar esta propriedade. Saiba mais sobre [o mapeamento do tipo de dados.](#data-type-mapping) | No       |
| formato   | Cadeia de formato a utilizar quando o tipo é `Datetime` ou `Datetimeoffset` . Consulte as [cordas de data e formato de hora personalizadas](/dotnet/standard/base-types/custom-date-and-time-format-strings) sobre como formatar a data. Em geral, não precisa de especificar ou alterar esta propriedade. Saiba mais sobre [o mapeamento do tipo de dados.](#data-type-mapping) | No       |

As seguintes propriedades são suportadas `translator` em além `mappings` de:

| Propriedade            | Descrição                                                  | Obrigatório |
| ------------------- | ------------------------------------------------------------ | -------- |
| coleçãoReferência | Aplicar ao copiar dados de origem hierárquica, por exemplo, Conectores Cosmos DB, MongoDB ou REST.<br>Se pretender iteração e extrair dados dos objetos dentro de **um campo** de matriz com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON dessa matriz para fazer a aplicação cruzada. | No       |

#### <a name="tabular-source-to-tabular-sink"></a>Fonte tabular para pia tabular

Por exemplo, copiar dados da Salesforce para a Base de Dados Azure SQL e mapear explicitamente três colunas:

1. No separador de mapeamento da atividade de cópia ->, clique no botão **de esquemas de importação** para importar esquemas de origem e pia.

2. Mapear os campos necessários e excluir/apagar o resto.

![Mapa tabular para tabular](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

O mesmo mapeamento pode ser configurado como o seguinte na carga útil da atividade de cópia `translator` (ver):

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

Para copiar dados de ficheiros de texto delimitados sem linha de cabeçalho, as colunas são representadas por ordinal em vez de nomes. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>Fonte hierárquica para afundar tabular

Ao copiar dados de fonte hierárquica para pia tabular, a atividade de cópia suporta as seguintes capacidades:

- Extrair dados de objetos e matrizes.
- Cross aplica vários objetos com o mesmo padrão de uma matriz, caso em que converter um objeto JSON em vários registos em resultado tabular.

Para uma transformação hierárquica-a-tabular mais avançada, pode utilizar [o Data Flow](concepts-data-flow-overview.md). 

Por exemplo, se tiver um documento mongoDB com o seguinte conteúdo:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

E pretende copiá-lo num ficheiro de texto no seguinte formato com linha de cabeçalho, achatando os dados dentro da matriz *(order_pd e order_price)* e cruzando a junção com a informação raiz comum *(número, data e cidade)*:

| número de encomendas | ordemDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Pode definir tal mapeamento na Data Factory autoriando UI:

1. No separador de mapeamento da atividade de cópia ->, clique no botão **de esquemas de importação** para importar esquemas de origem e pia. À medida que a Data Factory amostra os poucos objetos de topo ao importar esquema, se algum campo não aparecer, pode adicioná-lo à camada correta na hierarquia - pairar sobre um nome de campo existente e optar por adicionar um nó, um objeto ou uma matriz.

2. Selecione a matriz a partir da qual deseja iterar e extrair dados. Será auto-povoado como **referência de coleção.** Note que apenas uma matriz única é suportada para tal funcionamento.

3. Mapear os campos necessários para afundar. A Data Factory determina automaticamente os caminhos JSON correspondentes para o lado hierárquico.

![Mapa hierárquico para tabular usando UI](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

Também pode mudar para **editor Avançado,** caso em que pode ver e editar diretamente os caminhos JSON dos campos. Se optar por adicionar um novo mapeamento nesta vista, especifique o caminho JSON.

![Mapa hierárquico para tabular usando editor avançado](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

O mesmo mapeamento pode ser configurado como o seguinte na carga útil da atividade de cópia `translator` (ver):


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>Fonte tabular/hierárquica para afundar hierárquico

O fluxo de experiência do utilizador é semelhante à [fonte hierárquica à pia tabular.](#hierarchical-source-to-tabular-sink) 

Ao copiar dados de fonte tabular para pia hierárquica, escrever para matriz dentro do objeto não é suportado.

Ao copiar dados de fonte hierárquica para pia hierárquica, pode ainda preservar a hierarquia de toda a camada, selecionando o objeto/matriz e o mapa para afundar sem tocar nos campos internos.

Para uma transformação mais avançada de remodelação de dados, pode utilizar [o Data Flow](concepts-data-flow-overview.md). 

### <a name="parameterize-mapping"></a>Mapear parametrizar

Se pretender criar um gasoduto templificado para copiar um grande número de objetos de forma dinâmica, determine se pode alavancar o [mapeamento predefinido](#default-mapping) ou se precisa de definir [o mapeamento explícito](#explicit-mapping) para os respetivos objetos.

Se for necessário um mapeamento explícito, pode:

1. Defina um parâmetro com o tipo de objeto ao nível do gasoduto, por exemplo, `mapping` .

2. Parametrize o mapeamento: no separador de mapeamento da atividade da cópia ->, opte por adicionar conteúdo dinâmico e selecione o parâmetro acima. A carga útil da atividade seria o seguinte:

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. Construa o valor para passar para o parâmetro de mapeamento. Deve ser todo o objeto de `translator` definição, consulte as amostras na secção [de mapeamento explícito.](#explicit-mapping) Por exemplo, para a fonte tabular para a cópia tabular da pia, o valor deve ser `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>Mapeamento de tipo de dados

A atividade de cópia realiza tipos de origem para afundar tipos de mapeamento com o seguinte fluxo: 

1. Converta-se de tipos de dados nativos de origem para tipos de dados provisórios da Azure Data Factory.
2. Converter automaticamente o tipo de dados provisórios conforme necessário para corresponder aos tipos de pia correspondentes, aplicável tanto para [o mapeamento predefinido](#default-mapping) como para [o mapeamento explícito.](#explicit-mapping)
3. Converta-se a partir de tipos de dados provisórios da Azure Data Factory para afundar tipos de dados nativos.

A atividade de cópia suporta atualmente os seguintes tipos de dados provisórios: Boolean, Byte, Byte array, Datetime, DatetimeOffset, Decimal, Double, GUID, Int16, Int32, Int64, SByte, Single, String, Timespan, UInt16, UInt32 e UInt64.

As seguintes conversões do tipo de dados são suportadas entre os tipos provisórios de origem a afundanço.

| Fonte\Pia | Booleano | Matriz byte | Decimal | Data/Hora <small>(1)</small> | Ponto flutuante <small>(2)</small> | GUID | Inteiro <small>(3)</small> | String | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Booleano     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Matriz byte  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| Data/Hora   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Ponto flutuante | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Número inteiro     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| String      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) A data/hora inclui o DateTime e o DateTimeOffset.

(2) O ponto flutuante inclui single e double.

(3) O inteiro inclui SByte, Byte, Int16, UInt16, Int32, UInt32, Int64 e UInt64.

> [!NOTE]
> - Atualmente, essa conversão do tipo de dados é suportada ao copiar entre dados tabulares. As fontes/pias hierárquicas não são suportadas, o que significa que não existe conversão do tipo de dados definido pelo sistema entre tipos provisórios de origem e pia.
> - Esta funcionalidade funciona com o mais recente modelo de conjunto de dados. Se não vir esta opção a partir da UI, tente criar um novo conjunto de dados.

As seguintes propriedades são suportadas na atividade de cópia para conversão de tipo de dados (na `translator` secção para autoria programática):

| Propriedade                         | Descrição                                                  | Obrigatório |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| tipoConversão                   | Ativar a nova experiência de conversão do tipo de dados. <br>O valor predefinido é falso devido à compatibilidade retrógrada.<br><br>Para novas atividades de cópia criadas através da Data Factory que autoriza uI desde finais de junho de 2020, esta conversão de tipo de dados é ativada por padrão para a melhor experiência, e pode ver as seguintes definições de conversão de tipo na atividade de cópia -> separador de mapeamento para cenários aplicáveis. <br>Para criar o pipeline programáticamente, é necessário definir explicitamente `typeConversion` a propriedade para ser verdadeira para o permitir.<br>Para as atividades de cópia existentes criadas antes de esta funcionalidade ser lançada, não verá opções de conversão de tipo na Data Factory que autoriu UI para retrocompatibilidade. | No       |
| tipoConversionSettings           | Um grupo de definições de conversão do tipo. Aplicar quando `typeConversion` estiver definido para `true` . As seguintes propriedades estão todas sob este grupo. | No       |
| *Sob `typeConversionSettings`* |                                                              |          |
| permitirDataTruncation              | Permitir a truncação de dados ao converter dados de origem para afundar com diferente tipo durante a cópia, por exemplo, de decimal a inteiro, de DatetimeOffset a Datetime. <br>O valor predefinido é verdadeiro. | No       |
| treatBooleanAsNumber             | Trate os booleanos como números, por exemplo, verdadeiros como 1.<br>O valor predefinido é falso. | No       |
| dataTimeFormat                   | Cadeia de formato ao converter entre datas sem offset de fuso horário e cordas, por exemplo, `yyyy-MM-dd HH:mm:ss.fff` .  Consulte as cordas de data e formato de hora personalizadas para obter informações [detalhadas.](/dotnet/standard/base-types/custom-date-and-time-format-strings) | No       |
| dataTimeOffsetFormat             | Cadeia de formato ao converter entre datas com offset de fuso horário e cordas, por exemplo, `yyyy-MM-dd HH:mm:ss.fff zzz` .  Consulte as cordas de data e formato de hora personalizadas para obter informações [detalhadas.](/dotnet/standard/base-types/custom-date-and-time-format-strings) | No       |
| timeSpanFormat                   | Cadeia de formato ao converter entre períodos de tempo e cordas, por exemplo, `dd\.hh\:mm` . Consulte as [cadeias de formato TimeSpan personalizadas](/dotnet/standard/base-types/custom-timespan-format-strings) para obter informações detalhadas. | No       |
| cultura                          | Informações culturais a utilizar quando convertem tipos, por exemplo, `en-us` ou `fr-fr` . | No       |

**Exemplo:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>Modelos legados

> [!NOTE]
> Os modelos seguintes para mapear colunas/campos de origem para afundar ainda são suportados como é para retrocompatibilidade. Sugerimos que utilize o novo modelo mencionado no [mapeamento de esquemas.](#schema-mapping) A Data Factory, autora da UI, passou a gerar o novo modelo.

### <a name="alternative-column-mapping-legacy-model"></a>Mapeamento de colunas alternativa (modelo legado)

Pode especificar a atividade de cópia -> `translator`  ->  `columnMappings` para mapear entre dados em forma de tabular. Neste caso, a secção "estrutura" é necessária tanto para os conjuntos de dados de entrada como para a saída. O mapeamento da coluna suporta **o mapeamento de todas ou subconjuntos de colunas na "estrutura" do conjunto de dados de origem para todas as colunas da "estrutura" do conjunto de dados do lavatório**. Seguem-se as condições de erro que resultam numa exceção:

- O resultado da consulta da loja de dados de origem não tem um nome de coluna especificado na secção "estrutura" do conjunto de dados de entrada.
- A loja de dados de sumidouro (se com esquema pré-definido) não tiver um nome de coluna especificado na secção "estrutura" do conjunto de dados de saída.
- Ou menos colunas ou mais colunas na "estrutura" do conjunto de dados do lavatório do que o especificado no mapeamento.
- Mapeamento duplicado.

No exemplo seguinte, o conjunto de dados de entrada tem uma estrutura, e aponta para uma tabela numa base de dados oracle no local.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Nesta amostra, o conjunto de dados de saída tem uma estrutura e aponta para uma tabela na Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

O seguinte JSON define uma atividade de cópia num oleoduto. As colunas desde a fonte mapeadas a colunas na pia utilizando a coluna **de**  ->  **tradutorEssa** propriedade.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Se estiver a utilizar a sintaxe de especificar o mapeamento de `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` colunas, ainda é suportado como está.

### <a name="alternative-schema-mapping-legacy-model"></a>Mapeamento de esquemas alternativos (modelo legado)

Pode especificar a atividade de cópia -> `translator`  ->  `schemaMapping` para mapear entre dados hierárquicos e dados em forma de tabular, por exemplo, cópia de MongoDB/REST para ficheiro de texto e cópia da API da Oracle para Azure Cosmos DB para MongoDB. As seguintes propriedades são suportadas na secção de atividade de `translator` cópia:

| Propriedade            | Descrição                                                  | Obrigatório |
| :------------------ | :----------------------------------------------------------- | :------- |
| tipo                | A propriedade tipo do tradutor de atividade de cópia deve ser definida para: **TabularTranslator** | Yes      |
| schemaMapping       | Uma coleção de pares de valores-chave, que representa a relação de mapeamento **do lado da fonte para o lado da pia.**<br/>- **Chave:** representa a fonte. Para **a fonte tabular,** especifique o nome da coluna tal como definido na estrutura do conjunto de dados; para **a fonte hierárquica**, especifique a expressão do caminho JSON para cada campo extrair e mapear.<br>- **Valor:** representa a pia. Para **a pia tabular,** especifique o nome da coluna tal como definido na estrutura do conjunto de dados; para **a pia hierárquica**, especifique a expressão do caminho JSON para cada campo extrair e mapear. <br>No caso dos dados hierárquicos, para campos sob objeto raiz, o caminho JSON começa com raiz $; para campos dentro da matriz escolhida por `collectionReference` propriedade, o caminho JSON começa a partir do elemento matriz. | Yes      |
| coleçãoReferência | Se pretender iteração e extrair dados dos objetos dentro de **um campo** de matriz com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON dessa matriz para fazer a aplicação cruzada. Esta propriedade só é suportada quando os dados hierárquicos são fonte. | No       |

**Exemplo: cópia de MongoDB para o Oráculo:**

Por exemplo, se tiver documento MongoDB com o seguinte conteúdo:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

e pretende copiá-lo numa tabela Azure SQL no seguinte formato, achatando os dados dentro da matriz *(order_pd e order_price)* e cruzando a junção com a informação raiz comum *(número, data e cidade)*:

| número de encomendas | ordemDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Configure a regra do mapeamento de esquemas como a seguinte amostra JSON da atividade de cópia:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de Atividade de Cópia:

- [Visão geral da atividade da cópia](copy-activity-overview.md)
