---
title: Formatos de ficheiros suportados na Azure Data Factory (legado)
description: Este tópico descreve os formatos de ficheiro e códigos de compressão suportados pelo conectores baseados em ficheiros no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 423706c391e8d8c2c609798d9f50e5a22f5c39bb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260686"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Formatos de ficheiros suportados e codificadores de compressão na Azure Data Factory (legado)

*Este artigo aplica-se aos seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory introduziu novo modelo de conjunto de dados baseado em formato, ver artigo de formato correspondente com detalhes: <br>formato [- Avro](format-avro.md)<br>[- formato binário](format-binary.md)<br>- [formato de texto delimitado](format-delimited-text.md)<br>formato [JSON](format-json.md) - <br>[formato ORC](format-orc.md) - <br>formato [- Parquet](format-parquet.md)<br>As configurações restantes mencionadas neste artigo ainda são suportadas como é para a compabitilidade atrasada. É-lhe sugerido que use o novo modelo para a frente. 

## <a name="text-format"></a>Formato de texto (legado)

>[!NOTE]
>Conheça o novo modelo a partir do artigo de formato de [texto Delimited.](format-delimited-text.md) As seguintes configurações no conjunto de dados da loja de dados baseado em ficheiros ainda são suportadas como é para a compabitilidade retrógrada. É-lhe sugerido que use o novo modelo para a frente.

Se pretender ler a partir de um ficheiro de texto ou escrever para um ficheiro de texto, coloque a propriedade `type` na secção `format` do conjunto de dados para **TextFormat**. Também pode especificar as seguintes propriedades **opcionais** na secção `format`. Veja a secção [Exemplo de TextFormat](#textformat-example) sobre como configurar.

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| columnDelimiter |O caráter utilizado para separar colunas num ficheiro. Pode considerar para utilizar um caráter não imprimíveis raro que pode não existir nos seus dados. Por exemplo, especifica "\u0001", que representa o início do cabeçalho (SOH). |Só é permitido um caráter. O valor **predefinido** é a **vírgula (“,”)** . <br/><br/>Para utilizar um caracteres Unicode, consulte o [Unicode Characters](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obter o código correspondente para o mesmo. |Não |
| rowDelimiter |O caráter utilizado para separar linhas num ficheiro. |Só é permitido um caráter. O valor **predefinido** é um dos seguintes valores: **["\r\n", "\r", "\n"]** na leitura e **"\r\n"** na escrita. |Não |
| escapeChar |O caráter especial utilizado para escapar a um delimitador de colunas no conteúdo do ficheiro de entrada. <br/><br/>Não pode especificar simultaneamente o escapeChar e o quoteChar para uma tabela. |Só é permitido um caráter. Não existem valores predefinidos. <br/><br/>Exemplo: se utilizar a vírgula (“,”) como delimitador de colunas, mas quiser ter o caráter de vírgula no texto (exemplo: "Olá, mundo"), pode definir “$” como caráter de escape e utilizar a cadeia "Olá$, mundo" na origem. |Não |
| quoteChar |O caráter utilizado para colocar um valor de cadeia entre aspas. Os delimitadores de colunas e linhas dentro dos carateres de aspas são tratados como parte do valor de cadeia. Esta propriedade é aplicável a conjuntos de dados de entrada e de saída.<br/><br/>Não pode especificar simultaneamente o escapeChar e o quoteChar para uma tabela. |Só é permitido um caráter. Não existem valores predefinidos. <br/><br/>Exemplo: se utilizar a vírgula (“,”) como delimitador de colunas, mas quiser ter o caráter de vírgula no texto (exemplo: <Olá, mundo>), pode definir " (aspas duplas) como caráter de aspas e utilizar a cadeia "Olá, mundo" na origem. |Não |
| nullValue |Um ou mais carateres utilizados para representar um valor nulo. |Um ou mais carateres. Os valores **predefinidos** são **"\N" e "NULL"** na leitura e **"\N"** na escrita. |Não |
| encodingName |Especifique o nome de codificação. |Um nome de codificação válido. Veja [Encoding.EncodingName Property](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemplo: windows-1250 ou shift_jis. O valor **predefinido** é **UTF-8**. |Não |
| firstRowAsHeader |Especifica se a primeira linha é considerada um cabeçalho. Num conjunto de dados de entrada, o Data Factory lê a primeira linha como cabeçalho. Num conjunto de dados de saída, o Data Factory escreve a primeira linha como cabeçalho. <br/><br/>Veja [Cenários para utilizar `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) em cenários de exemplo. |Verdadeiro<br/><b>Falso (predefinição)</b> |Não |
| skipLineCount |Indica o número de linhas **não vazias** a saltar ao ler os dados dos ficheiros de entrada. Se as propriedades skipLineCount e firstRowAsHeader forem especificadas simultaneamente, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho são lidas a partir do ficheiro de entrada. <br/><br/>Veja [Cenários para utilizar `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) em cenários de exemplo. |Número inteiro |Não |
| treatEmptyAsNull |Especifica se as cadeias nulas ou vazias são tratadas como valor nulo durante a leitura de dados a partir de um ficheiro de entrada. |**Verdadeiro (predefinição)**<br/>Falso |Não |

### <a name="textformat-example"></a>Exemplo de TextFormat

Na seguinte definição JSON para um conjunto de dados, algumas das propriedades opcionais são especificadas.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Para utilizar um `escapeChar` em vez de `quoteChar`, substitua a linha por `quoteChar` com o seguinte escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Cenários para utilizar firstRowAsHeader e skipLineCount

* Está a copiar de uma origem de não ficheiro para um ficheiro de texto e quer adicionar uma linha de cabeçalho com os metadados de esquema (por exemplo: esquema SQL). Especifique `firstRowAsHeader` como verdadeiro no conjunto de dados de saída deste cenário.
* Está a copiar de um ficheiro de texto que contém uma linha de cabeçalho para um sink de não ficheiro e quer remover essa linha. Especifique `firstRowAsHeader` como verdadeiro no conjunto de dados de entrada.
* Está a copiar de um ficheiro de texto e quer ignorar algumas linhas no início que não contêm dados nem informações de cabeçalho. Especifique `skipLineCount` para indicar o número de linhas a ignorar. Se o resto do ficheiro contiver uma linha de cabeçalho, também pode especificar `firstRowAsHeader`. Se as propriedades `skipLineCount` e `firstRowAsHeader` forem especificadas simultaneamente, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho são lidas a partir do ficheiro de entrada

## <a name="json-format"></a>Formato JSON (legado)

>[!NOTE]
>Conheça o novo modelo a partir do artigo de [formato JSON.](format-json.md) As seguintes configurações no conjunto de dados da loja de dados baseado em ficheiros ainda são suportadas como é para a compabitilidade retrógrada. É-lhe sugerido que use o novo modelo para a frente.

Para **importar/exportar um ficheiro JSON como está dentro/a partir de Azure Cosmos DB,** consulte a secção de documentos JSON de Importação/Exportação em [Dados de Movimento para/a partir do artigo da Azure Cosmos DB.](connector-azure-cosmos-db.md)

Se pretender analisar os ficheiros JSON ou escrever os dados em formato JSON, detete te`type` propriedade na secção `format` para **JsonFormat**. Também pode especificar as seguintes propriedades **opcionais** na secção `format`. Veja a secção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| filePattern |Indica o padrão dos dados armazenados em cada ficheiro JSON. Os valores permitidos são **setOfObjects** e **arrayOfObjects**. O valor **predefinido** é **setOfObjects**. Veja a secção [Padrões de ficheiro JSON](#json-file-patterns) para obter detalhes sobre estes padrões. |Não |
| jsonNodeReference | Se quiser iterar e extrair dados dos objetos dentro de um campo de matriz com o mesmo padrão, especifique o caminho JSON dessa matriz. Esta propriedade só é suportada quando copia dados **de** ficheiros JSON. | Não |
| jsonPathDefinition | Especifique a expressão de caminho do JSON para cada mapeamento de colunas com um nome de coluna personalizado (começar com letra minúscula). Esta propriedade só é suportada quando copia dados **de** ficheiros JSON, e pode extrair dados de objetoou matriz. <br/><br/> Para os campos no objeto raiz, comece com a raiz $; para os campos dentro da matriz escolhida pela propriedade `jsonNodeReference`, comece a partir do elemento de matriz. Veja a secção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar. | Não |
| encodingName |Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, veja Propriedade [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemplo: windows-1250 ou shift_jis. O valor **predefinido** é **UTF-8**. |Não |
| nestingSeparator |Caráter utilizado para separar níveis de aninhamento. O valor predefinido é “.” (ponto). |Não |

>[!NOTE]
>Para o caso de dados de aplicação cruzada em matriz em várias linhas (caso 1 -> amostra 2 em [exemplos JsonFormat),](#jsonformat-example)você só pode optar por expandir uma matriz única usando `jsonNodeReference`propriedade .

### <a name="json-file-patterns"></a>Padrões de ficheiro JSON

Atividade de cópia pode analisar os seguintes padrões de ficheiros JSON:

- **Tipo I: setOfObjects**

    Cada ficheiro contém um único objeto ou múltiplos objetos delimitados por linha/concatenados. Quando esta opção está selecionada num conjunto de dados de saída, a atividade de cópia produz um único ficheiro JSON com cada objeto por linha (delimitados por linha).

    * **Exemplo de JSON de objeto único**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Exemplo de JSON delimitado por linha**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exemplo de JSON concatenado**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Tipo II: arrayOfObjects**

    Cada ficheiro contém uma matriz de objetos.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>Exemplo de JsonFormat

**Caso 1: Copiar dados de ficheiros JSON**

**Exemplo 1: extrair dados de objeto e matriz**

Neste exemplo, espera-se que um objeto JSON de raiz mapeie para um registo individual no resultado de tabela. Se tiver um ficheiro JSON com o seguinte conteúdo:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

e quiser copiá-lo para uma tabela do SQL do Azure no formato seguinte mediante a extração de dados de objetos e da matriz:

| ID | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

O conjunto de dados de entrada com o tipo **JsonFormat** é definido da seguinte forma: (definição parcial com apenas as partes relevantes). Mais especificamente:

- A secção `structure` define os nomes de colunas personalizados e o tipo de dados correspondente enquanto converte em dados tabulares. Esta secção é **opcional**, exceto se precisar de fazer o mapeamento de colunas. Para obter mais informações, consulte as colunas de conjunto de dados de [origem do Mapa para as colunas](copy-activity-schema-and-type-mapping.md)de dataset de destino .
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Para copiar dados da matriz, pode utilizar `array[x].property` para extrair valor da propriedade dada a partir do objeto `xth`, ou pode utilizar `array[*].property` para encontrar o valor de qualquer objeto que contenha tal propriedade.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Exemplo 2: aplicar transversalmente múltiplos objetos com o mesmo padrão da matriz**

Neste exemplo, espera-se transformar um objeto JSON de raiz em vários registos no resultado de tabela. Se tiver um ficheiro JSON com o seguinte conteúdo:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
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
    "city": [ { "sanmateo": "No 1" } ]
}
```

e quiser copiá-lo para uma tabela SQL do Azure no seguinte formato, ao simplificar os dados no interior da matriz e ao cruzá-los com as informações de raiz comuns:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


O conjunto de dados de entrada com o tipo **JsonFormat** é definido da seguinte forma: (definição parcial com apenas as partes relevantes). Mais especificamente:

- A secção `structure` define os nomes de colunas personalizados e o tipo de dados correspondente enquanto converte em dados tabulares. Esta secção é **opcional**, exceto se precisar de fazer o mapeamento de colunas. Para obter mais informações, consulte as colunas de conjunto de dados de [origem do Mapa para as colunas](copy-activity-schema-and-type-mapping.md)de dataset de destino .
- `jsonNodeReference` indica iterar e extrair dados dos objetos com o mesmo padrão sob `orderlines`de **matriz** .
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Neste exemplo, `ordernumber`, `orderdate`e `city` estão sob objeto de raiz com o caminho JSON começando com `$.`, enquanto `order_pd` e `order_price` são definidos com caminho derivado do elemento matriz sem `$.`.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Tenha em atenção os seguintes pontos:**

* Se `structure` e `jsonPathDefinition` não estiverem definidos no conjunto de dados do Data Factory, a Atividade de Cópia deteta o esquema do primeiro objeto e simplifica todo o objeto.
* Se a entrada JSON tiver uma matriz, a Atividade de Cópia, por predefinição, converte todo o valor de matriz numa cadeia. Pode optar por extrair dados da cadeia através de `jsonNodeReference` e/ou `jsonPathDefinition`, ou ignorá-la ao não especificá-la em `jsonPathDefinition`.
* Se existirem nomes duplicados ao mesmo nível, a Atividade de Cópia escolhe o último.
* Os nomes das propriedades são sensíveis às maiúsculas e minúsculas. Duas propriedades que tenham o mesmo nome, mas maiúsculas/minúsculas diferentes, são tratadas como duas propriedades separadas.

**Caso 2: Escrever dados no ficheiro JSON**

Se tiver a tabela seguinte na base de dados SQL:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | José |
| 3 | 20170121 | 4000 | João |

e para cada registo, espera escrever num objeto JSON no seguinte formato:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

O conjunto de dados de saída com o tipo **JsonFormat** é definido da seguinte forma: (definição parcial com apenas as partes relevantes). Mais especificamente, `structure` secção define os nomes de propriedade personalizados no ficheiro de destino, `nestingSeparator` (padrão é ".") são usados para identificar a camada de ninho do nome. Esta secção é **opcional**, exceto se quiser alterar o nome da propriedade para o equiparar ao nome da coluna de origem ou aninhar algumas das propriedades.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format"></a>Formato Parquet (legado)

>[!NOTE]
>Conheça o novo modelo a partir do artigo de [formato Parquet.](format-parquet.md) As seguintes configurações no conjunto de dados da loja de dados baseado em ficheiros ainda são suportadas como é para a compabitilidade retrógrada. É-lhe sugerido que use o novo modelo para a frente.

Se quiser analisar os ficheiros Parquet ou escrever os dados em formato Parquet, detete a `format` `type` imóvel para **parquetFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Tenha em atenção os seguintes pontos:

* Tipos de dados complexos não são suportados (MAP, LIST).
* Espaço branco em nome da coluna não é suportado.
* O ficheiro Parquet tem as seguintes opções relacionadas com a compressão: NENHUM, SNAPPY, GZIP e LZO. Data Factory suporta dados de leitura do ficheiro Parquet em qualquer um destes formatos comprimidos, exceto lZO - utiliza o codec de compressão nos metadados para ler os dados. No entanto, ao escrever num ficheiro Parquet, o Data Factory escolhe a opção SNAPPY, que é a predefinição para o formato Parquet. De momento, não existem opções para contornar este comportamento.

> [!IMPORTANT]
> Para cópia sufiada por Tempo de Execução de Integração Auto-hospedado, por exemplo, entre as lojas de dados no local e as lojas de dados em nuvem, se não estiver a copiar os ficheiros Parquet **como está,** precisa de instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou OpenJDK** na sua máquina de INFRAVERMELHOS. Consulte o parágrafo seguinte com mais detalhes.

Para cópia sintetizada em IR auto-hospedado com serialização/desserialização de ficheiros Parquet, a ADF localiza o tempo de funcionamento de Java verificando primeiro o registo *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, se não for encontrado, verificando em segundo lugar a variável do sistema *`JAVA_HOME`* para OpenJDK.

- **Para utilizar JRE**: O IR de 64 bits requer 64 bits JRE. Pode encontrá-lo [daqui.](https://go.microsoft.com/fwlink/?LinkId=808605)
- Para utilizar o **OpenJDK:** é suportado desde a versão 3.13 do IR. Emba o jvm.dll com todos os outros conjuntos necessários do OpenJDK em máquina de INFRAVERMELHOS auto-hospedada, e coloque a variável do ambiente do sistema JAVA_HOME em conformidade.

>[!TIP]
>Se copiar dados para/a partir do formato Parquet utilizando o Tempo de Execução de Integração Auto-hospedado e atingir um erro dizendo "Ocorreu um erro ao invocar java, mensagem: **java.lang.OutOfMemoryError:Java heap space",** pode adicionar uma variável ambiental `_JAVA_OPTIONS` na máquina que acolhe o IR auto-hospedado para ajustar o tamanho do monte min/max para jVM para capacitar essa cópia, em seguida, reexecutar o pipeline.

![Definir o tamanho da pilha JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: definir `_JAVA_OPTIONS` variável com `-Xms256m -Xmx16g`de valor . O `Xms` da bandeira especifica o conjunto inicial de atribuição de memória para uma Máquina Virtual Java (JVM), enquanto `Xmx` especifica o conjunto máximo de atribuição de memória. Isto significa que o JVM será iniciado com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por predefinição, a ADF utiliza min 64MB e máx.

### <a name="data-type-mapping-for-parquet-files"></a>Tipo de dados de mapeamento para ficheiros Parquet

| Tipo de dados intermediárias de fábrica de dados | Tipo de primitivo parquet | Parquet tipo Original (anular a serialização) | Parquet tipo Original (serialização) |
|:--- |:--- |:--- |:--- |
| Booleano | Booleano | N/D | N/D |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/binário | UInt64 | decimal |
| Único | Float | N/D | N/D |
| Valor de duplo | Valor de duplo | N/D | N/D |
| decimal | Binário | decimal | decimal |
| String | Binário | Utf8 | Utf8 |
| DateTime | Int96 | N/D | N/D |
| TimeSpan | Int96 | N/D | N/D |
| DateTimeOffset | Int96 | N/D | N/D |
| ByteArray | Binário | N/D | N/D |
| Guid | Binário | Utf8 | Utf8 |
| char | Binário | Utf8 | Utf8 |
| CharArray | Não suportado | N/D | N/D |

## <a name="orc-format"></a>Formato ORC (legado)

>[!NOTE]
>Conheça o novo modelo a partir do artigo de [formato ORC.](format-orc.md) As seguintes configurações no conjunto de dados da loja de dados baseado em ficheiros ainda são suportadas como é para a compabitilidade retrógrada. É-lhe sugerido que use o novo modelo para a frente.

Se pretender analisar os ficheiros ORC ou escrever os dados em formato ORC, detete a `format` `type` propriedade para **OrcFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "OrcFormat"
}
```

Tenha em atenção os seguintes pontos:

* Não são suportados tipos de dados complexos (STRUCT, MAP, LIST, UNION).
* Espaço branco em nome da coluna não é suportado.
* O ficheiro ORC tem três [opções relacionadas com a compressão](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NENHUM, ZLIB, SNAPPY. O Data Factory suporta a leitura de dados a partir de um ficheiro ORC em qualquer um destes formatos de compressão. Utiliza o codec de compressão existente nos metadados para ler os dados. No entanto, ao escrever num ficheiro ORC, o Data Factory escolhe a opção ZLIB, que é a predefinição para ORC. De momento, não existem opções para contornar este comportamento.

> [!IMPORTANT]
> Para cópia sufiada por Tempo de Execução de Integração Auto-hospedado, por exemplo, entre as lojas de dados no local e as lojas de dados em nuvem, se não estiver a copiar os ficheiros ORC **como está,** precisa de instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou OpenJDK** na sua máquina de INFRAVERMELHOS. Consulte o parágrafo seguinte com mais detalhes.

Para cópia sintetizada em IR auto-hospedado com serialização/desserialização de ficheiros ORC, a ADF localiza o tempo de funcionamento de Java verificando primeiro o registo *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, se não for encontrado, verificando em segundo lugar a variável do sistema *`JAVA_HOME`* para OpenJDK.

- **Para utilizar JRE**: O IR de 64 bits requer 64 bits JRE. Pode encontrá-lo [daqui.](https://go.microsoft.com/fwlink/?LinkId=808605)
- Para utilizar o **OpenJDK:** é suportado desde a versão 3.13 do IR. Emba o jvm.dll com todos os outros conjuntos necessários do OpenJDK em máquina de INFRAVERMELHOS auto-hospedada, e coloque a variável do ambiente do sistema JAVA_HOME em conformidade.

### <a name="data-type-mapping-for-orc-files"></a>Tipo de dados de mapeamento para ficheiros ORC

| Tipo de dados intermediárias de fábrica de dados | Tipos ORC |
|:--- |:--- |
| Booleano | Booleano |
| SByte | Byte |
| Byte | Curto |
| Int16 | Curto |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Longo |
| Int64 | Longo |
| UInt64 | String |
| Único | Float |
| Valor de duplo | Valor de duplo |
| decimal | decimal |
| String | String |
| DateTime | Carimbo de data/hora |
| DateTimeOffset | Carimbo de data/hora |
| TimeSpan | Carimbo de data/hora |
| ByteArray | Binário |
| Guid | String |
| char | Char(1) |

## <a name="avro-format"></a>Formato AVRO (legado)

>[!NOTE]
>Conheça o novo modelo a partir do artigo de [formato Avro.](format-avro.md) As seguintes configurações no conjunto de dados da loja de dados baseado em ficheiros ainda são suportadas como é para a compabitilidade retrógrada. É-lhe sugerido que use o novo modelo para a frente.

Se pretender analisar os ficheiros Avro ou escrever os dados em formato Avro, detete te`format` `type` imóvel para **AvroFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "AvroFormat",
}
```

Para utilizar o formato Avro numa tabela do Hive, veja o [tutorial do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Tenha em atenção os seguintes pontos:

* Não são suportados tipos de [dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) (registos, enums, matrizes, mapas, sindicatos e fixos).

## <a name="compression-support"></a>Apoio à compressão (legado)

O Azure Data Factory suporta descomprimir/comprimir dados durante a cópia. Quando especifica `compression` propriedade num conjunto de dados de entrada, a atividade de cópia lê os dados comprimidos da fonte e descomprime-os; e quando especifica a propriedade num conjunto de dados de saída, a comprime a atividade da cópia e, em seguida, escreva dados para o lavatório. Seguem-se alguns cenários de exemplo:

* Dados comprimidos GZIP de leitura de um blob do Azure, descompactá-lo e escrever dados de resultado para uma base de dados SQL do Azure. Define o conjunto de dados input Azure Blob com a propriedade `compression` `type` como GZIP.
* Ler dados a partir de um ficheiro de texto sem formatação do sistema de arquivos no local, comprimi-los usando o formato GZip e escrever os dados compactados para um blob do Azure. Você define um conjunto de dados de saída Azure Blob com a propriedade `compression` `type` como GZip.
* Ficheiro. zip de leitura do servidor FTP, descompactá-lo para obter os ficheiros dentro e encaminhado para esses ficheiros do Azure Data Lake Store. Define um conjunto de dados FTP de entrada com a propriedade `type` `compression` como ZipDeflate.
* Ler uma compressão de GZIP de dados de um blob do Azure, descompactá-lo, comprimi-los usando BZIP2 e escrever dados de resultado para um blob do Azure. Define o conjunto de dados input Azure Blob com `compression` `type` definido para GZIP e o conjunto de dados de saída com `compression` `type` definido para BZIP2.

Para especificar a compressão para um conjunto de dados, utilize a propriedade de **compressão** no conjunto de dados JSON como no seguinte exemplo:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

A secção de **compressão** tem duas propriedades:

* **Tipo:** o codec de compressão, que pode ser **GZIP,** **Deflate,** **BZIP2**, ou **ZipDeflate**. Note ao utilizar a atividade de cópia para descomprimir ficheiros ZipDeflate e escrever para uma loja de dados de sumidouro baseada em ficheiros, os ficheiros serão extraídos para a pasta: `<path specified in dataset>/<folder named as source zip file>/`.
* **Nível:** a relação de compressão, que pode ser **Ótima** ou **Mais Rápida**.

  * **Mais rápido:** A operação de compressão deve ser concluída o mais rapidamente possível, mesmo que o ficheiro resultante não esteja devidamente comprimido.
  * **Ótimo:** O funcionamento da compressão deve ser optimizadamente comprimido, mesmo que a operação leve mais tempo a concluir.

    Para mais informações, consulte o tópico [do Nível de Compressão.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)

> [!NOTE]
> As definições de compressão não são suportadas para dados no **AvroFormat,** **OrcFormat**, ou **ParquetFormat**. Durante a leitura de ficheiros nesses formatos, o Data Factory Deteta e utiliza o codec de compressão existente nos metadados. Ao escrever ficheiros nesses formatos, o Data Factory escolhe o codec de compressão predefinido para esse formato. Por exemplo, ZLIB para OrcFormat e SNAPPY para ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Tipos de ficheiros não suportados e formatos de compressão

Pode utilizar as funcionalidades de extensibility da Azure Data Factory para transformar ficheiros que não são suportados.
Duas opções incluem Funções Azure e tarefas personalizadas utilizando o Lote Azure.

Pode ver uma amostra que utiliza uma função Azure para [extrair o conteúdo de um ficheiro de alcatrão](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Para mais informações, consulte a [atividade das Funções Azure.](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)

Também pode construir esta funcionalidade usando uma atividade personalizada de dotnet. Mais informações estão disponíveis [aqui](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Passos seguintes

Conheça os mais recentes formatos de ficheiros suportados e compressões de [formatos e compressões de ficheiros suportados](supported-file-formats-and-compression-codecs.md).
