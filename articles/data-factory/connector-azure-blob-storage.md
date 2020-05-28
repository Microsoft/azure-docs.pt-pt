---
title: Copiar e transformar dados no armazenamento da Blob Azure
description: Aprenda a copiar dados de e para o armazenamento blob, e transforme dados no armazenamento blob utilizando data factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: 43474100844f3828107f67f1e80dca57692fec59
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021029"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Copiar e transformar dados no armazenamento da Blob Azure utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-blob-connector.md)
> * [Versão atual](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para o armazenamento da Blob Azure, e usar o Data Flow para transformar dados no armazenamento do Azure Blob. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

>[!TIP]
>Para o cenário de migração de data lake ou data warehouse, saiba mais com [a Use Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para o Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Blob é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Mapeando o fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Para a atividade da Cópia, este conector de armazenamento Blob suporta:

- Copiar bolhas de e para as contas de armazenamento azure de e para fins gerais e armazenamento de bolhas quente/fresca. 
- Copiar bolhas utilizando a chave de conta, a assinatura de acesso partilhado do serviço, o diretor de serviço ou as identidades geridas para autenticações de recursos Azure.
- Copiar bolhas de bloco, apêndice ou bolhas de página e copiar dados apenas para bloquear bolhas.
- Copiar bolhas como estão ou analisar ou gerar bolhas com [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs.md)de compressão .
- [Preservar metadados](#preserve-metadata-during-copy)de ficheiros durante a cópia .

>[!IMPORTANT]
>Se ativar os **serviços da Microsoft fidedignos** para aceder a esta opção de conta de armazenamento nas definições de firewall do Azure Storage e pretender utilizar o tempo de execução de integração do Azure para se ligar ao seu Armazenamento Blob, deve utilizar a [autenticação](#managed-identity)de identidade gerida .

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do armazenamento Blob.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

O conector Azure Blob suporta os seguintes tipos de autenticação, consulte a secção correspondente sobre os detalhes:

- [Autenticação da chave da conta](#account-key-authentication)
- [Autenticação de assinatura de acesso partilhado](#shared-access-signature-authentication)
- [Autenticação do principal de serviço](#service-principal-authentication)
- [Identidades geridas para autenticação de recursos Azure](#managed-identity)

>[!NOTE]
>Ao utilizar a PolyBase para carregar dados no SQL Data Warehouse, se o seu armazenamento de origem ou encenação blob estiver configurado com ponto final da Rede Virtual, deve utilizar a autenticação de identidade gerida conforme exigido pela PolyBase, e utilizar o Tempo de Execução de Integração Auto-hospedado com a versão 3.18 ou superior. Consulte a secção de [autenticação](#managed-identity) de identidade gerida com mais pré-requisitos de configuração.

>[!NOTE]
>As atividades hDInsights e Azure Machine Learning apenas suportam a autenticação da chave de armazenamento Azure Blob.

### <a name="account-key-authentication"></a>Autenticação da chave da conta

Para utilizar a autenticação da chave da conta de armazenamento, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para **AzureBlobStorage** (sugerido) ou **AzureStorage** (ver notas abaixo). |Sim |
| conexãoString | Especifique as informações necessárias para ligar ao Armazenamento para a propriedade de conexãoString. <br/> Também pode colocar a chave de conta no Cofre de Chaves Azure e retirar a `accountKey` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo do Cofre chave Azure com mais detalhes. |Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver numa rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

>[!NOTE]
>O ponto final do serviço de blob secundário não é suportado quando se utiliza a autenticação da chave da conta. Pode utilizar outros tipos de autenticação.

>[!NOTE]
>Se estava a utilizar o serviço ligado ao tipo "AzureStorage", ainda é suportado como está, enquanto é sugerido que utilize este novo tipo de serviço ligado ao "AzureBlobStorage" a avançar.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: chave da conta da loja no Cofre chave Azure**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
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

### <a name="shared-access-signature-authentication"></a>Autenticação de assinatura de acesso partilhado

Uma assinatura de acesso partilhado proporciona acesso delegado aos recursos na sua conta de armazenamento. Pode utilizar uma assinatura de acesso partilhado para conceder permissões limitadas a objetos na sua conta de armazenamento por um tempo determinado. Não tens de partilhar as chaves de acesso à conta. A assinatura de acesso partilhado é um URI que engloba nos seus parâmetros de consulta toda a informação necessária para o acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com a assinatura de acesso partilhado, o cliente apenas precisa passar a assinatura de acesso partilhado ao construtor ou método apropriado. Para obter mais informações sobre assinaturas de acesso partilhado, consulte [assinaturas de acesso partilhado: Compreenda o modelo de assinatura](../storage/common/storage-dotnet-shared-access-signature-part-1.md)de acesso partilhado .

> [!NOTE]
>- Data Factory agora suporta tanto **assinaturas** de acesso partilhado de serviço como **assinaturas de acesso partilhado**de conta. Para obter mais informações sobre assinaturas de acesso partilhado, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md).
>- Na configuração posterior do conjunto de dados, o caminho da pasta é o caminho absoluto a partir do nível do contentor. Precisa de configurar um alinhado com o caminho no seu SAS URI.

Para utilizar a autenticação de assinatura de acesso partilhado, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para **AzureBlobStorage** (sugerido) ou **AzureStorage** (ver notas abaixo). |Sim |
| sasUri | Especifique a assinatura de acesso partilhado URI aos recursos de armazenamento, tais como blob/container. <br/>Marque este campo como um SecureString para o armazenar de forma segura na Data Factory. Também pode colocar token SAS no Cofre de Chaves Azure para alavancar a rotação automática e remover a porção simbólica. Consulte as seguintes amostras e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo do Cofre chave Azure com mais detalhes. |Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Integração Auto-Hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

>[!NOTE]
>Se estava a utilizar o serviço ligado ao tipo "AzureStorage", ainda é suportado como está, enquanto é sugerido que utilize este novo tipo de serviço ligado ao "AzureBlobStorage" a avançar.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: chave da conta da loja no Cofre chave Azure**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
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

Quando criar uma assinatura de acesso partilhado URI, considere os seguintes pontos:

- Detete permissões de leitura/escrita adequadas em objetos com base na forma como o serviço ligado (ler, escrever, ler/escrever) é utilizado na sua fábrica de dados.
- Definir o **tempo de validade** adequadamente. Certifique-se de que o acesso aos objetos de armazenamento não expira dentro do período ativo do gasoduto.
- O URI deve ser criado no recipiente/bolha certo com base na necessidade. Uma assinatura de acesso partilhado URI a uma bolha permite que a Data Factory aceda a essa bolha em particular. Uma assinatura de acesso partilhado URI a um recipiente de armazenamento Blob permite que a Fábrica de Dados iteraatravés de bolhas nesse recipiente. Para fornecer acesso a mais ou menos objetos mais tarde, ou para atualizar a assinatura de acesso partilhado URI, lembre-se de atualizar o serviço ligado com o novo URI.

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para a autenticação principal do serviço de armazenamento Azure em geral, consulte o [acesso authenticado ao Armazenamento Azure utilizando o Diretório Ativo Azure](../storage/common/storage-auth-aad.md).

Para utilizar a autenticação principal do serviço, siga estes passos:

1. Registe uma entidade de candidatura no Azure Ative Directory (Azure AD) seguindo o registo da sua candidatura junto de [um inquilino da Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:

    - ID da Aplicação
    - Chave de aplicação
    - ID do inquilino

2. Conceda ao principal de serviço a permissão adequada no armazenamento da Blob Azure. Consulte a Gestão dos direitos de acesso aos dados do [Armazenamento Azure com RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como fonte,** no controlo de acesso (IAM), conceda pelo menos o papel de Leitor de **Dados Blob de Armazenamento.**
    - **Como pia,** no controlo de acesso (IAM), concede pelo menos o papel de Contribuinte de **Dados blob** de armazenamento.

Estas propriedades são suportadas para um serviço ligado ao armazenamento Azure Blob:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureBlobStorage**. |Sim |
| serviçoEndpoint | Especifique o ponto final do serviço de armazenamento Azure Blob com o padrão de `https://<accountName>.blob.core.windows.net/` . |Sim |
| serviçoPrincipalId | Especifique a identificação do cliente do pedido. | Sim |
| serviçoPrincipalKey | Especifique a chave da aplicação. Marque este campo como um **SecureString** para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver numa rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

>[!NOTE]
>A autenticação principal do serviço só é suportada pelo serviço ligado ao tipo "AzureBlobStorage", mas não pelo serviço ligado ao tipo "AzureStorage" anterior.

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Identidades geridas para autenticação de recursos Azure

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos Azure,](data-factory-service-identity.md)que representa esta fábrica de dados específica. Pode utilizar diretamente esta identidade gerida para autenticação de armazenamento Blob semelhante à utilização do seu próprio diretor de serviço. Permite que esta fábrica designada aceda e copie dados de/para o seu armazenamento Blob.

Consulte o [acesso authenticado ao Armazenamento Azure utilizando o Diretório Ativo Azure](../storage/common/storage-auth-aad.md) para autenticação de armazenamento Azure em geral. Para utilizar identidades geridas para autenticação de recursos Azure, siga estes passos:

1. [Recuperar informações](data-factory-service-identity.md#retrieve-managed-identity) de identidade geridas pela fábrica de dados através da cópia do valor do ID de objeto de **identidade gerido** gerado juntamente com a sua fábrica.

2. Conceda a permissão adequada à identidade gerida no armazenamento de Azure Blob. Consulte a Gestão dos direitos de acesso aos dados do [Armazenamento Azure com RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como fonte,** no controlo de acesso (IAM), conceda pelo menos o papel de Leitor de **Dados Blob de Armazenamento.**
    - **Como pia,** no controlo de acesso (IAM), concede pelo menos o papel de Contribuinte de **Dados blob** de armazenamento.

>[!IMPORTANT]
>Se utilizar a PolyBase para carregar dados da Blob (como fonte ou como encenação) no SQL Data Warehouse, quando utilizar a autenticação de identidade gerida para a Blob, certifique-se de que também segue os passos 1 e 2 [nesta orientação](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) para 1) registe o seu servidor com o Azure Ative Directory (Azure AD) e 2) atribuindo ao seu servidor a função de Colaborador de Dados blob de armazenamento; o resto é tratado pela Data Factory. Se o seu armazenamento Blob estiver configurado com um ponto final da Rede Virtual Azure, para utilizar o PolyBase para carregar dados a partir dele, deve utilizar a autenticação de identidade gerida conforme exigido pela PolyBase.

Estas propriedades são suportadas para um serviço ligado ao armazenamento Azure Blob:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureBlobStorage**. |Sim |
| serviçoEndpoint | Especifique o ponto final do serviço de armazenamento Azure Blob com o padrão de `https://<accountName>.blob.core.windows.net/` . |Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver numa rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

> [!NOTE]
> As identidades geridas para a autenticação de recursos Azure só são suportadas pelo serviço ligado ao tipo "AzureBlobStorage", mas não pelo anterior serviço ligado ao tipo "AzureStorage". 

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para O Blob Azure em `location` definições no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A propriedade tipo da localização em conjunto de dados deve ser definida para **AzureBlobStorageLocation**. | Sim      |
| contentor  | O recipiente de bolhas.                                          | Sim      |
| folderPath | O caminho para a pasta sob o recipiente dado. Se pretender utilizar o wildcard para filtrar a pasta, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |
| fileName   | O nome do ficheiro no recipiente dado + pastaPath. Se pretender utilizar ficheiros wildcard para filtrar ficheiros, ignore esta definição e especifique nas definições de fonte de atividade. | Não       |

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

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte de armazenamento blob e pia.

### <a name="blob-storage-as-a-source-type"></a>Armazenamento blob como um tipo de fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para O Blob Azure em `storeSettings` definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade do tipo em baixo `storeSettings` deve ser definida para **AzureBlobStorageReadSettings**. | Sim                                           |
| ***Localize os ficheiros para copiar:*** |  |  |
| OPÇÃO 1: caminho estático<br> | Cópia do recipiente ou da pasta/ficheiro especificado no conjunto de dados. Se pretender copiar todas as bolhas de um recipiente/pasta, especifique adicionalmente `wildcardFileName` como `*` . |  |
| OPÇÃO 2: prefixo blob<br>- prefixo | Prefixo para o nome blob sob o recipiente dado configurado em conjunto de dados para filtrar bolhas de origem. Blobs cujo nome começa com `container_in_dataset/this_prefix` são selecionados. Utiliza o filtro lateral de serviço da Blob que proporciona um melhor desempenho do que o filtro wildcard. | Não                                                          |
| OPÇÃO 3: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard sob o recipiente dado configurado no conjunto de dados para filtrar as pastas de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou personagem único); use `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga no seu interior. <br>Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 3: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard no recipiente dado + pastaPath/wildcardFolderPath para filtrar ficheiros de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou personagem único); use `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga no seu interior.  Consulte mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 4: uma lista de ficheiros<br>- fileListPath | Indica copiar um determinado conjunto de ficheiros. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar, um ficheiro por linha que é o caminho relativo para o caminho configurado no conjunto de dados.<br/>Ao utilizar esta opção, não especifique o nome do ficheiro no conjunto de dados. Veja mais exemplos nos [exemplos da lista de ficheiros](#file-list-examples). |Não |
| ***Configurações adicionais:*** |  | |
| recursivo | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Note que quando se recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia. <br>Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**.<br>Esta propriedade não se aplica quando configura `fileListPath` . |Não |
| alteradoDatetimeStart    | Filtro de ficheiros com base no atributo: Última Modificação. <br>Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br> As propriedades podem ser NU, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando tem o valor da `modifiedDatetimeStart` data mas `modifiedDatetimeEnd` é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando tem o valor da `modifiedDatetimeEnd` data mas `modifiedDatetimeStart` é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.<br/>Esta propriedade não se aplica quando configura `fileListPath` . | Não                                            |
| alteradoDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto Parquet/delimitado, a fonte de atividade de cópia do tipo **BlobSource** mencionada na próxima secção ainda é suportada como é para retrocompatibilidade. É-lhe sugerido que utilize este novo modelo para avançar, e a UI de autoria da ADF mudou para gerar estes novos tipos.

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="blob-storage-as-a-sink-type"></a>Armazenamento blob como um tipo de pia

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para O Blob Azure em `storeSettings` definições em sumidouro de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade do tipo em baixo `storeSettings` deve ser definida para **AzureBlobStorageWriteDefinições**. | Sim      |
| copiarComportamento             | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- PreserveHierarchy (predefinição)</b>: Preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro alvo para a pasta-alvo.<br/><b>- Hierarquia flattena</b>: Todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro ou da bolha for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não       |
| blockSizeInMB | Especifique o tamanho do bloco em MB usado para escrever dados para bloquear bolhas. Saiba mais [sobre block blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>O valor permitido é **entre 4 e 100 MB.** <br/>Por predefinição, a ADF determina automaticamente o tamanho do bloco com base no tipo e dados da loja de origem. Para cópias não binárias em Blob, o tamanho do bloco predefinido é de 100 MB de modo a encaixar no máximo 4,95 dados de TB. Pode não ser ideal quando os seus dados não são grandes, especialmente quando utiliza o Tempo de Funcionamento de Integração Auto-hospedado com uma rede deficiente, resultando num tempo de funcionamento ou problema de desempenho. Pode especificar explicitamente um tamanho de bloco, enquanto garantir que o blockSizeInMB*50000 é grande o suficiente para armazenar os dados, caso contrário a execução da atividade de cópia falhará. | Não |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não       |

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
                    "type": "AzureBlobStorageWriteSettings",
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
| `container/Folder*` | (vazio, utilização por defeito) | false | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (vazio, utilização por defeito) | true | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra pastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de ficheiros

Esta secção descreve o comportamento resultante da utilização do caminho da lista de ficheiros na fonte de atividade da cópia.

Assumindo que tem a seguinte estrutura de pasta de origem e que pretende copiar os ficheiros em negrito:

| Estrutura de origem da amostra                                      | Conteúdo em FileListToCopy.txt                             | Configuração ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subpasta1/File3.csv<br>Subpasta1/File5.csv | **No conjunto de dados:**<br>- Contentor:`container`<br>- Caminho das pastas:`FolderA`<br><br>**Na fonte de atividade de cópia:**<br>- Caminho da lista de ficheiros:`container/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar, um ficheiro por linha com o caminho relativo para o caminho configurado no conjunto de dados. |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos recursivos e copyBehavior

Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de valores recursivos e copyBehavior.

| recursivo | copiarComportamento | Estrutura de pasta de origem | Alvo resultante |
|:--- |:--- |:--- |:--- |
| true |preservar Hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo Pasta 1 é criada com a mesma estrutura que a fonte:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 |
| true |achatar a hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File5 |
| true |mergeFiles | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 conteúdos são fundidos num só ficheiro com um nome de ficheiro autogerado. |
| false |preservar Hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo Pasta 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com File3, File4 e File5 não é captada. |
| false |achatar a hierarquia | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo Pasta 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/><br/>Subpasta1 com File3, File4 e File5 não é captada. |
| false |mergeFiles | Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5 | A pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos File1 + File2 são fundidos num só ficheiro com um nome de ficheiro autogerado. nome autogerado para File1<br/><br/>Subpasta1 com File3, File4 e File5 não é captada. |

## <a name="preserve-metadata-during-copy"></a>Preservar metadados durante a cópia

Quando copia ficheiros da Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 para O Armazenamento de Lagos Azure Data Gen2/Azure Blob, pode optar por preservar os metadados do ficheiro juntamente com os dados. Saiba mais com [a Preserve metadados.](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="mapping-data-flow-properties"></a>Mapeando propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever ficheiros a partir do Armazenamento De Blob Azure em formato JSON, Avro, Texto Delimited ou Parquet. Para obter mais informações, consulte a [transformação](data-flow-source.md) de origem e a transformação do [sumidouro](data-flow-sink.md) na funcionalidade de fluxo de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

Na transformação da fonte, pode ler-se a partir de um recipiente, pasta ou ficheiro individual no Armazenamento De Blob Azure. O separador **opções Source** permite-lhe gerir a forma como os ficheiros são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de origem")

**Caminho wildcard:** A utilização de um padrão wildcard instruirá a ADF a passar por cada pasta correspondente e arquivar numa única transformação de Origem. Esta é uma forma eficaz de processar vários ficheiros dentro de um único fluxo. Adicione vários padrões de correspondência wildcard com o sinal + que aparece ao pairar sobre o padrão wildcard existente.

Do seu recipiente de origem, escolha uma série de ficheiros que correspondam a um padrão. Só o recipiente pode ser especificado no conjunto de dados. O seu caminho wildcard deve, portanto, incluir também o seu caminho de pasta a partir da pasta raiz.

Exemplos wildcard:

* ```*```Representa qualquer conjunto de caracteres
* ```**```Representa a nidificação do diretório recursivo
* ```?```Substitui um personagem
* ```[]```Corresponde a um dos mais caracteres nos suportes

* ```/data/sales/**/*.csv```Obtém todos os ficheiros CSV em /data/vendas
* ```/data/sales/20??/**/```Recebe todos os ficheiros no século XX
* ```/data/sales/*/*/*.csv```Obtém ficheiros csv dois níveis em /data/vendas
* ```/data/sales/2004/*/12/[XY]1?.csv```Obtém todos os ficheiros CSV em 2004 em dezembro, começando com X ou Y pré-fixados por um número de dois dígitos

**Caminho raiz da partilha:** Se tiver pastas divididas na fonte de ficheiros com um ```key=value``` formato (por exemplo, ano=2019), então pode atribuir o nível superior dessa árvore de pasta de divisória a um nome de coluna no fluxo de dados de fluxo de dados de dados.

Em primeiro lugar, detete um wildcard para incluir todos os caminhos que são as pastas divididas mais os ficheiros de folhas que pretende ler.

![Definições de ficheiro de origem de partição](media/data-flow/partfile2.png "Definição de ficheiro de partição")

Utilize a definição do Caminho da Raiz da Partição para definir qual é o nível superior da estrutura da pasta. Quando visualizar o conteúdo dos seus dados através de uma pré-visualização de dados, verá que a ADF irá adicionar as divisórias resolvidas encontradas em cada um dos níveis das suas pastas.

![Caminho raiz da partição](media/data-flow/partfile1.png "Pré-visualização do caminho raiz da divisória")

**Lista de ficheiros:** Isto é um conjunto de ficheiros. Crie um ficheiro de texto que inclua uma lista de ficheiros relativos para processar. Aponte para este ficheiro de texto.

**Coluna para armazenar nome de ficheiro:** Guarde o nome do ficheiro fonte numa coluna nos seus dados. Introduza aqui um novo nome de coluna para armazenar a cadeia de nomes de ficheiros.

**Após a conclusão:** Opte por não fazer nada com o ficheiro fonte depois de o fluxo de dados ser executado, eliminar o ficheiro fonte ou mover o ficheiro fonte. Os caminhos para a mudança são relativos.

Para mover ficheiros de origem para outro local pós-processamento, primeiro selecione "Move" para a operação de ficheiros. Em seguida, definir o diretório "de" de. Se não estiver a utilizar nenhum wildcard para o seu caminho, então a definição "a partir" será a mesma pasta que a sua pasta fonte.

Se tiver um caminho de origem com wildcard, a sua sintaxe será assim abaixo:

```/data/sales/20??/**/*.csv```

Pode especificar "a partir"

```/data/sales```

E "para" como

```/backup/priorSales```

Neste caso, todos os ficheiros que foram obtidos em /data/vendas são transferidos para /backup/priorSales.

> [!NOTE]
> As operações de ficheiro saem apenas quando inicia o fluxo de dados a partir de uma execução de gasoduto (um depurador de gasoduto ou execução) que utiliza a atividade executar data Flow num pipeline. As operações de *ficheironão* funcionam no modo de depuração do Fluxo de Dados.

**Filtrar por último modificado:** Pode filtrar quais os ficheiros que processa especificando uma gama de datas de quando foram modificados pela última vez. Todas as datas-data são na UTC. 

### <a name="sink-properties"></a>Propriedades de pia

Na transformação da pia, pode escrever para um recipiente ou pasta no Armazenamento De Blob Azure. o separador **Definições** permite-lhe gerir a forma como os ficheiros são escritos.

![opções de afundar](media/data-flow/file-sink-settings.png "opções de afundar")

**Limpe a pasta:** Determina se a pasta de destino é ou não desmarcada antes de os dados forem escritos.

**Opção de nome** de ficheiro: Determina como os ficheiros de destino são nomeados na pasta de destino. As opções de nome do ficheiro são:
   * **Predefinição**: Permitir que a Spark nomeie ficheiros com base nas predefinições da PARTE.
   * **Padrão**: Introduza um padrão que enumera os seus ficheiros de saída por divisória. Por exemplo, **os empréstimos[n].csv** criarão empréstimos1.csv, empréstimos2.csv, e assim por diante.
   * **Por partição**: Introduza um nome de ficheiro por partição.
   * **Como dados na coluna**: Desloque o ficheiro de saída ao valor de uma coluna. O caminho é relativo ao recipiente dataset, não à pasta de destino. Se tiver um caminho de pasta no seu conjunto de dados, será ultrapassado.
   * **Saída para um único ficheiro:** Combine os ficheiros de saída divididos num único ficheiro nomeado. O caminho é relativo à pasta dataset. Por favor, esteja ciente de que a operação de fusão te pode possivelmente falhar com base no tamanho do nó. Esta opção não é recomendada para grandes conjuntos de dados.

**Cite todos:** Determina se deve encerrar todos os valores em cotações

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
| tipo | A propriedade tipo do conjunto de dados deve ser definida para **AzureBlob**. |Sim |
| folderPath | Caminho para o recipiente e pasta no armazenamento de bolhas. <br/><br/>O filtro Wildcard é suportado para o caminho excluindo o nome do recipiente. Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou personagem único); use `^` para escapar se o seu nome real de pasta tiver wildcard ou este char de fuga no seu interior. <br/><br/>Exemplos: myblobcontainer/myblobfolder/, veja mais exemplos em exemplos de [pastas e filtros de ficheiros](#folder-and-file-filter-examples). |Sim para a atividade de Cópia/Procuração, Não para a atividade getMetadata |
| fileName | **Nome ou filtro wildcard** para a bolha(s) sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todas as bolhas na pasta. <br/><br/>Para o filtro, os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e (corresponde a `?` zero ou personagem individual).<br/>- Exemplo 1:`"fileName": "*.csv"`<br/>- Exemplo 2:`"fileName": "???20180427.txt"`<br/>Use para escapar se o seu nome de `^` ficheiro real tiver wildcard ou este char de fuga dentro.<br/><br/>Quando o nome do ficheiro não é especificado para um conjunto de dados de saída e preservar a **hierarquia** não é especificada no sumidouro de atividade, a atividade de cópia gera automaticamente o nome blob com o seguinte padrão: "*Dados.. atividade executar ID GUID]. [GUID se AHierarquia Achatada]. [formato se configurado]. [compressão se configurado]*", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; se copiar a partir de fonte tabular usando o nome da mesa em vez de consulta, o padrão de nome é " [nome de*mesa].[ formato]. [compressão se configurado]*", por exemplo, "MyTable.csv". |Não |
| alteradoDatetimeStart | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> Esteja ciente de que o desempenho global do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAs, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando tem o valor da `modifiedDatetimeStart` data mas `modifiedDatetimeEnd` é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando tem o valor da `modifiedDatetimeEnd` data mas `modifiedDatetimeStart` é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| Não |
| alteradoDatetimeEnd | Filtro de ficheiros com base no atributo: Última Modificação. Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . O tempo é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00:00Z". <br/><br/> Esteja ciente de que o desempenho global do movimento de dados será impactado, permitindo esta definição quando pretender fazer filtro de ficheiros a partir de grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser NUAs, o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando tem o valor da `modifiedDatetimeStart` data mas `modifiedDatetimeEnd` é NULO, significa que os ficheiros cujo último atributo modificado é maior ou igual ao valor da data serão selecionados.  Quando tem o valor da `modifiedDatetimeEnd` data mas `modifiedDatetimeStart` é NULO, significa que os ficheiros cujo último atributo modificado é inferior ao valor da data serão selecionados.| Não |
| formato | Se pretender copiar ficheiros como está entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat**e **ParquetFormat**. Desloque a propriedade **tipo** em **formato** a um destes valores. Para mais informações, consulte as secções de [formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para mais informações, consulte [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)de compressão .<br/>Os tipos suportados são **GZip,** **Deflate,** **BZip2**e **ZipDeflate**.<br/>Os níveis suportados são **Ideais** e **Mais Rápidos.** |Não |

>[!TIP]
>Para copiar todas as bolhas sob uma pasta, especifique apenas **pastaPath.**<br>Para copiar uma única bolha com um nome dado, especifique **a pastaPath** com peça de pasta e nome de **ficheiro** com nome de ficheiro.<br>Para copiar um subconjunto de bolhas sob uma pasta, especifique **a pastaPath** com peça de pasta e nome de **ficheiro** com filtro wildcard. 

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de fonte de fonte de atividade de cópia legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **BlobSource**. |Sim |
| recursivo | Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. Note que quando se recursivo é definido como verdadeiro e a pia é uma loja baseada em ficheiros, uma pasta vazia ou subpasta não é copiada ou criada na pia.<br/>Os valores permitidos são **verdadeiros** (predefinidos) e **falsos**. | Não |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

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

### <a name="legacy-copy-activity-sink-model"></a>Modelo de pia de atividade de cópia legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do lavatório de atividade sicopiadora deve ser definida para **BlobSink**. |Sim |
| copiarComportamento | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- PreserveHierarchy (predefinição)</b>: Preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro alvo para a pasta-alvo.<br/><b>- Hierarquia flattena</b>: Todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro ou da bolha for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não |

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

## <a name="next-steps"></a>Próximos passos

Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
