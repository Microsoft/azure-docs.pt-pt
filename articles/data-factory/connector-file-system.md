---
title: Copiar dados de/para um sistema de arquivos usando Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do sistema de arquivos para armazenamentos de dados de coletor com suporte (ou) de armazenamentos de dados de origem com suporte para o sistema de arquivos usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 6b1e1dfec69d73b7fe2648a1eb9ead2ae4622bc5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897756"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Copiar dados de ou para um sistema de arquivos usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-file-system-connector.md)
> * [Versão atual](connector-file-system.md)

Este artigo descreve como copiar dados de e para o sistema de arquivos. Para saber mais sobre Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do sistema de arquivos tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, esse conector do sistema de arquivos dá suporte a:

- Copiando arquivos de/para o computador local ou compartilhamento de arquivos de rede. Para usar um compartilhamento de arquivos do Linux, instale o [samba](https://www.samba.org/) no seu servidor Linux.
- Copiando arquivos usando a autenticação do **Windows** .
- Copiando arquivos no estado em que se encontram ou analisando/gerando arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao sistema de arquivos.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do sistema de arquivos:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida **como:** . | Sim |
| hospedeira | Especifica o caminho raiz da pasta que você deseja copiar. Use o caractere de escape "\" para caracteres especiais na cadeia de caracteres. Consulte [amostra de serviço vinculado e definições de conjunto](#sample-linked-service-and-dataset-definitions) de exemplos. | Sim |
| ID | Especifique a ID do usuário que tem acesso ao servidor. | Sim |
| palavra-passe | Especifique a senha para o usuário (UserID). Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

### <a name="sample-linked-service-and-dataset-definitions"></a>Exemplo de definições de conjunto de exemplos e de serviço vinculado

| Cenário | "host" na definição de serviço vinculado | "folderPath" na definição do conjunto de |
|:--- |:--- |:--- |
| Pasta local no computador Integration Runtime: <br/><br/>Exemplos: D:\\\* ou D:\folder\subfolder\\* |Em JSON: `D:\\`<br/>Na interface do usuário: `D:\` |Em JSON: `.\\` ou `folder\\subfolder`<br>Na interface do usuário: `.\` ou `folder\subfolder` |
| Pasta compartilhada remota: <br/><br/>Exemplos: \\\\meuservidor\\compartilhamento\\\* ou \\\\MyServer\\compartilhamento\\pasta\\\\subpasta* |Em JSON: `\\\\myserver\\share`<br/>Na interface do usuário: `\\myserver\share` |Em JSON: `.\\` ou `folder\\subfolder`<br/>Na interface do usuário: `.\` ou `folder\subfolder` |

>[!NOTE]
>Ao criar por meio da interface do usuário, não é necessário inserir barras invertidas duplas (`\\`) para escapar como você faz por meio de JSON, especifique uma barra invertida simples.

**Exemplo:**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . 

- Para **parquet, texto delimitado, JSON, Avro e formato binário**, consulte a seção [parquet, texto delimitado, JSON, Avro e DataSet de formato binário](#format-based-dataset) .
- Para outros formatos, como o **formato Orc**, consulte outra seção do conjunto de um [formato](#other-format-dataset) .

### <a name="format-based-dataset"></a>Parquet, texto delimitado, JSON, Avro e conjunto de DataSet de formato binário

Para copiar dados de e para **parquet, texto delimitado, JSON, Avro e formato binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), formato de [Avro](format-avro.md) e artigo de [formato binário](format-binary.md) em conjunto de dados com base em formato e configurações com suporte . As propriedades a seguir têm suporte para o sistema de arquivos em configurações `location` no conjunto de base de formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade Type em `location` no DataSet deve ser definida como **FileServerLocation**. | Sim      |
| folderPath | O caminho para a pasta. Se você quiser usar curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o folderPath fornecido. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |

> [!NOTE]
> O conjunto de dados do tipo **FileShare** com parquet/formato de texto mencionado na próxima seção ainda tem suporte como é para a atividade de cópia/pesquisa/GetMetadata para compatibilidade com versões anteriores, mas não funciona com o fluxo de dados de mapeamento. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
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

### <a name="other-format-dataset"></a>Outro conjunto de DataSet de formato

Para copiar dados de e para o sistema de arquivos no **formato Orc**, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **FileShare** |Sim |
| folderPath | Caminho para a pasta. Há suporte para o filtro curinga, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br/><br/>Exemplos: RootFolder/subfolder/, veja mais exemplos em [exemplo de serviço vinculado e definições de conjunto](#sample-linked-service-and-dataset-definitions) de informações e exemplos de filtro de pasta e [arquivo](#folder-and-file-filter-examples). |Não |
| fileName | **Nome ou filtro curinga** para os arquivos sob o "FolderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de pontos apontará para todos os arquivos na pasta. <br/><br/>Para filtros, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome de arquivo real tiver curinga ou este caractere de escape dentro de.<br/><br/>Quando fileName não é especificado para um conjunto de dados de saída e **preserveHierarchy** não é especificado no coletor de atividade, a atividade de cópia gera automaticamente o nome do arquivo com o seguinte padrão: "*Data. [ GUID de ID de execução de atividade]. [GUID If FlattenHierarchy]. [Formatar se configurado]. [compactação se configurada]* ", por exemplo," Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz "; Se você copiar da fonte de tabela usando o nome da tabela em vez da consulta, o padrão de nome será " *[nome da tabela]. [ formato]. [compactação se configurada]* ", por exemplo," MyTable. csv ". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem o valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem o valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| ao | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, há suporte para os seguintes tipos de formato de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format)e seções de [formato parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Não (somente para o cenário de cópia binária) |
| çã | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [formatos de arquivo com suporte e codecs de compactação](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**.<br/>Os níveis com suporte são: **ideal** e **mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **FolderPath** apenas.<br>Para copiar um único arquivo com um nome específico, especifique **FolderPath** com a parte da **pasta e o nome do** arquivo com o nome do arquivos.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **FolderPath** com a parte da pasta e o **nome do arquivo** com o filtro curinga.

>[!NOTE]
>Se você estiver usando a propriedade "FileFilter" para o filtro de arquivo, ainda haverá suporte como está, enquanto você deve usar o novo recurso de filtro adicionado ao "fileName" no futuro.

**Exemplo:**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do sistema de arquivos.

### <a name="file-system-as-source"></a>Sistema de arquivos como fonte

- Para copiar de **parquet, de texto delimitado, JSON, Avro e formato binário**, consulte a seção [parquet, texto delimitado, JSON, Avro e fonte de formato binário](#format-based-source) .
- Para copiar de outros formatos, como o **formato Orc**, consulte [outra seção fonte de formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, texto delimitado, JSON, Avro e fonte de formato binário

Para copiar dados de **parquet, texto delimitado, JSON, Avro e formato binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), formato de [Avro](format-avro.md) e artigo de [formato binário](format-binary.md) na fonte da atividade de cópia baseada em formato e com suporte Configurações. As propriedades a seguir têm suporte para o sistema de arquivos em configurações `storeSettings` em fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade Type em `storeSettings` deve ser definida como **FileServerReadSetting**. | Sim                                           |
| recursiva                | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br>Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome do arquivo com caracteres curinga sob o folderPath/wildcardFolderPath fornecido para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro.  Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). [Observe que isso anulará a configuração do nome de arquivo] | Sim se `fileName` não for especificado no DataSet |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem o valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado. | Não                                            |
| modifiedDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto parquet/delimitado, a fonte da atividade de cópia do tipo **FileSystemProvider** mencionada na próxima seção ainda tem suporte como está para compatibilidade com versões anteriores. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
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
                    "type": "FileServerReadSetting",
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

Para copiar dados do sistema de arquivos no **formato Orc**, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **FileSystemName** |Sim |
| recursiva | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observação Quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, a pasta/subpasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
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

### <a name="file-system-as-sink"></a>Sistema de arquivos como coletor

- Para copiar para **parquet, texto delimitado, JSON, Avro e formato binário**, consulte a seção [parquet, texto delimitado, JSON, Avro e coletor de formato binário](#format-based-sink) .
- Para copiar para outros formatos, como o **formato Orc**, consulte outra seção de [coletor de formato](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, texto delimitado, JSON, Avro e coletor de formato binário

Para copiar dados em **formato parquet, de texto delimitado, JSON, Avro e binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), [formato de Avro](format-avro.md) e artigo de [formato binário](format-binary.md) no coletor de atividade de cópia com base em formato e com suporte Configurações. As propriedades a seguir têm suporte para o sistema de arquivos em configurações `storeSettings` no coletor de cópia com base em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade Type em `storeSettings` deve ser definida como **FileServerWriteSetting**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a origem é arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Os valores permitidos são:<br/><b>-PreserveHierarchy (padrão)</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm nomes gerados automaticamente. <br/><b>-MergeFiles</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, será um nome de arquivo gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não       |

> [!NOTE]
> Para o formato de texto parquet/delimitado, o coletor de atividade de cópia do tipo **FileSystemSink** mencionado na próxima seção ainda tem suporte como está para compatibilidade com versões anteriores. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Outro coletor de formato

Para copiar dados para o sistema de arquivos no **formato Orc**, há suporte para as seguintes propriedades na seção **Sink** :

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do coletor da atividade de cópia deve ser definida como: **FileSystemSink** |Sim |
| copyBehavior | Define o comportamento de cópia quando a origem é de arquivos do armazenamento de dados baseado em arquivo.<br/><br/>Os valores permitidos são:<br/><b>-PreserveHierarchy (padrão)</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm o nome gerado automaticamente. <br/><b>-MergeFiles</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, seria um nome de arquivo gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
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

### <a name="recursive-and-copybehavior-examples"></a>exemplos recursivos e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursivos e copyBehavior.

| recursiva | copyBehavior | Estrutura da pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a mesma estrutura que a origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | O Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | O Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo de file1 + arquivo2 + arquivo3 + Arquivo4 + arquivo 5 é mesclado em um arquivo com o nome de arquivo gerado automaticamente |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para arquivo2<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo de file1 + arquivo2 é mesclado em um arquivo com o nome de arquivo gerado automaticamente. nome gerado automaticamente para file1<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não são selecionados. |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter detalhes sobre as propriedades, marque a [atividade de exclusão](delete-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
