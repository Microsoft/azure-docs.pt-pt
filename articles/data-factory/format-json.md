---
title: Formato JSON na Azure Data Factory
description: Este tópico descreve como lidar com o formato JSON na Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: f19f8fb3811435e9bbc207d2d130c0655a6dee02
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706086"
---
# <a name="json-format-in-azure-data-factory"></a>Formato JSON na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando quiser **analisar os ficheiros JSON ou escrever os dados no formato JSON**. 

O formato JSON é suportado para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados JSON.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade do tipo do conjunto de dados deve ser definida para **Json**. | Sim      |
| localização         | Definições de localização do(s) ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location` . **Consulte os detalhes na secção de propriedades do conector -> Dataset**. | Sim      |
| encodingName     | O tipo de codificação utilizado para ler/escrever ficheiros de teste. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254",.| Não       |
| compressão | Grupo de propriedades para configurar a compressão do ficheiro. Configure esta secção quando pretender fazer compressão/descompressão durante a execução da atividade. | Não |
| tipo<br/>*(em) `compression`* | O códice de compressão usado para ler/escrever ficheiros JSON. <br>Os valores permitidos são **bzip2**, **gzip,** **deflate,** **ZipDeflate,** **TarGzip,** **Tar,** **snappy,** ou **lz4**. O padrão não é comprimido.<br>**Nota** atualmente A atividade copy não suporta "snappy" & "lz4", e o fluxo de dados de mapeamento não suporta "ZipDeflate", "TarGzip" e "Tar".<br>**Nota** quando utilizar a atividade de cópia para **descomprimir os** / ficheiros **TarGzip TarGzip** /  e escrever para a loja de dados da pia baseada em ficheiros, por predefinição os ficheiros são extraídos para a pasta: , `<path specified in dataset>/<folder named as source compressed file>/` utilize na fonte de atividade de `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [cópia](#json-as-source) para controlar se deve preservar o nome dos ficheiros(s) comprimidos como estrutura de pasta.| N.º  |
| nível<br/>*(em) `compression`* | A relação de compressão. <br>Os valores permitidos são **ótimos** ou **mais rápidos.**<br>- **Mais rápido:** O funcionamento da compressão deve ser concluído o mais rapidamente possível, mesmo que o ficheiro resultante não seja perfeitamente comprimido.<br>- **Ótimo**: O funcionamento da compressão deve ser perfeitamente comprimido, mesmo que a operação leve mais tempo a ser concluída. Para mais informações, consulte o tópico [nível de compressão.](/dotnet/api/system.io.compression.compressionlevel) | Não       |

Abaixo está um exemplo do conjunto de dados JSON no Azure Blob Storage:

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
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte JSON e pia.

Saiba como extrair dados dos ficheiros JSON e mapear para afundar a loja/formato de dados ou vice-versa do [mapeamento](copy-activity-schema-and-type-mapping.md)de esquemas.

### <a name="json-as-source"></a>JSON como fonte

As seguintes propriedades são suportadas na secção ***\* de origem \**** da atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **JSONSource**. | Sim      |
| formatoStas | Um grupo de propriedades. Consulte a tabela de **definições de leitura JSON** abaixo. | Não       |
| lojaSs | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings` . **Consulte os detalhes na secção de propriedades de atividade do conector -> Copy**. | Não       |

Configurações de **leitura JSON** suportadas em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | O tipo de formatoStais devem ser definidos para **JsonReadSettings**. | Sim      |
| compressãoProperties | Um grupo de propriedades sobre como descomprimir dados para um determinado codec de compressão. | Não       |
| preservarZipFileNameAsFolder<br>*(em `compressionProperties` -> `type` `ZipDeflateReadSettings` conforme)*  | Aplica-se quando o conjunto de dados de entrada é configurado com compressão **ZipDeflate.** Indica se deve preservar o nome do ficheiro zip de origem como estrutura de pasta durante a cópia.<br>- Quando definido como **verdadeiro (predefinido)**, a Data Factory escreve ficheiros desapertados para `<path specified in dataset>/<folder named as source zip file>/` .<br>- Quando definidos como **falsos,** a Data Factory escreve ficheiros desapertados diretamente para `<path specified in dataset>` . Certifique-se de que não tem nomes de ficheiros duplicados em diferentes ficheiros zip de origem para evitar corridas ou comportamentos inesperados.  | Não |
| preservar CompressãoFileNameAsFolder<br>(em *`compressionProperties` -> `type` ou `TarGZipReadSettings` `TarReadSettings` menos)* | Aplica-se quando o conjunto de dados de entrada é configurado com a compressão  / **tarGzip.** Indica se deve preservar o nome do ficheiro comprimido de origem como estrutura de pasta durante a cópia.<br>- Quando definido como **verdadeiro (predefinido)**, a Data Factory escreve ficheiros descomprimidos para `<path specified in dataset>/<folder named as source compressed file>/` . <br>- Quando definido como **falso,** a Data Factory escreve ficheiros descomprimidos diretamente para `<path specified in dataset>` . Certifique-se de que não tem nomes de ficheiros duplicados em diferentes ficheiros de origem para evitar corridas ou comportamentos inesperados. | Não |

### <a name="json-as-sink"></a>JSON como pia

As seguintes propriedades são suportadas na secção de ***\* lavatório \**** de atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida como **JSONSink**. | Sim      |
| formatoStas | Um grupo de propriedades. Consulte a tabela de **definições de escrita JSON** abaixo. | Não       |
| lojaSs | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de escrita suportadas em `storeSettings` . **Consulte os detalhes na secção de propriedades de atividade do conector -> Copy**. | Não       |

Configurações de **escrita JSON** suportadas em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tipo          | O tipo de formatoStas devem ser definidas para **JsonWriteSettings**. | Sim                                                   |
| filePattern |Indica o padrão dos dados armazenados em cada ficheiro JSON. Os valores permitidos são: **setOfObjects (Linhas** JSON) e **arrayOfObjects**. O valor **predefinido** é **setOfObjects**. Veja a secção [Padrões de ficheiro JSON](#json-file-patterns) para obter detalhes sobre estes padrões. |Não |

### <a name="json-file-patterns"></a>Padrões de ficheiro JSON

Ao copiar dados de ficheiros JSON, a atividade de cópia pode detetar e analisar automaticamente os seguintes padrões de ficheiros JSON. Ao escrever dados para ficheiros JSON, pode configurar o padrão de ficheiro na pia da atividade da cópia.

- **Tipo I: setOfObjects**

    Cada ficheiro contém um único objeto, linhas JSON ou objetos concatenados.

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

    * **Linhas JSON (predefinição para pia)**

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

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

No mapeamento dos fluxos de dados, pode ler e escrever para o formato JSON nas seguintes lojas de dados: [Azure Blob Storage,](connector-azure-blob-storage.md#mapping-data-flow-properties) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties), e [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte json. Pode editar estas propriedades no separador **Opções Fonte.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Caminhos de wild card | Todos os ficheiros correspondentes ao caminho wildcard serão processados. Substitui a pasta e o caminho do ficheiro definido no conjunto de dados. | não | Corda[] | wildcardPaths |
| Caminho da raiz da partição | Para os dados de ficheiros que são divididos, pode introduzir um caminho de raiz de partição para ler pastas partidas como colunas | não | String | partitionRootPath |
| Lista de ficheiros | Se a sua fonte está a apontar para um ficheiro de texto que lista ficheiros para processar | não | `true` ou `false` | fileList |
| Coluna para armazenar nome de ficheiro | Criar uma nova coluna com o nome e caminho do ficheiro de origem | não | String | rowUrlColumn |
| Após a conclusão | Elimine ou mova os ficheiros após o processamento. O caminho do arquivo começa a partir da raiz do recipiente | não | Excluir: `true` ou `false` <br> Mover-se: `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtrar por última modificação | Opte por filtrar ficheiros com base na última alteração que foram alterados | não | CarimboDeDataEHora | modificado Depois <br> modificadoSForo antes |
| Documento único | Fluxos de dados de mapeamento lêem um documento JSON de cada ficheiro | não | `true` ou `false` | únicoDocumento |
| Nomes de colunas não citados | Se **forem selecionados nomes de colunas não citados,** os fluxos de dados de mapeamento lêem colunas JSON que não estão rodeadas de citações. | não | `true` ou `false` |  não citadoColumnAmes |
| Tem comentários | Selecione Tem comentários se os **dados** JSON têm comentários de estilo C ou C++ | não | `true` ou `false` | asCommentos |
| Único citado | Lê colunas JSON que não estão rodeadas de citações | não | `true` ou `false` | singleQuoted |
| Backslash escapou | Selecione **Backslash escapou** se as costas forem usadas para escapar de caracteres nos dados do JSON | não | `true` ou `false` | backslashEscape |
| Não permita que não encontrem ficheiros | Se for verdade, um erro não é jogado se nenhum ficheiro for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="source-format-options"></a>Opções de formato de origem

A utilização de um conjunto de dados JSON como fonte no fluxo de dados permite-lhe definir cinco definições adicionais. Estas definições podem ser encontradas no acordeão de **definições JSON** no separador **Opções de Origem.** Para a definição **do Formulário de Documento,** pode selecionar um dos tipos de **documento único,** **documento por linha** e matriz **de documentos.**

![Definições JSON](media/data-flow/json-settings.png "Definições de JSON")

#### <a name="default"></a>Predefinição

Por predefinição, os dados JSON são lidos no seguinte formato.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Documento único

Se **o documento único** for selecionado, os fluxos de dados de mapeamento leiam um documento JSON de cada ficheiro. 

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
> [!NOTE]
> Se os fluxos de dados lançarem um erro indicando "corrupt_record" ao visualizar os seus dados JSON, é provável que os seus dados contenham um único documento no seu ficheiro JSON. A definição de "documento único" deve limpar esse erro.

#### <a name="unquoted-column-names"></a>Nomes de colunas não citados

Se **forem selecionados nomes de colunas não citados,** os fluxos de dados de mapeamento lêem colunas JSON que não estão rodeadas de citações. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Tem comentários

Selecione Tem comentários se os **dados** JSON tecem o estilo C ou C++ comentando.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Único citado

Selecione **Single citado** se os campos e valores JSON usarem cotações únicas em vez de cotações duplas.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Backslash escapou

Selecione **Backslash escapou** se as costas forem usadas para escapar de caracteres nos dados JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>Propriedades de Sink

A tabela abaixo lista as propriedades suportadas por um lavatório json. Pode editar estas propriedades no **separador Definições.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Limpe a pasta | Se a pasta de destino for apurada antes de escrever | não | `true` ou `false` | truncato |
| Opção de nome de ficheiro | O formato de nomeação dos dados escritos. Por predefinição, um ficheiro por partição em formato `part-#####-tid-<guid>` | não | Padrão: Corda <br> Por partição: String[] <br> Como dados na coluna: String <br> Saída para um único ficheiro: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>Criar estruturas JSON numa coluna derivada

Pode adicionar uma coluna complexa ao fluxo de dados através do construtor de expressão de colunas derivada. Na transformação da coluna derivada, adicione uma nova coluna e abra o construtor de expressão clicando na caixa azul. Para fazer um complexo de colunas, pode introduzir a estrutura JSON manualmente ou utilizar o UX para adicionar subcolumns interativamente.

#### <a name="using-the-expression-builder-ux"></a>Usando o construtor de expressão UX

No painel lateral do esquema de saída, paire sobre uma coluna e clique no ícone mais. **Selecione Adicione subcolumn** para tornar a coluna um tipo complexo.

![Adicionar subcolumn](media/data-flow/derive-add-subcolumn.png "Adicionar Subcolumn")

Pode adicionar colunas e subcolumns adicionais da mesma forma. Para cada campo não complexo, uma expressão pode ser adicionada no editor de expressão à direita.

![Adicionar coluna complexa](media/data-flow/derive-complex-column.png "Adicionar colunas")

#### <a name="entering-the-json-structure-manually"></a>Entrando na estrutura JSON manualmente

Para adicionar manualmente uma estrutura JSON, adicione uma nova coluna e introduza a expressão no editor. A expressão segue o seguinte formato geral:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Se esta expressão fosse inserida para uma coluna chamada "complexColumn", então seria escrita para a pia como o seguinte JSON:

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Roteiro manual da amostra para definição completa hierárquica
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

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
