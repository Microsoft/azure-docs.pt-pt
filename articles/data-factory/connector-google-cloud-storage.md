---
title: Copiar dados do Google Cloud Storage usando o Azure Data Factory
description: Saiba mais sobre como copiar dados do Google Cloud Storage para armazenamentos de dados de coletor com suporte usando Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 4eedf54f3824adfb92ee22e5338325ccc5de3f75
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680907"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Copiar dados do Google Cloud Storage usando o Azure Data Factory

Este artigo descreve como copiar dados do Google Cloud Storage. Para saber mais sobre Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do Google Cloud Storage tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, esse conector do Google Cloud Storage dá suporte à cópia de arquivos no estado em que se encontram ou à análise de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>A cópia de dados do Google Cloud Storage aproveita o [conector do Amazon S3](connector-amazon-simple-storage-service.md) com o ponto de extremidade S3 personalizado correspondente, pois o Google Cloud Storage fornece a interoperabilidade compatível com S3.

## <a name="prerequisites"></a>Pré-requisitos

A seguinte configuração é necessária em sua conta de armazenamento do Google Cloud:

1. Habilitar a interoperabilidade para sua conta de armazenamento do Google Cloud
2. Defina o projeto padrão que contém os dados que você deseja copiar
3. Crie uma chave de acesso.

![Recuperar a chave de acesso para o Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Permissões obrigatórias

Para copiar dados do Google Cloud Storage, certifique-se de ter recebido as seguintes permissões:

- **Para a execução da atividade de cópia:** : `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto.
- **Para data Factory criação de GUI**: as permissões de `s3:ListAllMyBuckets` e `s3:ListBucket`/`s3:GetBucketLocation` para operações de Bucket também são necessárias para operações como conexão de teste e navegação/navegação de caminhos de arquivo. Se você não quiser conceder essas permissões, ignore testar conexão na página de criação de serviço vinculado e especifique o caminho diretamente nas configurações do conjunto de configuração.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao Google Cloud Storage.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Google Cloud Storage:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **GoogleCloudStorage**. | Sim |
| accessKeyid | ID da chave de acesso secreta. Para localizar a chave de acesso e o segredo, consulte [pré-requisitos](#prerequisites). |Sim |
| secretAccessKey | A própria chave de acesso de segredo. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| serviceUrl | Especifique o ponto de extremidade S3 personalizado como **`https://storage.googleapis.com`** . | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar os Integration Runtime Azure Integration Runtime ou auto-hospedado (se o armazenamento de dados estiver localizado em rede privada). Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

Segue-se um exemplo:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para o armazenamento em nuvem do Google em configurações de `location` no conjunto de entrada baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade Type em `location` no conjunto de texto deve ser definida como **AmazonS3Location**. | Sim      |
| bucketName | O nome do Bucket S3.                                          | Sim      |
| folderPath | O caminho para a pasta sob o Bucket fornecido. Se você quiser usar curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob a partição especificada + folderPath. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
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

### <a name="legacy-dataset-model"></a>Modelo de conjunto de DataSet herdado

>[!NOTE]
>O modelo de conjunto de itens a seguir ainda tem suporte como está para compatibilidade com versões anteriores. Você deve usar o novo modelo mencionado na seção acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **AmazonS3Object** |Sim |
| bucketName | O nome do Bucket S3. Não há suporte para o filtro curinga. |Sim para atividade de cópia/pesquisa, não para a atividade GetMetadata |
| key | O **nome ou filtro curinga** da chave de objeto S3 no Bucket especificado. Aplica-se somente quando a propriedade "prefix" não é especificada. <br/><br/>O filtro curinga tem suporte para a parte da pasta e do nome do arquivo. Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere).<br/>-Exemplo 1: `"key": "rootfolder/subfolder/*.csv"`<br/>-Exemplo 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). Use `^` para escapar se o nome real de pasta/arquivo tiver curinga ou este caractere de escape dentro de. |Não |
| prefixo | Prefixo da chave do objeto S3. Os objetos cujas chaves começam com esse prefixo são selecionados. Aplica-se somente quando a propriedade "Key" não é especificada. |Não |
| versão | A versão do objeto S3, se o controle de versão S3 estiver habilitado. |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| ao | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, há suporte para os seguintes tipos de formato de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format)e seções de [formato parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Não (somente para o cenário de cópia binária) |
| çã | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [formatos de arquivo com suporte e codecs de compactação](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**.<br/>Os níveis com suporte são: **ideal** e **mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **bucketname** para o Bucket e o **prefixo** da parte da pasta.<br>Para copiar um único arquivo com um nome específico, especifique **bucketname** para Bucket e **chave** para a parte da pasta mais o nome do arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **bucketname** para o Bucket e a **chave** para a parte da pasta, além do filtro curinga.

**Exemplo: usando o prefixo**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
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

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte de armazenamento do Google Cloud.

### <a name="google-cloud-storage-as-source"></a>Armazenamento em nuvem do Google como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para o armazenamento em nuvem do Google em configurações de `storeSettings` em fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| tipo                     | A propriedade Type em `storeSettings` deve ser definida como **AmazonS3ReadSetting**. | Sim                                                         |
| recursiva                | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                                          |
| prefixo                   | Prefixo da chave de objeto S3 sob o Bucket especificado configurado no conjunto de dados para filtrar objetos de origem. Os objetos cujas chaves começam com esse prefixo são selecionados. Aplica-se somente quando as propriedades `wildcardFolderPath` e `wildcardFileName` não são especificadas. |                                                             |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga no Bucket especificado configurado no conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br>Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                                          |
| wildcardFileName         | O nome do arquivo com caracteres curinga sob a partição especificada + folderPath/wildcardFolderPath para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro.  Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Sim se `fileName` em DataSet e `prefix` não forem especificados |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado. | Não                                                          |
| modifiedDatetimeEnd      | O mesmo que acima.                                               | Não                                                          |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                                          |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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

#### <a name="legacy-source-model"></a>Modelo de origem herdado

>[!NOTE]
>O modelo de origem de cópia a seguir ainda tem suporte como está para compatibilidade com versões anteriores. Você deve usar o novo modelo mencionado acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **FileSystemName** |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observação Quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, a pasta/subpasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e o nome do arquivo com filtros curinga.

| sequencia | key | recursiva | Estrutura da pasta de origem e resultado do filtro (arquivos em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| sequencia | `Folder*/*` | false | sequencia<br/>&nbsp;&nbsp;&nbsp;&nbsp;pastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo2. JSON**&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| sequencia | `Folder*/*` | true | sequencia<br/>&nbsp;&nbsp;&nbsp;&nbsp;pastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo2. JSON**&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| sequencia | `Folder*/*.csv` | false | sequencia<br/>&nbsp;&nbsp;&nbsp;&nbsp;pastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo2. JSON&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| sequencia | `Folder*/*.csv` | true | sequencia<br/>&nbsp;&nbsp;&nbsp;&nbsp;pastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo2. JSON&nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter detalhes sobre as propriedades, marque a [atividade de exclusão](delete-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
