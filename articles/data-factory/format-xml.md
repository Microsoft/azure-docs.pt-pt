---
title: Formato XML na Azure Data Factory
description: Este tópico descreve como lidar com o formato XML na Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: a4082e1e00ecb1d3b0157fcc35616704eaab0aae
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386361"
---
# <a name="xml-format-in-azure-data-factory"></a>Formato XML na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando quiser **analisar os ficheiros XML**. 

O formato XML é suportado para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md). É suportado como fonte, mas não afundado.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados XML.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade do tipo do conjunto de dados deve ser definida como **Xml**. | Yes      |
| localização         | Definições de localização do(s) ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location` . **Consulte os detalhes na secção de propriedades do conector -> Dataset**. | Yes      |
| encodingName     | O tipo de codificação utilizado para ler/escrever ficheiros de teste. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254",.| No       |
| nullValue | Especifica a representação de cadeia de valor nulo.<br/>O valor predefinido é **a corda vazia.** | No |
| compressão | Grupo de propriedades para configurar a compressão do ficheiro. Configure esta secção quando pretender fazer compressão/descompressão durante a execução da atividade. | No |
| tipo<br>*(em) `compression`* | O códice de compressão usado para ler/escrever ficheiros XML. <br>Os valores permitidos são **bzip2**, **gzip,** **deflate,** **ZipDeflate,** **TarGzip,** **Tar,** **snappy,** ou **lz4**. O padrão não é comprimido.<br>**Nota** atualmente A atividade copy não suporta "snappy" & "lz4", e o fluxo de dados de mapeamento não suporta "ZipDeflate", "TarGzip" e "Tar".<br>**Nota** quando utilizar a atividade de cópia para **descomprimir os** / ficheiros **TarGzip TarGzip** /  e escrever para a loja de dados da pia baseada em ficheiros, por predefinição os ficheiros são extraídos para a pasta: , `<path specified in dataset>/<folder named as source compressed file>/` utilize na fonte de atividade de `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [cópia](#xml-as-source) para controlar se deve preservar o nome dos ficheiros(s) comprimidos como estrutura de pasta. | N.º  |
| nível<br/>*(em) `compression`* | A relação de compressão. <br>Os valores permitidos são **ótimos** ou **mais rápidos.**<br>- **Mais rápido:** O funcionamento da compressão deve ser concluído o mais rapidamente possível, mesmo que o ficheiro resultante não seja perfeitamente comprimido.<br>- **Ótimo**: O funcionamento da compressão deve ser perfeitamente comprimido, mesmo que a operação leve mais tempo a ser concluída. Para mais informações, consulte o tópico [nível de compressão.](/dotnet/api/system.io.compression.compressionlevel) | No       |

Abaixo está um exemplo do conjunto de dados XML no Azure Blob Storage:

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte XML.

Saiba como mapear dados XML e afundar a loja/formato de dados a partir do [mapeamento](copy-activity-schema-and-type-mapping.md)de esquemas. Ao pré-visualizar ficheiros XML, os dados são mostrados com a hierarquia JSON e utiliza o caminho JSON para apontar para os campos.

### <a name="xml-as-source"></a>XML como fonte

As seguintes propriedades são suportadas na secção ***\* de origem \**** da atividade de cópia. Saiba mais sobre o comportamento do [conector XML.](#xml-connector-behavior)

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **XmlSource**. | Yes      |
| formatoStas | Um grupo de propriedades. Consulte a tabela de **definições de leitura XML** abaixo. | No       |
| lojaSs | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings` . **Consulte os detalhes na secção de propriedades de atividade do conector -> Copy**. | No       |

Definições de **leitura de XML** suportadas em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | O tipo de formatoStais devem ser definidos para **XmlReadSettings**. | Yes      |
| validaçãoMode | Especifica se valida o esquema XML.<br>Os valores permitidos não são **nenhum** (predefinição, sem validação), **xsd** (validar usando XSD), **dtd** (validar usando DTD). | No |
| espaços de nome | Se ativar o espaço de nome ao analisar os ficheiros XML. Os valores permitidos são: **verdadeiros** (padrão), **falsos.** | No |
| namespacePrefixes | Namespace URI para pré-fixar o mapeamento, que é usado para nomear campos ao analisar o ficheiro xml.<br/>Se um ficheiro XML tiver espaço de nome e o espaço de nome estiver ativado, por padrão, o nome de campo é o mesmo que está no documento XML.<br>Se houver um item definido para o namespace URI neste mapa, o nome de campo é `prefix:fieldName` . | No |
| detectarDataType | Se detetar números inteiros, duplos e booleanos. Os valores permitidos são: **verdadeiros** (padrão), **falsos.**| No |
| compressãoProperties | Um grupo de propriedades sobre como descomprimir dados para um determinado codec de compressão. | No       |
| preservarZipFileNameAsFolder<br>*(em `compressionProperties` -> `type` `ZipDeflateReadSettings` conforme)*  | Aplica-se quando o conjunto de dados de entrada é configurado com compressão **ZipDeflate.** Indica se deve preservar o nome do ficheiro zip de origem como estrutura de pasta durante a cópia.<br>- Quando definido como **verdadeiro (predefinido)**, a Data Factory escreve ficheiros desapertados para `<path specified in dataset>/<folder named as source zip file>/` .<br>- Quando definidos como **falsos,** a Data Factory escreve ficheiros desapertados diretamente para `<path specified in dataset>` . Certifique-se de que não tem nomes de ficheiros duplicados em diferentes ficheiros zip de origem para evitar corridas ou comportamentos inesperados.  | No |
| preservar CompressãoFileNameAsFolder<br>(em *`compressionProperties` -> `type` ou `TarGZipReadSettings` `TarReadSettings` menos)* | Aplica-se quando o conjunto de dados de entrada é configurado com a compressão  / **tarGzip.** Indica se deve preservar o nome do ficheiro comprimido de origem como estrutura de pasta durante a cópia.<br>- Quando definido como **verdadeiro (predefinido)**, a Data Factory escreve ficheiros descomprimidos para `<path specified in dataset>/<folder named as source compressed file>/` . <br>- Quando definido como **falso,** a Data Factory escreve ficheiros descomprimidos diretamente para `<path specified in dataset>` . Certifique-se de que não tem nomes de ficheiros duplicados em diferentes ficheiros de origem para evitar corridas ou comportamentos inesperados. | No |

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

No mapeamento dos fluxos de dados, pode ler e escrever para o formato XML nas seguintes lojas de dados: [Azure Blob Storage,](connector-azure-blob-storage.md#mapping-data-flow-properties) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Pode apontar para ficheiros XML utilizando conjunto de dados XML ou utilizando um [conjunto de dados inline](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte XML. Pode editar estas propriedades no separador **Opções Fonte.** Saiba mais sobre o comportamento do [conector XML.](#xml-connector-behavior) Ao utilizar o conjunto de dados inline, verá definições de ficheiros adicionais, que são as mesmas que as propriedades descritas na secção [de propriedades do conjunto de dados.](#dataset-properties) 

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Caminhos de wild card | Todos os ficheiros correspondentes ao caminho wildcard serão processados. Substitui a pasta e o caminho do ficheiro definido no conjunto de dados. | No | Corda[] | wildcardPaths |
| Caminho da raiz da partição | Para os dados de ficheiros que são divididos, pode introduzir um caminho de raiz de partição para ler pastas partidas como colunas | Não | String | partitionRootPath |
| Lista de ficheiros | Se a sua fonte está a apontar para um ficheiro de texto que lista ficheiros para processar | No | `true` ou `false` | fileList |
| Coluna para armazenar nome de ficheiro | Criar uma nova coluna com o nome e caminho do ficheiro de origem | Não | String | rowUrlColumn |
| Após a conclusão | Elimine ou mova os ficheiros após o processamento. O caminho do arquivo começa a partir da raiz do recipiente | No | Excluir: `true` ou `false` <br> Mover-se: `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| Filtrar por última modificação | Opte por filtrar ficheiros com base na última alteração que foram alterados | No | CarimboDeDataEHora | modificado Depois <br>modificadoSForo antes |
| Modo de validação | Especifica se valida o esquema XML. | No | `None` (predefinição, sem validação)<br>`xsd` (validar a utilização do XSD)<br>`dtd` (validar usando DTD). | validaçãoMode |
| Espaços de nomes | Se ativar o espaço de nome ao analisar os ficheiros XML. | No | `true` (predefinido) ou `false` | espaços de nome |
| Pares de prefixos do espaço de nome | Namespace URI para pré-fixar o mapeamento, que é usado para nomear campos ao analisar o ficheiro xml.<br/>Se um ficheiro XML tiver espaço de nome e o espaço de nome estiver ativado, por padrão, o nome de campo é o mesmo que está no documento XML.<br>Se houver um item definido para o namespace URI neste mapa, o nome de campo é `prefix:fieldName` . | No | Matriz com padrão`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| Não permita que não encontrem ficheiros | Se for verdade, um erro não é jogado se nenhum ficheiro for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>Exemplo de script de origem XML

O script abaixo é um exemplo de uma configuração de origem XML no mapeamento de fluxos de dados usando o modo dataset.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

O script abaixo é um exemplo de uma configuração de origem XML utilizando o modo de conjunto de dados inline.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>Comportamento do conector XML

Note o seguinte ao utilizar xML como fonte.

- Atributos XML:

    - Os atributos de um elemento são analisados como os subcampos do elemento na hierarquia.
    - O nome do campo de atributos segue o `@attributeName` padrão.

- Validação do esquema XML:

    - Pode optar por não validar o esquema, nem validar o esquema utilizando XSD ou DTD.
    - Ao utilizar XSD ou DTD para validar ficheiros XML, o XSD/DTD deve ser encaminhado para dentro dos ficheiros XML através de um caminho relativo.

- Tratamento do espaço de nome:

    - O espaço de nome pode ser desativado quando se utilizam o fluxo de dados, caso em que os atributos que definem o espaço de nome serão analisados como atributos normais.
    - Quando o espaço de nome está ativado, os nomes do elemento e atributos seguem o padrão       `namespaceUri,elementName` e `namespaceUri,@attributeName` por padrão. Pode definir o prefixo do espaço de nome para cada nome URI na fonte, caso em que os nomes do elemento e atributos seguem o padrão `definedPrefix:elementName` `definedPrefix:@attributeName` ou, em vez disso.

- Coluna de valor:

    - Se um elemento XML tiver um valor de texto simples e atributos/elementos infantis, o valor de texto simples é analisado como o valor de uma "coluna de valor" com nome de campo incorporado `_value_` . E herda o espaço de nome do elemento também se aplica.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)