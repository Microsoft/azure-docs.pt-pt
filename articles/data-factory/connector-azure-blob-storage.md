---
title: Copiar dados de ou para armazenamento de Blobs do Azure com o Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de arquivos de dados de origem suportada para o armazenamento de Blobs do Azure ou de armazenamento de BLOBs para arquivos de dados de sink suportado, com o Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: jingwang
ms.openlocfilehash: cacc17464d15e8872508cff0e626b383ee9a2808
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985398"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Copiar dados de ou para armazenamento de Blobs do Azure com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-blob-connector.md)
> * [Versão atual](connector-azure-blob-storage.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados para e do armazenamento de Blobs do Azure. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

Para saber mais sobre o Azure Data Factory, leia os [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de qualquer arquivo de dados de origem suportada para o armazenamento de Blobs. Também pode copiar dados de armazenamento de BLOBs para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md) tabela.

Especificamente, este conector de armazenamento de BLOBs suporta:

- Copiar blobs entre contas de armazenamento do Azure para fins gerais e de armazenamento de BLOBs de acesso frequente/esporádico. 
- Copiar blobs ao utilizar a chave da conta, assinatura de acesso partilhado do serviço, as identidades de serviço principal ou gerenciado para autenticações de recursos do Azure.
- Copiar blobs de bloco, acrescentar ou blobs de páginas e copiar os dados apenas para blobs de blocos.
- Copiar blobs, tal como estão ou análise ou gerar blobs com [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Se ativar a _"Permitir confiável a serviços da Microsoft para aceder a esta conta de armazenamento"_ opção nas definições de firewall de armazenamento do Azure, com o Runtime de integração do Azure para ligar ao armazenamento de BLOBs irá falhar com um erro proibido, porque não está ADF Tratado como um serviço confiável da Microsoft. Ligue através de um Runtime de integração autoalojado em vez disso.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o armazenamento de Blobs.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

Conector de Blobs do Azure suporta os seguintes tipos de autenticação, consulte a seção correspondente em detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação da assinatura de acesso partilhado](#shared-access-signature-authentication)
- [Autenticação do principal de serviço](#service-principal-authentication)
- [Identidades geridas para a autenticação de recursos do Azure](#managed-identity)

>[!NOTE]
>HDInsights, Azure Machine Learning e carga do Azure SQL Data Warehouse PolyBase suportam apenas a autenticação de chave de conta de armazenamento de Blobs do Azure.

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para utilizar a autenticação de chave de conta de armazenamento, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **AzureBlobStorage** (sugerida) ou **AzureStorage** (ver notas abaixo). |Sim |
| connectionString | Especifique as informações necessárias para ligar ao armazenamento para a propriedade connectionString. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também é possível incluir a chave da conta no Azure Key Vault e obter o `accountKey` configuração fora de cadeia de ligação. Consulte os exemplos seguintes e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se seu arquivo de dados estiver numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Se estivesse usando o serviço de ligado de tipo de "AzureStorage", ainda é suportado como-é, ao passo que lhe é sugeridas para utilizar este novo "AzureBlobStorage" ligado do tipo de serviço no futuro.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a chave de conta no Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autenticação da assinatura de acesso partilhado

Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Pode usar uma assinatura de acesso partilhado para conceder que um cliente permissões limitadas a objetos na conta de armazenamento para um período de tempo especificado. Não tem de partilhar as chaves de acesso da conta. A assinatura de acesso partilhado é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a assinatura de acesso partilhado, o cliente precisa apenas passar a assinatura de acesso partilhado para o método ou construtor apropriado. Para obter mais informações sobre assinaturas de acesso partilhado, consulte [assinaturas de acesso partilhado: Compreender o modelo de assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- O Data Factory suporta agora ambos **serviço de assinaturas de acesso partilhado** e **assinaturas de acesso partilhado de conta**. Para obter mais informações sobre estes dois tipos e como construí-las, consulte [tipos de assinaturas de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures).
>- Configuração de conjunto de dados posterior, o caminho da pasta é o caminho absoluto a partir de nível de contêiner. Tem de configurar um alinhada ao caminho do URI de SAS.

> [!TIP]
> Para gerar uma assinatura de acesso partilhado do serviço para a sua conta de armazenamento, pode executar os seguintes comandos do PowerShell. Substitua os marcadores de posição e conceder a permissão necessária.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para utilizar a autenticação da assinatura de acesso partilhado, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **AzureBlobStorage** (sugerida) ou **AzureStorage** (ver notas abaixo). |Sim |
| sasUri | Especifique o URI de assinatura de acesso partilhado para os recursos de armazenamento como/contentor de Blobs. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar o SAS token no Azure Key Vault para rotação automática de leverate e remover a parte do token. Consulte os exemplos seguintes e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode usar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Se estivesse usando o serviço de ligado de tipo de "AzureStorage", ainda é suportado como-é, ao passo que lhe é sugeridas para utilizar este novo "AzureBlobStorage" ligado do tipo de serviço no futuro.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a chave de conta no Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Quando cria um URI de assinatura de acesso partilhado, considere os seguintes pontos:

- Definir permissões de leitura/escrita adequadas em objetos com base em como o serviço ligado (leitura, escrita, de leitura/escrita) é utilizado na sua fábrica de dados.
- Definir **tempo de expiração** adequadamente. Certifique-se de que o acesso a objetos de armazenamento não expira com o período ativo do pipeline.
- O URI deve ser criado com o blob/contentor certo com base na necessidade. Um URI de assinatura de acesso partilhado para um blob permite que a fábrica de dados para esse determinado blob de acesso. Um URI de assinatura de acesso partilhado a um contentor de armazenamento de BLOBs permite que a fábrica de dados para iterar por meio de blobs existentes nesse contentor. Para fornecer acesso a mais ou menos objetos mais tarde, ou para atualizar o URI de assinatura de acesso partilhado, lembre-se de atualizar o serviço ligado com o URI de novo.

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para a autenticação principal de serviço de armazenamento do Azure em geral, consulte [autenticar o acesso ao armazenamento do Azure com o Azure Active Directory](../storage/common/storage-auth-aad.md).

Para utilizar autenticação do principal de serviço, siga estes passos:

1. Registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) ao seguir [registar a aplicação com um inquilino do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

2. Conceda a service principal permissão adequada no armazenamento de Blobs do Azure. Consulte a [gerir direitos de acesso aos dados de armazenamento do Azure com o RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como origem**, acesso para controlar (IAM), pelo menos a conceder **leitor de dados de Blob de armazenamento** função.
    - **Como sink**, acesso para controlar (IAM), pelo menos a conceder **contribuinte de dados de Blob de armazenamento** função.

Estas propriedades são suportadas para um serviço ligado do armazenamento de Blobs do Azure:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **AzureBlobStorage**. |Sim |
| serviceEndpoint | Especifique o ponto de final de serviço de armazenamento de Blobs do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se seu arquivo de dados estiver numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Serviço ligado do serviço de autenticação do principal só é suportada pelo tipo de "AzureBlobStorage" mas o serviço ligado de tipo de não anterior "AzureStorage".

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
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

Uma fábrica de dados pode ser associada com um [identidade de recursos do Azure gerida](data-factory-service-identity.md), que representa esta fábrica de dados específicos. Pode utilizar diretamente esta identidade gerida para a autenticação de armazenamento de BLOBs semelhante à utilização da sua própria principal de serviço. Ele permite que esta fábrica designada para aceder e copiar dados de/para o seu armazenamento de Blobs.

Consulte a [autenticar o acesso ao armazenamento do Azure com o Azure Active Directory](../storage/common/storage-auth-aad.md) para autenticação de armazenamento do Azure em geral. Para utilizar identidades geridas para a autenticação de recursos do Azure, siga estes passos:

1. [Obter informações de identidade do data factory gerido](data-factory-service-identity.md#retrieve-managed-identity) ao copiar o valor de "Serviço de ID da IDENTITY APPLICATION" gerada juntamente com sua fábrica.

2. Conceda a permissão adequada de identidade gerida no armazenamento de Blobs do Azure. Consulte a [gerir direitos de acesso aos dados de armazenamento do Azure com o RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como origem**, acesso para controlar (IAM), pelo menos a conceder **leitor de dados de Blob de armazenamento** função.
    - **Como sink**, acesso para controlar (IAM), pelo menos a conceder **contribuinte de dados de Blob de armazenamento** função.

Estas propriedades são suportadas para um serviço ligado do armazenamento de Blobs do Azure:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **AzureBlobStorage**. |Sim |
| serviceEndpoint | Especifique o ponto de final de serviço de armazenamento de Blobs do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se seu arquivo de dados estiver numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

> [!NOTE]
> Serviço ligado de tipo de identidades geridas para recursos do Azure, a autenticação só é suportada pelo "AzureBlobStorage", mas não anterior "AzureStorage" tipo de serviço ligado. 

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados do armazenamento de Blobs.

Para copiar dados de e para armazenamento de BLOBs, defina a propriedade de tipo de conjunto de dados para **AzureBlob**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida **AzureBlob**. |Sim |
| folderPath | Caminho para o contentor e a pasta no armazenamento de Blobs. <br/><br/>Filtro de carateres universais é suportado para o caminho excluindo o nome do contentor. Permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape. <br/><br/>Exemplos: myblobcontainer/myblobfolder /, veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). |Sim para a atividade de cópia/Lookup, não para a atividade GetMetadata |
| fileName | **Filtro de nome ou o caráter universal** para os BLOBs no "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os blobs na pasta. <br/><br/>Para o filtro, permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Utilize `^` para se o seu nome de ficheiro real tem carateres universais ou esse caractere de escape dentro de escape.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída e **preserveHierarchy** não seja especificado no sink de atividade, a atividade de cópia gera automaticamente o nome do blob com o seguinte padrão: "*Dados. [id de execução da atividade GUID]. [GUID se FlattenHierarchy]. [formato se configurado]. [compressão se configurado]* ", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz;" Se copiar a partir da origem de tabela com o nome da tabela em vez de consulta, o padrão de nome é "*[nome da tabela]. [ formato]. [compressão se configurado]* ", por exemplo, "MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| Formato | Se pretender copiar ficheiros como está entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar arquivos com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, e **ParquetFormat**. Definir o **tipo** propriedade sob **formato** para um dos seguintes valores. Para obter mais informações, consulte a [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Não (apenas para o cenário de cópia binária) |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Os níveis de suporte são **Optimal** e **Fastest**. |Não |

>[!TIP]
>Para copiar todos os blobs numa pasta, especifique **folderPath** apenas.<br>Para copiar um blob único com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com o nome de ficheiro.<br>Para copiar um subconjunto de blobs numa pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro de carateres universais. 

**Exemplo:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem do armazenamento de BLOBs e de sink.

### <a name="blob-storage-as-a-source-type"></a>Armazenamento de BLOBs como um tipo de origem

Para copiar dados de armazenamento de BLOBs, definir o tipo de origem na atividade de cópia para **BlobSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida **BlobSource**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que quando recursiva é definida como true e o sink é um arquivo baseado em arquivo, uma pasta vazia ou uma subpasta não é copiada ou criada no coletor.<br/>Valores permitidos são **true** (predefinição) e **falso**. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Armazenamento de BLOBs como um tipo de sink

Para copiar dados para o armazenamento de BLOBs, defina o tipo de sink na atividade de cópia para **BlobSink**. As seguintes propriedades são suportadas os **sink** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida **BlobSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (predefinição)</b>: Preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível de pasta de destino. Os ficheiros de destino têm nomes de geradas automaticamente. <br/><b>-MergeFiles</b>: Une todos os ficheiros da pasta de origem para um ficheiro. Se o nome de ficheiro ou blob for especificado, o nome de ficheiro intercalada é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Pasta e exemplos de filtro de ficheiro

Esta secção descreve o comportamento resultante o nome de ficheiro e caminho de pasta com filtros de caráter universal.

| folderPath | fileName | recursiva | Resultado de estrutura e o filtro de pasta de origem (arquivos no **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (vazio, utilizar a predefinição) | false | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (vazio, utilizar a predefinição) | true | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursiva e copyBehavior

Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| recursiva | copyBehavior | Estrutura de pastas de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 conteúdo é mesclado num ficheiro com um nome de ficheiro gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com um nome de ficheiro gerado automaticamente. nome gerado automaticamente para File1<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
