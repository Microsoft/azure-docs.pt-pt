---
title: Copiar dados de/para um sistema de ficheiros utilizando a Azure Data Factory
description: Saiba como copiar dados do sistema de ficheiros para lojas de dados de sink suportadas (ou) de lojas de dados de origem suportada para sistema de ficheiros utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 21329e3fada62036bc5b8d2bf6cdf6b3be5adad3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417397"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Copiar dados de ou para um sistema de ficheiros utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-file-system-connector.md)
> * [Versão atual](connector-file-system.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados de e para o sistema de ficheiros. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector do sistema de ficheiros é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, este conector do sistema de ficheiros suporta:

- Copiar ficheiros de/para a máquina local ou partilha de ficheiros de rede. Para utilizar uma partilha de ficheiros Linux, instale [o Samba](https://www.samba.org/) no seu servidor Linux.
- Copiar ficheiros utilizando a autenticação **do Windows.**
- Copiar ficheiros como estão ou analisar/gerar ficheiros com os [formatos de ficheiros suportados e os códigos](supported-file-formats-and-compression-codecs.md)de compressão .

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas para sistema de ficheiros.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao sistema de ficheiros:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para: **FileServer**. | Sim |
| anfitrião | Especifica o caminho raiz da pasta que pretende copiar. Use o personagem de fuga "\" para personagens especiais na corda. Consulte as definições de serviço ligados à [amostra e definições](#sample-linked-service-and-dataset-definitions) de conjunto de dados, por exemplo. | Sim |
| usado | Especifique a identificação do utilizador que tem acesso ao servidor. | Sim |
| palavra-passe | Especifique a palavra-passe para o utilizador (userid). Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

### <a name="sample-linked-service-and-dataset-definitions"></a>Definições de serviço ligados à amostra e conjunto de dados

| Cenário | "anfitrião" na definição de serviço ligado | "folderPath" na definição de conjunto de dados |
|:--- |:--- |:--- |
| Pasta local na máquina de tempo de integração: <br/><br/>Exemplos: D:\\ \* ou D:\folder\subpasta\\* |Em JSON:`D:\\`<br/>Na UI:`D:\` |Em JSON: `.\\` ou`folder\\subfolder`<br>Na UI: `.\` ou`folder\subfolder` |
| Pasta partilhada remota: <br/><br/>Exemplos: \\ \\\\myserver\\ \* \\ \\share\\ou\\\\myserver share folder subfolder\\* |Em JSON:`\\\\myserver\\share`<br/>Na UI:`\\myserver\share` |Em JSON: `.\\` ou`folder\\subfolder`<br/>Na UI: `.\` ou`folder\subfolder` |

>[!NOTE]
>Ao ser autor através da UI, não é necessário`\\`inserir o duplo backslash ( ) para escapar como faz através da JSON, especifique um único backslash.

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para sistema de ficheiros sob `location` definições no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade `location` do tipo em conjunto de dados deve ser definida para **FileServerLocation**. | Sim      |
| folderPath | O caminho para a pasta. Se pretender utilizar o wildcard para filtrar a pasta, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |
| fileName   | O nome do ficheiro sob a pasta dadaPath. Se pretender utilizar ficheiros wildcard para filtrar ficheiros, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |

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

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte e pia do sistema de ficheiros.

### <a name="file-system-as-source"></a>Sistema de ficheiros como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para sistema de ficheiros sob `storeSettings` definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade `storeSettings` do tipo em baixo deve ser definida para **FileServerReadSettings**. | Sim                                           |
| recursivo                | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Note que quando se recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia. Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres wildcard para filtrar as pastas de origem. <br>Os wildcards `*` permitidos são: (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro. <br>Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| nome wildcardFile         | O nome do ficheiro com caracteres wildcard sob a pasta dadaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards `*` permitidos são: (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou personagem único); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro.  Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). [Por favor, note que isto irá anular a definição de nome de ficheiro] | Sim, `fileName` se não for especificado no conjunto de dados |
| alteradoDatetimeStart    | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o `modifiedDatetimeEnd` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem o `modifiedDatetimeStart` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados. | Não                                            |
| alteradoDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                            |

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

### <a name="file-system-as-sink"></a>Sistema de ficheiros como pia

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para sistema de ficheiros em `storeSettings` definições em sumidouro de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade `storeSettings` do tipo em baixo deve ser definida para **FileServerWriteSettings**. | Sim      |
| copiarComportamento             | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- PreserveHierarchy (predefinição)</b>: Preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro alvo para a pasta-alvo.<br/><b>- Hierarquia flattena</b>: Todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não       |
| maxConcurrentConnections | O número das ligações para ligar à loja de dados simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não       |

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
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
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

### <a name="recursive-and-copybehavior-examples"></a>exemplos recursivos e copyBehavior

Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de valores recursivos e copyBehavior.

| recursivo | copiarComportamento | Estrutura de pasta de origem | Alvo resultante |
|:--- |:--- |:--- |:--- |
| true |preservar Hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo Pasta 1 é criada com a mesma estrutura que a fonte:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5. |
| true |achatar a hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + Ficheiro 5 conteúdos são fundidos num só ficheiro com nome de ficheiro gerado automaticamente |
| false |preservar Hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com File3, File4 e File5 não são captadas. |
| false |achatar a hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Subpasta1 com File3, File4 e File5 não são captadas. |
| false |mergeFiles | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos File1 + File2 são fundidos num ficheiro com nome de ficheiro gerado automaticamente. nome gerado automaticamente para File1<br/><br/>Subpasta1 com File3, File4 e File5 não são captadas. |

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
| folderPath | Caminho para a pasta. O filtro Wildcard é suportado, `*` os wildcards permitidos `?` são: (corresponde a zero ou mais caracteres) e (corresponde a zero ou personagem individual); usar `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga dentro. <br/><br/>Exemplos: pasta/subpasta/, ver mais exemplos no serviço ligado à [amostra e definições](#sample-linked-service-and-dataset-definitions) de conjunto de dados e exemplos de filtros de [pastas e ficheiros](#folder-and-file-filter-examples). |Não |
| fileName | **Nome ou filtro wildcard** para os ficheiros(s) sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros da pasta. <br/><br/>Para o filtro, os `*` wildcards permitidos são: (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou personagem individual).<br/>- Exemplo 1:`"fileName": "*.csv"`<br/>- Exemplo 2:`"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de ficheiro real tiver wildcard ou este char de fuga dentro.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída e preservar a **hierarquia** não é especificada no sumidouro de atividade, a atividade de cópia gera automaticamente o nome do ficheiro com o seguinte padrão: "*Dados.. atividade executar ID GUID]. [GUID se AHierarquia Achatada]. [formato se configurado]. [compressão se configurado]*", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; se copiar a partir de fonte tabular usando o nome da mesa em vez de consulta, o padrão de nome é " [nome de*mesa].[ formato]. [compressão se configurado]*", por exemplo, "MyTable.csv". |Não |
| alteradoDatetimeStart | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> Esteja ciente de que o desempenho global do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAs, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o `modifiedDatetimeEnd` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem o `modifiedDatetimeStart` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| Não |
| alteradoDatetimeEnd | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu `modifiedDatetimeStart` último `modifiedDatetimeEnd`tempo modificado estiver dentro do intervalo de tempo entre e . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> Esteja ciente de que o desempenho global do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAs, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o `modifiedDatetimeEnd` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando `modifiedDatetimeEnd` tem o `modifiedDatetimeStart` valor da data mas é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| Não |
| formato | Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Formato Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [Formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para mais informações, consulte [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)de compressão .<br/>Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**.<br/>Os níveis suportados são: **Optimal** e **Fastest**. |Não |

>[!TIP]
>Para copiar todos os ficheiros sob uma pasta, especifique apenas **pastaPath.**<br>Para copiar um único ficheiro com um nome dado, especifique **a pastaPath** com peça de pasta e nome de **ficheiro** com nome de ficheiro.<br>Para copiar um subconjunto de ficheiros sob uma pasta, especifique **a pastaPath** com peça de pasta e nome de **ficheiro** com filtro wildcard.

>[!NOTE]
>Se estava a utilizar a propriedade "fileFilter" para filtro de ficheiros, ainda é suportado como está, enquanto é sugerido que utilize a nova capacidade de filtro adicionada ao "fileName" que vai para a frente.

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

### <a name="legacy-copy-activity-sink-model"></a>Modelo de pia de atividade de cópia legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para: **FileSystemSink** |Sim |
| copiarComportamento | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- PreserveHierarchy (predefinição)</b>: preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro alvo para a pasta-alvo.<br/><b>- Hierarquia flatten:</b>todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm um nome gerado automaticamente. <br/><b>- MergeFiles</b>: funde todos os ficheiros da pasta de origem para um ficheiro. Não é efetuada qualquer desduplicação de registo durante a fusão. Se o nome do ficheiro for especificado, o nome do ficheiro fundido será o nome especificado; caso contrário, seria o nome de ficheiro gerado automaticamente. | Não |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

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

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
