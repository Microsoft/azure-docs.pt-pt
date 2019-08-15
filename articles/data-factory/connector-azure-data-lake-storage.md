---
title: Copiar dados de ou para Azure Data Lake Storage Gen2 usando Data Factory | Microsoft Docs
description: Saiba como copiar dados de e para Azure Data Lake Storage Gen2 usando Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 1882e994c5d062d3ca841025edb61965f7eb0aa0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967059"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiar dados de ou para Azure Data Lake Storage Gen2 usando Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) é um conjunto de recursos dedicados à análise de Big Data interno do [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md). Você pode usá-lo para fazer a interface com seus dados usando paradigmas de armazenamento de objeto e de sistema de arquivos.

Este artigo descreve como copiar dados de e para Azure Data Lake Storage Gen2. Para saber mais sobre o Azure Data Factory, leia os [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de Azure Data Lake Storage Gen2 tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com a [origem com suporte ou a matriz de coletor](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, com esse conector, você pode:

- Copie dados usando chave de conta, entidade de serviço ou identidades gerenciadas para autenticações de recursos do Azure.
- Copie arquivos como estão ou analise ou gere arquivos com [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Se você habilitar o namespace hierárquico, no momento não haverá interoperabilidade entre operações entre o blob e as APIs de Data Lake Storage Gen2. Se você clicar no erro "ErrorCode = FilesystemNotFound" com a mensagem "o sistema de arquivos especificado não existe", ele será causado pelo sistema de arquivos de coletor especificado que foi criado por meio da API de BLOB em vez de Data Lake Storage Gen2 API em outro lugar. Para corrigir o problema, especifique um novo sistema de arquivos com um nome que não exista como o nome de um contêiner de BLOBs. Em seguida, Data Factory cria automaticamente esse sistema de arquivos durante a cópia de dados.

>[!NOTE]
>Se você habilitar a opção **permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento** nas configurações do firewall do armazenamento do Azure, o Azure Integration Runtime não se conectará ao data Lake Storage Gen2 e mostrará um erro proibido. A mensagem de erro é exibida porque Data Factory não é tratado como um serviço confiável da Microsoft. Use o Integration Runtime de hospedagem interna para se conectar.

## <a name="get-started"></a>Introdução

>[!TIP]
>Para obter um passo a passo de como usar o conector de Data Lake Storage Gen2, consulte [carregar dados em Azure data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem informações sobre as propriedades que são usadas para definir Data Factory entidades específicas para Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

O conector de Azure Data Lake Storage Gen2 dá suporte aos seguintes tipos de autenticação. Consulte as seções correspondentes para obter detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação do principal de serviço](#service-principal-authentication)
- [Identidades geridas para a autenticação de recursos do Azure](#managed-identity)

>[!NOTE]
>Ao usar o polybase para carregar dados em SQL Data Warehouse, se o Data Lake Storage Gen2 de origem estiver configurado com o ponto de extremidade de rede virtual, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo polybase. Consulte a seção [autenticação de identidade gerenciada](#managed-identity) com mais pré-requisitos de configuração.

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para utilizar a autenticação de chave de conta de armazenamento, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para Data Lake Storage Gen2 com o `https://<accountname>.dfs.core.windows.net`padrão de. | Sim |
| accountKey | Chave de conta para Data Lake Storage Gen2. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o armazenamento de dados estiver em uma rede privada. Se essa propriedade não for especificada, o tempo de execução de integração do Azure padrão será usado. |Não |

>[!NOTE]
>Não há suporte para o ponto de extremidade do sistema de arquivos ADLS secundário ao usar a autenticação de chave de conta. Você pode usar outros tipos de autenticação.

**Example:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para usar a autenticação de entidade de serviço, siga estas etapas.

1. Registre uma entidade de aplicativo no Azure Active Directory (AD do Azure) seguindo as etapas em [registrar seu aplicativo com um locatário do Azure ad](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

2. Conceda a permissão apropriada à entidade de serviço. Saiba mais sobre como a permissão funciona em Data Lake Storage Gen2 de [listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Como fonte**: No Gerenciador de Armazenamento, conceda pelo menos permissão de **execução** a partir do sistema de arquivos de origem, juntamente com a permissão de **leitura** para os arquivos a serem copiados. Como alternativa, no controle de acesso (IAM), conceda pelo menos a função de **leitor de dados de blob de armazenamento** .
    - **Como coletor**: No Gerenciador de Armazenamento, conceda pelo menos a permissão de **execução** a partir do sistema de arquivos do coletor, juntamente com a permissão de **gravação** para a pasta do coletor. Como alternativa, no controle de acesso (IAM), conceda pelo menos a função de **colaborador de dados de blob de armazenamento** .

>[!NOTE]
>Para listar pastas a partir do nível de conta ou para testar a conexão, você precisa definir a permissão da entidade de serviço que está sendo concedida à **conta de armazenamento com a permissão "leitor de dados de blob de armazenamento" em iam**. Isso é verdadeiro quando você usa:
>- **Ferramenta copiar dados** para criar pipeline de cópia.
>- **Data Factory interface do usuário** para testar a conexão e navegar pelas pastas durante a criação. 
>Se você tiver preocupações com a concessão de permissão no nível da conta, durante a criação, ignorar a conexão de teste e inserir um caminho pai com a permissão concedida, escolha procurar o caminho especificado. A atividade de cópia funciona contanto que a entidade de serviço seja concedida com a permissão adequada nos arquivos a serem copiados.

Essas propriedades têm suporte para o serviço vinculado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para Data Lake Storage Gen2 com o `https://<accountname>.dfs.core.windows.net`padrão de. | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como um `SecureString` para armazená-lo com segurança em data Factory. Ou, você pode [fazer referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recupere-o passando o mouse no canto superior direito do portal do Azure. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o armazenamento de dados estiver em uma rede privada. Se não for especificado, o tempo de execução de integração do Azure padrão será usado. |Não |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identidades geridas para a autenticação de recursos do Azure

Uma fábrica de dados pode ser associada com um [identidade de recursos do Azure gerida](data-factory-service-identity.md), que representa esta fábrica de dados específicos. Você pode usar essa identidade gerenciada diretamente para Data Lake Storage Gen2 autenticação, semelhante ao uso de sua própria entidade de serviço. Ele permite que essa fábrica designada acesse e copie dados de ou para seu Data Lake Storage Gen2.

Para usar identidades gerenciadas para a autenticação de recursos do Azure, siga estas etapas.

1. [Recupere o data Factory informações de identidade gerenciadas](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor da **ID do aplicativo de identidade de serviço** gerada junto com sua fábrica.

2. Conceda a permissão adequada à identidade gerenciada. Saiba mais sobre como a permissão funciona em Data Lake Storage Gen2 de [listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Como fonte**: No Gerenciador de Armazenamento, conceda pelo menos permissão de **execução** a partir do sistema de arquivos de origem, juntamente com a permissão de **leitura** para os arquivos a serem copiados. Como alternativa, no controle de acesso (IAM), conceda pelo menos a função de **leitor de dados de blob de armazenamento** .
    - **Como coletor**: No Gerenciador de Armazenamento, conceda pelo menos a permissão de **execução** a partir do sistema de arquivos do coletor, juntamente com a permissão de **gravação** para a pasta do coletor. Como alternativa, no controle de acesso (IAM), conceda pelo menos a função de **colaborador de dados de blob de armazenamento** .

>[!NOTE]
>Para listar pastas a partir do nível de conta ou para testar a conexão, você precisa definir a permissão da identidade gerenciada que está sendo concedida à **conta de armazenamento com a permissão "leitor de dados de blob de armazenamento" em iam**. Isso é verdadeiro quando você usa:
>- **Ferramenta copiar dados** para criar pipeline de cópia.
>- **Data Factory interface do usuário** para testar a conexão e navegar pelas pastas durante a criação. 
>Se você tiver preocupações com a concessão de permissão no nível da conta, durante a criação, ignorar a conexão de teste e inserir um caminho pai com a permissão concedida, escolha procurar o caminho especificado. A atividade de cópia funciona contanto que a entidade de serviço seja concedida com a permissão adequada nos arquivos a serem copiados.

>[!IMPORTANT]
>Se você usar o polybase para carregar dados de Data Lake Storage Gen2 no SQL Data Warehouse, ao usar a autenticação de identidade gerenciada para o Data Lake Storage Gen2, siga as etapas 1 e 2 nesta [orientação](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) como 1) registrar seu servidor de banco de dados SQL com o Azure Active Directory (Azure AD) e 2) atribuir a função de colaborador de dados de blob de armazenamento ao servidor do banco de dados SQL; o restante são tratados por Data Factory. Se seu Data Lake Storage Gen2 estiver configurado com um ponto de extremidade de rede virtual do Azure, para usar o polybase para carregar dados dele, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo polybase.

Essas propriedades têm suporte para o serviço vinculado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para Data Lake Storage Gen2 com o `https://<accountname>.dfs.core.windows.net`padrão de. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o armazenamento de dados estiver em uma rede privada. Se não for especificado, o tempo de execução de integração do Azure padrão será usado. |Não |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os [](concepts-datasets-linked-services.md), consulte DataSets.

- Para **parquet, texto delimitado e formato binário**, consulte a seção [parquet, texto delimitado e o conjunto de DataSet de formato binário](#format-based-dataset) .
- Para outros formatos, como o **formato Orc/Avro/JSON**, consulte outra seção do [conjunto](#other-format-dataset) de exemplo.

### <a name="format-based-dataset"></a>Parquet, texto delimitado e conjunto de DataSet de formato binário

Para copiar dados de e para **parquet, texto delimitado ou formato binário**, consulte [formato de parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md) e artigo de [formato binário](format-binary.md) em conjunto de dados com base em formato e configurações com suporte. As propriedades a seguir têm suporte para data Lake Storage Gen2 `location` em configurações no conjunto de entrada baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade Type em `location` no DataSet deve ser definida como **AzureBlobFSLocation**. | Sim      |
| WPD | O nome do sistema de arquivos Data Lake Storage Gen2.                              | Não       |
| folderPath | O caminho para uma pasta sob o sistema de arquivos fornecido. Se você quiser usar um curinga para filtrar pastas, ignore essa configuração e especifique-a nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo no sistema de arquivos fornecido + folderPath. Se você quiser usar um curinga para filtrar arquivos, ignore essa configuração e especifique-a nas configurações de origem da atividade. | Não       |

> [!NOTE]
> O conjunto de **AzureBlobFSFile** do tipo parquet ou o formato de texto mencionado na seção a seguir ainda tem suporte como é para a atividade de cópia, pesquisa ou GetMetadata para compatibilidade com versões anteriores. Mas ele não funciona com o recurso de fluxo de dados de mapeamento. É recomendável que você use esse novo modelo no futuro. A interface do usuário de criação de Data Factory gera esses novos tipos.

**Example:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

Para copiar dados de e para Data Lake Storage Gen2 no **formato Orc/Avro/JSON**, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados tem de ser definida **AzureBlobFSFile**. |Sim |
| folderPath | Caminho para a pasta em Data Lake Storage Gen2. Se não for especificado, ele aponta para a raiz. <br/><br/>Há suporte para o filtro curinga. Curingas permitidos são `*` (corresponde a zero ou mais caracteres) `?` e (corresponde a zero ou a um único caractere). Use `^` para escapar se o nome real da pasta tiver um curinga ou se esse caractere de escape estiver dentro de. <br/><br/>Exemplos: FileSystem/Folder/. Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Não |
| fileName | Filtro de nome ou curinga para os arquivos sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros na pasta. <br/><br/>Para o filtro, os curingas permitidos `*` são (corresponde a zero ou mais caracteres `?` ) e (corresponde a zero ou a um único caractere).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome de arquivo real tiver um curinga ou se esse caractere de escape estiver dentro de.<br/><br/>Quando o nome de arquivo não é especificado para um conjunto de resultados e **preserveHierarchy** não é especificado no coletor de atividade, a atividade de cópia gera automaticamente o nome do arquivo com o seguinte padrão: "*Dados. [GUID de ID de execução de atividade]. [GUID If FlattenHierarchy]. [Formatar se configurado]. [compactação se configurada]* ", por exemplo," Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz ". Se você copiar de uma fonte de tabela usando um nome de tabela em vez de uma consulta, o padrão de nome será " *[nome da tabela]. [ formato]. [compactação se configurada]* ", por exemplo," MyTable. csv ". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo modificado pela última vez. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de `modifiedDatetimeStart` tempo `modifiedDatetimeEnd`entre e. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado pela habilitação dessa configuração quando você deseja fazer o filtro de arquivo com grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores. Quando `modifiedDatetimeStart` tem um valor de DateTime `modifiedDatetimeEnd` , mas é nulo, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora são selecionados. Quando `modifiedDatetimeEnd` tem um valor de DateTime `modifiedDatetimeStart` , mas é nulo, significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora são selecionados.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo modificado pela última vez. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de `modifiedDatetimeStart` tempo `modifiedDatetimeEnd`entre e. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado pela habilitação dessa configuração quando você deseja fazer o filtro de arquivo com grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores. Quando `modifiedDatetimeStart` tem um valor de DateTime `modifiedDatetimeEnd` , mas é nulo, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora são selecionados. Quando `modifiedDatetimeEnd` tem um valor de DateTime `modifiedDatetimeStart` , mas é nulo, significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora são selecionados.| Não |
| format | Se pretender copiar ficheiros como está entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se você deseja analisar ou gerar arquivos com um formato específico, há suporte para os seguintes tipos de formato de arquivo:TextFormat **, JsonFormat**, **AvroFormat**, **OrcFormat**e **ParquetFormat**. Definir o **tipo** propriedade sob **formato** para um dos seguintes valores. Para obter mais informações, consulte as seções [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), formato [Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format)e [formato parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Não (apenas para o cenário de cópia binária) |
| compression | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Os níveis de suporte são **Optimal** e **Fastest**. |Não |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique **folderPath** apenas.<br>Para copiar um único arquivo com um nome específico, especifique **FolderPath** com uma parte da pasta e o nome do arquivo com um nome de arquivos.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **FolderPath** com uma parte de pasta e um **nome de arquivo** com um filtro curinga. 

**Example:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [copiar](copy-activity-overview.md#configuration) atividades e [pipelines e atividades](concepts-pipelines-activities.md). Esta secção fornece uma lista das propriedades compatíveis com a origem de geração 2 de armazenamento do Data Lake e de sink.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 como um tipo de origem

- Para copiar de **parquet, texto delimitado e formato binário**, consulte a seção [parquet, texto delimitado e fonte de formato binário](#format-based-source) .
- Para copiar de outros formatos, como o **formato Orc/Avro/JSON**, consulte [outra seção fonte de formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, texto delimitado e fonte de formato binário

Para copiar dados de **parquet, texto delimitado ou formato binário**, consulte o [formato parquet](format-parquet.md), o [formato de texto delimitado](format-delimited-text.md) e o artigo de [formato binário](format-binary.md) na fonte da atividade de cópia baseada em formato e nas configurações com suporte. As propriedades a seguir têm suporte para data Lake Storage Gen2 `storeSettings` em configurações em fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AzureBlobFSReadSetting**. | Sim                                           |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. Valores permitidos são **true** (predefinição) e **falso**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga no sistema de arquivos fornecido configurado no conjunto de dados para filtrar as pastas de origem. <br>Curingas permitidos são `*` (corresponde a zero ou mais caracteres) `?` e (corresponde a zero ou a um único caractere). Use `^` para escapar se o nome real da pasta tiver um curinga ou esse caractere de escape dentro. <br>Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome do arquivo com caracteres curinga no sistema de arquivos especificado + folderPath/wildcardFolderPath para filtrar os arquivos de origem. <br>Curingas permitidos são `*` (corresponde a zero ou mais caracteres) `?` e (corresponde a zero ou a um único caractere). Use `^` para escapar se o nome real da pasta tiver um curinga ou esse caractere de escape dentro. Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no DataSet |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo modificado pela última vez. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de `modifiedDatetimeStart` tempo `modifiedDatetimeEnd`entre e. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores. Quando `modifiedDatetimeStart` tem um valor de DateTime `modifiedDatetimeEnd` , mas é nulo, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor de DateTime são selecionados. Quando `modifiedDatetimeEnd` tem um valor de DateTime `modifiedDatetimeStart` , mas é nulo, significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora são selecionados. | Não                                            |
| modifiedDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto parquet ou delimitado, a fonte da atividade de cópia do tipo **AzureBlobFSSource** mencionada na seção a seguir ainda é suportada como é para compatibilidade com versões anteriores. É recomendável que você use esse novo modelo no futuro. A interface do usuário de criação de Data Factory gera esses novos tipos.

**Example:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSetting",
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

Para copiar dados de Data Lake Storage Gen2 no **formato Orc/Avro/JSON**, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida **AzureBlobFSSource**. |Sim |
| recursive | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor.<br/>Valores permitidos são **true** (predefinição) e **falso**. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>O Azure Data Lake Storage geração 2, como um tipo de sink

- Para copiar para **parquet, texto delimitado ou formato binário**, consulte a seção [parquet, texto delimitado e coletor de formato binário](#format-based-sink) .
- Para copiar para outros formatos, como o **formato Orc/Avro/JSON**, consulte outra seção de [coletor de formato](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, texto delimitado e coletor de formato binário

Para copiar dados para **parquet, texto delimitado ou formato binário**, consulte [formato de parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md) e artigo de [formato binário](format-binary.md) no coletor de atividade de cópia com base em formato e configurações com suporte. As propriedades a seguir têm suporte para data Lake Storage Gen2 `storeSettings` em configurações no coletor de cópia com base em formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AzureBlobFSWriteSetting**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os ficheiros de destino têm nomes de geradas automaticamente. <br/><b>-MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se não for especificado o nome de ficheiro, o nome de ficheiro intercalada é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não       |

> [!NOTE]
> Para o formato de texto parquet ou delimitado, o coletor de atividade de cópia do tipo **AzureBlobFSSink** mencionado na seção a seguir ainda tem suporte como é para compatibilidade com versões anteriores. É recomendável que você use esse novo modelo no futuro. A interface do usuário de criação de Data Factory gera esses novos tipos.

**Example:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
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
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Outro coletor de formato

Para copiar dados para Data Lake Storage Gen2 no **formato Orc/Avro/JSON**, as propriedades a seguir têm suporte na seção **Sink** :

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de sink de atividade de cópia tem de ser definida **AzureBlobFSSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os ficheiros de destino têm nomes de geradas automaticamente. <br/><b>-MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se não for especificado o nome de ficheiro, o nome de ficheiro intercalada é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Example:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e o nome do arquivo com filtros curinga.

| folderPath | fileName | recursive | Estrutura da pasta de origem e resultado do filtro (arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vazio, usar padrão) | false | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vazio, usar padrão) | true | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursiva e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursivos e copyBehavior.

| recursive | copyBehavior | Estrutura de pastas de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O Pasta1 de destino é criado com a mesma estrutura que a origem:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 conteúdo é mesclado num ficheiro com um nome de ficheiro gerado automaticamente. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não é selecionado. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não é selecionado. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com um nome de ficheiro gerado automaticamente. nome gerado automaticamente para File1<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não é selecionado. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Preservar ACLs de Data Lake Storage Gen1

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para o Gen2 em geral, consulte [copiar dados de Azure data Lake Storage Gen1 para Gen2 com Azure data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para obter uma orientação e práticas recomendadas.

Ao copiar arquivos de Azure Data Lake Storage Gen1 para Gen2, você pode optar por preservar as ACLs (listas de controle de acesso) do POSIX junto com os dados. Para obter mais informações sobre o controle de acesso, consulte [controle de acesso em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) e [controle de acesso em Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Os tipos de ACLs a seguir podem ser preservados usando a atividade de cópia Azure Data Factory. Você pode selecionar um ou mais tipos:

- **ACL**: Copie e preserve listas de controle de acesso POSIX em arquivos e diretórios. Ele copia as ACLs completas existentes da origem para o coletor. 
- **Proprietário**: Copie e preserve o usuário proprietário de arquivos e diretórios. O acesso de superusuário ao Data Lake Storage Gen2 de coletor é necessário.
- **Grupo**: Copie e preserve o grupo proprietário de arquivos e diretórios. É necessário o acesso de superusuário ao coletor Data Lake Storage Gen2 ou ao usuário proprietário (se o usuário proprietário também for um membro do grupo de destino).

Se você especificar para copiar de uma pasta, data Factory Replica as ACLs para aquela pasta especificada e os arquivos e diretórios sob ela, se `recursive` for definido como true. Se você especificar para copiar de um único arquivo, as ACLs nesse arquivo serão copiadas.

>[!IMPORTANT]
>Ao optar por preservar as ACLs, certifique-se de conceder permissões altas o suficiente para Data Factory operar em sua conta de Data Lake Storage Gen2 do coletor. Por exemplo, use a autenticação de chave de conta ou atribua a função de proprietário de dados do blob de armazenamento à entidade de serviço ou à identidade gerenciada.

Quando você configura a origem como Data Lake Storage Gen1 com a opção de cópia binária ou o formato binário e o coletor como Data Lake Storage Gen2 com a opção de cópia binária ou o formato binário, é possível encontrar a opção **preservar** na página **copiar dados configurações da ferramenta** ou em a guia**configurações** da **atividade** > de cópia para a criação de atividades.

![Data Lake Storage Gen1 Gen2 preservar ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Aqui está um exemplo de configuração JSON (consulte `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Saiba mais sobre [transformação de origem](data-flow-source.md) e [transformação de coletor](data-flow-sink.md) no recurso de fluxo de dados de mapeamento.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
