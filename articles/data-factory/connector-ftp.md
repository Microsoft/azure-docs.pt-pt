---
title: Copiar dados de um servidor FTP utilizando a Azure Data Factory
description: Aprenda a copiar dados de um servidor FTP para uma loja de dados de sink suportado utilizando uma atividade de cópia num pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: jingwang
ms.openlocfilehash: 55687529045e705f0a80b900b1cddaa49dba64d9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417329"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Copiar dados do servidor FTP utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-ftp-connector.md)
> * [Versão atual](connector-ftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados do servidor FTP. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector FTP é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, este conector FTP suporta:

- Copiar ficheiros utilizando a autenticação **Básica** ou **Anónima.**
- Copiar ficheiros como estão ou analisar ficheiros com os [formatos de ficheiros suportados e os códigos](supported-file-formats-and-compression-codecs.md)de compressão .

O servidor ftp suporta o servidor FTP em modo passivo. O modo ativo não é suportado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas à FTP.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à FTP:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para: **FtpServer**. | Sim |
| anfitrião | Especifique o nome ou endereço IP do servidor FTP. | Sim |
| porta | Especifique a porta em que o servidor FTP está a ouvir.<br/>Os valores permitidos são: inteiro, o valor predefinido é **de 21**. | Não |
| enableSsl | Especifique se utilizar ftp sobre um canal SSL/TLS.<br/>Os valores permitidos são: **verdadeiros** (padrão), **falsos**. | Não |
| permitirServerCertificateValidação | Especifique se ativa a validação do certificado TLS/SSL do servidor quando estiver a utilizar ftP através do canal SSL/TLS.<br/>Os valores permitidos são: **verdadeiros** (padrão), **falsos**. | Não |
| authenticationType | Especifique o tipo de autenticação.<br/>Os valores permitidos são: **Básico,** **Anónimo** | Sim |
| userName | Especifique o utilizador que tem acesso ao servidor FTP. | Não |
| palavra-passe | Especifique a palavra-passe para o utilizador (userName). Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Não |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

>[!NOTE]
>O conector FTP suporta aceder ao servidor FTP sem encriptação ou encriptação explícita SSL/TLS; não suporta encriptação implícita sSL/TLS.

**Exemplo 1: utilização de autenticação anónima**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: utilização da autenticação básica**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

As seguintes propriedades são suportadas para FTP em `location` definições no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade `location` do tipo em conjunto de dados deve ser definida para **FtpServerLocation**. | Sim      |
| folderPath | O caminho para a pasta. Se pretender utilizar o wildcard para filtrar a pasta, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |
| fileName   | O nome do ficheiro sob a pasta dadaPath. Se pretender utilizar ficheiros wildcard para filtrar ficheiros, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FtpServerLocation",
                "folderPath": "root/folder/subfolder"
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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte ftp.

### <a name="ftp-as-source"></a>FtP como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para FTP em `storeSettings` definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade `storeSettings` do tipo em baixo deve ser definida para **FtpReadSettings**. | Sim                                           |
| recursivo                | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Note que quando se recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia. Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres wildcard para filtrar as pastas de origem. <br>Os wildcards `*` permitidos são: (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro. <br>Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| nome wildcardFile         | O nome do ficheiro com caracteres wildcard sob a pasta dadaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards `*` permitidos são: (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro.  Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Sim, `fileName` se não for especificado no conjunto de dados |
| useBinaryTransfer | Especifique se utiliza o modo de transferência binário. Os valores são verdadeiros para o modo binário (predefinido) e falsos para a ASCII. |Não |
| maxConcurrentConnections | O número das ligações para ligar à loja de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
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
                    "type": "FtpReadSettings",
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

| folderPath | fileName | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, utilização por defeito) | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vazio, utilização por defeito) | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

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
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **FileShare** |Sim |
| folderPath | Caminho para a pasta. O filtro Wildcard é suportado, `*` os wildcards permitidos `?` são: (corresponde a zero ou mais caracteres) e (corresponde a zero ou personagem individual); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro. <br/><br/>Exemplos: pasta/subpasta/, ver mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). |Sim |
| fileName | **Nome ou filtro wildcard** para os ficheiros(s) sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os `*` wildcards permitidos são: (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou personagem individual).<br/>- Exemplo 1:`"fileName": "*.csv"`<br/>- Exemplo 2:`"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de ficheiro real tiver wildcard ou este char de fuga dentro. |Não |
| formato | Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar ficheiros com um formato específico, os seguintes tipos de formato de ficheiro são suportados: **TextFormat**, **JsonFormat**, **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Formato Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [Formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para mais informações, consulte [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)de compressão .<br/>Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**.<br/>Os níveis suportados são: **Optimal** e **Fastest**. |Não |
| useBinaryTransfer | Especifique se utiliza o modo de transferência binário. Os valores são verdadeiros para o modo binário (predefinido) e falsos para a ASCII. |Não |

>[!TIP]
>Para copiar todos os ficheiros sob uma pasta, especifique apenas **pastaPath.**<br>Para copiar um único ficheiro com um nome dado, especifique **a pastaPath** com peça de pasta e nome de **ficheiro** com nome de ficheiro.<br>Para copiar um subconjunto de ficheiros sob uma pasta, especifique **a pastaPath** com peça de pasta e nome de **ficheiro** com filtro wildcard.

>[!NOTE]
>Se estava a utilizar a propriedade "fileFilter" para filtro de ficheiros, ainda é suportado como está, enquanto é sugerido que utilize a nova capacidade de filtro adicionada ao "fileName" que vai para a frente.

**Exemplo:**

```json
{
    "name": "FTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
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
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<FTP input dataset name>",
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
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
