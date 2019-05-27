---
title: Copiar dados de ou para a geração 2 de armazenamento do Azure Data Lake com o Data Factory | Documentos da Microsoft
description: Saiba como copiar dados para e de geração 2 de armazenamento do Azure Data Lake com o Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: c0591a7850516a419cb59045754cc4eb02979dfd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66122585"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiar dados de ou para a geração 2 de armazenamento do Azure Data Lake com o Azure Data Factory

Geração de armazenamento 2 do Azure Data Lake (ADLS Gen2) é um conjunto de recursos dedicado a análise de macrodados, incorporado ao [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md). Permite-lhe interagir com os dados através de ambos os paradigmas de armazenamento de sistema e o objeto de ficheiro.

Este artigo descreve como copiar dados para e de geração 2 de armazenamento do Azure Data Lake. Para saber mais sobre o Azure Data Factory, leia os [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de geração 2 de armazenamento do Azure Data Lake é suportada para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [suportado de matriz de origem/sink](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, este conector suporta:

- Copiar dados utilizando a chave da conta, principal de serviço ou identidades geridas para autenticações de recursos do Azure.
- Copiar ficheiros como-está ou análise ou ficheiros com a gerar [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Não se ativar o espaço de nomes hierárquico, existe atualmente nenhum interoperabilidade de operações entre BLOBs e as APIs de geração 2 do ADLS. No caso de atingir o erro de "código de erro = FilesystemNotFound" com a mensagem detalhada como "o sistema de ficheiros especificado não existe.", é provocado por coletor especificado sistema de ficheiros foi criado através da API de Blob em vez da API de geração 2 do ADLS em outro lugar. Para corrigir o problema, especifique um novo sistema de ficheiros com um nome que não existe como o nome de um contentor de BLOBs e ADF criará automaticamente esse sistema de ficheiros durante a cópia de dados.

>[!NOTE]
>Se permite _"Permitir confiável a serviços da Microsoft para aceder a esta conta de armazenamento"_ opção nas definições de firewall de armazenamento do Azure, com o Runtime de integração do Azure para ligar ao Data Lake Storage Gen2 falha com erro proibido, como o ADF não são tratados como serviço confiável da Microsoft. Utilize o Runtime de integração autoalojado que estabelecer ligação através do qual em vez disso.

## <a name="get-started"></a>Introdução

>[!TIP]
>Para obter instruções para utilizar o conector de geração 2 de armazenamento do Data Lake, veja [carregar dados para a geração 2 de armazenamento do Azure Data Lake](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para a geração 2 de armazenamento do Data Lake.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

Conector de geração 2 de armazenamento do Azure Data Lake suporta os seguintes tipos de autenticação, consulte a seção correspondente em detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação do principal de serviço](#service-principal-authentication)
- [Identidades geridas para a autenticação de recursos do Azure](#managed-identity)

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para utilizar a autenticação de chave de conta de armazenamento, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto final para a Gen2 de armazenamento do Data Lake com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim |
| accountKey | Chave da conta para o serviço de geração 2 de armazenamento do Data Lake. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se seu arquivo de dados estiver numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

**Exemplo:**

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

Para utilizar autenticação do principal de serviço, siga estes passos:

1. Registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) ao seguir [registar a aplicação com um inquilino do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

2. Conceda a permissão de adequada principal do serviço.

    - **Como origem**, no Explorador de armazenamento, conceder, pelo menos, **leitura + execução** permissão para listar e copie os ficheiros em pastas e subpastas ou conceder **leitura** permissão para copiar um ficheiro individual. Em alternativa, no controlo de acesso (IAM), conceder, pelo menos, **leitor de dados de Blob de armazenamento** função.
    - **Como sink**, no Explorador de armazenamento, conceder, pelo menos, **escrita + execução** permissão para criar itens subordinados numa pasta. Em alternativa, no controlo de acesso (IAM), conceder, pelo menos, **contribuinte de dados de Blob de armazenamento** função.

>[!NOTE]
>A lista de pastas a partir do nível da conta ou a ligação de teste, precisa definir a permissão do principal de serviço, sendo concedido ao **conta de armazenamento com a permissão de "Execução" no IAM**. Isso é verdade, quando utiliza o:
>- **Ferramenta copiar dados** para o pipeline de cópia de autor.
>- **IU do Data Factory** para testar a ligação e navegar pastas durante a criação. 
>Se tiver a preocupação de conceder permissão ao nível da conta, pode ignorar manualmente a ligação de teste e o caminho de entrada durante a criação. Atividade de cópia continuarão a funcionar, desde que o principal de serviço é concedido com a permissão adequada nos arquivos sejam copiados.

Estas propriedades são suportadas no serviço ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto final para a Gen2 de armazenamento do Data Lake com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se seu arquivo de dados estiver numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

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

Uma fábrica de dados pode ser associada com um [identidade de recursos do Azure gerida](data-factory-service-identity.md), que representa esta fábrica de dados específicos. Pode utilizar diretamente esta identidade gerida para a autenticação de geração 2 do ADLS semelhante à utilização da sua própria principal de serviço. Ele permite que esta fábrica designada para aceder e copiar dados de/para a geração 2 do ADLS.

Para utilizar identidades geridas para a autenticação de recursos do Azure, siga estes passos:

1. [Obter informações de identidade do data factory gerido](data-factory-service-identity.md#retrieve-managed-identity) ao copiar o valor de "Serviço de ID da IDENTITY APPLICATION" gerada juntamente com sua fábrica.

2. Conceda a permissão adequada de identidade gerida. 

    - **Como origem**, no Explorador de armazenamento, conceder, pelo menos, **leitura + execução** permissão para listar e copie os ficheiros em pastas e subpastas ou conceder **leitura** permissão para copiar um ficheiro individual. Em alternativa, no controlo de acesso (IAM), conceder, pelo menos, **leitor de dados de Blob de armazenamento** função.
    - **Como sink**, no Explorador de armazenamento, conceder, pelo menos, **escrita + execução** permissão para criar itens subordinados numa pasta. Em alternativa, no controlo de acesso (IAM), conceder, pelo menos, **contribuinte de dados de Blob de armazenamento** função.

>[!NOTE]
>A lista de pastas a partir do nível da conta ou a ligação de teste, precisa definir a permissão de a identidade gerida sendo concedido ao **conta de armazenamento com a permissão de "Execução" no IAM**. Isso é verdade, quando utiliza o:
>- **Ferramenta copiar dados** para o pipeline de cópia de autor.
>- **IU do Data Factory** para testar a ligação e navegar pastas durante a criação. 
>Se tiver a preocupação de conceder permissão ao nível da conta, pode ignorar manualmente a ligação de teste e o caminho de entrada durante a criação. Atividade de cópia continuarão a funcionar, desde que a identidade gerida é concedida com a permissão adequada nos arquivos sejam copiados.

>[!IMPORTANT]
>Se utilizar o PolyBase para carregar dados de geração 2 do ADLS no armazém de dados SQL, quando utilizar ADLS Gen2 gerida a autenticação de identidade, certifique-se também de configurar o SQL DW adequadamente para utilizar MSI para o armazenamento de geração 2 do ADLS, siga os passos #1 para #3.b na [esta orientação](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Se sua geração 2 do ADLS está configurado com o ponto final de serviço da VNet, a utilizar o PolyBase para carregar dados a partir do mesmo, tem de utilizar a autenticação de identidade gerida.

Estas propriedades são suportadas no serviço ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto final para a Gen2 de armazenamento do Data Lake com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se seu arquivo de dados estiver numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. 

- Para **Parquet e o formato de texto delimitado**, consulte [conjunto de dados de formato de Parquet e texto delimitado](#parquet-and-delimited-text-format-dataset) secção.
- Como outros formatos **formato ORC/Avro/JSON/binário**, consulte [outro conjunto de dados do formato](#other-format-dataset) secção.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet e o conjunto de dados de formato de texto delimitado

Para copiar dados para e de geração 2 do ADLS na **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo num conjunto de dados com base no formato e definições suportadas. As seguintes propriedades são suportadas para a geração 2 do ADLS em `location` definições no conjunto de dados com base no formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade de tipo sob `location` no conjunto de dados deve ser definida como **AzureBlobFSLocation**. | Sim      |
| fileSystem | O nome de sistema de ficheiros do ADLS Gen2.                              | Não       |
| folderPath | O caminho para a pasta em que o sistema de ficheiro especificado. Se pretender utilizar o caráter universal para a pasta de filtro, ignorar esta definição e especifique nas definições da origem de atividade. | Não       |
| fileName   | O nome de ficheiro, sob o sistema de ficheiros de determinado + folderPath. Se pretender utilizar o caráter universal para filtrar os ficheiros, ignorar esta definição e especifique nas definições da origem de atividade. | Não       |

> [!NOTE]
> **AzureBlobFSFile** tipo conjunto de dados com o formato Parquet/Text mencionado na secção seguinte ainda é suportado como-é para a atividade de cópia/pesquisa/GetMetadata para compatibilidade com versões anteriores, mas ele não funciona com o mapeamento de fluxo de dados. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen2 linked service name>",
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

### <a name="other-format-dataset"></a>Outro conjunto de dados do formato

Para copiar dados para e de geração 2 do ADLS na **formato ORC/Avro/JSON/binário**, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados tem de ser definida **AzureBlobFSFile**. |Sim |
| folderPath | Caminho para a pasta na Gen2 de armazenamento do Data Lake. Se não for especificado, ele aponta para a raiz. <br/><br/>Filtro de carateres universais é suportado, permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape. <br/><br/>Exemplos:. o sistema de ficheiros/pastas /, veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). |Não |
| fileName | **Filtro de nome ou o caráter universal** para o ficheiro ou ficheiros sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros na pasta. <br/><br/>Para o filtro, permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Utilize `^` para se o seu nome de ficheiro real tem carateres universais ou esse caractere de escape dentro de escape.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída e **preserveHierarchy** não seja especificado no sink de atividade, a atividade de cópia gera automaticamente o nome de ficheiro com o seguinte padrão: "*Dados. [o GUID de ID de execução da atividade]. [GUID se FlattenHierarchy]. [formato se configurado]. [compressão se configurado]* ", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz;" Se copiar a partir da origem de tabela com o nome da tabela em vez de consulta, o padrão de nome é " *[nome da tabela]. [ formato]. [compressão se configurado]* ", por exemplo, "MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral do movimento de dados vai ser afetado por ativar esta definição quando pretender que o filtro de ficheiros a partir de enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser nulo, isso significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral do movimento de dados vai ser afetado por ativar esta definição quando pretender que o filtro de ficheiros a partir de enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser nulo, isso significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| format | Se pretender copiar ficheiros como está entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar arquivos com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, e **ParquetFormat**. Definir o **tipo** propriedade sob **formato** para um dos seguintes valores. Para obter mais informações, consulte a [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Não (apenas para o cenário de cópia binária) |
| compression | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Os níveis de suporte são **Optimal** e **Fastest**. |Não |

>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique **folderPath** apenas.<br>Para copiar um único ficheiro com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com o nome de ficheiro.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro de carateres universais. 

**Exemplo:**

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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [copiar as configurações de atividade](copy-activity-overview.md#configuration) e [pipelines e atividades](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem de geração 2 de armazenamento do Data Lake e de sink.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Armazenamento de Azure Data Lake geração 2 como um tipo de origem

- Para copiar a partir **Parquet e o formato de texto delimitado**, consulte [Parquet e origem de formato de texto delimitado](#parquet-and-delimited-text-format-source) secção.
- Para copiar a partir de outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outra origem de formato](#other-format-source) secção.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e origem de formato de texto delimitado

Para copiar dados de geração 2 do ADLS na **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo na origem de atividade de cópia baseada em formato e definições suportadas. As seguintes propriedades são suportadas para a geração 2 do ADLS em `storeSettings` definições na origem de cópia baseada no formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade de tipo sob `storeSettings` deve ser definida como **AzureBlobFSReadSetting**. | Sim                                           |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que quando recursiva é definida como true e o sink é um arquivo baseado em arquivo, uma pasta vazia ou uma subpasta não é copiada ou criada no coletor. Valores permitidos são **true** (predefinição) e **falso**. | Não                                            |
| wildcardFolderPath       | O caminho de pasta com carateres universais sob o sistema de ficheiro especificado configurado no conjunto de dados para pastas de origem do filtro. <br>Permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape. <br>Veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome de ficheiro com carateres universais sob o sistema de ficheiros de determinado + folderPath/wildcardFolderPath para ficheiros de origem do filtro. <br>Permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape.  Veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). | Se Sim `fileName` não está especificado no conjunto de dados |
| modifiedDatetimeStart    | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de armazenamento em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto delimitados/Parquet, **AzureBlobFSSource** continua a ser suportada como origem da atividade de cópia de tipo mencionada na secção seguinte-é para compatibilidade com versões anteriores. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

**Exemplo:**

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

#### <a name="other-format-source"></a>Outra origem de formato

Para copiar dados de geração 2 do ADLS na **formato ORC/Avro/JSON/binário**, as seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida **AzureBlobFSSource**. |Sim |
| recursive | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que quando recursiva é definida como true e o sink é um arquivo baseado em arquivo, uma pasta vazia ou uma subpasta não é copiada ou criada no coletor.<br/>Valores permitidos são **true** (predefinição) e **falso**. | Não |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de dados em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não |

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

- Para copiar para **Parquet e o formato de texto delimitado**, consulte [Parquet e de sink de formato de texto delimitado](#parquet-and-delimited-text-format-sink) secção.
- Para copiar para outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outro sink de formato](#other-format-sink) secção.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet e de sink de formato de texto delimitado

Para copiar dados para a geração 2 do ADLS na **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo sobre o sink de atividade de cópia baseada em formato e definições suportadas. As seguintes propriedades são suportadas para a geração 2 do ADLS em `storeSettings` as definições de sink de cópia baseada no formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade de tipo sob `storeSettings` deve ser definida como **AzureBlobFSWriteSetting**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (predefinição)</b>: Preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível de pasta de destino. Os ficheiros de destino têm nomes de geradas automaticamente. <br/><b>-MergeFiles</b>: Une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de ficheiro, o nome de ficheiro intercalada é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de dados em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não       |

> [!NOTE]
> Para o formato de texto delimitados/Parquet, **AzureBlobFSSink** continua a ser suportado como sink de atividade de cópia de tipo mencionado na secção seguinte-é para compatibilidade com versões anteriores. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

**Exemplo:**

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

#### <a name="other-format-sink"></a>Outro sink de formato

Para copiar dados para a geração 2 do ADLS no **formato ORC/Avro/JSON/binário**, as seguintes propriedades são suportadas os **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de sink de atividade de cópia tem de ser definida **AzureBlobFSSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (predefinição)</b>: Preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível de pasta de destino. Os ficheiros de destino têm nomes de geradas automaticamente. <br/><b>-MergeFiles</b>: Une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de ficheiro, o nome de ficheiro intercalada é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. | Não |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de dados em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não |

**Exemplo:**

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

### <a name="folder-and-file-filter-examples"></a>Pasta e exemplos de filtro de ficheiro

Esta secção descreve o comportamento resultante o nome de ficheiro e caminho de pasta com filtros de caráter universal.

| folderPath | fileName | recursive | Resultado de estrutura e o filtro de pasta de origem (arquivos no **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, utilizar a predefinição) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vazio, utilizar a predefinição) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursiva e copyBehavior

Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| recursive | copyBehavior | Estrutura de pastas de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 conteúdo é mesclado num ficheiro com um nome de ficheiro gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com um nome de ficheiro gerado automaticamente. nome gerado automaticamente para File1<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Preservar as ACLs do Data Lake Storage Gen1

>[!TIP]
>Para copiar dados a partir do Azure Data Lake Storage Gen1 na geração 2 em geral, consulte [copiar dados de geração 1 de armazenamento do Azure Data Lake para geração 2 com o Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) com instruções e as melhores práticas.

Quando a cópia de ficheiros do Azure Data Lake Storage (ADLS) Gen1 para geração 2, pode optar por manter as listas de controlo de acesso (ACLs) de POSIX, juntamente com dados. Para o controlo de acesso nos detalhes, consulte [controlo de acesso no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) e [controlo de acesso no Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Os seguintes tipos de ACLs podem ser preservados com a atividade de cópia de fábrica de dados do Azure, pode selecionar um ou mais tipos:

- **ACL**: Copie e preservar **listas de controlo de acesso POSIX** em arquivos e diretórios. Ele copiará as ACLs existentes completas de origem para o sink. 
- **Proprietário**: Copie e preservar **o utilizador proprietário** de arquivos e diretórios. É necessário o acesso de Superutilizador para sink de geração 2 do ADLS.
- **Grupo**: Copie e preservar **o grupo proprietário** de arquivos e diretórios. É necessário o acesso de Superutilizador para sink ADLS Gen2 ou o utilizador proprietário (se o utilizador proprietário também seja membro do grupo de destino).

Se especificar a copiar de uma pasta, o Data Factory replica as ACLs para essa determinada pasta, bem como os ficheiros e diretórios sob a mesma (se `recursive` está definido como true). Se especificar para copiar de um único arquivo, as ACLs no arquivo é copiado.

>[!IMPORTANT]
>Quando optar por manter as ACLs, certifique-se de que alto concede permissões suficientes para o ADF para em relação a sua conta do ADLS Gen2 de sink. Por exemplo, utilizar a autenticação de chave de conta ou o atribuir a função de proprietário de dados de Blob de armazenamento para a identidade de geridos/principal de serviço.

Ao configurar origem como ADLS Gen1 com o formato de opção/binário de cópia binária e de sink como ADLS Gen2 com o formato de opção/binário de cópia binária, é possível encontrar **preservar** opção **página de definições da ferramenta copiar dados** ou na **atividade de cópia -> definições** separador para a criação de atividade.

![Geração 1 do ADLS para ger2 preservar ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Eis um exemplo de configuração JSON (consulte `preserve`): 

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

## <a name="mapping-data-flow-properties"></a>Propriedades de fluxo de dados de mapeamento

Conheça os detalhes da [transformação de origem](data-flow-source.md) e [sink transformação](data-flow-sink.md) no mapeamento de fluxo de dados.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
