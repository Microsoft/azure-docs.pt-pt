---
title: Copiar dados do Amazon S3 (Simple Storage Service) usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como copiar dados do Amazon Simple Storage Service (S3) para os arquivos de dados de sink suportados através do Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: aebcefadf4dfdb9301a01b0b4117e8aa2e429898
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276525"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Copiar dados do Amazon Simple Storage Service com o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
>
> * [Versão 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versão atual](connector-amazon-simple-storage-service.md)

Este artigo descreve como copiar dados do Amazon S3 (Simple Storage Service). Para saber mais sobre o Azure Data Factory, leia os [artigo introdutório](introduction.md).

Para o cenário de migração de dados do Amazon S3 para o armazenamento do Azure, saiba mais em [usar Azure data Factory para migrar dados do Amazon S3 para o armazenamento do Azure](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Esse conector Amazon S3 tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, este conector Amazon S3 oferece suporte a cópia de arquivos como-é ou analisar ficheiros com o [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md). Ele usa a [assinatura AWS versão 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) para autenticar solicitações para S3.

>[!TIP]
>Você pode usar esse conector Amazon S3 para copiar dados de **qualquer provedor de armazenamento compatível com S3** , por exemplo, [Google Cloud Storage](connector-google-cloud-storage.md). Especifique a URL do serviço correspondente na configuração do serviço vinculado.

## <a name="required-permissions"></a>Permissões obrigatórias

Para copiar dados do Amazon S3, certificar-se de que lhe foram concedidas as permissões seguintes:

- **Para a execução de atividade de cópia:** : `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto do Amazon S3.
- **Para a criação de GUI da fábrica de dados**: `s3:ListAllMyBuckets` e `s3:ListBucket` / `s3:GetBucketLocation` para operações de Bucket do Amazon S3 são necessárias permissões de adicionalmente para operações como ligação de teste e procurar/navegue caminhos de ficheiros. Se você não quiser conceder essas permissões, ignore testar conexão na página de criação de serviço vinculado e especifique o caminho diretamente nas configurações do conjunto de configuração.

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
| serviceUrl | Especifique o ponto de extremidade S3 personalizado se você estiver copiando dados de um provedor de armazenamento compatível com S3 que não seja o serviço do Amazon S3 oficial. Por exemplo, para copiar dados do Google Cloud Storage, especifique `https://storage.googleapis.com`. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!TIP]
>Especifique a URL do serviço S3 personalizado se você estiver copiando dados de um armazenamento compatível com S3 que não seja o serviço do Amazon S3 oficial.

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

- Para **parquet, texto delimitado, Avro e formato binário**, consulte a seção [parquet, texto delimitado e conjunto de banco de forma binário](#format-based-dataset) .
- Para outros formatos como o **formato Orc/JSON**, consulte [outra](#other-format-dataset) seção do conjunto de exemplo.

### <a name="format-based-dataset"></a>Parquet, texto delimitado, Avro e conjunto de um formato binário

Para copiar dados do Amazon S3 em **parquet, texto delimitado, formato Avro ou binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), [formato Avro](format-avro.md) e artigo [formato binário](format-binary.md) em conjunto de dados com base em formato e configurações com suporte. As propriedades a seguir têm suporte para o Amazon `location` S3 em configurações em conjunto de base de formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade Type em `location` DataSet deve ser definida como **AmazonS3Location**. | Sim      |
| bucketName | O nome do registo de S3.                                          | Sim      |
| folderPath | O caminho para a pasta sob o Bucket fornecido. Se você quiser usar curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob a partição especificada + folderPath. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| version | A versão do objeto, S3, se o controlo de versões de S3 está ativado. Se não for especificado, a versão mais recente será buscada. |Não |

> [!NOTE]
> **AmazonS3Object** tipo de conjunto de dados com parquet/formato de texto mencionado na próxima seção ainda tem suporte como é para a atividade de cópia/pesquisa/GetMetadata para compatibilidade com versões anteriores, mas não funciona com o fluxo de dados de mapeamento. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Example:**

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

### <a name="other-format-dataset"></a>Outro conjunto de DataSet de formato

Para copiar dados do Amazon S3 no **formato Orc/JSON**, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **AmazonS3Object** |Sim |
| bucketName | O nome do registo de S3. Não é suportado o filtro de carateres universais. |Sim para a atividade de cópia/Lookup, não para a atividade GetMetadata |
| key | O **filtro de nome ou o caráter universal** da chave de objeto de S3 sob o bucket especificado. Aplica-se apenas quando a propriedade de "prefixo" não está especificada. <br/><br/>O filtro curinga tem suporte para a parte da pasta e do nome do arquivo. Permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual).<br/>-Exemplo 1: `"key": "rootfolder/subfolder/*.csv"`<br/>-Exemplo 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Veja mais exemplo nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). Use `^` para escapar se o nome real de pasta/arquivo tiver curinga ou este caractere de escape dentro de. |Não |
| prefix | Prefixo para a chave de objeto de S3. Objetos cujas chaves iniciados com este prefixo estão selecionados. Aplica-se apenas quando a propriedade "key" não está especificada. |Não |
| version | A versão do objeto, S3, se o controlo de versões de S3 está ativado. Se não for especificado, a versão mais recente será buscada. |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| format | Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se você deseja analisar ou gerar arquivos com um formato específico, há suporte para os seguintes tipos de formato de arquivo:TextFormat **, JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Não (apenas para o cenário de cópia binária) |
| compression | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **Gzip**,deflate, **bzip2**e **ZipDeflate**.<br/>Os níveis com suporte são: **Ideal** e **mais rápido**. |Não |

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

- Para copiar de **parquet, texto delimitado, Avro e formato binário**, consulte a seção [parquet, texto delimitado e fonte de formato binário](#format-based-source) .
- Para copiar de outros formatos como o **formato Orc/JSON**, consulte [outra seção fonte de formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, texto delimitado, Avro e fonte de formato binário

Para copiar dados do Amazon S3 em **parquet, texto delimitado, formato Avro ou binário**, consulte o [formato parquet](format-parquet.md), o formato de [texto delimitado](format-delimited-text.md), o [formato Avro](format-avro.md) e o artigo [formato binário](format-binary.md) na fonte da atividade de cópia baseada em formato e configurações com suporte. As propriedades a seguir têm suporte para o Amazon `storeSettings` S3 em configurações na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AmazonS3ReadSetting**. | Sim                                                         |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que quando recursiva é definida como true e o sink é um arquivo baseado em arquivo, uma pasta vazia ou uma subpasta não é copiada ou criada no coletor. Valores permitidos são **true** (predefinição) e **falso**. | Não                                                          |
| prefix                   | Prefixo da chave de objeto S3 sob o Bucket especificado configurado no conjunto de dados para filtrar objetos de origem. Objetos cujas chaves iniciados com este prefixo estão selecionados. <br>Aplica-se `wildcardFolderPath` somente `wildcardFileName` quando as propriedades e não são especificadas. | Não                                                          |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga no Bucket especificado configurado no conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são `*` : (corresponde a zero ou mais caracteres `?` ) e (corresponde a zero ou a um `^` único caractere); Use para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br>Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                                          |
| wildcardFileName         | O nome do arquivo com caracteres curinga sob a partição especificada + folderPath/wildcardFolderPath para filtrar os arquivos de origem. <br>Os curingas permitidos são `*` : (corresponde a zero ou mais caracteres `?` ) e (corresponde a zero ou a um `^` único caractere); Use para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro.  Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Sim se `fileName` estiver no DataSet `prefix` e não for especificado |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado. | Não                                                          |
| modifiedDatetimeEnd      | O mesmo que acima.                                               | Não                                                          |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                                          |

> [!NOTE]
> Para o formato de texto parquet/delimitado, a fonte da atividade de cópia do tipo FileSystemProvider mencionada na próxima seção ainda tem suporte como está para compatibilidade com versões anteriores. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Example:**

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

#### <a name="other-format-source"></a>Outra fonte de formato

Para copiar dados do Amazon S3 no **formato Orc/JSON**, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **FileSystemSource** |Sim |
| recursive | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção quando recursiva é definida como true e de sink é baseada em ficheiros de arquivo, vazia pasta/subutilização-folder não serão copiados/criado no sink.<br/>Valores permitidos são: **true** (predefinição), **FALSO** | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Example:**

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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e o nome do arquivo com filtros curinga.

| sequencia | key | recursive | Estrutura da pasta de origem e resultado do filtro (arquivos em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| sequencia | `Folder*/*` | false | sequencia<br/>&nbsp;&nbsp;&nbsp;&nbsp;PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| sequencia | `Folder*/*` | true | sequencia<br/>&nbsp;&nbsp;&nbsp;&nbsp;PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| sequencia | `Folder*/*.csv` | false | sequencia<br/>&nbsp;&nbsp;&nbsp;&nbsp;PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| sequencia | `Folder*/*.csv` | true | sequencia<br/>&nbsp;&nbsp;&nbsp;&nbsp;PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte armazenamentos de [dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
