---
title: Copiar dados do Amazon Simple Storage Service (S3) com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como copiar dados do Amazon Simple Storage Service (S3) para os arquivos de dados de sink suportados através do Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: ca764c7e78f6ffe221386d1d320582e394d0a78a
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875884"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Copiar dados do Amazon Simple Storage Service com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
>
> * [Versão 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versão atual](connector-amazon-simple-storage-service.md)

Este artigo descreve como copiar dados do Amazon Simple Storage Service (Amazon S3). Para saber mais sobre o Azure Data Factory, leia os [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Amazon S3 é suportada para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [suportado de matriz de origem/sink](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, este conector Amazon S3 oferece suporte a cópia de arquivos como-é ou analisar ficheiros com o [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md). Ele usa [4 de versão de assinatura de AWS](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) para autenticar pedidos para S3.

>[!TIP]
>Pode utilizar este conector Amazon S3 para copiar dados a partir **quaisquer fornecedores de armazenamento S3-compatible** por exemplo, [Google Cloud Storage](connector-google-cloud-storage.md). Especifique o URL de serviço correspondente na configuração do serviço ligado.

## <a name="required-permissions"></a>Permissões obrigatórias

Para copiar dados do Amazon S3, certificar-se de que lhe foram concedidas as permissões seguintes:

- **Para a execução de atividade de cópia:**: `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto do Amazon S3.
- **Para a criação de GUI da fábrica de dados**: `s3:ListAllMyBuckets` e `s3:ListBucket` / `s3:GetBucketLocation` para operações de Bucket do Amazon S3 são necessárias permissões de adicionalmente para operações como ligação de teste e procurar/navegue caminhos de ficheiros. Se não pretender conceder essas permissões, ignore a ligação de teste na página de criação do serviço ligado e especifique o caminho diretamente nas definições do conjunto de dados.

Para obter detalhes sobre a lista completa de permissões do Amazon S3, consulte [especificar permissões numa política](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o Amazon S3.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Amazon S3:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AmazonS3**. | Sim |
| accessKeyId | ID da chave de acesso a segredos. |Sim |
| secretAccessKey | A chave de acesso a segredos em si. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| serviceUrl | Especifique o ponto de final de S3 personalizado se estiver a copiar dados de um fornecedor de armazenamento S3-compatible além do serviço do Amazon S3 oficial. Por exemplo, para copiar dados do Google Cloud Storage, especifique `https://storage.googleapis.com`. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!TIP]
>Especifique o URL do serviço de S3 personalizado se estiver a copiar dados de um armazenamento compatível com o S3 além do serviço do Amazon S3 oficial.

>[!NOTE]
>Este conector requer chaves de acesso para a conta IAM copiar dados do Amazon S3. [Credencial de segurança temporário](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) não é suportada.
>

Segue-se um exemplo:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. 

- Para **Parquet e o formato de texto delimitado**, consulte [conjunto de dados de formato de Parquet e texto delimitado](#parquet-and-delimited-text-format-dataset) secção.
- Como outros formatos **formato ORC/Avro/JSON/binário**, consulte [outro conjunto de dados do formato](#other-format-dataset) secção.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet e o conjunto de dados de formato de texto delimitado

Para copiar dados do Amazon S3 em **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) num conjunto de dados com base no formato de artigos e suporte definições. As seguintes propriedades são suportadas para o Amazon S3 em `location` definições no conjunto de dados com base no formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade de tipo sob `location` no conjunto de dados deve ser definida como **AmazonS3Location**. | Sim      |
| bucketName | O nome do registo de S3.                                          | Sim      |
| folderPath | O caminho para uma pasta sob o bucket especificado. Se pretender utilizar o caráter universal para a pasta de filtro, ignorar esta definição e especifique nas definições da origem de atividade. | Não       |
| fileName   | O nome de ficheiro, sob o bucket especificado + folderPath. Se pretender utilizar o caráter universal para filtrar os ficheiros, ignorar esta definição e especifique nas definições da origem de atividade. | Não       |

> [!NOTE]
> **AmazonS3Object** tipo conjunto de dados com o formato Parquet/Text mencionado na secção seguinte ainda é suportado como-é para a atividade de cópia/pesquisa/GetMetadata para compatibilidade com versões anteriores, mas ele não funciona com o mapeamento de fluxo de dados. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Outro conjunto de dados do formato

Para copiar dados do Amazon S3 em **formato ORC/Avro/JSON/binário**, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **AmazonS3Object** |Sim |
| bucketName | O nome do registo de S3. Não é suportado o filtro de carateres universais. |Sim para a atividade de cópia/Lookup, não para a atividade GetMetadata |
| key | O **filtro de nome ou o caráter universal** da chave de objeto de S3 sob o bucket especificado. Aplica-se apenas quando a propriedade de "prefixo" não está especificada. <br/><br/>O filtro de caráter universal é suportado para a parte de pasta e parte do nome de ficheiro. Permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual).<br/>-Exemplo 1: `"key": "rootfolder/subfolder/*.csv"`<br/>-Exemplo 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Consulte mais exemplo na [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). Utilize `^` para se o seu nome de pasta/ficheiro real tem carateres universais ou esse caractere de escape dentro de escape. |Não |
| prefix | Prefixo para a chave de objeto de S3. Objetos cujas chaves iniciados com este prefixo estão selecionados. Aplica-se apenas quando a propriedade "key" não está especificada. |Não |
| version | A versão do objeto, S3, se o controlo de versões de S3 está ativado. Se não for especificado, será possível obter a versão mais recente. |Não |
| modifiedDatetimeStart | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| format | Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar arquivos com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Não (apenas para o cenário de cópia binária) |
| compression | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis suportados são: **Ideal** e **mais rápida**. |Não |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique **bucketName** para o registo e **prefixo** para parte de pasta.<br>Para copiar um único ficheiro com um determinado nome, especifique **bucketName** para o registo e **chave** para o nome de parte e o ficheiro de pasta.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique **bucketName** para o registo e **chave** para o filtro de parte além de caráter universal de pasta.

**Exemplo: usando o prefixo**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Exemplo: usando a chave e versão (opcional)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 como origem

- Para copiar a partir **Parquet e o formato de texto delimitado**, consulte [Parquet e origem de formato de texto delimitado](#parquet-and-delimited-text-format-source) secção.
- Para copiar a partir de outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outra origem de formato](#other-format-source) secção.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e origem de formato de texto delimitado

Para copiar dados do Amazon S3 em **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo na origem de atividade de cópia baseada em formato e definições suportadas. As seguintes propriedades são suportadas para o Amazon S3 em `storeSettings` definições na origem de cópia baseada no formato:

| Propriedade                 | Descrição                                                  | Necessário                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A propriedade de tipo sob `storeSettings` deve ser definida como **AmazonS3ReadSetting**. | Sim                                                         |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que quando recursiva é definida como true e o sink é um arquivo baseado em arquivo, uma pasta vazia ou uma subpasta não é copiada ou criada no coletor. Valores permitidos são **true** (predefinição) e **falso**. | Não                                                          |
| prefix                   | Prefixo para a chave de objeto de S3 sob o bucket especificado configurado no conjunto de dados para filtrar objetos de origem. Objetos cujas chaves iniciados com este prefixo estão selecionados. <br>Aplica-se apenas quando `wildcardFolderPath` e `wildcardFileName` propriedades não estão especificadas. | Não                                                          |
| wildcardFolderPath       | O caminho de pasta com carateres universais sob o bucket especificado configurado no conjunto de dados para pastas de origem do filtro. <br>Permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape. <br>Veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). | Não                                                          |
| wildcardFileName         | O nome de ficheiro com carateres universais sob o bucket especificado + folderPath/wildcardFolderPath para ficheiros de origem do filtro. <br>Permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape.  Veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). | Se Sim `fileName` no conjunto de dados e `prefix` não forem especificadas |
| modifiedDatetimeStart    | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado. | Não                                                          |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                                          |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de armazenamento em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não                                                          |

> [!NOTE]
> Para o formato de texto delimitados/Parquet, **FileSystemSource** continua a ser suportada como origem da atividade de cópia de tipo mencionada na secção seguinte-destina-se para compatibilidade com versões anteriores. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Outra origem de formato

Para copiar dados do Amazon S3 em **formato ORC/Avro/JSON/binário**, as seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **FileSystemSource** |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção quando recursiva é definida como true e de sink é baseada em ficheiros de arquivo, vazia pasta/subutilização-folder não serão copiados/criado no sink.<br/>Valores permitidos são: **true** (predefinição), **FALSO** | Não |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de dados em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Pasta e exemplos de filtro de ficheiro

Esta secção descreve o comportamento resultante o nome de ficheiro e caminho de pasta com filtros de caráter universal.

| bucket | key | recursiva | Resultado de estrutura e o filtro de pasta (os ficheiros em negrito são obtidos) de origem|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados que são suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
