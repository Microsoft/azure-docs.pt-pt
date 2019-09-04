---
title: Copiar dados de ou para armazenamento de Blobs do Azure com o Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de arquivos de dados de origem suportada para o armazenamento de Blobs do Azure ou de armazenamento de BLOBs para arquivos de dados de sink suportado, com o Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: c845c4bcc8dc57371304b5917ee09191b5256c51
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276343"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Copiar dados de ou para armazenamento de Blobs do Azure com o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-blob-connector.md)
> * [Versão atual](connector-azure-blob-storage.md)

Este artigo descreve como copiar dados de e para o armazenamento de BLOBs do Azure. Para saber mais sobre o Azure Data Factory, leia os [artigo introdutório](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de blob do Azure tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, este conector de armazenamento de BLOBs suporta:

- Copiar blobs entre contas de armazenamento do Azure para fins gerais e de armazenamento de BLOBs de acesso frequente/esporádico. 
- Copiar blobs ao utilizar a chave da conta, assinatura de acesso partilhado do serviço, as identidades de serviço principal ou gerenciado para autenticações de recursos do Azure.
- Copiar blobs de bloco, acrescentar ou blobs de páginas e copiar os dados apenas para blobs de blocos.
- Copiar blobs, tal como estão ou análise ou gerar blobs com [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Se você habilitar a opção _"permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento"_ nas configurações do firewall do armazenamento do Azure, o uso de Azure Integration Runtime para se conectar ao armazenamento de BLOBs falhará com um erro proibido, pois o ADF não será tratado como confiável Serviço da Microsoft. Conecte-se por meio de um Integration Runtime hospedado internamente.

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
>Ao usar o polybase para carregar dados em SQL Data Warehouse, se o armazenamento de blob de origem ou de preparo estiver configurado com o ponto de extremidade de rede virtual, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo polybase e usar o Integration Runtime de hospedagem interna com a versão 3,18 ou superior. Consulte a seção [autenticação de identidade gerenciada](#managed-identity) com mais pré-requisitos de configuração.

>[!NOTE]
>As atividades HDInsights e Azure Machine Learning só dão suporte à autenticação de chave de conta de armazenamento de BLOBs do Azure

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para utilizar a autenticação de chave de conta de armazenamento, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobStorage** (sugerida) ou **AzureStorage** (ver notas abaixo). |Sim |
| connectionString | Especifique as informações necessárias para ligar ao armazenamento para a propriedade connectionString. <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar a chave de conta em Azure Key Vault e `accountKey` efetuar pull da configuração da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se seu arquivo de dados estiver numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Não há suporte para o ponto de extremidade do serviço blob secundário ao usar a autenticação de chave de conta. Você pode usar outros tipos de autenticação.

>[!NOTE]
>Se estivesse usando o serviço de ligado de tipo de "AzureStorage", ainda é suportado como-é, ao passo que lhe é sugeridas para utilizar este novo "AzureBlobStorage" ligado do tipo de serviço no futuro.

**Example:**

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

**Exemplo: armazenar chave de conta no Azure Key Vault**

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

Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Pode usar uma assinatura de acesso partilhado para conceder que um cliente permissões limitadas a objetos na conta de armazenamento para um período de tempo especificado. Não tem de partilhar as chaves de acesso da conta. A assinatura de acesso partilhado é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a assinatura de acesso partilhado, o cliente precisa apenas passar a assinatura de acesso partilhado para o método ou construtor apropriado. Para obter mais informações sobre assinaturas de acesso compartilhado [, consulte assinaturas de acesso compartilhado: Entenda o modelo](../storage/common/storage-dotnet-shared-access-signature-part-1.md)de assinatura de acesso compartilhado.

> [!NOTE]
>- O Data Factory suporta agora ambos **serviço de assinaturas de acesso partilhado** e **assinaturas de acesso partilhado de conta**. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-sas-overview.md).
>- Configuração de conjunto de dados posterior, o caminho da pasta é o caminho absoluto a partir de nível de contêiner. Tem de configurar um alinhada ao caminho do URI de SAS.

> [!TIP]
> Para gerar uma assinatura de acesso partilhado do serviço para a sua conta de armazenamento, pode executar os seguintes comandos do PowerShell. Substitua os marcadores de posição e conceder a permissão necessária.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para utilizar a autenticação da assinatura de acesso partilhado, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobStorage** (sugerida) ou **AzureStorage** (ver notas abaixo). |Sim |
| sasUri | Especifique o URI da assinatura de acesso compartilhado para os recursos de armazenamento, como blob/contêiner. <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar o token SAS em Azure Key Vault para aproveitar a rotação automática e remover a parte do token. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode usar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Se estivesse usando o serviço de ligado de tipo de "AzureStorage", ainda é suportado como-é, ao passo que lhe é sugeridas para utilizar este novo "AzureBlobStorage" ligado do tipo de serviço no futuro.

**Example:**

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

**Exemplo: armazenar chave de conta no Azure Key Vault**

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
- O URI deve ser criado no contêiner/blob correto com base na necessidade. Um URI de assinatura de acesso partilhado para um blob permite que a fábrica de dados para esse determinado blob de acesso. Um URI de assinatura de acesso compartilhado para um contêiner de armazenamento de BLOBs permite que Data Factory iterar por meio de BLOBs nesse contêiner. Para fornecer acesso a mais ou menos objetos mais tarde, ou para atualizar o URI de assinatura de acesso partilhado, lembre-se de atualizar o serviço ligado com o URI de novo.

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
| type | A propriedade de tipo deve ser definida como **AzureBlobStorage**. |Sim |
| serviceEndpoint | Especifique o ponto de final de serviço de armazenamento de Blobs do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |
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

Uma fábrica de dados pode ser associada com um [identidade de recursos do Azure gerida](data-factory-service-identity.md), que representa esta fábrica de dados específicos. Você pode usar essa identidade gerenciada diretamente para autenticação de armazenamento de BLOBs semelhante ao uso de sua própria entidade de serviço. Ele permite que esta fábrica designada para aceder e copiar dados de/para o seu armazenamento de Blobs.

Consulte autenticar o [acesso ao armazenamento do Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md) para a autenticação de armazenamento do Azure em geral. Para utilizar identidades geridas para a autenticação de recursos do Azure, siga estes passos:

1. [Recupere data Factory informações de identidade gerenciadas](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor de "ID do aplicativo de identidade de serviço" gerado junto com sua fábrica.

2. Conceda a permissão adequada de identidade gerida no armazenamento de Blobs do Azure. Consulte a [gerir direitos de acesso aos dados de armazenamento do Azure com o RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como origem**, acesso para controlar (IAM), pelo menos a conceder **leitor de dados de Blob de armazenamento** função.
    - **Como sink**, acesso para controlar (IAM), pelo menos a conceder **contribuinte de dados de Blob de armazenamento** função.

>[!IMPORTANT]
>Se você usar o polybase para carregar dados do blob (como origem ou como preparo) em SQL Data Warehouse, ao usar a autenticação de identidade gerenciada para o blob, certifique-se de também seguir as etapas 1 e 2 nesta [orientação](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) como 1) registrar seu servidor de banco de dados SQL com o Azure active Directory (Azure AD) e 2) atribuir a função de colaborador de dados de blob de armazenamento ao servidor do banco de dados SQL; o restante são tratados por Data Factory. Se o armazenamento de BLOBs estiver configurado com um ponto de extremidade de rede virtual do Azure, para usar o polybase para carregar dados dele, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo polybase.

Estas propriedades são suportadas para um serviço ligado do armazenamento de Blobs do Azure:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobStorage**. |Sim |
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. 

- Para **parquet, texto delimitado, Avro e formato binário**, consulte a seção [parquet, texto delimitado e conjunto de banco de forma binário](#format-based-dataset) .
- Para outros formatos como o **formato Orc/JSON**, consulte [outra](#other-format-dataset) seção do conjunto de exemplo.

### <a name="format-based-dataset"></a>Parquet, texto delimitado, Avro e conjunto de um formato binário

Para copiar dados de e para o armazenamento de BLOBs em parquet, texto delimitado, formato Avro ou binário, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), formato de [Avro](format-avro.md) e artigo de [formato binário](format-binary.md) em conjunto de dados com base em formato e configurações com suporte. As propriedades a seguir têm suporte para o blob `location` do Azure em configurações em conjunto de base de formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade Type do local no DataSet deve ser definida como **AzureBlobStorageLocation**. | Sim      |
| container  | O contêiner de BLOB.                                          | Sim      |
| folderPath | O caminho para a pasta sob o contêiner fornecido. Se você quiser usar curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o contêiner especificado + folderPath. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |

> [!NOTE]
>
> **AzureBlob** tipo de conjunto de dados com parquet/formato de texto mencionado na próxima seção ainda tem suporte como é para a atividade de cópia/pesquisa/GetMetadata para compatibilidade com versões anteriores, mas não funciona com o fluxo de dados de mapeamento. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Example:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

Para copiar dados de e para o armazenamento de BLOBs no formato ORC/JSON, defina a propriedade Type do conjunto de dados como **AzureBlob**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados tem de ser definida **AzureBlob**. |Sim |
| folderPath | Caminho para o contentor e a pasta no armazenamento de Blobs. <br/><br/>Há suporte para o filtro curinga no caminho, excluindo o nome do contêiner. Os curingas permitidos são `*` : (corresponde a zero ou mais caracteres `?` ) e (corresponde a zero ou a um `^` único caractere); Use para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br/><br/>Exemplos: myblobcontainer/myblobfolder/, veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Sim para a atividade de cópia/Lookup, não para a atividade GetMetadata |
| fileName | **Filtro de nome ou o caráter universal** para os BLOBs no "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os blobs na pasta. <br/><br/>Para o filtro, permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Utilize `^` para se o seu nome de ficheiro real tem carateres universais ou esse caractere de escape dentro de escape.<br/><br/>Quando o nome de arquivo não é especificado para um conjunto de resultados de saída e **preserveHierarchy** não é especificado no coletor de atividade, a atividade de cópia gera automaticamente o nome do blob com o seguinte padrão: "*Dados. [GUID de ID de execução de atividade]. [GUID If FlattenHierarchy]. [Formatar se configurado]. [compactação se configurada]* ", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz;" Se copiar a partir da origem de tabela com o nome da tabela em vez de consulta, o padrão de nome é " *[nome da tabela]. [ formato]. [compressão se configurado]* ", por exemplo, "MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| format | Se pretender copiar ficheiros como está entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se você deseja analisar ou gerar arquivos com um formato específico, há suporte para os seguintes tipos de formato de arquivo:TextFormat **, JsonFormat**, **AvroFormat**, **OrcFormat**e **ParquetFormat**. Definir o **tipo** propriedade sob **formato** para um dos seguintes valores. Para obter mais informações, consulte a [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Não (apenas para o cenário de cópia binária) |
| compression | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Os níveis de suporte são **Optimal** e **Fastest**. |Não |

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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem do armazenamento de BLOBs e de sink.

### <a name="blob-storage-as-a-source-type"></a>Armazenamento de BLOBs como um tipo de origem

- Para copiar de **parquet, texto delimitado, Avro e formato binário**, consulte a seção [parquet, texto delimitado e fonte de formato binário](#format-based-source) .
- Para copiar de outros formatos como o **formato Orc/JSON**, consulte [outra seção fonte de formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, texto delimitado, Avro e fonte de formato binário

Para copiar dados de e para o armazenamento de BLOBs em **parquet, texto delimitado, formato Avro ou binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), formato de [Avro](format-avro.md) e artigo de [formato binário](format-binary.md) em conjunto de dados com base em formato e com suporte Configurações. As propriedades a seguir têm suporte para o blob `storeSettings` do Azure em configurações em fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AzureBlobStorageReadSetting**. | Sim                                           |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que quando recursiva é definida como true e o sink é um arquivo baseado em arquivo, uma pasta vazia ou uma subpasta não é copiada ou criada no coletor. Valores permitidos são **true** (predefinição) e **falso**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga no contêiner fornecido configurado no conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são `*` : (corresponde a zero ou mais caracteres `?` ) e (corresponde a zero ou a um `^` único caractere); Use para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br>Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome do arquivo com caracteres curinga no contêiner especificado + folderPath/wildcardFolderPath para filtrar os arquivos de origem. <br>Os curingas permitidos são `*` : (corresponde a zero ou mais caracteres `?` ) e (corresponde a zero ou a um `^` único caractere); Use para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro.  Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no DataSet |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado. | Não                                            |
| modifiedDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto parquet/delimitado, a origem da atividade de cópia do tipo **blobry** mencionada na próxima seção ainda tem suporte como está para compatibilidade com versões anteriores. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Example:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSetting",
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

Para copiar dados do armazenamento de BLOBs no **formato orc ou JSON**, defina o tipo de origem na atividadede cópia como blobname. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida **BlobSource**. |Sim |
| recursive | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que quando recursiva é definida como true e o sink é um arquivo baseado em arquivo, uma pasta vazia ou uma subpasta não é copiada ou criada no coletor.<br/>Valores permitidos são **true** (predefinição) e **falso**. | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

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

- Para copiar de **parquet, texto delimitado, Avro e formato binário**, consulte a seção [parquet, texto delimitado e fonte de formato binário](#format-based-source) .
- Para copiar de outros formatos como o **formato Orc/JSON**, consulte [outra seção fonte de formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, texto delimitado, Avro e fonte de formato binário

Para copiar dados do armazenamento de BLOBs em **parquet, texto delimitado, formato Avro ou binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), formato de [Avro](format-avro.md) e artigo de [formato binário](format-binary.md) na fonte da atividade de cópia baseada em formato e configurações com suporte. As propriedades a seguir têm suporte para o blob `storeSettings` do Azure em configurações no coletor de cópia com base em formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AzureBlobStorageWriteSetting**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os ficheiros de destino têm nomes de geradas automaticamente. <br/><b>-MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome de ficheiro ou blob for especificado, o nome de ficheiro intercalada é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não       |

> [!NOTE]
> Para o formato de texto parquet/delimitado, o coletor de atividade de cópia do tipo **BlobSink** mencionado na próxima seção ainda tem suporte como está para compatibilidade com versões anteriores. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Example:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Outro coletor de formato

Para copiar dados para o armazenamento de blob no **formato Orc/JSON**, defina o tipo de coletor na atividade de cópia como **BlobSink**. As seguintes propriedades são suportadas os **sink** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de sink de atividade de cópia tem de ser definida **BlobSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os ficheiros de destino têm nomes de geradas automaticamente. <br/><b>-MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome de ficheiro ou blob for especificado, o nome de ficheiro intercalada é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Example:**

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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e o nome do arquivo com filtros curinga.

| folderPath | fileName | recursive | Estrutura da pasta de origem e resultado do filtro (arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (vazio, usar padrão) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (vazio, usar padrão) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;PastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursiva e copyBehavior

Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| recursive | copyBehavior | Estrutura de pastas de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 conteúdo é mesclado num ficheiro com um nome de ficheiro gerado automaticamente. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com um nome de ficheiro gerado automaticamente. nome gerado automaticamente para File1<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Aprenda detalhes da [transformação de origem](data-flow-source.md) e da [transformação de coletor](data-flow-sink.md) no fluxo de dados de mapeamento.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
