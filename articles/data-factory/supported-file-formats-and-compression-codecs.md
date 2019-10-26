---
title: Formatos de arquivo com suporte no Azure Data Factory | Microsoft Docs
description: Este tópico descreve os formatos de arquivo e os códigos de compactação que são suportados por conectores baseados em arquivo no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 00d8fb69abb6ce74a36ff017f3f356cb86114d99
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72930920"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Formatos de arquivo com suporte e codecs de compactação no Azure Data Factory

*Este artigo se aplica aos seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Armazenamento](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).*

Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. Se você quiser **analisar ou gerar arquivos com um formato específico**, o Azure data Factory oferece suporte aos seguintes tipos de formato de arquivo:

* [Formato de texto](#text-format)
* [Formato JSON](#json-format)
* [Formato parquet](#parquet-format)
* [Formato ORC](#orc-format)
* [Formato Avro](#avro-format)
* [Formato binário](#binary-format)

> [!TIP]
> Saiba como a atividade de cópia mapeia os dados de origem para o coletor do [mapeamento de esquema na atividade de cópia](copy-activity-schema-and-type-mapping.md).

## <a name="text-format"></a>Formato de texto

>[!NOTE]
>Data Factory introduziu um novo conjunto de informações de formato de texto delimitado, consulte o artigo [formato de texto delimitado](format-delimited-text.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se você quiser ler um arquivo de texto ou gravar em um arquivo de texto, defina a propriedade `type` na seção `format` do conjunto de propriedades para **TextFormat**. Também pode especificar as seguintes propriedades **opcionais** na secção `format`. Veja a secção [Exemplo de TextFormat](#textformat-example) sobre como configurar.

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| columnDelimiter |O caráter utilizado para separar colunas num ficheiro. Você pode considerar o uso de um caractere incomum não imprimível que pode não existir em seus dados. Por exemplo, especifique "\u0001", que representa o início do título (SOH). |Só é permitido um caráter. O valor **predefinido** é a **vírgula (“,”)** . <br/><br/>Para usar um caractere Unicode, consulte [caracteres Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obter o código correspondente. |Não |
| rowDelimiter |O caráter utilizado para separar linhas num ficheiro. |Só é permitido um caráter. O valor **predefinido** é um dos seguintes valores: **["\r\n", "\r", "\n"]** na leitura e **"\r\n"** na escrita. |Não |
| escapeChar |O caráter especial utilizado para escapar a um delimitador de colunas no conteúdo do ficheiro de entrada. <br/><br/>Não pode especificar simultaneamente o escapeChar e o quoteChar para uma tabela. |Só é permitido um caráter. Não existem valores predefinidos. <br/><br/>Exemplo: se utilizar a vírgula (“,”) como delimitador de colunas, mas quiser ter o caráter de vírgula no texto (exemplo: "Olá, mundo"), pode definir “$” como caráter de escape e utilizar a cadeia "Olá$, mundo" na origem. |Não |
| quoteChar |O caráter utilizado para colocar um valor de cadeia entre aspas. Os delimitadores de colunas e linhas dentro dos carateres de aspas são tratados como parte do valor de cadeia. Esta propriedade é aplicável a conjuntos de dados de entrada e de saída.<br/><br/>Não pode especificar simultaneamente o escapeChar e o quoteChar para uma tabela. |Só é permitido um caráter. Não existem valores predefinidos. <br/><br/>Exemplo: se utilizar a vírgula (“,”) como delimitador de colunas, mas quiser ter o caráter de vírgula no texto (exemplo: <Olá, mundo>), pode definir " (aspas duplas) como caráter de aspas e utilizar a cadeia "Olá, mundo" na origem. |Não |
| nullValue |Um ou mais carateres utilizados para representar um valor nulo. |Um ou mais carateres. Os valores **predefinidos** são **"\N" e "NULL"** na leitura e **"\N"** na escrita. |Não |
| encodingName |Especifique o nome de codificação. |Um nome de codificação válido. Veja [Encoding.EncodingName Property](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemplo: windows-1250 ou shift_jis. O valor **predefinido** é **UTF-8**. |Não |
| firstRowAsHeader |Especifica se a primeira linha é considerada um cabeçalho. Num conjunto de dados de entrada, o Data Factory lê a primeira linha como cabeçalho. Num conjunto de dados de saída, o Data Factory escreve a primeira linha como cabeçalho. <br/><br/>Veja [Cenários para utilizar `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) em cenários de exemplo. |Verdadeiro<br/><b>Falso (predefinição)</b> |Não |
| skipLineCount |Indica o número de linhas **não vazias** a serem ignoradas ao ler dados de arquivos de entrada. Se as propriedades skipLineCount e firstRowAsHeader forem especificadas simultaneamente, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho são lidas a partir do ficheiro de entrada. <br/><br/>Veja [Cenários para utilizar `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) em cenários de exemplo. |Número inteiro |Não |
| treatEmptyAsNull |Especifica se as cadeias nulas ou vazias são tratadas como valor nulo durante a leitura de dados a partir de um ficheiro de entrada. |**Verdadeiro (predefinição)**<br/>Falso |Não |

### <a name="textformat-example"></a>Exemplo de TextFormat

Na definição JSON a seguir para um conjunto de um DataSet, algumas das propriedades opcionais são especificadas.

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

## <a name="json-format"></a>Formato JSON

>[!NOTE]
>Data Factory introduziu o novo conjunto de informações de formato JSON, consulte o artigo [JSON](format-json.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Para **importar/exportar um arquivo JSON no estado em/de Azure Cosmos DB**, consulte a seção importar/exportar documentos JSON no artigo [mover dados de/para Azure Cosmos DB](connector-azure-cosmos-db.md) .

Se você quiser analisar os arquivos JSON ou gravar os dados no formato JSON, defina a propriedade `type` na seção `format` como **JsonFormat**. Também pode especificar as seguintes propriedades **opcionais** na secção `format`. Veja a secção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar.

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

A atividade de cópia pode analisar os seguintes padrões de arquivos JSON:

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

- A secção `structure` define os nomes de colunas personalizados e o tipo de dados correspondente enquanto converte em dados tabulares. Esta secção é **opcional**, exceto se precisar de fazer o mapeamento de colunas. Para obter mais informações, consulte [mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de destino](copy-activity-schema-and-type-mapping.md)
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Para copiar dados da matriz, você pode usar `array[x].property` para extrair o valor da propriedade fornecida do objeto `xth` ou pode usar `array[*].property` para localizar o valor de qualquer objeto que contenha tal propriedade.

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

- A secção `structure` define os nomes de colunas personalizados e o tipo de dados correspondente enquanto converte em dados tabulares. Esta secção é **opcional**, exceto se precisar de fazer o mapeamento de colunas. Para obter mais informações, consulte [mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de destino](copy-activity-schema-and-type-mapping.md)
- `jsonNodeReference` indica iterar e extrair dados dos objetos com o mesmo padrão em `orderlines`de **matriz** .
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Neste exemplo, `ordernumber`, `orderdate`e `city` estão sob o objeto raiz com o caminho JSON começando com `$.`, enquanto `order_pd` e `order_price` são definidos com o caminho derivado do elemento de matriz sem `$.`.

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

Se você tiver a seguinte tabela no banco de dados SQL:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | José |
| 3 | 20170121 | 4000 | João |

e, para cada registro, você espera gravar em um objeto JSON no seguinte formato:

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

O conjunto de dados de saída com o tipo **JsonFormat** é definido da seguinte forma: (definição parcial com apenas as partes relevantes). Mais especificamente, `structure` seção define os nomes de propriedade personalizada no arquivo de destino, `nestingSeparator` (o padrão é ".") são usados para identificar a camada de aninhamento do nome. Esta secção é **opcional**, exceto se quiser alterar o nome da propriedade para o equiparar ao nome da coluna de origem ou aninhar algumas das propriedades.

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

## <a name="parquet-format"></a>Formato Parquet

>[!NOTE]
>Data Factory introduziu o novo conjunto de informações de formato parquet, consulte o artigo [formato parquet](format-parquet.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se quiser analisar os ficheiros Parquet ou escrever os dados em formato Parquet, defina a propriedade `format` `type` para **ParquetFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

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
- **Para usar OpenJDK**: tem suporte desde a versão de ir 3,13. Empacote o JVM. dll com todos os outros assemblies necessários de OpenJDK no computador IR auto-hospedado e defina a variável de ambiente do sistema JAVA_HOME de acordo.

>[!TIP]
>Se você copiar dados de/para o formato parquet usando o autohospedado Integration Runtime e erro de ocorrência que diz "ocorreu um erro ao invocar Java, mensagem: **Java. lang. OutOfMemoryError: espaço de heap Java**", você pode adicionar uma variável de ambiente `_JAVA_OPTIONS` no computador que hospeda o IR auto-hospedado para ajustar o tamanho de heap mínimo/máximo para a JVM para capacitar tal cópia, em seguida, execute novamente o pipeline.

![Definir o tamanho do heap JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: definir a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para um Máquina Virtual Java (JVM), enquanto `Xmx` especifica o pool de alocação de memória máximo. Isso significa que a JVM será iniciada com a quantidade `Xms` de memória e poderá usar uma quantidade máxima de `Xmx` de memória. Por padrão, o ADF usa mín. de 64MB e Max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Mapeamento de tipo de dados para arquivos parquet

| Tipo de dados provisório do data Factory | Tipo primitivo parquet | Parquet tipo original (desserializar) | Parquet tipo original (Serialize) |
|:--- |:--- |:--- |:--- |
| Booleano | Booleano | N/A | N/A |
| SByte | Int32 | Int8 | Int8 |
| Minuciosa | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/Binary | UInt64 | Vírgula |
| Único | Barra | N/A | N/A |
| Clique | Clique | N/A | N/A |
| Vírgula | binário | Vírgula | Vírgula |
| String | binário | UTF8 | UTF8 |
| DateTime | Int96 | N/A | N/A |
| Período | Int96 | N/A | N/A |
| DateTimeOffset | Int96 | N/A | N/A |
| ByteArray | binário | N/A | N/A |
| GUID | binário | UTF8 | UTF8 |
| º | binário | UTF8 | UTF8 |
| Matriz | Não suportado | N/A | N/A |

## <a name="orc-format"></a>Formato ORC

>[!NOTE]
>Data Factory introduziu o novo conjunto de informações de formato ORC, consulte o artigo [formato Orc](format-orc.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se quiser analisar os ficheiros ORC ou escrever os dados em formato ORC, defina a propriedade `format` `type` para **OrcFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

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
- **Para usar OpenJDK**: tem suporte desde a versão de ir 3,13. Empacote o JVM. dll com todos os outros assemblies necessários de OpenJDK no computador IR auto-hospedado e defina a variável de ambiente do sistema JAVA_HOME de acordo.

### <a name="data-type-mapping-for-orc-files"></a>Mapeamento de tipo de dados para arquivos ORC

| Tipo de dados provisório do data Factory | Tipos de ORC |
|:--- |:--- |
| Booleano | Booleano |
| SByte | Minuciosa |
| Minuciosa | Curto |
| Int16 | Curto |
| UInt16 | inteiro |
| Int32 | inteiro |
| UInt32 | Longo |
| Int64 | Longo |
| UInt64 | String |
| Único | Barra |
| Clique | Clique |
| Vírgula | Vírgula |
| String | String |
| DateTime | Carimbo de data/hora |
| DateTimeOffset | Carimbo de data/hora |
| Período | Carimbo de data/hora |
| ByteArray | binário |
| GUID | String |
| º | Char (1) |

## <a name="avro-format"></a>Formato AVRO

>[!NOTE]
>Data Factory introduziu o novo conjunto de informações de formato Avro, consulte o artigo [formato avri](format-avro.md) com detalhes. As configurações a seguir no conjunto de dados de armazenamento com base em arquivo ainda têm suporte no estado em que se encontram para compabitility para trás. Você é sugerido para usar o novo modelo no futuro.

Se quiser analisar os ficheiros Avro ou escrever os dados em formato Avro, defina a propriedade `format` `type` para **AvroFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "AvroFormat",
}
```

Para utilizar o formato Avro numa tabela do Hive, veja o [tutorial do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Tenha em atenção os seguintes pontos:

* Não há suporte para [tipos de dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) (registros, enumerações, matrizes, mapas, uniões e fixos).

## <a name="binary-format"></a>Formato binário

Consulte o artigo [formato binário](format-binary.md) em detalhes.

## <a name="compression-support"></a>Suporte à compactação

O Azure Data Factory dá suporte a compactar/descompactar dados durante a cópia. Quando você especifica `compression` Propriedade em um conjunto de dados de entrada, a atividade de cópia lê os dados compactados da origem e os descompactam; e quando você especifica a propriedade em um conjunto de dados de saída, a atividade de cópia é compactada e grava dados no coletor. Aqui estão alguns cenários de exemplo:

* Ler dados compactados GZIP de um blob do Azure, descompactá-los e gravar dados de resultado em um banco de dado SQL do Azure. Você define o conjunto de dados de blob do Azure de entrada com a propriedade `compression` `type` como GZIP.
* Ler dados de um arquivo de texto sem formatação do sistema de arquivos local, compactá-los usando o formato GZip e gravar os dados compactados em um blob do Azure. Você define um conjunto de resultados de blob do Azure com a propriedade `compression` `type` como GZip.
* Leia o arquivo. zip do servidor FTP, descompacte-o para obter os arquivos dentro e pouse esses arquivos em Azure Data Lake Store. Você define um conjunto de dados de FTP de entrada com a propriedade `compression` `type` como ZipDeflate.
* Ler dados compactados em GZIP de um blob do Azure, descompactá-los, compactá-los usando BZIP2 e gravar dados de resultado em um blob do Azure. Defina o conjunto de dados de blob do Azure com `compression` `type` definida como GZIP e o conjunto de dados de saída com `compression` `type` definido como BZIP2.

Para especificar a compactação de um conjunto de um, use a propriedade **Compression** no conjunto de um JSON como no exemplo a seguir:

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

A seção de **compactação** tem duas propriedades:

* **Tipo:** o codec de compactação, que pode ser **gzip**, **deflate**, **bzip2**ou **ZipDeflate**.
* **Nível:** a taxa de compactação, que pode ser **ideal** ou **mais rápida**.

  * **Mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.
  * **Ideal**: a operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída.

    Para obter mais informações, consulte o tópico [nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) .

> [!NOTE]
> As configurações de compactação não têm suporte para dados em **AvroFormat**, **OrcFormat**ou **ParquetFormat**. Ao ler arquivos nesses formatos, Data Factory detecta e usa o codec de compactação nos metadados. Ao gravar em arquivos nesses formatos, Data Factory escolhe o codec de compactação padrão para esse formato. Por exemplo, ZLIB para OrcFormat e ajuste para ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Tipos de arquivo e formatos de compactação sem suporte

Você pode usar os recursos de extensibilidade do Azure Data Factory para transformar arquivos que não têm suporte.
Duas opções incluem Azure Functions e tarefas personalizadas usando o lote do Azure.

Você pode ver um exemplo que usa uma função do Azure para [extrair o conteúdo de um arquivo tar](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Para obter mais informações, consulte [Azure Functions atividade](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Você também pode criar essa funcionalidade usando uma atividade dotnet personalizada. Mais informações estão disponíveis [aqui](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para armazenamentos de dados baseados em arquivo com suporte pelo Azure Data Factory:

- [Conector do armazenamento de BLOBs do Azure](connector-azure-blob-storage.md)
- [Conector de Azure Data Lake Store](connector-azure-data-lake-store.md)
- [Conector do Amazon S3](connector-amazon-simple-storage-service.md)
- [Conector do sistema de arquivos](connector-file-system.md)
- [Conector de FTP](connector-ftp.md)
- [Conector SFTP](connector-sftp.md)
- [Conector HDFS](connector-hdfs.md)
- [Conector HTTP](connector-http.md)
