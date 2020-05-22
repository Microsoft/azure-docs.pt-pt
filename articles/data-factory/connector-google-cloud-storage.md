---
title: Copiar dados do Google Cloud Storage utilizando a Azure Data Factory
description: Saiba como copiar dados do Google Cloud Storage para armazenar dados de sumidouro suportado utilizando a Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f6b488fc4817e360ed512441f67d2e00356e6134
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758610"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Copiar dados do Google Cloud Storage utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados do Google Cloud Storage (GCS). Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector google Cloud Storage é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Especificamente, este conector google Cloud Storage suporta copiar ficheiros como está ou analisar ficheiros com os [formatos de ficheiros suportados e codificadores](supported-file-formats-and-compression-codecs.md)de compressão . Aproveita a interoperabilidade compatível com S3 da GCS.

## <a name="prerequisites"></a>Pré-requisitos

A seguinte configuração é necessária na sua conta de Armazenamento de Nuvem do Google:

1. Ativar a interoperabilidade para a sua conta de Armazenamento de Nuvem do Google
2. Desprecie o projeto predefinido que contém os dados que pretende copiar a partir do balde de GCS alvo
3. Crie uma conta de serviço e defina os níveis certos de permissões usando o Cloud IAM no GCP 
4. Gere as chaves de acesso para esta conta de serviço

![Recuperar chave de acesso para o Armazenamento de Nuvem do Google](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Permissões obrigatórias

Para copiar dados do Google Cloud Storage, certifique-se de que concedeu as permissões necessárias. As permissões definidas na conta de serviço podem `storage.buckets.get` `storage.buckets.list` conter, para operações de `storage.objects.get` objetos.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do Google Cloud Storage.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao Google Cloud Storage:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para **o GoogleCloudStorage**. | Sim |
| acessoKeyId | Identificação da chave de acesso secreta. Para encontrar a chave de acesso e segredo, consulte [os pré-requisitos.](#prerequisites) |Sim |
| secretAccessKey | A chave de acesso secreta em si. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim |
| serviçoUrl | Especifique o ponto final gGcS personalizado como **`https://storage.googleapis.com`** . | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para o Google Cloud Storage em `location` definições em conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade do tipo em conjunto de `location` dados deve ser definida para o **GoogleCloudStorageLocation**. | Sim      |
| bucketName | O nome do balde gcs.                                          | Sim      |
| folderPath | O caminho para pasta sob o balde dado. Se pretender utilizar o wildcard para filtrar a pasta, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |
| fileName   | O nome do ficheiro sob o balde dado + pastaPath. Se pretender utilizar ficheiros wildcard para filtrar ficheiros, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |

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
                "type": "GoogleCloudStorageLocation",
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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte do Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Armazenamento de Nuvem do Google como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para o Google Cloud Storage em `storeSettings` definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| tipo                     | A propriedade do tipo em baixo `storeSettings` deve ser definida para **googleCloudStorageReadSettings**. | Sim                                                         |
| ***Localize os ficheiros para copiar:*** |  |  |
| OPÇÃO 1: caminho estático<br> | Cópia do conjunto de dados especificado no conjunto de dados. Se pretender copiar todos os ficheiros de um balde/pasta, especifique adicionalmente `wildcardFileName` como `*` . |  |
| OPÇÃO 2: Prefixo GCS<br>- prefixo | Prefixo para o nome da chave GCS sob o balde dado configurado no conjunto de dados para filtrar ficheiros GCS de origem. As teclas GCS cujo nome começa são `bucket_in_dataset/this_prefix` selecionadas. Utiliza o filtro lateral de serviço do GCS que proporciona um melhor desempenho do que o filtro wildcard. | Não |
| OPÇÃO 3: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard sob o balde dado configurado no conjunto de dados para filtrar as pastas de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou personagem único); use `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga no seu interior. <br>Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 3: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard sob o balde dado + pastaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou personagem único); use `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga no seu interior.  Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 3: uma lista de ficheiros<br>- fileListPath | Indica copiar um determinado conjunto de ficheiros. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar, um ficheiro por linha que é o caminho relativo para o caminho configurado no conjunto de dados.<br/>Ao utilizar esta opção, não especifique o nome do ficheiro no conjunto de dados. Veja mais exemplos nos [exemplos da lista de ficheiros](#file-list-examples). |Não |
| ***Configurações adicionais:*** |  | |
| recursivo | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Note que quando se recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia. <br>Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**.<br>Esta propriedade não se aplica quando configura `fileListPath` . |Não |
| alteradoDatetimeStart    | Filtro de ficheiros com base no atributo: Última Modificação. <br>Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando tem o valor da `modifiedDatetimeStart` data mas `modifiedDatetimeEnd` é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando tem o valor da `modifiedDatetimeEnd` data mas `modifiedDatetimeStart` é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.<br/>Esta propriedade não se aplica quando configura `fileListPath` . | Não                                            |
| alteradoDatetimeEnd      | O mesmo que acima.                                               | Não                                                          |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                                          |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "GoogleCloudStorageReadSettings",
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

| balde | chave | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| balde | `Folder*/*` | false | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| balde | `Folder*/*` | true | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| balde | `Folder*/*.csv` | false | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| balde | `Folder*/*.csv` | true | balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de ficheiros

Esta secção descreve o comportamento resultante da utilização do caminho da lista de ficheiros na fonte de atividade da cópia.

Assumindo que tem a seguinte estrutura de pasta de origem e que pretende copiar os ficheiros em negrito:

| Estrutura de origem da amostra                                      | Conteúdo em FileListToCopy.txt                             | Configuração ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| balde<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subpasta1/File3.csv<br>Subpasta1/File5.csv | **No conjunto de dados:**<br>- Balde:`bucket`<br>- Caminho das pastas:`FolderA`<br><br>**Na fonte de atividade de cópia:**<br>- Caminho da lista de ficheiros:`bucket/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar, um ficheiro por linha com o caminho relativo para o caminho configurado no conjunto de dados. |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Obtenha propriedades de atividadede Metadados

Para saber mais detalhes sobre as propriedades, consulte [a atividade do GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eliminar propriedades de atividade

Para saber detalhes sobre as propriedades, consulte a [atividade de Eliminar](delete-activity.md)

## <a name="legacy-models"></a>Modelos legados

Se estava a usar o conector Amazon S3 para copiar dados do Google Cloud Storage, ainda é suportado como é para retrocompatibilidade. É-lhe sugerido que utilize o novo modelo mencionado nas secções acima, e a UI de autoria da ADF mudou para gerar o novo modelo.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados que são suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
