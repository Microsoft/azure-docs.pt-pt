---
title: Copiar dados do servidor SFTP usando o Azure Data Factory
description: Saiba mais sobre o conector do MySQL no Azure Data Factory que permite copiar dados de um servidor SFTP para um repositório de dados com suporte como coletor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: a3eb3193e0c1cd508bc1fc269cd75254d9392885
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680207"
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Copiar dados do servidor SFTP usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sftp-connector.md)
> * [Versão atual](connector-sftp.md)

Este artigo descreve como copiar dados do servidor SFTP. Para saber mais sobre Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector SFTP tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, esse conector SFTP dá suporte a:

- Copiar arquivos usando a autenticação **básica** ou **SshPublicKey** .
- Copiando arquivos no estado em que se encontram ou analisando arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao SFTP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SFTP:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **SFTP**. |Sim |
| hospedeira | Nome ou endereço IP do servidor SFTP. |Sim |
| porta | Porta na qual o servidor SFTP está escutando.<br/>Os valores permitidos são: inteiro, o valor padrão é **22**. |Não |
| skipHostKeyValidation | Especifique se deseja ignorar a validação da chave do host.<br/>Os valores permitidos são: **true**, **false** (padrão).  | Não |
| hostKeyFingerprint | Especifique a impressão digital da chave do host. | Sim se "skipHostKeyValidation" estiver definido como false.  |
| authenticationType | Especifique o tipo de autenticação.<br/>Os valores permitidos são: **Basic**, **SshPublicKey**. Consulte [usando a autenticação básica](#using-basic-authentication) e [usando seções de autenticação de chave pública SSH](#using-ssh-public-key-authentication) em mais propriedades e exemplos JSON, respectivamente. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

### <a name="using-basic-authentication"></a>Usando a autenticação básica

Para usar a autenticação básica, defina a propriedade "authenticationType" como **básica**e especifique as propriedades a seguir, além das genéricas do conector SFTP introduzidas na última seção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| Usu | Usuário que tem acesso ao servidor SFTP. |Sim |
| palavra-passe | Senha do usuário (userName). Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

**Exemplo:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
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

### <a name="using-ssh-public-key-authentication"></a>Usando a autenticação de chave pública SSH

Para usar a autenticação de chave pública SSH, defina a propriedade "authenticationType" como **SshPublicKey**e especifique as propriedades a seguir, além das genéricas do conector SFTP introduzidas na última seção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| Usu | Usuário que tem acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique o caminho absoluto para o arquivo de chave privada que Integration Runtime pode acessar. Aplica-se somente quando o tipo auto-hospedado de Integration Runtime é especificado em "connectVia". | Especifique o `privateKeyPath` ou o `privateKeyContent`.  |
| privateKeyContent | Conteúdo da chave privada SSH codificada em base64. A chave privada SSH deve ser o formato OpenSSH. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Especifique o `privateKeyPath` ou o `privateKeyContent`. |
| passPhrase | Especifique a frase secreta/senha para descriptografar a chave privada se o arquivo de chave estiver protegido por uma frase secreta. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim se o arquivo de chave privada for protegido por uma frase secreta. |

> [!NOTE]
> O conector SFTP dá suporte à chave RSA/DSA OpenSSH. Verifique se o conteúdo do arquivo de chave começa com "-----BEGIN [RSA/DSA]-----chave privada". Se o arquivo de chave privada for um arquivo de formato PPK, use a ferramenta de saída para converter de. PPK para o formato OpenSSH. 

**Exemplo 1: autenticação SshPublicKey usando filePath de chave privada**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: autenticação SshPublicKey usando conteúdo de chave privada**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para SFTP em configurações de `location` no conjunto de conjuntos de base de formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade Type em `location` no conjunto de texto deve ser definida como **SftpLocation**. | Sim      |
| folderPath | O caminho para a pasta. Se você quiser usar curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o folderPath fornecido. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

### <a name="legacy-dataset-model"></a>Modelo de conjunto de DataSet herdado

>[!NOTE]
>O modelo de conjunto de itens a seguir ainda tem suporte como está para compatibilidade com versões anteriores. Você deve usar o novo modelo mencionado na seção acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **FileShare** |Sim |
| folderPath | Caminho para a pasta. Há suporte para o filtro curinga, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome de arquivo real tiver curinga ou este caractere de escape dentro de. <br/><br/>Exemplos: RootFolder/subfolder/, veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Sim |
| fileName |  **Nome ou filtro curinga** para os arquivos sob o "FolderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de pontos apontará para todos os arquivos na pasta. <br/><br/>Para filtros, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| ao | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída.<br/><br/>Se você quiser analisar arquivos com um formato específico, há suporte para os seguintes tipos de formato de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format)e seções de [formato parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Não (somente para o cenário de cópia binária) |
| çã | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [formatos de arquivo com suporte e codecs de compactação](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**.<br/>Os níveis com suporte são: **ideal** e **mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **FolderPath** apenas.<br>Para copiar um único arquivo com um nome específico, especifique **FolderPath** com a parte da **pasta e o nome do** arquivo com o nome do arquivos.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **FolderPath** com a parte da pasta e o **nome do arquivo** com o filtro curinga.

>[!NOTE]
>Se você estiver usando a propriedade "FileFilter" para o filtro de arquivo, ainda haverá suporte como está, enquanto você deve usar o novo recurso de filtro adicionado ao "fileName" no futuro.

**Exemplo:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte SFTP.

### <a name="sftp-as-source"></a>SFTP como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para SFTP em configurações de `storeSettings` na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade Type em `storeSettings` deve ser definida como **SftpReadSetting**. | Sim                                           |
| recursiva                | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br>Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome do arquivo com caracteres curinga sob o folderPath/wildcardFolderPath fornecido para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro.  Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no DataSet |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado. | Não                                            |
| modifiedDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                            |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSetting",
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
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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

| folderPath | fileName | recursiva | Estrutura da pasta de origem e resultado do filtro (arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, usar padrão) | false | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**arquivo2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (vazio, usar padrão) | true | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**arquivo2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter detalhes sobre as propriedades, marque a [atividade de exclusão](delete-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
