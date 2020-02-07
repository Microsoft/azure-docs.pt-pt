---
title: Formato JSON no Azure Data Factory
description: Este tópico descreve como lidar com o formato JSON no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049853"
---
# <a name="json-format-in-azure-data-factory"></a>Formato JSON no Azure Data Factory

Siga este artigo quando pretender **analisar os ficheiros JSON ou escrever os dados no formato JSON**. 

O formato JSON é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta seção fornece uma lista das propriedades com suporte pelo DataSet JSON.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade tipo do conjunto de dados deve ser definida para **Json**. | Sim      |
| localização         | Configurações de local dos arquivos. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location`. **Consulte detalhes no artigo do conector -> Secção**de propriedades do conjunto de dados . | Sim      |
| encodingName     | O tipo de codificação usado para ler/gravar arquivos de teste. <br>Os valores permitidos são os seguintes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".| Não       |
| compressionCodec | O codec de compactação usado para ler/gravar arquivos de texto. <br>Os valores permitidos são **bzip2,** **gzip,** **desinsuflação,** **ZipDeflate,** **snappy,** ou **lz4**. para usar ao salvar o arquivo. <br>Observação a atividade de cópia atualmente não dá suporte a "encaixar" & "lz4".<br>Note ao utilizar a atividade de cópia para descomprimir ficheiros ZipDeflate e escrever para uma loja de dados de sumidouro baseada em ficheiros, os ficheiros serão extraídos para a pasta: `<path specified in dataset>/<folder named as source zip file>/`. | Não       |
| compressionLevel | A taxa de compactação. <br>Os valores permitidos são **Ideais** ou **Mais Rápidos.**<br>- **Mais rápido:** A operação de compressão deve estar concluída o mais rapidamente possível, mesmo que o ficheiro resultante não esteja devidamente comprimido.<br>- **Optimal**: O funcionamento da compressão deve ser optimizadamente comprimido, mesmo que a operação leve mais tempo a concluir. Para mais informações, consulte o tópico [do Nível de Compressão.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Não       |

Veja abaixo um exemplo de conjunto de um DataSet no armazenamento de BLOBs do Azure:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor JSON.

### <a name="json-as-source"></a>JSON como fonte

As seguintes propriedades são suportadas na atividade de cópia ***\*secção\*fonte.***

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **JSONSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings`. **Consulte os detalhes no artigo do conector -> Secção de propriedades**de atividadede cópia . | Não       |

### <a name="json-as-sink"></a>JSON como coletor

As seguintes propriedades são suportadas na atividade de cópia ***\*pia\**** secção.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **JSONSink**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a tabela de definições de **escrita JSON** abaixo. | Não       |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em ficheiros tem as suas próprias definições de escrita suportadas em `storeSettings`. **Consulte os detalhes no artigo do conector -> Secção de propriedades**de atividadede cópia . | Não       |

Definições de **escrita JSON** suportadas em `formatSettings`:

| Propriedade      | Descrição                                                  | Necessário                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tipo          | O tipo de formatoDefinições devem ser definidas para **JsonWriteSettings**. | Sim                                                   |
| filePattern |Indica o padrão dos dados armazenados em cada ficheiro JSON. Os valores permitidos são **setOfObjects** e **arrayOfObjects**. O valor **predefinido** é **setOfObjects**. Veja a secção [Padrões de ficheiro JSON](#json-file-patterns) para obter detalhes sobre estes padrões. |Não |

### <a name="json-file-patterns"></a>Padrões de ficheiro JSON

A atividade de cópia pode detectar e analisar automaticamente os padrões de arquivos JSON a seguir. 

- **Tipo I: setOfObjects**

    Cada ficheiro contém um único objeto ou múltiplos objetos delimitados por linha/concatenados. 
    Quando essa opção é escolhida no coletor da atividade de cópia, a atividade de cópia produz um único arquivo JSON com cada objeto por linha (delimitado por linha).

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

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Os tipos de ficheiros JSON podem ser usados como um lavatório e uma fonte no fluxo de dados de mapeamento.

### <a name="creating-json-structures-in-a-derived-column"></a>Criar estruturas JSON numa coluna derivada

Pode adicionar uma coluna complexa ao fluxo de dados através do construtor de expressão de coluna derivada. Na transformação da coluna derivada, adicione uma nova coluna e abra o construtor de expressão clicando na caixa azul. Para tornar um complexo de colunas, pode introduzir manualmente a estrutura JSON ou utilizar o UX para adicionar subcolunas interativamente.

#### <a name="using-the-expression-builder-ux"></a>Usando o construtor de expressão UX

No painel lateral do esquema de saída, paire sobre uma coluna e clique no ícone plus. **Selecione Adicionar subcoluna** para tornar a coluna um tipo complexo.

![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar Subcoluna")

Pode adicionar colunas e subcolunas adicionais da mesma forma. Para cada campo não complexo, uma expressão pode ser adicionada no editor de expressão à direita.

![Coluna complexa](media/data-flow/complexcolumn.png "Coluna complexa")

#### <a name="entering-the-json-structure-manually"></a>Entrando manualmente na estrutura jSON

Para adicionar manualmente uma estrutura JSON, adicione uma nova coluna e introduza a expressão no editor. A expressão segue o seguinte formato geral:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Se esta expressão fosse inserida para uma coluna chamada "complexColumn", então seria escrita para a pia como a seguinte JSON:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Script manual de amostra para definição hierárquica completa
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>Opções de formato de origem

A utilização de um conjunto de dados JSON como fonte no fluxo de dados permite-lhe definir cinco definições adicionais. Estas definições podem ser encontradas sob o acordeão de **definições JSON** no separador **Opções de Origem.**  

![Definições JSON](media/data-flow/json-settings.png "Definições JSON")

#### <a name="default"></a>Predefinição

Por predefinição, os dados da JSON são lidos no seguinte formato.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Documento único

Se o **documento único** for selecionado, os fluxos de dados de mapeamento lêem um documento JSON de cada ficheiro. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

#### <a name="unquoted-column-names"></a>Nomes de colunas não citados

Se os **nomes de colunas não citados** forem selecionados, os fluxos de dados de mapeamento lêem colunas JSON que não estão rodeadas por citações. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Tem comentários

Select **Tem comentários** se os dados jSON têm comentários c ou C++ estilo.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Único citado

Selecione **Single quoteed** se os campos e valores JSON utilizarem cotações únicas em vez de cotações duplas.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Backslash escapou

Selecione **Single quoted** se as pestanas são usadas para escapar de caracteres nos dados jSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeando o fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
