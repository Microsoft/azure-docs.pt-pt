---
title: Formatos de arquivo e compactação no Azure Data Factory
description: Saiba mais sobre os formatos de arquivo com suporte pelo Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 82d69c739e56a344036e8b91cacdd3e955a4c1d6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665865"
---
# <a name="file-and-compression-formats-supported-by-azure-data-factory"></a>Formatos de arquivo e compactação com suporte pelo Azure Data Factory
*Este tópico aplica-se aos seguintes conectores: [Amazon S3](data-factory-amazon-simple-storage-service-connector.md), [blob do Azure](data-factory-azure-blob-connector.md), [Azure data Lake Store](data-factory-azure-datalake-connector.md), [sistema de arquivos](data-factory-onprem-file-system-connector.md), [FTP](data-factory-ftp-connector.md), [HDFS](data-factory-hdfs-connector.md), [http](data-factory-http-connector.md)e [SFTP](data-factory-sftp-connector.md).*

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [formatos de arquivo com suporte e codecs de compactação no data Factory](../supported-file-formats-and-compression-codecs.md).

O Azure Data Factory dá suporte aos seguintes tipos de formato de arquivo:

* [Formato de texto](#text-format)
* [Formato JSON](#json-format)
* [Formato Avro](#avro-format)
* [Formato ORC](#orc-format)
* [Formato parquet](#parquet-format)

## <a name="text-format"></a>Formato de texto
Se você quiser ler um arquivo de texto ou gravar em um arquivo de texto, defina a propriedade `type` na seção `format` do conjunto de propriedades para **TextFormat**. Também pode especificar as seguintes propriedades **opcionais** na secção `format`. Veja a secção [Exemplo de TextFormat](#textformat-example) sobre como configurar.

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| columnDelimiter |O caráter utilizado para separar colunas num ficheiro. Você pode considerar o uso de um caractere incomum não imprimível que talvez provavelmente não exista em seus dados. Por exemplo, especifique "\u0001", que representa o início do título (SOH). |Só é permitido um caráter. O valor **predefinido** é a **vírgula (“,”)** . <br/><br/>Para usar um caractere Unicode, consulte [caracteres Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obter o código correspondente. |Não |
| rowDelimiter |O caráter utilizado para separar linhas num ficheiro. |Só é permitido um caráter. O valor **predefinido** é um dos seguintes valores: **["\r\n", "\r", "\n"]** na leitura e **"\r\n"** na escrita. |Não |
| escapeChar |O caráter especial utilizado para escapar a um delimitador de colunas no conteúdo do ficheiro de entrada. <br/><br/>Não pode especificar simultaneamente o escapeChar e o quoteChar para uma tabela. |Só é permitido um caráter. Não existem valores predefinidos. <br/><br/>Exemplo: se utilizar a vírgula (“,”) como delimitador de colunas, mas quiser ter o caráter de vírgula no texto (exemplo: "Olá, mundo"), pode definir “$” como caráter de escape e utilizar a cadeia "Olá$, mundo" na origem. |Não |
| quoteChar |O caráter utilizado para colocar um valor de cadeia entre aspas. Os delimitadores de colunas e linhas dentro dos carateres de aspas são tratados como parte do valor de cadeia. Esta propriedade é aplicável a conjuntos de dados de entrada e de saída.<br/><br/>Não pode especificar simultaneamente o escapeChar e o quoteChar para uma tabela. |Só é permitido um caráter. Não existem valores predefinidos. <br/><br/>Exemplo: se utilizar a vírgula (“,”) como delimitador de colunas, mas quiser ter o caráter de vírgula no texto (exemplo: <Olá, mundo>), pode definir " (aspas duplas) como caráter de aspas e utilizar a cadeia "Olá, mundo" na origem. |Não |
| nullValue |Um ou mais carateres utilizados para representar um valor nulo. |Um ou mais carateres. Os valores **predefinidos** são **"\N" e "NULL"** na leitura e **"\N"** na escrita. |Não |
| encodingName |Especifique o nome de codificação. |Um nome de codificação válido. Veja [Encoding.EncodingName Property](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemplo: windows-1250 ou shift_jis. O valor **predefinido** é **UTF-8**. |Não |
| firstRowAsHeader |Especifica se a primeira linha é considerada um cabeçalho. Num conjunto de dados de entrada, o Data Factory lê a primeira linha como cabeçalho. Num conjunto de dados de saída, o Data Factory escreve a primeira linha como cabeçalho. <br/><br/>Veja [Cenários para utilizar `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) em cenários de exemplo. |Verdadeiro<br/><b>Falso (predefinição)</b> |Não |
| skipLineCount |Indica o número de linhas a ignorar durante a leitura de dados a partir dos ficheiros de entrada. Se as propriedades skipLineCount e firstRowAsHeader forem especificadas simultaneamente, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho são lidas a partir do ficheiro de entrada. <br/><br/>Veja [Cenários para utilizar `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) em cenários de exemplo. |Número inteiro |Não |
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
Para **importar/exportar um arquivo JSON no estado em/de Azure Cosmos DB**, consulte a seção [importar/exportar documentos JSON](data-factory-azure-documentdb-connector.md#importexport-json-documents) no artigo [mover dados de/para Azure Cosmos DB](data-factory-azure-documentdb-connector.md) .

Se você quiser analisar os arquivos JSON ou gravar os dados no formato JSON, defina a propriedade `type` na seção `format` como **JsonFormat**. Também pode especificar as seguintes propriedades **opcionais** na secção `format`. Veja a secção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| filePattern |Indica o padrão dos dados armazenados em cada ficheiro JSON. Os valores permitidos são **setOfObjects** e **arrayOfObjects**. O valor **predefinido** é **setOfObjects**. Veja a secção [Padrões de ficheiro JSON](#json-file-patterns) para obter detalhes sobre estes padrões. |Não |
| jsonNodeReference | Se quiser iterar e extrair dados dos objetos dentro de um campo de matriz com o mesmo padrão, especifique o caminho JSON dessa matriz. Esta propriedade só é suportada quando se copiam dados a partir de ficheiros JSON. | Não |
| jsonPathDefinition | Especifique a expressão de caminho do JSON para cada mapeamento de colunas com um nome de coluna personalizado (começar com letra minúscula). Esta propriedade só é suportada quando se copiam dados a partir de ficheiros JSON, sendo que pode extrair dados de objetos ou matrizes. <br/><br/> Para os campos no objeto raiz, comece com a raiz $; para os campos dentro da matriz escolhida pela propriedade `jsonNodeReference`, comece a partir do elemento de matriz. Veja a secção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar. | Não |
| encodingName |Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, veja: Propriedade [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemplo: windows-1250 ou shift_jis. O valor **predefinido** é **UTF-8**. |Não |
| nestingSeparator |Caráter utilizado para separar níveis de aninhamento. O valor predefinido é “.” (ponto). |Não |

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

Consulte os dois exemplos a seguir ao copiar dados de arquivos JSON. Os pontos genéricos a serem observados:

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

- A secção `structure` define os nomes de colunas personalizados e o tipo de dados correspondente enquanto converte para dados tabulares. Esta secção é **opcional**, exceto se precisar de fazer o mapeamento de colunas. Consulte [mapear colunas do DataSet de origem para a seção colunas do conjunto](data-factory-map-columns.md) de informações de destino para obter mais detalhes.
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Para copiar dados da matriz, você pode usar a **matriz [x]. Property** para extrair o valor da propriedade fornecida do objeto xth ou você pode usar **array [*]. Property** para localizar o valor de qualquer objeto que contenha tal propriedade.

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

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

O conjunto de dados de entrada com o tipo **JsonFormat** é definido da seguinte forma: (definição parcial com apenas as partes relevantes). Mais especificamente:

- A secção `structure` define os nomes de colunas personalizados e o tipo de dados correspondente enquanto converte para dados tabulares. Esta secção é **opcional**, exceto se precisar de fazer o mapeamento de colunas. Consulte [mapear colunas do DataSet de origem para a seção colunas do conjunto](data-factory-map-columns.md) de informações de destino para obter mais detalhes.
- `jsonNodeReference` indica a iteração e extração de dados a partir dos objetos com o mesmo padrão em orderlines **matriz**.
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Neste exemplo, "ordernumber", "orderdate" e "city" estão no objeto raiz com o caminho JSON a começar com "$.", enquanto "order_pd" e "order_price" estão definidos com o caminho derivado do elemento de matriz sem "$.".

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

## <a name="avro-format"></a>Formato AVRO
Se quiser analisar os ficheiros Avro ou escrever os dados em formato Avro, defina a propriedade `format` `type` como **AvroFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "AvroFormat",
}
```

Para utilizar o formato Avro numa tabela do Hive, veja o [tutorial do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Tenha em atenção os seguintes pontos:  

* Não há suporte para [tipos de dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) (registros, enumerações, matrizes, mapas, uniões e fixos).

## <a name="orc-format"></a>Formato ORC
Se quiser analisar os ficheiros ORC ou escrever os dados em formato ORC, defina a propriedade `format` `type` como **OrcFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Se não estiver a copiar ficheiros ORC **como estão** entre arquivos de dados no local e na cloud, tem de instalar o JRE 8 (Java Runtime Environment) no seu computador de gateway. Um gateway de 64 bits requer um JRE de 64 bits e um gateway de 32 bits requer um JRE de 32 bits. Pode encontrar ambas as versões [aqui](https://go.microsoft.com/fwlink/?LinkId=808605). Escolha a versão adequada.
>
>

Tenha em atenção os seguintes pontos:

* Os tipos de dados complexos não são suportados (ESTRUTURA, MAPA, LISTA, UNIÃO)
* O ficheiro ORC tem três [opções relacionadas com a compressão](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NENHUM, ZLIB, SNAPPY. O Data Factory suporta a leitura de dados a partir de um ficheiro ORC em qualquer um destes formatos de compressão. Utiliza o codec de compressão existente nos metadados para ler os dados. No entanto, ao escrever num ficheiro ORC, o Data Factory escolhe a opção ZLIB, que é a predefinição para ORC. De momento, não existem opções para contornar este comportamento.

## <a name="parquet-format"></a>Formato Parquet
Se quiser analisar os ficheiros Parquet ou escrever os dados em formato Parquet, defina a propriedade `format` `type` como **ParquetFormat**. Não precisa de especificar quaisquer propriedades na secção Formato no âmbito da secção typeProperties. Exemplo:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Se não estiver a copiar ficheiros Parquet **como estão** entre arquivos de dados no local e na cloud, tem de instalar o JRE 8 (Java Runtime Environment) no seu computador de gateway. Um gateway de 64 bits requer um JRE de 64 bits e um gateway de 32 bits requer um JRE de 32 bits. Pode encontrar ambas as versões [aqui](https://go.microsoft.com/fwlink/?LinkId=808605). Escolha a versão adequada.
>
>

Tenha em atenção os seguintes pontos:

* Os tipos de dados complexos não são suportados (MAPA, LISTA)
* O ficheiro Parquet tem as seguintes opções relacionadas com a compressão: NENHUM, SNAPPY, GZIP e LZO. O Data Factory suporta a leitura de dados a partir de um ficheiro ORC em qualquer um destes formatos de compressão. Utiliza o codec de compressão existente nos metadados para ler os dados. No entanto, ao escrever num ficheiro Parquet, o Data Factory escolhe a opção SNAPPY, que é a predefinição para o formato Parquet. De momento, não existem opções para contornar este comportamento.

## <a name="compression-support"></a>Suporte à compactação
O processamento de grandes conjuntos de dados pode causar gargalos de e/s e de rede. Portanto, os dados compactados em armazenamentos podem não apenas acelerar a transferência de dados pela rede e economizar espaço em disco, mas também proporcionar melhorias significativas de desempenho no processamento de Big Data. Atualmente, há suporte para a compactação em armazenamentos de dados baseados em arquivo, como o blob do Azure ou o sistema de arquivos local.  

Para especificar a compactação de um conjunto de um, use a propriedade **Compression** no conjunto de um JSON como no exemplo a seguir:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```

Suponha que o conjunto de dados de exemplo seja usado como a saída de uma atividade de cópia, a atividade de cópia compacta o codec de saída usando a taxa ideal e, em seguida, grava os dados compactados em um arquivo chamado pagecounts. csv. gz no armazenamento de BLOBs do Azure.

> [!NOTE]
> As configurações de compactação não têm suporte para dados em **AvroFormat**, **OrcFormat**ou **ParquetFormat**. Ao ler arquivos nesses formatos, Data Factory detecta e usa o codec de compactação nos metadados. Ao gravar em arquivos nesses formatos, Data Factory escolhe o codec de compactação padrão para esse formato. Por exemplo, ZLIB para OrcFormat e ajuste para ParquetFormat.   

A seção de **compactação** tem duas propriedades:  

* **Tipo:** o codec de compactação, que pode ser **gzip**, **deflate**, **bzip2**ou **ZipDeflate**.  
* **Nível:** a taxa de compactação, que pode ser **ideal** ou **mais rápida**.

  * **Mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.
  * **Ideal**: a operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída.

    Para obter mais informações, consulte o tópico [nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) .

Quando você especifica `compression` Propriedade em um conjunto de dados de entrada JSON, o pipeline pode ler dados compactados da origem; e quando você especifica a propriedade em um JSON de conjunto de dados de saída, a atividade de cópia pode gravar dados compactados no destino. Aqui estão alguns cenários de exemplo:

* Ler dados compactados GZIP de um blob do Azure, descompactá-los e gravar dados de resultado em um banco de dado SQL do Azure. Você define o conjunto de dados de blob do Azure de entrada com a propriedade JSON de `compression` `type` como GZIP.
* Ler dados de um arquivo de texto sem formatação do sistema de arquivos local, compactá-los usando o formato GZip e gravar os dados compactados em um blob do Azure. Você define um conjunto de resultados de blob do Azure com o `compression` `type` Propriedade JSON como GZip.
* Leia o arquivo. zip do servidor FTP, descompacte-o para obter os arquivos dentro e pouse esses arquivos em Azure Data Lake Store. Você define um conjunto de dados de FTP de entrada com o `compression` `type` Propriedade JSON como ZipDeflate.
* Ler dados compactados em GZIP de um blob do Azure, descompactá-los, compactá-los usando BZIP2 e gravar dados de resultado em um blob do Azure. Você define o conjunto de dados de blob do Azure de entrada com `compression` `type` definido como GZIP e o DataSet de saída com `compression` `type` definido como BZIP2 nesse caso.   


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para armazenamentos de dados baseados em arquivo com suporte pelo Azure Data Factory:

- [Armazenamento de Blobs do Azure](data-factory-azure-blob-connector.md)
- [Azure Data Lake Store](data-factory-azure-datalake-connector.md)
- [FTP](data-factory-ftp-connector.md)
- [HDFS](data-factory-hdfs-connector.md)
- [Sistema de Ficheiros](data-factory-onprem-file-system-connector.md)
- [Amazon S3](data-factory-amazon-simple-storage-service-connector.md)
