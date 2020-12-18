---
title: Copie os dados de um servidor FTP utilizando a Azure Data Factory
description: Saiba como copiar dados de um servidor FTP para uma loja de dados de lavatórios suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: b3f84d48cd65c37495f00a17b586ff4a6954795d
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680860"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Copiar dados do servidor FTP utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
>
> * [Versão 1](v1/data-factory-ftp-connector.md)
> * [Versão atual](connector-ftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados do servidor FTP. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector FTP é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, este conector FTP suporta:

- Copiar ficheiros utilizando a autenticação **básica** ou **anónima.**
- Copiar ficheiros como está ou analisar ficheiros com os [formatos de ficheiros suportados e os codecs de compressão](supported-file-formats-and-compression-codecs.md).

O conector FTP suporta o servidor FTP em funcionamento em modo passivo. O modo ativo não é suportado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas da FTP.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ftp:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **FtpServer**. | Sim |
| anfitrião | Especifique o nome ou endereço IP do servidor FTP. | Sim |
| porta | Especifique a porta na qual o servidor FTP está a ouvir.<br/>Os valores permitidos são: inteiro, o valor por defeito é **de 21**. | Não |
| ativarSl | Especificar se deve utilizar o FTP num canal SSL/TLS.<br/>Os valores permitidos são: **verdadeiros** (padrão), **falsos.** | Não |
| enableServerCertificateValidation | Especificar se deve ativar a validação do certificado TLS/SSL do servidor quando estiver a utilizar o ftp sobre o canal SSL/TLS.<br/>Os valores permitidos são: **verdadeiros** (padrão), **falsos.** | Não |
| authenticationType | Especifique o tipo de autenticação.<br/>Os valores permitidos são: **Básico,** **Anónimo** | Sim |
| userName | Especifique o utilizador que tem acesso ao servidor FTP. | Não |
| palavra-passe | Especifique a palavra-passe para o utilizador (userName). Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Não |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |Não |

>[!NOTE]
>O conector FTP suporta o acesso ao servidor FTP sem encriptação ou encriptação SSL/TLS explícita; não suporta encriptação implícita SSL/TLS.

**Exemplo 1: utilização da autenticação anónima**

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

As seguintes propriedades são suportadas para FTP em `location` configurações no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade tipo `location` em conjunto de dados deve ser definida como **FtpServerLocation**. | Sim      |
| folderPath | O caminho para a pasta. Se pretender utilizar o wildcard para filtrar a pasta, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |
| fileName   | O nome do ficheiro sob a pasta DadaPa. Se pretender utilizar o wildcard para filtrar ficheiros, ignore esta definição e especifique nas definições de origem da atividade. | Não       |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte FTP.

### <a name="ftp-as-source"></a>FTP como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para FTP em `storeSettings` configurações na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade tipo em baixo `storeSettings` deve ser definida para **FtpReadSettings**. | Sim                                           |
| **_Localize os ficheiros para copiar:_* _ |  |  |
| OPÇÃO 1: caminho estático<br> | Cópia do caminho da pasta/ficheiro especificado no conjunto de dados. Se pretender copiar todos os ficheiros de uma pasta, especificar ainda `wildcardFileName` como `_` . |  |
| OPÇÃO 2: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard para filtrar pastas de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior. <br>Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 2: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard sob a pasta DadaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o seu nome de ficheiro real tiver wildcard ou este char de fuga no interior.  Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 3: uma lista de ficheiros<br>- fileListPath | Indica copiar um determinado conjunto de ficheiros. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar, um ficheiro por linha, que é o caminho relativo para o caminho configurado no conjunto de dados.<br/>Ao utilizar esta opção, não especifique o nome do ficheiro no conjunto de dados. Ver mais exemplos em [exemplos da lista de ficheiros.](#file-list-examples) |Não |
| ***Definições adicionais:** _ |  | |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Note que quando a recursiva é definida como verdadeira e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobrador vazio não é copiado ou criado na pia. <br>Os valores permitidos são _ *verdadeiros** (padrão) e **falsos**.<br>Esta propriedade não se aplica quando se `fileListPath` configura. |Não |
| eliminarFilesAfterCompletion | Indica se os ficheiros binários serão eliminados da loja de origem depois de se mudarem com sucesso para a loja de destino. A eliminação do ficheiro é por ficheiro, pelo que quando a atividade da cópia falhar, verá que alguns ficheiros já foram copiados para o destino e eliminados da fonte, enquanto outros ainda permanecem na loja de origem. <br/>Esta propriedade é válida apenas em cenário de cópia de ficheiros binários. O valor predefinido: falso. |Não |
| useTransfera | Especificar se deve utilizar o modo de transferência binário. Os valores são verdadeiros para o modo binário (padrão) e falsos para o ASCII. |Não |
| permitirPartitionDiscovery | Para os ficheiros que são divididos, especifique se analisar as divisórias do caminho do ficheiro e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **falsos** (padrão) e **verdadeiros.** | Não                                            |
| partitionRootPath | Quando a descoberta da partição estiver ativada, especifique o caminho da raiz absoluta para ler as pastas partidas como colunas de dados.<br/><br/>Se não for especificado, por defeito,<br/>- Quando utiliza o caminho do ficheiro no conjunto de dados ou na lista de ficheiros na fonte, o caminho da raiz da partição é o caminho configurado no conjunto de dados.<br/>- Quando utiliza o filtro de pasta wildcard, o caminho da raiz da partição é o sub-caminho antes do primeiro wildcard.<br/><br/>Por exemplo, assumindo que configura o caminho no conjunto de dados como "raiz/pasta/ano=2020/mês=08/dia=27":<br/>- Se especificar o caminho da raiz da partição como "raiz/pasta/ano=2020", a atividade da cópia gerará mais duas colunas `month` e com o valor `day` "08" e "27", respectivamente, para além das colunas dentro dos ficheiros.<br/>- Se não for especificado o caminho da raiz da partição, não será gerada nenhuma coluna extra. | Não                                            |
| maxConcurrentConnections | O número de ligações para ligar ao armazenamento de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

Ao copiar o formulário de dados FTP, atualmente a ADF tenta obter primeiro o comprimento do ficheiro, em seguida, dividir o ficheiro em várias partes e lê-los em paralelo. Se o seu servidor FTP não suportar obter o comprimento do ficheiro ou procurar ler a partir de uma determinada compensação, poderá encontrar falhas.

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

Esta secção descreve o comportamento resultante do caminho da pasta e nome do ficheiro com filtros wildcard.

| folderPath | fileName | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, uso padrão) | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vazio, uso padrão) | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de ficheiros

Esta secção descreve o comportamento resultante da utilização do caminho da lista de ficheiros na fonte de atividade de cópia.

Assumindo que tem a seguinte estrutura de pasta de origem e quer copiar os ficheiros em negrito:

| Estrutura de origem da amostra                                      | Conteúdo em FileListToCopy.txt                             | Configuração ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| raiz<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sub-página1/File3.csv<br>Sub-página1/File5.csv | **No conjunto de dados:**<br>- Caminho da pasta: `root/FolderA`<br><br>**Na fonte de atividade de cópia:**<br>- Caminho da lista de ficheiros: `root/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar, um ficheiro por linha com o caminho relativo ao caminho configurado no conjunto de dados. |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade getMetadata

Para saber mais detalhes sobre as propriedades, consulte a [atividade da GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eliminar propriedades de atividade

Para obter detalhes sobre as propriedades, verifique [a atividade de Eliminar](delete-activity.md)

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os modelos seguintes ainda são suportados como é para retrocompatibilidade. Sugere-se que utilize o novo modelo mencionado nas secções acima, e a UI de autoria da ADF passou a gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **FileShare** |Sim |
| folderPath | Caminho para a pasta. O filtro Wildcard é suportado, os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior. <br/><br/>Exemplos: rootfolder/subfolder/, ver mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). |Sim |
| fileName | **Nome ou filtro wildcard** para os ficheiros sob a especificada "pastaPath". Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais).<br/>- Exemplo 1: `"fileName": "*.csv"`<br/>- Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de ficheiro real tiver wildcard ou este char de fuga dentro. |Não |
| formato | Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ficheiros com um formato específico, suportam-se os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [formato Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e secções [de Formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para obter mais informações, consulte [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos suportados são: **GZip,** **Deflate,** **BZip2** e **ZipDeflate**.<br/>Os níveis suportados são: **Ideal** e **Mais rápido**. |Não |
| useTransfera | Especificar se deve utilizar o modo de transferência binário. Os valores são verdadeiros para o modo binário (padrão) e falsos para o ASCII. |Não |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique apenas **o apêndio.**<br>Para copiar um único ficheiro com um nome próprio, especifique **a pastaPata** com a peça de pasta e **o nome de ficheiro** com o nome do ficheiro.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique **a pastaPata** com a peça de pasta e **o nome de ficheiro** com o filtro wildcard.

>[!NOTE]
>Se estiver a utilizar a propriedade "fileFilter" para filtro de ficheiros, ainda é suportada como está, enquanto é sugerido que utilize a nova capacidade de filtro adicionada ao "fileName" que vai para a frente.

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem de origem de atividade de cópia de legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **FileSystemSource** |Sim |
| recursivo | Indica se os dados são lidos novamente a partir das sub-pastas ou apenas a partir da pasta especificada. Note quando a recursiva é definida como verdadeira e a pia é uma loja baseada em ficheiros, a pasta/sub-pasta vazia não será copiada/criada na pia.<br/>Os valores permitidos são: **verdadeiros** (padrão), **falsos** | Não |
| maxConcurrentConnections | O número de ligações a ligar ao armazém simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

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
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
