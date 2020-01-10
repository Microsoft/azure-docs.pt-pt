---
title: Formatos de arquivo com suporte no Azure Data Factory (Herdado)
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483222"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Formatos de arquivo com suporte e codecs de compactação no Azure Data Factory (Herdado)

*Este artigo se aplica aos seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory introduziu um novo modelo de conjunto de informações baseado em formato, consulte o artigo de formato correspondente com detalhes: <br>[formato Avro](format-avro.md) - <br>- [formato binário](format-binary.md)<br>- [formato de texto delimitado](format-delimited-text.md)<br>- [formato JSON](format-json.md)<br>- [formato Orc](format-orc.md)<br>- [formato parquet](format-parquet.md)<br>As configurações REST mencionadas neste artigo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro. 

## <a name="text-format"></a>Formato de texto (Herdado)

>[!NOTE]
>Conheça o artigo novo modelo de [formato de texto delimitado](format-delimited-text.md) . As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se quiser ler um arquivo de texto ou escrever num ficheiro de texto, defina o `type` propriedade no `format` secção do conjunto de dados para **TextFormat**. Também pode especificar as seguintes propriedades **opcionais** na secção `format`. Veja a secção [Exemplo de TextFormat](#textformat-example) sobre como configurar.

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| columnDelimiter |O caráter utilizado para separar colunas num ficheiro. Pode considerar para utilizar um caráter não imprimíveis raro que pode não existir nos seus dados. Por exemplo, especifica "\u0001", que representa o início do cabeçalho (SOH). |Só é permitido um caráter. O valor **predefinido** é a **vírgula (“,”)** . <br/><br/>Para utilizar um caráter Unicode, veja [caracteres Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obter o código correspondente para o mesmo. |Não |
| rowDelimiter |O caráter utilizado para separar linhas num ficheiro. |Só é permitido um caráter. O valor **predefinido** é um dos seguintes valores: **["\r\n", "\r", "\n"]** na leitura e **"\r\n"** na escrita. |Não |
| escapeChar |O caráter especial utilizado para escapar a um delimitador de colunas no conteúdo do ficheiro de entrada. <br/><br/>Não pode especificar simultaneamente o escapeChar e o quoteChar para uma tabela. |Só é permitido um caráter. Não existem valores predefinidos. <br/><br/>Exemplo: se utilizar a vírgula (“,”) como delimitador de colunas, mas quiser ter o caráter de vírgula no texto (exemplo: "Olá, mundo"), pode definir “$” como caráter de escape e utilizar a cadeia "Olá$, mundo" na origem. |Não |
| quoteChar |O caráter utilizado para colocar um valor de cadeia entre aspas. Os delimitadores de colunas e linhas dentro dos carateres de aspas são tratados como parte do valor de cadeia. Esta propriedade é aplicável a conjuntos de dados de entrada e de saída.<br/><br/>Não pode especificar simultaneamente o escapeChar e o quoteChar para uma tabela. |Só é permitido um caráter. Não existem valores predefinidos. <br/><br/>Exemplo: se utilizar a vírgula (“,”) como delimitador de colunas, mas quiser ter o caráter de vírgula no texto (exemplo: <Olá, mundo>), pode definir " (aspas duplas) como caráter de aspas e utilizar a cadeia "Olá, mundo" na origem. |Não |
| nullValue |Um ou mais carateres utilizados para representar um valor nulo. |Um ou mais carateres. Os valores **predefinidos** são **"\N" e "NULL"** na leitura e **"\N"** na escrita. |Não |
| encodingName |Especifique o nome de codificação. |Um nome de codificação válido. Veja [Encoding.EncodingName Property](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemplo: windows-1250 ou shift_jis. O valor **predefinido** é **UTF-8**. |Não |
| firstRowAsHeader |Especifica se a primeira linha é considerada um cabeçalho. Num conjunto de dados de entrada, o Data Factory lê a primeira linha como cabeçalho. Num conjunto de dados de saída, o Data Factory escreve a primeira linha como cabeçalho. <br/><br/>Veja [Cenários para utilizar `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) em cenários de exemplo. |Verdadeiro<br/><b>Falso (predefinição)</b> |Não |
| skipLineCount |Indica o número de **nula** linhas a ignorar durante a leitura de dados de ficheiros de entrada. Se as propriedades skipLineCount e firstRowAsHeader forem especificadas simultaneamente, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho são lidas a partir do ficheiro de entrada. <br/><br/>Veja [Cenários para utilizar `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) em cenários de exemplo. |Número inteiro |Não |
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

## <a name="json-format"></a>Formato JSON (Herdado)

>[!NOTE]
>Conheça o artigo novo modelo do [formato JSON](format-json.md) . As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Para **importar/exportar um ficheiro JSON como-é em/do Azure Cosmos DB**, consulte a secção documentos JSON de importação/exportação [mover dados de/para o Azure Cosmos DB](connector-azure-cosmos-db.md) artigo.

Se quiser analisar os ficheiros JSON ou escrever os dados no formato JSON, defina o `type` propriedade no `format` secção a **JsonFormat**. Também pode especificar as seguintes propriedades **opcionais** na secção `format`. Veja a secção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| filePattern |Indica o padrão dos dados armazenados em cada ficheiro JSON. Os valores permitidos são **setOfObjects** e **arrayOfObjects**. O valor **predefinido** é **setOfObjects**. Veja a secção [Padrões de ficheiro JSON](#json-file-patterns) para obter detalhes sobre estes padrões. |Não |
| jsonNodeReference | Se quiser iterar e extrair dados dos objetos dentro de um campo de matriz com o mesmo padrão, especifique o caminho JSON dessa matriz. Essa propriedade tem suporte apenas ao copiar dados **de** arquivos JSON. | Não |
| jsonPathDefinition | Especifique a expressão de caminho do JSON para cada mapeamento de colunas com um nome de coluna personalizado (começar com letra minúscula). Essa propriedade tem suporte apenas ao copiar dados **de** arquivos JSON, e você pode extrair dados do objeto ou da matriz. <br/><br/> Para os campos no objeto raiz, comece com a raiz $; para os campos dentro da matriz escolhida pela propriedade `jsonNodeReference`, comece a partir do elemento de matriz. Veja a secção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar. | Não |
| encodingName |Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, veja Propriedade [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemplo: windows-1250 ou shift_jis. O valor **predefinido** é **UTF-8**. |Não |
| nestingSeparator |Caráter utilizado para separar níveis de aninhamento. O valor predefinido é “.” (ponto). |Não |

>[!NOTE]
>Para o caso de aplicação cruzada de dados na matriz em várias linhas (caso 1-> exemplo 2 em [exemplos de JsonFormat](#jsonformat-example)), você pode optar por expandir apenas uma matriz usando a propriedade `jsonNodeReference`.

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

- A secção `structure` define os nomes de colunas personalizados e o tipo de dados correspondente enquanto converte em dados tabulares. Esta secção é **opcional**, exceto se precisar de fazer o mapeamento de colunas. Para obter mais informações, consulte [mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de destino](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Para copiar dados de matriz, pode usar `array[x].property` para extrair o valor da propriedade específica do `xth` objeto ou pode usar `array[*].property` para encontrar o valor a partir de qualquer objeto que contenha essa propriedade.

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

- A secção `structure` define os nomes de colunas personalizados e o tipo de dados correspondente enquanto converte em dados tabulares. Esta secção é **opcional**, exceto se precisar de fazer o mapeamento de colunas. Para obter mais informações, consulte [mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de destino](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` Indica a iteração e extrair dados de objetos com o mesmo padrão em **matriz** `orderlines`.
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Neste exemplo, `ordernumber`, `orderdate`, e `city` são no objeto de raiz com JSON caminho começado por `$.`, enquanto `order_pd` e `order_price` são definidas com o caminho derivado do elemento de matriz sem `$.` .

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

O conjunto de dados de saída com o tipo **JsonFormat** é definido da seguinte forma: (definição parcial com apenas as partes relevantes). Mais especificamente, `structure` secção define os nomes de propriedade personalizados no ficheiro de destino, `nestingSeparator` (a predefinição é ".") são utilizados para identificar a camada aninhada do nome. Esta secção é **opcional**, exceto se quiser alterar o nome da propriedade para o equiparar ao nome da coluna de origem ou aninhar algumas das propriedades.

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

## <a name="parquet-format"></a>Formato parquet (Herdado)

>[!NOTE]
>Aprenda o novo modelo no artigo [formato parquet](format-parquet.md) . As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se quiser analisar os ficheiros Parquet ou escrever os dados em formato Parquet, defina a propriedade `format` `type` como **ParquetFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Tenha em atenção os seguintes pontos:

* Não há suporte para tipos de dados complexos (mapa, lista).
* Não há suporte para o espaço em branco no nome da coluna.
* O ficheiro Parquet tem as seguintes opções relacionadas com a compressão: NENHUM, SNAPPY, GZIP e LZO. Data Factory dá suporte à leitura de dados do arquivo parquet em qualquer um desses formatos compactados, exceto LZO-ele usa o codec de compactação nos metadados para ler os dados. No entanto, ao escrever num ficheiro Parquet, o Data Factory escolhe a opção SNAPPY, que é a predefinição para o formato Parquet. De momento, não existem opções para contornar este comportamento.

> [!IMPORTANT]
> Para cópia habilitada pelo Integration Runtime autohospedado, por exemplo, entre armazenamentos de dados locais e na nuvem, se você não estiver copiando arquivos parquet **como estão**, será necessário instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou o OpenJDK** em seu computador ir. Consulte o parágrafo a seguir com mais detalhes.

Para a cópia em execução no IR auto-hospedado com serialização/desserialização de arquivo parquet, o ADF localiza o tempo de execução do Java verificando primeiro o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para o JRE, se não for encontrado, verificando a variável do sistema *`JAVA_HOME`* para OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. Você pode encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: tem suporte desde a versão de ir 3,13. Empacote o JVM. dll com todos os outros assemblies necessários do OpenJDK no computador IR auto-hospedado e defina a variável de ambiente do sistema JAVA_HOME de acordo.

>[!TIP]
>Se você copiar dados de/para o formato parquet usando o autohospedado Integration Runtime e erro de ocorrência indicando "ocorreu um erro ao invocar Java, mensagem: **Java. lang. OutOfMemoryError: espaço de heap Java**", você pode adicionar uma variável de ambiente `_JAVA_OPTIONS` no computador que hospeda o ir do modo auto-hospedado para ajustar o tamanho de heap mínimo/máximo para a JVM para capacitar tal cópia e

![Definir o tamanho do heap JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: definir a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para um Máquina Virtual Java (JVM), enquanto `Xmx` especifica o pool de alocação de memória máximo. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e poderá usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa mín. de 64MB e Max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Tipo de dados de mapeamento para ficheiros Parquet

| Tipo de dados intermediárias de fábrica de dados | Tipo de primitivo parquet | Parquet tipo Original (anular a serialização) | Parquet tipo Original (serialização) |
|:--- |:--- |:--- |:--- |
| Booleano | Booleano | N/A | N/A |
| SByte | Int32 | Int8 | Int8 |
| bytes | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/binário | UInt64 | Decimal |
| Único | Flutuante | N/A | N/A |
| Double | Double | N/A | N/A |
| Decimal | Binary | Decimal | Decimal |
| String | Binary | Utf8 | Utf8 |
| DateTime | Int96 | N/A | N/A |
| TimeSpan | Int96 | N/A | N/A |
| DateTimeOffset | Int96 | N/A | N/A |
| ByteArray | Binary | N/A | N/A |
| GUID | Binary | Utf8 | Utf8 |
| char | Binary | Utf8 | Utf8 |
| CharArray | Não suportado | N/A | N/A |

## <a name="orc-format"></a>Formato ORC (Herdado)

>[!NOTE]
>Aprenda o novo modelo no artigo [formato Orc](format-orc.md) . As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se quiser analisar os ficheiros ORC ou escrever os dados em formato ORC, defina a propriedade `format` `type` como **OrcFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "OrcFormat"
}
```

Tenha em atenção os seguintes pontos:

* Não há suporte para tipos de dados complexos (STRUCT, MAP, LIST, UNION).
* Não há suporte para o espaço em branco no nome da coluna.
* O ficheiro ORC tem três [opções relacionadas com a compressão](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NENHUM, ZLIB, SNAPPY. O Data Factory suporta a leitura de dados a partir de um ficheiro ORC em qualquer um destes formatos de compressão. Utiliza o codec de compressão existente nos metadados para ler os dados. No entanto, ao escrever num ficheiro ORC, o Data Factory escolhe a opção ZLIB, que é a predefinição para ORC. De momento, não existem opções para contornar este comportamento.

> [!IMPORTANT]
> Para cópia habilitada pelo Integration Runtime autohospedado, por exemplo, entre armazenamentos de dados locais e na nuvem, se você não estiver copiando arquivos ORC **como estão**, será necessário instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou o OpenJDK** em seu computador ir. Consulte o parágrafo a seguir com mais detalhes.

Para a cópia em execução no IR auto-hospedado com serialização/desserialização de arquivo ORC, o ADF localiza o tempo de execução do Java verificando primeiro o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para o JRE, se não for encontrado, verificando a variável do sistema *`JAVA_HOME`* para OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. Você pode encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: tem suporte desde a versão de ir 3,13. Empacote o JVM. dll com todos os outros assemblies necessários do OpenJDK no computador IR auto-hospedado e defina a variável de ambiente do sistema JAVA_HOME de acordo.

### <a name="data-type-mapping-for-orc-files"></a>Tipo de dados de mapeamento para ficheiros ORC

| Tipo de dados intermediárias de fábrica de dados | Tipos ORC |
|:--- |:--- |
| Booleano | Booleano |
| SByte | bytes |
| bytes | Curto |
| Int16 | Curto |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Longo |
| Int64 | Longo |
| UInt64 | String |
| Único | Flutuante |
| Double | Double |
| Decimal | Decimal |
| String | String |
| DateTime | Carimbo de data/hora |
| DateTimeOffset | Carimbo de data/hora |
| TimeSpan | Carimbo de data/hora |
| ByteArray | Binary |
| GUID | String |
| char | Char(1) |

## <a name="avro-format"></a>Formato AVRO (Herdado)

>[!NOTE]
>Conheça o artigo novo modelo do [formato Avro](format-avro.md) . As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se quiser analisar os ficheiros Avro ou escrever os dados em formato Avro, defina a propriedade `format` `type` como **AvroFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "AvroFormat",
}
```

Para utilizar o formato Avro numa tabela do Hive, veja o [tutorial do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Tenha em atenção os seguintes pontos:

* [Tipos de dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) não são suportados (registos, enumerações, matrizes, mapas, uniões e fixo).

## <a name="compression-support"></a>Suporte à compactação (Herdado)

O Azure Data Factory suporta descomprimir/comprimir dados durante a cópia. Quando especificar `compression` propriedade num conjunto de dados de entrada, a atividade de cópia ler os dados comprimidos de origem e descompactá-lo; e, quando especificar a propriedade num conjunto de dados de saída, a atividade de cópia comprimir, em seguida, escrever dados para o sink. Seguem-se alguns cenários de exemplo:

* Dados comprimidos GZIP de leitura de um blob do Azure, descompactá-lo e escrever dados de resultado para uma base de dados SQL do Azure. Defina o conjunto de dados de Blobs do Azure entrado com o `compression` `type` propriedade como GZIP.
* Ler dados a partir de um ficheiro de texto sem formatação do sistema de arquivos no local, comprimi-los usando o formato GZip e escrever os dados compactados para um blob do Azure. Define um conjunto de dados de Blobs do Azure de saída com o `compression` `type` propriedade como GZip.
* Ficheiro. zip de leitura do servidor FTP, descompactá-lo para obter os ficheiros dentro e encaminhado para esses ficheiros do Azure Data Lake Store. Define um conjunto de dados FTP de entrada com o `compression` `type` propriedade como ZipDeflate.
* Ler uma compressão de GZIP de dados de um blob do Azure, descompactá-lo, comprimi-los usando BZIP2 e escrever dados de resultado para um blob do Azure. Defina o conjunto de dados de Blobs do Azure entrado com `compression` `type` definido como GZIP e o conjunto de dados de saída com `compression` `type` definido como BZIP2.

Para especificar a compressão para um conjunto de dados, utilize o **compressão** propriedade no conjunto de dados JSON como no exemplo seguinte:

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

O **compressão** secção tem duas propriedades:

* **Tipo:** o codec de compressão, que pode ser **GZIP**, **Deflate**, **BZIP2**, ou **ZipDeflate**. Observação ao usar a atividade de cópia para descompactar arquivo (s) ZipDeflate e gravar no armazenamento de dados de coletor baseado em arquivo, os arquivos serão extraídos para a pasta: `<path specified in dataset>/<folder named as source zip file>/`.
* **Nível:** taxa de compressão, que pode ser **Optimal** ou **Fastest**.

  * **O mais rápido:** a operação de compactação deve ser concluído mais rápido possível, mesmo que o ficheiro resultante não será compactado ideal.
  * **Ideal**: A operação de compactação deve ser ideal compactada, mesmo que a operação demora mais tempo a concluir.

    Para obter mais informações, consulte [nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tópico.

> [!NOTE]
> As definições de compressão não são suportadas para os dados no **AvroFormat**, **OrcFormat**, ou **ParquetFormat**. Durante a leitura de ficheiros nesses formatos, o Data Factory Deteta e utiliza o codec de compressão existente nos metadados. Ao escrever ficheiros nesses formatos, o Data Factory escolhe o codec de compressão predefinido para esse formato. Por exemplo, ZLIB para OrcFormat e SNAPPY para ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Tipos de arquivo e formatos de compactação sem suporte

Você pode usar os recursos de extensibilidade do Azure Data Factory para transformar arquivos que não têm suporte.
Duas opções incluem Azure Functions e tarefas personalizadas usando o lote do Azure.

Você pode ver um exemplo que usa uma função do Azure para [extrair o conteúdo de um arquivo tar](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Para obter mais informações, consulte [Azure Functions atividade](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Você também pode criar essa funcionalidade usando uma atividade dotnet personalizada. Mais informações estão disponíveis [aqui](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Passos seguintes

Conheça os formatos de arquivo com suporte mais recentes e as compactações de [formatos de arquivo e compactações com suporte](supported-file-formats-and-compression-codecs.md).
