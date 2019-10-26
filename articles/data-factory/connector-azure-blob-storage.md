---
title: Copiar dados de ou para o armazenamento de BLOBs do Azure usando Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o armazenamento de BLOBs do Azure ou do armazenamento de BLOBs para armazenamentos de dados de coletor com suporte, usando Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: a68549622972bfa031bc2934473dc65f0a656231
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935706"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Copiar dados de ou para o armazenamento de BLOBs do Azure usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-blob-connector.md)
> * [Versão atual](connector-azure-blob-storage.md)

Este artigo descreve como copiar dados de e para o armazenamento de BLOBs do Azure. Para saber mais sobre Azure Data Factory, leia o [artigo introdutório](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector de blob do Azure tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, esse conector de armazenamento de BLOBs dá suporte a:

- Copiar blobs de e para contas de armazenamento do Azure de uso geral e armazenamento de BLOBs quente/frio. 
- Copiando BLOBs usando chave de conta, assinatura de acesso compartilhado do serviço, entidade de serviço ou identidades gerenciadas para autenticações de recursos do Azure.
- Copiando blobs de blocos, acréscimos ou BLOBs de páginas e copiando dados somente para BLOBs de blocos.
- Copiando BLOBs como está ou analisando ou gerando BLOBs com [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

>[!IMPORTANT]
>Se você habilitar a opção **permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento** nas configurações do firewall do armazenamento do Azure e quiser usar o tempo de execução de integração do Azure para se conectar ao armazenamento de BLOBs, será necessário usar a [autenticação de identidade gerenciada](#managed-identity).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao armazenamento de BLOBs.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O conector de blob do Azure dá suporte aos seguintes tipos de autenticação, consulte a seção correspondente em detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação de assinatura de acesso compartilhado](#shared-access-signature-authentication)
- [Autenticação do principal de serviço](#service-principal-authentication)
- [Identidades gerenciadas para autenticação de recursos do Azure](#managed-identity)

>[!NOTE]
>Ao usar o polybase para carregar dados em SQL Data Warehouse, se o armazenamento de blob de origem ou de preparo estiver configurado com o ponto de extremidade de rede virtual, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo polybase e usar o Integration Runtime de hospedagem interna com a versão 3,18 ou superior. Consulte a seção [autenticação de identidade gerenciada](#managed-identity) com mais pré-requisitos de configuração.

>[!NOTE]
>As atividades HDInsights e Azure Machine Learning só dão suporte à autenticação de chave de conta de armazenamento de BLOBs do Azure

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para usar a autenticação de chave de conta de armazenamento, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **AzureBlobStorage** (sugerida) ou **AzureStorage** (consulte as observações abaixo). |Sim |
| connectionString | Especifique as informações necessárias para se conectar ao armazenamento para a propriedade connectionString. <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar a chave de conta em Azure Key Vault e efetuar pull da configuração de `accountKey` fora da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar os Integration Runtime Azure Integration Runtime ou auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

>[!NOTE]
>Não há suporte para o ponto de extremidade do serviço blob secundário ao usar a autenticação de chave de conta. Você pode usar outros tipos de autenticação.

>[!NOTE]
>Se você estivesse usando o serviço vinculado de tipo "AzureStorage", ele ainda tem suporte como está, enquanto você é sugerido para usar esse novo tipo de serviço vinculado "AzureBlobStorage" no futuro.

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

### <a name="shared-access-signature-authentication"></a>Autenticação de assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos em sua conta de armazenamento. Você pode usar uma assinatura de acesso compartilhado para conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por um período especificado. Você não precisa compartilhar suas chaves de acesso da conta. A assinatura de acesso compartilhado é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar os recursos de armazenamento com a assinatura de acesso compartilhado, o cliente só precisa passar a assinatura de acesso compartilhado para o construtor ou método apropriado. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [assinaturas de acesso compartilhado: entender o modelo de assinatura de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- O Data Factory agora dá suporte a **assinaturas de acesso compartilhado de serviço** e **assinaturas de acesso compartilhado de conta**. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-sas-overview.md).
>- Na configuração mais recente do conjunto de configurações, o caminho da pasta é o caminho absoluto a partir do nível de contêiner. Você precisa configurar um alinhado com o caminho no URI de SAS.

> [!TIP]
> Para gerar uma assinatura de acesso compartilhado de serviço para sua conta de armazenamento, você pode executar os comandos do PowerShell a seguir. Substitua os espaços reservados e conceda a permissão necessária.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para usar a autenticação de assinatura de acesso compartilhado, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **AzureBlobStorage** (sugerida) ou **AzureStorage** (consulte as observações abaixo). |Sim |
| sasUri | Especifique o URI da assinatura de acesso compartilhado para os recursos de armazenamento, como blob/contêiner. <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar o token SAS em Azure Key Vault para aproveitar a rotação automática e remover a parte do token. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Azure Integration Runtime ou o Integration Runtime de hospedagem interna (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

>[!NOTE]
>Se você estivesse usando o serviço vinculado de tipo "AzureStorage", ele ainda tem suporte como está, enquanto você é sugerido para usar esse novo tipo de serviço vinculado "AzureBlobStorage" no futuro.

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

Ao criar um URI de assinatura de acesso compartilhado, considere os seguintes pontos:

- Defina permissões de leitura/gravação apropriadas em objetos com base em como o serviço vinculado (leitura, gravação, leitura/gravação) é usado em seu data factory.
- Defina o **tempo de expiração** adequadamente. Certifique-se de que o acesso aos objetos de armazenamento não expire no período ativo do pipeline.
- O URI deve ser criado no contêiner/blob correto com base na necessidade. Um URI de assinatura de acesso compartilhado para um blob permite que Data Factory acesse esse blob específico. Um URI de assinatura de acesso compartilhado para um contêiner de armazenamento de BLOBs permite que Data Factory iterar por meio de BLOBs nesse contêiner. Para fornecer acesso a mais ou menos objetos posteriormente, ou para atualizar o URI da assinatura de acesso compartilhado, lembre-se de atualizar o serviço vinculado com o novo URI.

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para autenticação de entidade de serviço de armazenamento do Azure em geral, consulte [autenticar o acesso ao armazenamento do Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md).

Para usar a autenticação de entidade de serviço, siga estas etapas:

1. Registre uma entidade de aplicativo no Azure Active Directory (AD do Azure) seguindo [registrar seu aplicativo com um locatário do Azure ad](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que você usa para definir o serviço vinculado:

    - ID da aplicação
    - Chave do aplicativo
    - ID do inquilino

2. Conceda a permissão apropriada à entidade de serviço no armazenamento de BLOBs do Azure. Consulte [gerenciar direitos de acesso aos dados do armazenamento do Azure com RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como fonte**, no controle de acesso (iam), conceda pelo menos a função de **leitor de dados de blob de armazenamento** .
    - **Como coletor**, no controle de acesso (iam), conceda pelo menos a função de **colaborador de dados de blob de armazenamento** .

Essas propriedades têm suporte para um serviço vinculado do armazenamento de BLOBs do Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **AzureBlobStorage**. |Sim |
| serviceEndpoint | Especifique o ponto de extremidade do serviço de armazenamento de BLOBs do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| vários | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Recupere-o passando o mouse no canto superior direito do portal do Azure. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar os Integration Runtime Azure Integration Runtime ou auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

>[!NOTE]
>A autenticação da entidade de serviço é suportada apenas pelo serviço vinculado do tipo "AzureBlobStorage", mas não pelo serviço vinculado do tipo "AzureStorage" anterior.

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

### <a name="managed-identity"></a>Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data Factory específico. Você pode usar essa identidade gerenciada diretamente para autenticação de armazenamento de BLOBs semelhante ao uso de sua própria entidade de serviço. Ele permite que essa fábrica designada acesse e copie dados de/para seu armazenamento de BLOBs.

Consulte [autenticar o acesso ao armazenamento do Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md) para a autenticação de armazenamento do Azure em geral. Para usar identidades gerenciadas para a autenticação de recursos do Azure, siga estas etapas:

1. [Recupere data Factory informações de identidade gerenciadas](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor de "ID do aplicativo de identidade de serviço" gerado junto com sua fábrica.

2. Conceda a permissão adequada à identidade gerenciada no armazenamento de BLOBs do Azure. Consulte [gerenciar direitos de acesso aos dados do armazenamento do Azure com RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como fonte**, no controle de acesso (iam), conceda pelo menos a função de **leitor de dados de blob de armazenamento** .
    - **Como coletor**, no controle de acesso (iam), conceda pelo menos a função de **colaborador de dados de blob de armazenamento** .

>[!IMPORTANT]
>Se você usar o polybase para carregar dados do blob (como origem ou como preparo) em SQL Data Warehouse, ao usar a autenticação de identidade gerenciada para o blob, certifique-se de também seguir as etapas 1 e 2 nesta [orientação](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) como 1) registrar seu servidor de banco de dados SQL com o Azure active Directory (Azure AD) e 2) atribuir a função de colaborador de dados de blob de armazenamento ao servidor do banco de dados SQL; o restante são tratados por Data Factory. Se o armazenamento de BLOBs estiver configurado com um ponto de extremidade de rede virtual do Azure, para usar o polybase para carregar dados dele, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo polybase.

Essas propriedades têm suporte para um serviço vinculado do armazenamento de BLOBs do Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **AzureBlobStorage**. |Sim |
| serviceEndpoint | Especifique o ponto de extremidade do serviço de armazenamento de BLOBs do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar os Integration Runtime Azure Integration Runtime ou auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

> [!NOTE]
> As identidades gerenciadas para a autenticação de recursos do Azure só têm suporte pelo serviço vinculado de tipo "AzureBlobStorage", mas não pelo serviço vinculado de tipo "AzureStorage" anterior. 

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para o blob do Azure em configurações `location` no conjunto de base de formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade Type do local no DataSet deve ser definida como **AzureBlobStorageLocation**. | Sim      |
| contentor  | O contêiner de BLOB.                                          | Sim      |
| folderPath | O caminho para a pasta sob o contêiner fornecido. Se você quiser usar curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o contêiner especificado + folderPath. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |

**Exemplo:**

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

### <a name="legacy-dataset-model"></a>Modelo de conjunto de DataSet herdado

>[!NOTE]
>O modelo de conjunto de itens a seguir ainda tem suporte como está para compatibilidade com versões anteriores. Você deve usar o novo modelo mencionado na seção acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como **AzureBlob**. |Sim |
| folderPath | Caminho para o contêiner e a pasta no armazenamento de BLOBs. <br/><br/>Há suporte para o filtro curinga no caminho, excluindo o nome do contêiner. Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br/><br/>Exemplos: myblobcontainer/myblobfolder/, veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Sim para atividade de cópia/pesquisa, não para a atividade GetMetadata |
| fileName | O **nome ou o filtro curinga** para os BLOBs sob o "FolderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de pontos apontará para todos os BLOBs na pasta. <br/><br/>Para filtros, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome de arquivo real tiver curinga ou este caractere de escape dentro de.<br/><br/>Quando fileName não é especificado para um conjunto de dados de saída e **preserveHierarchy** não é especificado no coletor de atividade, a atividade de cópia gera automaticamente o nome do blob com o seguinte padrão: "*Data. [ GUID de ID de execução de atividade]. [GUID If FlattenHierarchy]. [Formatar se configurado]. [compactação se configurada]* ", por exemplo," Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz "; Se você copiar da fonte de tabela usando o nome da tabela em vez da consulta, o padrão de nome será " *[nome da tabela]. [ formato]. [compactação se configurada]* ", por exemplo," MyTable. csv ". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem o valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem o valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado.| Não |
| ao | Se você quiser copiar arquivos como estão entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, há suporte para os seguintes tipos de formato de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**e **ParquetFormat**. Defina a propriedade **Type** em **Format** para um desses valores. Para obter mais informações, consulte as seções [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), formato [Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format)e [formato parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Não (somente para o cenário de cópia binária) |
| çã | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [formatos de arquivo com suporte e codecs de compactação](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são **gzip**, **deflate**, **bzip2**e **ZipDeflate**.<br/>Os níveis com suporte são **ideais** e **mais rápidos**. |Não |

>[!TIP]
>Para copiar todos os BLOBs em uma pasta, especifique **FolderPath** apenas.<br>Para copiar um único blob com um nome específico, especifique **FolderPath** com a parte da **pasta e o nome do** arquivo com o nome.<br>Para copiar um subconjunto de BLOBs em uma pasta, especifique **FolderPath** com a parte da pasta e o **nome do arquivo** com o filtro curinga. 

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte de armazenamento de BLOBs e pelo coletor.

### <a name="blob-storage-as-a-source-type"></a>Armazenamento de BLOBs como um tipo de origem

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para o blob do Azure em configurações `storeSettings` em fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade Type em `storeSettings` deve ser definida como **AzureBlobStorageReadSetting**. | Sim                                           |
| recursiva                | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga no contêiner fornecido configurado no conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro. <br>Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome do arquivo com caracteres curinga no contêiner especificado + folderPath/wildcardFolderPath para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); Use `^` para escapar se o nome real da pasta tiver curinga ou este caractere de escape dentro.  Veja mais exemplos nos [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no DataSet |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem o valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime será selecionado. | Não                                            |
| modifiedDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto parquet/delimitado, a origem da atividade de cópia do tipo **blobry** mencionada na próxima seção ainda tem suporte como está para compatibilidade com versões anteriores. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Exemplo:**

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

#### <a name="legacy-source-model"></a>Modelo de origem herdado

>[!NOTE]
>O modelo de origem de cópia a seguir ainda tem suporte como está para compatibilidade com versões anteriores. Você deve usar o novo modelo mencionado acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como **blobname**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor.<br/>Os valores permitidos são **true** (padrão) e **false**. | Não |
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

### <a name="blob-storage-as-a-sink-type"></a>Armazenamento de BLOBs como um tipo de coletor

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para o blob do Azure em configurações `storeSettings` no coletor de cópia com base em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade Type em `storeSettings` deve ser definida como **AzureBlobStorageWriteSetting**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a origem é arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Os valores permitidos são:<br/><b>-PreserveHierarchy (padrão)</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm nomes gerados automaticamente. <br/><b>-MergeFiles</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo ou do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, será um nome de arquivo gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não       |

**Exemplo:**

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

#### <a name="legacy-sink-model"></a>Modelo de coletor herdado

>[!NOTE]
>O modelo de coletor de cópia a seguir ainda tem suporte como está para compatibilidade com versões anteriores. Você deve usar o novo modelo mencionado acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do coletor da atividade de cópia deve ser definida como **BlobSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a origem é arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Os valores permitidos são:<br/><b>-PreserveHierarchy (padrão)</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm nomes gerados automaticamente. <br/><b>-MergeFiles</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo ou do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, será um nome de arquivo gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e o nome do arquivo com filtros curinga.

| folderPath | fileName | recursiva | Estrutura da pasta de origem e resultado do filtro (arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (vazio, usar padrão) | false | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;pastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | (vazio, usar padrão) | true | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;pastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | false | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;pastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | true | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;pastaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**arquivo5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos recursivos e copyBehaviors

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursivos e copyBehavior.

| recursiva | copyBehavior | Estrutura da pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a mesma estrutura que a origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | O Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | O Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo de file1 + arquivo2 + arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não é selecionado. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para arquivo2<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não é selecionado. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo de file1 + arquivo2 é mesclado em um arquivo com um nome de arquivo gerado automaticamente. nome gerado automaticamente para file1<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não é selecionado. |

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Aprenda detalhes da [transformação de origem](data-flow-source.md) e da [transformação de coletor](data-flow-sink.md) no fluxo de dados de mapeamento.

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter detalhes sobre as propriedades, marque a [atividade de exclusão](delete-activity.md)

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
