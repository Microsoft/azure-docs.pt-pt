---
title: Copiar dados de/para Azure File Storage
description: Saiba como copiar dados do Azure File Storage para lojas de dados de sumidouros suportados (ou) de lojas de dados de origem suportadas para Azure File Storage utilizando a Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 43d5b787152c3c14ef11af513907fc5f41fe7cf0
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737766"
---
# <a name="copy-data-from-or-to-azure-file-storage-by-using-azure-data-factory"></a>Copiar dados de ou para Azure File Storage utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados de e para o Azure File Storage. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de armazenamento de ficheiros Azure é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, este conector de armazenamento de ficheiros Azure suporta a cópia de ficheiros como está ou analisa/gerar ficheiros com os [formatos de ficheiros suportados e os codecs de compressão](supported-file-formats-and-compression-codecs.md).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do Azure File Storage.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao armazenamento de ficheiros Azure:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzureFileStorage**. | Yes |
| anfitrião | Especifica o ponto final de armazenamento de ficheiros Azure como: <br/>-Utilização de UI: especificar`\\<storage name>.file.core.windows.net\<file service name>`<br/>- Utilização de JSON: `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"` . | Yes |
| userid | Especifique o utilizador para aceder ao Armazenamento de Ficheiros Azure como: <br/>-Utilização de UI: especificar`AZURE\<storage name>`<br/>-Utilizando json: `"userid": "AZURE\\<storage name>"` . | Yes |
| palavra-passe | Especifique a chave de acesso ao armazenamento. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |Não para a fonte, sim para a pia |

>[!IMPORTANT]
> - Para copiar dados no Azure File Storage utilizando o Tempo de Execução da Integração Azure, criar explicitamente [um Azure IR](create-azure-integration-runtime.md#create-azure-ir) com a localização do seu Armazenamento de Ficheiros e associar no serviço ligado como o seguinte exemplo.
> - Para copiar dados de/para Azure File Storage utilizando o tempo de execução de integração auto-hospedado fora de Azure, lembre-se de abrir a porta TCP de saída 445 na sua rede local.

>[!TIP]
>Ao utilizar o ADF UI para autoria, pode encontrar a entrada específica de "Azure File Storage" para criação de serviços ligados, que por baixo gera `FileServer` objeto tipo.

**Exemplo:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "host": "\\\\<storage name>.file.core.windows.net\\<file service name>",
            "userid": "AZURE\\<storage name>",
            "password": {
                "type": "SecureString",
                "value": "<storage access key>"
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

As seguintes propriedades são suportadas para armazenamento de ficheiros Azure em `location` configurações no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade tipo `location` em conjunto de dados deve ser definida para **FileServerLocation**. | Yes      |
| folderPath | O caminho para a pasta. Se pretender utilizar o wildcard para filtrar a pasta, ignore esta definição e especifique nas definições de fonte de atividade. | No       |
| fileName   | O nome do ficheiro sob a pasta DadaPa. Se pretender utilizar o wildcard para filtrar ficheiros, ignore esta definição e especifique nas definições de origem da atividade. | No       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure File Storage linked service name>",
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

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte de armazenamento de ficheiros Azure e pia.

### <a name="azure-file-storage-as-source"></a>Armazenamento de ficheiros Azure como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para armazenamento de ficheiros Azure em `storeSettings` configurações na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade tipo em baixo `storeSettings` deve ser definida para **FileServerReadSettings**. | Yes                                           |
| ***Localize os ficheiros para copiar:*** |  |  |
| OPÇÃO 1: caminho estático<br> | Cópia do caminho da pasta/ficheiro especificado no conjunto de dados. Se pretender copiar todos os ficheiros de uma pasta, especificar ainda `wildcardFileName` como `*` . |  |
| OPÇÃO 2: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard para filtrar pastas de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior. <br>Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | No                                            |
| OPÇÃO 2: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard sob a pasta DadaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior.  Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Yes |
| OPÇÃO 3: uma lista de ficheiros<br>- fileListPath | Indica copiar um determinado conjunto de ficheiros. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar, um ficheiro por linha que é o caminho relativo para o caminho configurado no conjunto de dados.<br/>Ao utilizar esta opção, não especifique o nome do ficheiro no conjunto de dados. Ver mais exemplos em [exemplos da lista de ficheiros.](#file-list-examples) |No |
| ***Definições adicionais:*** |  | |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Note que quando a recursiva é definida como verdadeira e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobrador vazio não é copiado ou criado na pia. <br>Os valores permitidos são **verdadeiros** (padrão) e **falsos.**<br>Esta propriedade não se aplica quando se `fileListPath` configura. |No |
| eliminarFilesAfterCompletion | Indica se os ficheiros binários serão eliminados da loja de origem depois de se mudarem com sucesso para a loja de destino. A eliminação do ficheiro é por ficheiro, pelo que quando a atividade da cópia falhar, verá que alguns ficheiros já foram copiados para o destino e eliminados da fonte, enquanto outros ainda permanecem na loja de origem. <br/>Esta propriedade é válida apenas em cenário de cópia binária, onde as lojas de fontes de dados são Blob, ADLS Gen1, ADLS Gen2, S3, Google Cloud Storage, File, Azure File, SFTP ou FTP. O valor predefinido: falso. |No |
| modificadoDatetimeStart    | Filtro de ficheiros com base no atributo: Última Modificação. <br>Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário utc no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser NUAS, o que significa que nenhum filtro de atributo de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem valor de data mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é maior ou igual com o valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem valor de data mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.<br/>Esta propriedade não se aplica quando se `fileListPath` configura. | No                                            |
| modificadoDatetimeEnd      | O mesmo que acima.                                               | No                                            |
| maxConcurrentConnections | O número de ligações a ligar ao armazém simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | No                                            |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
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
                    "type": "FileServerReadSettings",
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

### <a name="azure-file-storage-as-sink"></a>Armazenamento de arquivo azure como pia

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para armazenamento de ficheiros Azure em `storeSettings` configurações no lavatório de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade tipo em baixo `storeSettings` deve ser definida para **FileServerWriteSettings**. | Yes      |
| copyOportundo             | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- Preservar AHierarquia (predefinição)</b>: Preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro alvo para a pasta alvo.<br/><b>- FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível da pasta alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | No       |
| maxConcurrentConnections | O número de ligações para ligar ao armazenamento de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | No       |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
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
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
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
| raiz<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sub-página1/File3.csv<br>Sub-página1/File5.csv | **No conjunto de dados:**<br>- Caminho da pasta:`root/FolderA`<br><br>**Na fonte de atividade de cópia:**<br>- Caminho da lista de ficheiros:`root/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar, um ficheiro por linha com o caminho relativo ao caminho configurado no conjunto de dados. |

### <a name="recursive-and-copybehavior-examples"></a>recursivo e copySA examples de comportamento

Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de valores recursivos e de cópias.

| recursivo | copyOportundo | Estrutura de pasta de origem | Alvo resultante |
|:--- |:--- |:--- |:--- |
| true |preservarHierarquia | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo1 é criada com a mesma estrutura que a fonte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |achatamento | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File5 |
| true |fusõesFilias | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + O conteúdo do Ficheiro 5 é fundido num ficheiro com nome de ficheiro autogerido |
| false |preservarHierarquia | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A sub-dobra1 com File3, File4 e File5 não são recolhidas. |
| false |achatamento | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/><br/>A sub-dobra1 com File3, File4 e File5 não são recolhidas. |
| false |fusõesFilias | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;O conteúdo do Ficheiro1 + Ficheiro2 é fundido num ficheiro com nome de ficheiro autogerido. nome autogerado para File1<br/><br/>A sub-dobra1 com File3, File4 e File5 não são recolhidas. |

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

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **FileShare** |Yes |
| folderPath | Caminho para a pasta. <br/><br/>O filtro Wildcard é suportado, os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais); use `^` para escapar se o nome da sua pasta tiver wildcard ou este char de fuga no interior. <br/><br/>Exemplos: rootfolder/subfolder/, ver mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). |Yes |
| fileName | **Nome ou filtro wildcard** para os ficheiros sob a especificada "pastaPath". Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais).<br/>- Exemplo 1:`"fileName": "*.csv"`<br/>- Exemplo 2:`"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de ficheiro real tiver wildcard ou este char de fuga dentro.<br/><br/>Quando o data de ficheiro não é especificado para um conjunto de dados de saída e **a preservaçãoHierarquia** não é especificado na pia da atividade, a atividade da cópia gera automaticamente o nome do ficheiro com o seguinte padrão: "*Dados.". atividade executar ID GUID]. [GUID se FlattenHierarchy]. [formato se configurado]. [compressão se configurado]*", por exemplo "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; se copiar de fonte tabular usando o nome da mesa em vez de consultar, o padrão de nome é "*[nome de mesa].[ formato]. [compressão se configurado]*", por exemplo "MyTable.csv". |No |
| modificadoDatetimeStart | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário utc no formato de "2018-12-01T05:00:00Z". <br/><br/> Esteja ciente de que o desempenho geral do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUS, o que significa que nenhum filtro de atributo de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem valor de data mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é maior ou igual com o valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem valor de data mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| No |
| modificadoDatetimeEnd | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário utc no formato de "2018-12-01T05:00:00Z". <br/><br/> Esteja ciente de que o desempenho geral do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUS, o que significa que nenhum filtro de atributo de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tem valor de data mas é `modifiedDatetimeEnd` NU, significa que os ficheiros cujo último atributo modificado é maior ou igual com o valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem valor de data mas é `modifiedDatetimeStart` NU, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| No |
| formato | Se pretender **copiar ficheiros como-está** entre lojas baseadas em ficheiros (cópia binária), salte a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, suportam-se os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte [formato de texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [formato Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e secções [de Formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para obter mais informações, consulte [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos suportados são: **GZip,** **Deflate,** **BZip2**e **ZipDeflate**.<br/>Os níveis suportados são: **Ideal** e **Mais rápido**. |No |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique apenas **o apêndio.**<br>Para copiar um único ficheiro com um nome próprio, especifique **a pastaPata** com a peça de pasta e **o nome de ficheiro** com o nome do ficheiro.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique **a pastaPata** com a peça de pasta e **o nome de ficheiro** com o filtro wildcard.

>[!NOTE]
>Se estiver a utilizar a propriedade "fileFilter" para filtro de ficheiros, ainda é suportada como está, enquanto é sugerido que utilize a nova capacidade de filtro adicionada ao "fileName" que vai para a frente.

**Exemplo:**

```json
{
    "name": "AzureFileStorageDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<Azure File Storage linked service name>",
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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem de origem de atividade de cópia de legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **FileSystemSource** |Yes |
| recursivo | Indica se os dados são lidos novamente a partir das sub-pastas ou apenas a partir da pasta especificada. Note quando a recursiva é definida como verdadeira e a pia é uma loja baseada em ficheiros, a pasta/sub-pasta vazia não será copiada/criada na pia.<br/>Os valores permitidos são: **verdadeiros** (padrão), **falsos** | No |
| maxConcurrentConnections | O número de ligações a ligar ao armazém simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | No |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure File Storage input dataset name>",
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

### <a name="legacy-copy-activity-sink-model"></a>Modelo de pia de atividade de cópia legacy

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do lavatório de atividade de cópia deve ser definida para: **FileSystemSink** |Yes |
| copyOportundo | Define o comportamento da cópia quando a fonte é ficheiros da loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- Preservar AHierarquia (predefinição)</b>: preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro alvo para a pasta alvo.<br/><b>- FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível de pasta alvo. Os ficheiros-alvo têm nome autogerado. <br/><b>- MergeFiles</b>: funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido será o nome especificado; caso contrário, seria nome de ficheiro autogerado. | No |
| maxConcurrentConnections | O número de ligações a ligar ao armazém simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | No |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure File Storage output dataset name>",
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

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
