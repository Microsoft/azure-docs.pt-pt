---
title: Copiar dados do Amazon Simple Storage Service (S3)
description: Saiba como copiar dados do Amazon Simple Storage Service (S3) para armazenar nausite suportada através da Utilização da Fábrica de Dados Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.openlocfilehash: 6975c876bb31e36b7b6268d284f747fd51803d63
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415459"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Copiar dados do Serviço de Armazenamento Simples da Amazon utilizando a Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versão atual](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados do Amazon Simple Storage Service (Amazon Simple Storage Service). Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

>[!TIP]
>Para o cenário de migração de dados da Amazon S3 para o Armazenamento Azure, saiba mais com [a Use Azure Data Factory para migrar dados da Amazon S3 para o Armazenamento Azure.](data-migration-guidance-s3-azure-storage.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Amazon S3 é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, este conector Amazon S3 suporta copiar ficheiros como está ou analisar ficheiros com os [formatos de ficheiros suportados e os códigos](supported-file-formats-and-compression-codecs.md)de compressão . Também pode optar por preservar metadados de [ficheiros durante a cópia](#preserve-metadata-during-copy). O conector utiliza a Versão 4 da [Assinatura AWS](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) para autenticar pedidos ao S3.

>[!TIP]
>Pode utilizar este conector Amazon S3 para copiar dados de **quaisquer fornecedores de armazenamento compatíveis com o S3,** por exemplo, [o Google Cloud Storage](connector-google-cloud-storage.md). Especifique o URL de serviço correspondente na configuração do serviço ligado.

## <a name="required-permissions"></a>Permissões obrigatórias

Para copiar dados do Amazon S3, certifique-se de que lhe foram concedidas as seguintes permissões:

- **Para a execução** `s3:GetObjectVersion` da atividade de cópia: : e `s3:GetObject` para a Amazon S3 Object Operations.
- **Para**a autoria de `s3:ListAllMyBuckets` `s3:ListBucket` / `s3:GetBucketLocation` Data Factory GUI : e para a Amazon S3 Bucket Operations são adicionalmente necessárias para operações como conexão de teste e navegar/navegar em caminhos de ficheiros. Se não quiser conceder estas permissões, ignore a ligação de teste na página de criação de serviço selecionada e especifique o caminho diretamente nas definições do conjunto de dados.

Para mais detalhes sobre a lista completa de permissões Amazon S3, consulte [a especção de permissões numa Política](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data factory específicas da Amazon S3.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Amazon S3:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AmazonS3**. | Sim |
| acessoKeyId | Identificação da chave de acesso secreta. |Sim |
| secretAccessKey | A chave de acesso secreta em si. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim |
| serviçoUrl | Especifique o ponto final personalizado Do S3 se estiver a copiar dados de um fornecedor de armazenamento compatível com S3 que não seja o serviço oficial Amazon S3. Por exemplo, para copiar dados do `https://storage.googleapis.com`Google Cloud Storage, especifique . | Não |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

>[!TIP]
>Especifique o URL de serviço S3 personalizado se estiver a copiar dados de um armazenamento compatível com S3 que não seja o serviço oficial amazon S3.

>[!NOTE]
>Este conector requer chaves de acesso para a conta IAM para copiar dados do Amazon S3. [A Credencial de Segurança Temporária](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) não é apoiada.
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas `location` para Amazon S3 em configurações em conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade `location` tipo em conjunto de dados deve ser definida para **AmazonS3Location**. | Sim      |
| bucketName | O nome do balde S3.                                          | Sim      |
| folderPath | O caminho para pasta sob o balde dado. Se pretender utilizar o wildcard para filtrar a pasta, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |
| fileName   | O nome do ficheiro sob o balde dado + pastaPath. Se pretender utilizar ficheiros wildcard para filtrar ficheiros, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |
| versão | A versão do objeto S3, se a versão S3 estiver ativada. Se não for especificada, a versão mais recente será recolhida. |Não |

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

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte da Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas `storeSettings` para Amazon S3 em configurações em fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| tipo                     | A propriedade `storeSettings` do tipo em baixo deve ser definida para **AmazonS3ReadSettings**. | Sim                                                         |
| recursivo                | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Note que quando se recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia. Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**. | Não                                                          |
| prefixo                   | Prefixo para a chave de objetos S3 sob o balde dado configurado no conjunto de dados para filtrar objetos de origem. São selecionados objetos cujas teclas começam com este prefixo. <br>Aplica-se `wildcardFolderPath` apenas quando e `wildcardFileName` as propriedades não são especificadas. | Não                                                          |
| wildcardFolderPath       | O caminho da pasta com caracteres wildcard sob o balde dado configurado no conjunto de dados para filtrar as pastas de origem. <br>Os wildcards `*` permitidos são: (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro. <br>Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Não                                                          |
| nome wildcardFile         | O nome do ficheiro com caracteres wildcard sob o balde dado + pastaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards `*` permitidos são: (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro.  Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Sim `fileName` se em `prefix` conjunto de dados e não forespecificado |
| alteradoDatetimeStart    | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o `modifiedDatetimeEnd` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem o `modifiedDatetimeStart` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados. | Não                                                          |
| alteradoDatetimeEnd      | O mesmo que acima.                                               | Não                                                          |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                                          |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e ficheiro

Esta secção descreve o comportamento resultante do caminho da pasta e nome de ficheiro com filtros wildcard.

| balde | key | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| balde | `Folder*/*` | false | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| balde | `Folder*/*` | true | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| balde | `Folder*/*.csv` | false | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| balde | `Folder*/*.csv` | true | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="preserve-metadata-during-copy"></a>Preservar metadados durante a cópia

Quando copia ficheiros da Amazon S3 para o Azure Data Lake Storage Gen2/Azure Blob, pode optar por preservar os metadados do ficheiro juntamente com os dados. Saiba mais com [a Preserve metadados.](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Obtenha propriedades de atividadede Metadados

Para saber mais detalhes sobre as propriedades, consulte [a atividade do GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eliminar propriedades de atividade

Para saber detalhes sobre as propriedades, consulte a [atividade de Eliminar](delete-activity.md)

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os seguintes modelos ainda são suportados como é para retrocompatibilidade. É-lhe sugerido que utilize o novo modelo mencionado nas secções acima, e a UI de autoria da ADF mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **AmazonS3Object** |Sim |
| bucketName | O nome do balde S3. O filtro Wildcard não é suportado. |Sim para a atividade de Cópia/Procuração, Não para a atividade getMetadata |
| key | O **nome ou filtro wildcard** da chave do objeto S3 sob o balde especificado. Aplica-se apenas quando a propriedade "prefixo" não é especificada. <br/><br/>O filtro wildcard é suportado tanto para a parte da pasta como para a parte do nome do ficheiro. Os wildcards `*` permitidos são: (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único).<br/>- Exemplo 1:`"key": "rootfolder/subfolder/*.csv"`<br/>- Exemplo 2:`"key": "rootfolder/subfolder/???20180427.txt"`<br/>Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). Utilize `^` para escapar se o seu nome real de pasta/ficheiro tiver wildcard ou este char de fuga no seu interior. |Não |
| prefixo | Prefixo para a tecla de objeto S3. São selecionados objetos cujas teclas começam com este prefixo. Aplica-se apenas quando a propriedade "chave" não é especificada. |Não |
| versão | A versão do objeto S3, se a versão S3 estiver ativada. Se não for especificada, a versão mais recente será recolhida. |Não |
| alteradoDatetimeStart | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> Esteja ciente de que o desempenho global do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o `modifiedDatetimeEnd` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem o `modifiedDatetimeStart` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| Não |
| alteradoDatetimeEnd | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> Esteja ciente de que o desempenho global do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o `modifiedDatetimeEnd` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem o `modifiedDatetimeStart` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| Não |
| formato | Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Formato Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [Formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para mais informações, consulte [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)de compressão .<br/>Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**.<br/>Os níveis suportados são: **Optimal** e **Fastest**. |Não |

>[!TIP]
>Para copiar todos os ficheiros sob uma pasta, especifique **o bucketName** para balde e **prefixo** para a peça da pasta.<br>Para copiar um único ficheiro com um nome dado, especifique **o bucketName** para balde e **chave** para a parte da pasta mais o nome do ficheiro.<br>Para copiar um subconjunto de ficheiros sob uma pasta, especifique **o bucketName** para balde e **chave** para a parte da pasta mais o filtro wildcard.

**Exemplo: utilização de prefixo**

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

**Exemplo: utilização da chave e versão (opcional)**

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de fonte de fonte de atividade de cópia legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **FileSystemSource** |Sim |
| recursivo | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Note que quando a recursiva for definida como verdadeira e a pia for uma loja baseada em ficheiros, a pasta/subpasta vazia não será copiada/criada na pia.<br/>Os valores permitidos são: **verdadeiros** (padrão), **falsos** | Não |
| maxConcurrentConnections | O número das ligações para ligar à loja de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

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

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados que são suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
