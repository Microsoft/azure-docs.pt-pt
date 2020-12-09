---
title: Copiar e transformar dados no armazenamento da Azure Blob
description: Aprenda a copiar dados de e para o armazenamento blob e transforme dados no armazenamento blob utilizando a Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 1dd40756c05370d32b26c0a97ba23d0d7d7f1208
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96902379"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Copiar e transformar dados no armazenamento da Azure Blob utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-blob-connector.md)
> * [Versão atual](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade Copy na Azure Data Factory para copiar dados de e para o armazenamento de Azure Blob. Também descreve como usar a atividade do Fluxo de Dados para transformar dados no armazenamento do Azure Blob. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

>[!TIP]
>Para conhecer um cenário de migração para um lago de dados ou um armazém de dados, consulte [a Use Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para Azure.](data-migration-guidance-overview.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de armazenamento Azure Blob é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)

Para a atividade Copy, este conector de armazenamento Blob suporta:

- Cópia de bolhas de e para contas de armazenamento Azure de uso geral e armazenamento de bolhas quentes/frescas. 
- Copiar bolhas utilizando uma chave de conta, uma assinatura de acesso partilhado de serviço (SAS), um principal serviço ou identidades geridas para autenticações de recursos Azure.
- Copiar bolhas de blocos, apêndices ou bolhas de página e copiar dados apenas para bloquear bolhas.
- Copiar bolhas como está, ou analisar ou gerar bolhas com [formatos de ficheiros suportados e códigos de compressão](supported-file-formats-and-compression-codecs.md).
- [Preservação dos metadados de ficheiros durante a cópia](#preserving-metadata-during-copy).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas para o armazenamento blob.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

Este conector de armazenamento Blob suporta os seguintes tipos de autenticação. Consulte as secções correspondentes para mais detalhes.

- [Autenticação chave de conta](#account-key-authentication)
- [Autenticação de assinatura de acesso partilhado](#shared-access-signature-authentication)
- [Autenticação principal do serviço](#service-principal-authentication)
- [Identidades geridas para autenticação de recursos Azure](#managed-identity)

>[!NOTE]
>- Se pretender utilizar o tempo de funcionamento da integração do Azure público para se ligar ao seu armazenamento Blob, aproveitando os **serviços da Microsoft fidedignas para aceder a esta** opção de conta de armazenamento ativada na firewall do Azure Storage, deve utilizar a [autenticação de identidade gerida.](#managed-identity)
>- Quando utilizar a declaração PolyBase ou COPY para carregar dados no Azure Synapse Analytics, se a sua origem ou encenação O armazenamento blob estiver configurado com um ponto final da Rede Virtual Azure, deve utilizar a autenticação de identidade gerida conforme exigido pela Synapse. Consulte a secção [de autenticação de identidade gerida](#managed-identity) para obter mais pré-requisitos de configuração.

>[!NOTE]
>As atividades Azure HDInsight e Azure Machine Learning apenas suportam a autenticação que utiliza chaves de conta de armazenamento Azure Blob.

### <a name="account-key-authentication"></a>Autenticação chave de conta

A Data Factory suporta as seguintes propriedades para autenticação da chave de conta de armazenamento:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** deve ser definida como **AzureBlobStorage** (sugerida) ou **AzureStorage** (ver as seguintes notas). |Sim |
| conexãoStragem | Especifique as informações necessárias para ligar ao Armazenamento para a **propriedade connectionString.** <br/> Também pode colocar a chave de conta no Cofre da Chave Azure e retirar a `accountKey` configuração da cadeia de ligação. Para mais informações, consulte as seguintes amostras e as credenciais da Loja no artigo [do Azure Key Vault.](store-credentials-in-key-vault.md) |Sim |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou o tempo de integração auto-hospedado (se a sua loja de dados estiver numa rede privada). Se esta propriedade não for especificada, o serviço utiliza o tempo de execução de integração Azure padrão. |Não |

>[!NOTE]
>Um ponto final secundário do serviço Blob não é suportado quando se utiliza a autenticação da chave de conta. Pode utilizar outros tipos de autenticação.

>[!NOTE]
>Se estiver a utilizar o serviço ligado do tipo "AzureStorage", ainda é suportado como está. Mas sugerimos que utilize o novo tipo de serviço "AzureBlobStorage" ligado.

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

**Exemplo: armazenar a chave da conta no Cofre da Chave Azure**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Uma assinatura de acesso partilhado proporciona acesso delegado aos recursos na sua conta de armazenamento. Pode utilizar uma assinatura de acesso partilhado para conceder ao cliente permissões limitadas a objetos na sua conta de armazenamento por um tempo especificado. 

Não tens de partilhar as chaves de acesso à tua conta. A assinatura de acesso partilhado é um URI que engloba nos seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com a assinatura de acesso partilhado, o cliente apenas precisa de passar na assinatura de acesso partilhado ao construtor ou método apropriado. 

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [assinaturas de acesso partilhado: Compreenda o modelo de assinatura de acesso partilhado.](../storage/common/storage-sas-overview.md)

> [!NOTE]
>- A Data Factory suporta agora as *assinaturas de acesso partilhado* do serviço e *as assinaturas de acesso partilhado à conta.* Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Grant acesso limitado aos recursos de Armazenamento Azure usando assinaturas de acesso partilhado.](../storage/common/storage-sas-overview.md)
>- Nas configurações posteriores do conjunto de dados, o caminho da pasta é o caminho absoluto a partir do nível do recipiente. É necessário configurar um alinhado com o caminho do seu SAS URI.

A Data Factory suporta as seguintes propriedades para a utilização da autenticação de assinatura de acesso partilhado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** deve ser definida para **AzureBlobStorage** (sugerido) ou **AzureStorage** (ver a seguinte nota). |Sim |
| SasUri | Especifique a assinatura de acesso partilhado URI aos recursos de Armazenamento, tais como bolha ou recipiente. <br/>Marque este campo como **SecureString** para armazená-lo de forma segura na Data Factory. Também pode colocar o token SAS no Cofre da Chave Azure para utilizar a rotação automática e remover a porção de token. Para mais informações, consulte as seguintes amostras e [guarde as credenciais no Cofre da Chave Azure.](store-credentials-in-key-vault.md) |Sim |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou o tempo de integração auto-hospedado (se a sua loja de dados estiver numa rede privada). Se esta propriedade não for especificada, o serviço utiliza o tempo de execução de integração Azure padrão. |Não |

>[!NOTE]
>Se estiver a utilizar o serviço ligado do tipo "AzureStorage", ainda é suportado como está. Mas sugerimos que utilize o novo tipo de serviço "AzureBlobStorage" ligado.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a chave da conta no Cofre da Chave Azure**

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
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>" 
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

- Descreva permissões de leitura/escrita apropriadas em objetos baseados na forma como o serviço ligado (ler, escrever, ler/escrever) é utilizado na sua fábrica de dados.
- Desavendar **o tempo de validade** adequadamente. Certifique-se de que o acesso aos objetos de armazenamento não expira dentro do período ativo do gasoduto.
- O URI deve ser criado no recipiente ou bolha certo com base na necessidade. Uma assinatura de acesso partilhado URI a uma bolha permite que a Data Factory aceda a essa bolha em particular. Uma assinatura de acesso partilhado URI a um recipiente de armazenamento Blob permite que a Data Factory itere através de bolhas nesse recipiente. Para fornecer acesso a mais ou menos objetos mais tarde, ou atualizar a assinatura de acesso partilhado URI, lembre-se de atualizar o serviço ligado com o novo URI.

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para obter informações gerais sobre a autenticação principal do serviço Azure Storage, consulte [autenticar o acesso ao Armazenamento Azure utilizando o Azure Ative Directory](../storage/common/storage-auth-aad.md).

Para utilizar a autenticação principal do serviço, siga estes passos:

1. Registe uma entidade de candidatura no Azure Ative Directory (Azure AD) seguindo [o Registo da sua candidatura junto de um inquilino da AD Azure.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Tome nota destes valores, que utiliza para definir o serviço ligado:

    - ID da aplicação
    - Chave de aplicação
    - ID do inquilino

2. Conceda ao diretor de serviço a permissão adequada no armazenamento da Azure Blob. Para obter mais informações sobre as funções, consulte [utilizar o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Como fonte**, no **Controlo de Acesso (IAM)**, conceda pelo menos a função de Leitor de **Dados blob de armazenamento.**
    - **Como sink**, in **Access control (IAM)**, concede pelo menos a função de Contribuinte de **Dados blob de armazenamento.**

Estas propriedades são suportadas para um serviço ligado ao armazenamento Azure Blob:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **AzureBlobStorage**. |Sim |
| serviceEndpoint | Especifique o ponto final do serviço de armazenamento Azure Blob com o padrão de `https://<accountName>.blob.core.windows.net/` . |Sim |
| contaAndo | Especifique o tipo da sua conta de armazenamento. Os valores permitidos são: **Armazenamento** (finalidade geral v1), **ArmazenamentoV2** (finalidade geral v2), **BlobStorage** ou **BlockBlobStorage**. <br/> Quando se utiliza o serviço ligado a Azure Blob no fluxo de dados, a autenticação principal de identidade ou serviço gerida não é suportada quando o tipo de conta é vazio ou "Armazenamento". Especifique o tipo de conta adequada, escolha uma autenticação diferente ou atualize a sua conta de armazenamento para fins gerais v2. |Não |
| servicePrincipalId | Especifique a identificação do cliente da aplicação. | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Recuperá-lo pairando sobre o canto superior direito do portal Azure. | Sim |
| AzureCloudType | Para a autenticação principal do serviço, especifique o tipo de ambiente em nuvem Azure, ao qual está registado o seu pedido de Diretório Azure Ative. <br/> Os valores permitidos são **AzurePublic,** **AzureChina,** **AzureUsGovernment,** e **AzureGermany**. Por padrão, o ambiente em nuvem da fábrica de dados é utilizado. | Não |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou o tempo de integração auto-hospedado (se a sua loja de dados estiver numa rede privada). Se esta propriedade não for especificada, o serviço utiliza o tempo de execução de integração Azure padrão. |Não |

>[!NOTE]
>Se a sua conta blob permitir [a eliminação suave,](../storage/blobs/soft-delete-blob-overview.md)a autenticação principal do serviço não é suportada no Fluxo de Dados.

>[!NOTE]
>A autenticação principal do serviço é suportada apenas pelo serviço ligado do tipo "AzureBlobStorage", e não pelo serviço anterior de ligação do tipo "AzureStorage".

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Identidades geridas para autenticação de recursos Azure

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos da Azure,](data-factory-service-identity.md)que representa esta fábrica de dados específica. Pode utilizar diretamente esta identidade gerida para a autenticação de armazenamento Blob, que é semelhante à utilização do seu próprio chefe de serviço. Permite que esta fábrica designada aceda e copie dados de ou para o armazenamento blob.

Para obter informações gerais sobre a autenticação do Armazenamento Azure, consulte [autenticar o acesso ao Armazenamento Azure utilizando o Azure Ative Directory](../storage/common/storage-auth-aad.md). Para utilizar identidades geridas para a autenticação de recursos Azure, siga estes passos:

1. [A Fábrica de Dados de Recuperação geriu informações](data-factory-service-identity.md#retrieve-managed-identity) de identidade copiando o valor do ID do objeto de identidade gerido gerado juntamente com a sua fábrica.

2. Conceda a permissão de identidade gerida no armazenamento da Azure Blob. Para obter mais informações sobre as funções, consulte [utilizar o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Como fonte**, no **Controlo de Acesso (IAM)**, conceda pelo menos a função de Leitor de **Dados blob de armazenamento.**
    - **Como sink**, in **Access control (IAM)**, concede pelo menos a função de Contribuinte de **Dados blob de armazenamento.**

>[!IMPORTANT]
>Se utilizar a declaração PolyBase ou COPY para carregar dados do armazenamento blob (como fonte ou como encenação) no Azure Synapse Analytics, quando utilizar a autenticação de identidade gerida para armazenamento de Blob, certifique-se de que também segue os passos 1 a 3 [nesta orientação](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Esses passos registarão o seu servidor com Azure AD e atribuirão a função de Contribuinte de Dados de Armazenamento blob ao seu servidor. A Fábrica de Dados trata do resto. Se configurar o armazenamento blob com um ponto final da Rede Virtual Azure, também precisa de permitir **que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento** ligados no menu de firewalls da conta de armazenamento Azure e redes **virtuais,** conforme exigido pelo Synapse.

Estas propriedades são suportadas para um serviço ligado ao armazenamento Azure Blob:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **AzureBlobStorage**. |Sim |
| serviceEndpoint | Especifique o ponto final do serviço de armazenamento Azure Blob com o padrão de `https://<accountName>.blob.core.windows.net/` . |Sim |
| contaAndo | Especifique o tipo da sua conta de armazenamento. Os valores permitidos são: **Armazenamento** (finalidade geral v1), **ArmazenamentoV2** (finalidade geral v2), **BlobStorage** ou **BlockBlobStorage**. <br/> Quando se utiliza o serviço ligado a Azure Blob no fluxo de dados, a autenticação principal de identidade ou serviço gerida não é suportada quando o tipo de conta é vazio ou "Armazenamento". Especifique o tipo de conta adequada, escolha uma autenticação diferente ou atualize a sua conta de armazenamento para fins gerais v2. |Não |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou o tempo de integração auto-hospedado (se a sua loja de dados estiver numa rede privada). Se esta propriedade não for especificada, o serviço utiliza o tempo de execução de integração Azure padrão. |Não |

> [!NOTE]
> Se a sua conta blob permitir [a eliminação suave,](../storage/blobs/soft-delete-blob-overview.md)a autenticação de identidade gerida não é suportada no Fluxo de Dados.

> [!NOTE]
> As identidades geridas para a autenticação de recursos Azure são suportadas apenas pelo serviço ligado ao tipo "AzureBlobStorage", e não pelo serviço ligado anterior do tipo "AzureStorage".

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
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

As seguintes propriedades são suportadas para armazenamento Azure Blob `location` em configurações num conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| tipo       | A **type** propriedade tipo da localização no conjunto de dados deve ser definida para **AzureBlobStorageLocation**. | Sim      |
| contentor  | O recipiente da bolha.                                          | Sim      |
| folderPath | O caminho para a pasta sob o recipiente dado. Se pretender utilizar um wildcard para filtrar a pasta, ignore esta definição e especifique-a nas definições de origem da atividade. | Não       |
| fileName   | O nome do ficheiro sob o caminho do recipiente e da pasta. Se pretender utilizar o wildcard para filtrar ficheiros, ignore esta definição e especifique-a nas definições de fonte de atividade. | Não       |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades que a fonte de armazenamento Blob e suporte para o lavatório.

### <a name="blob-storage-as-a-source-type"></a>Armazenamento de bolhas como tipo de fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para o armazenamento Azure Blob `storeSettings` em configurações numa fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tipo                     | A propriedade **tipo** em baixo `storeSettings` deve ser definida para **AzureBlobStorageReadSettings**. | Sim                                           |
| **_Localize os ficheiros para copiar:_* _ |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar a partir do dado caminho de contentor ou pasta/ficheiro especificado no conjunto de dados. Se pretender copiar todas as bolhas de um recipiente ou pasta, especificar ainda `wildcardFileName` mais como `_` . |  |
| OPÇÃO 2: prefixo blob<br>- prefixo | Prefixo para o nome da bolha sob o dado recipiente configurado num conjunto de dados para filtrar as bolhas de origem. Blobs cujos nomes começam `container_in_dataset/this_prefix` por ser selecionados. Utiliza o filtro do lado do serviço para o armazenamento Blob, que proporciona um melhor desempenho do que um filtro wildcard.<br><br>Quando utilizar o prefixo e optar por copiar para a pia baseada em ficheiros com hierarquia de preservação, note o sub-caminho após a preservação do último "/" no prefixo. Por exemplo, tem fonte  `container/folder/subfolder/file.txt` , e configurar prefixo como `folder/sub` , então o caminho de arquivo preservado é `subfolder/file.txt` . | Não                                                          |
| OPÇÃO 3: wildcard<br>- wildcardFolderPath | O caminho da pasta com caracteres wildcard sob o dado recipiente configurado num conjunto de dados para filtrar pastas de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais). Utilize `^` para escapar se o nome da sua pasta tiver wildcard ou este personagem de fuga no interior. <br>Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 3: wildcard<br>- wildcardFileName | O nome do ficheiro com caracteres wildcard sob o caminho do recipiente e da pasta (ou caminho da pasta wildcard) para filtrar ficheiros de origem. <br>Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais). Use `^` para escapar se o nome da sua pasta tiver um wildcard ou este personagem de fuga no interior. Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 4: uma lista de ficheiros<br>- fileListPath | Indica copiar um determinado conjunto de ficheiros. Aponte para um ficheiro de texto que inclua uma lista de ficheiros que pretende copiar, um ficheiro por linha, que é o caminho relativo para o caminho configurado no conjunto de dados.<br/>Quando estiver a utilizar esta opção, não especifique um nome de ficheiro no conjunto de dados. Ver mais exemplos em [exemplos da lista de ficheiros.](#file-list-examples) |Não |
| ***Definições adicionais:** _ |  | |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Note que quando _ *recursivo** é definido como **verdadeiro** e a pia é uma loja baseada em arquivos, uma pasta ou sub-dobrador vazio não é copiado ou criado na pia. <br>Os valores permitidos são **verdadeiros** (padrão) e **falsos.**<br>Esta propriedade não se aplica quando se `fileListPath` configura. |Não |
| eliminarFilesAfterCompletion | Indica se os ficheiros binários serão eliminados da loja de origem depois de se mudarem com sucesso para a loja de destino. A eliminação do ficheiro é por ficheiro, pelo que quando a atividade da cópia falhar, verá que alguns ficheiros já foram copiados para o destino e eliminados da fonte, enquanto outros ainda permanecem na loja de origem. <br/>Esta propriedade é válida apenas em cenário de cópia de ficheiros binários. O valor predefinido: falso. |Não |
| modificadoDatetimeStart    | Os ficheiros são filtrados com base no atributo: última modificada. <br>Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado a um fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser **NUAS,** o que significa que nenhum filtro de atributos de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tiver um valor de data, mas é `modifiedDatetimeEnd` **NU,** serão selecionados os ficheiros cujo último atributo modificado é superior ou igual ao valor da data.  Quando `modifiedDatetimeEnd` tiver um valor de data mas é `modifiedDatetimeStart` **NU,** serão selecionados os ficheiros cujo último atributo modificado é inferior ao valor da data.<br/>Esta propriedade não se aplica quando se `fileListPath` configura. | Não                                            |
| modificadoDatetimeEnd      | O mesmo que acima.                                               | Não                                            |
| permitirPartitionDiscovery | Para os ficheiros que são divididos, especifique se analisar as divisórias do caminho do ficheiro e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **falsos** (padrão) e **verdadeiros.** | Não                                            |
| partitionRootPath | Quando a descoberta da partição estiver ativada, especifique o caminho da raiz absoluta para ler as pastas partidas como colunas de dados.<br/><br/>Se não for especificado, por defeito,<br/>- Quando utiliza o caminho do ficheiro no conjunto de dados ou na lista de ficheiros na fonte, o caminho da raiz da partição é o caminho configurado no conjunto de dados.<br/>- Quando utiliza o filtro de pasta wildcard, o caminho da raiz da partição é o sub-caminho antes do primeiro wildcard.<br/>- Quando se utiliza prefixo, o caminho da raiz da partição é sub-caminho antes do último "/". <br/><br/>Por exemplo, assumindo que configura o caminho no conjunto de dados como "raiz/pasta/ano=2020/mês=08/dia=27":<br/>- Se especificar o caminho da raiz da partição como "raiz/pasta/ano=2020", a atividade da cópia gerará mais duas colunas `month` e com o valor `day` "08" e "27", respectivamente, para além das colunas dentro dos ficheiros.<br/>- Se não for especificado o caminho da raiz da partição, não será gerada nenhuma coluna extra. | Não                                            |
| maxConcurrentConnections | O número de ligações simultâneas ao armazenamento. Especifique apenas quando pretende limitar as ligações simultâneas à loja de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto Delimitado/Parquet, o tipo **BlobSource** para a fonte de atividade Copy mencionado na secção seguinte ainda é suportado como é para retrocompatibilidade. Sugerimos que utilize o novo modelo até que a UI de autoria da Data Factory tenha mudado para gerar estes novos tipos.

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

### <a name="blob-storage-as-a-sink-type"></a>Armazenamento de bolhas como um tipo de pia

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

As seguintes propriedades são suportadas para o armazenamento Azure Blob `storeSettings` em configurações num lavatório de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade **tipo** em baixo `storeSettings` deve ser definida para **AzureBlobStorageWriteSettings**. | Sim      |
| copyOportundo             | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- Preservar AHierarquia (predefinição)</b>: Preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro-alvo para a pasta alvo.<br/><b>- FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível da pasta alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro ou do blob for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não       |
| blockSizeInMB | Especifique o tamanho do bloco, em megabytes, usado para escrever dados para bloquear bolhas. Saiba mais [sobre Block Blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>O valor permitido é *entre 4 MB e 100 MB.* <br/>Por predefinição, a Data Factory determina automaticamente o tamanho do bloco com base no tipo e dados da sua loja de origem. Para a cópia não binária no armazenamento blob, o tamanho do bloco predefinido é de 100 MB para que possa caber (no máximo) 4,95 TB de dados. Pode não ser ótimo quando os seus dados não são grandes, especialmente quando utiliza o tempo de integração auto-hospedado com ligações de rede deficientes que resultam em problemas de tempo de funcionamento ou desempenho. Pode especificar explicitamente um tamanho de bloco, garantindo que `blockSizeInMB*50000` é grande o suficiente para armazenar os dados. Caso contrário, a atividade copy irá falhar. | Não |
| maxConcurrentConnections | O número de ligações simultâneas ao armazenamento. Especifique apenas quando pretende limitar as ligações simultâneas à loja de dados. | Não       |

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

Esta secção descreve o comportamento resultante do caminho da pasta e nome do ficheiro com filtros wildcard.

| folderPath | fileName | recursivo | Estrutura de pasta de origem e resultado do filtro (os ficheiros em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (vazio, uso padrão) | false | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (vazio, uso padrão) | true | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Outra 14h<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de ficheiros

Esta secção descreve o comportamento resultante da utilização de um caminho de lista de ficheiros na fonte de atividade copy.

Assuma que tem a seguinte estrutura de pasta de origem e quer copiar os ficheiros em negrito:

| Estrutura de origem da amostra                                      | Conteúdo em FileListToCopy.txt                             | Configuração da Fábrica de Dados                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| contentor<br/>&nbsp;&nbsp;&nbsp;&nbsp;Pasta<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sub-página1/File3.csv<br>Sub-página1/File5.csv | **No conjunto de dados:**<br>- Contentor: `container`<br>- Caminho da pasta: `FolderA`<br><br>**Na fonte de atividade do Copy:**<br>- Caminho da lista de ficheiros: `container/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de ficheiros aponta para um ficheiro de texto na mesma loja de dados que inclui uma lista de ficheiros que pretende copiar, um ficheiro por linha, com o caminho relativo para o caminho configurado no conjunto de dados. |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos recursivos e copyBehavior

Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de **valores recursivos** e **de cópias.**

| recursivo | copyOportundo | Estrutura de pasta de origem | Alvo resultante |
|:--- |:--- |:--- |:--- |
| true |preservarHierarquia | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo, Pasta1, é criada com a mesma estrutura que a fonte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |achatamento | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo, Pasta1, é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File5 |
| true |fusõesFilias | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo, Pasta1, é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 os conteúdos são fundidos num ficheiro com um nome de ficheiro autogerado. |
| false |preservarHierarquia | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo, Pasta1, é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A subfolder1 com File3, File4 e File5 não é recolhida. |
| false |achatamento | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo, Pasta1, é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome autogerado para File2<br/><br/>A subfolder1 com File3, File4 e File5 não é recolhida. |
| false |fusõesFilias | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta-alvo, Pasta1, é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;O conteúdo do Ficheiro1 + Ficheiro2 é fundido num ficheiro com um nome de ficheiro autogerido. nome autogerado para File1<br/><br/>A subfolder1 com File3, File4 e File5 não é recolhida. |

## <a name="preserving-metadata-during-copy"></a>Conservação de metadados durante a cópia

Ao copiar ficheiros do armazenamento amazon S3, Azure Blob ou Azure Data Lake Storage Gen2 para Azure Data Lake Storage Gen2 ou Azure Blob armazenamento, pode optar por preservar os metadados de ficheiros juntamente com dados. Saiba mais [com os metadados da Preserve.](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Quando estiver a transformar dados em fluxos de dados de mapeamento, pode ler e escrever ficheiros a partir do armazenamento do Azure Blob nos seguintes formatos:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

As definições específicas do formato estão localizadas na documentação para este formato. Para obter mais informações, consulte [a transformação de Fonte no fluxo de dados de mapeamento](data-flow-source.md) e [transformação de sumidouro no fluxo de dados de mapeamento.](data-flow-sink.md)

### <a name="source-transformation"></a>Transformação de origem

Na transformação da fonte, pode ler-se a partir de um recipiente, pasta ou ficheiro individual no armazenamento Azure Blob. Utilize o separador **Opções Source** para gerir a forma como os ficheiros são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de origem")

**Percursos wildcard:** A utilização de um padrão wildcard instruirá a Data Factory a circular através de cada pasta e ficheiro correspondente numa única transformação de origem. Esta é uma forma eficaz de processar vários ficheiros dentro de um único fluxo. Adicione vários padrões de correspondência wildcard com o sinal de mais que aparece quando paira sobre o seu padrão wildcard existente.

A partir do seu recipiente de origem, escolha uma série de ficheiros que correspondam a um padrão. Apenas um recipiente pode ser especificado no conjunto de dados. O seu percurso wildcard deve, portanto, incluir também o seu caminho de pasta a partir da pasta raiz.

Exemplos wildcard:

* ```*``` Representa qualquer conjunto de caracteres.
* ```**``` Representa o nidificação recursiva do diretório.
* ```?``` Substitui um personagem.
* ```[]``` Corresponde a um ou mais caracteres nos parênteses.

* ```/data/sales/**/*.csv``` Obtém todos os ficheiros .csv em /dados/vendas.
* ```/data/sales/20??/**/``` Recebe todos os ficheiros do século 20.
* ```/data/sales/*/*/*.csv``` Obtém .csv ficheiros dois níveis em /dados/vendas.
* ```/data/sales/2004/*/12/[XY]1?.csv``` Recebe todos os ficheiros .csv em dezembro de 2004, começando com X ou Y prefixados por um número de dois dígitos.

**Caminho da raiz da partição:** Se tiver pastas divididas na sua fonte de ficheiro com um ```key=value``` formato (por exemplo, `year=2019` ), então pode atribuir o nível superior dessa pasta de divisória a um nome de coluna no fluxo de dados do fluxo de dados.

Em primeiro lugar, desajuste um wildcard para incluir todos os caminhos que são as pastas divisórias mais os ficheiros de folhas que pretende ler.

![Definições de ficheiros de fonte de partição](media/data-flow/partfile2.png "Definição de ficheiro de partição")

Utilize a definição do caminho da raiz da **partição** para definir qual é o nível superior da estrutura da pasta. Quando visualizar o conteúdo dos seus dados através de uma pré-visualização de dados, verá que a Data Factory adicionará as divisórias resolvidas encontradas em cada um dos níveis das suas pastas.

![Caminho da raiz da partição](media/data-flow/partfile1.png "Pré-visualização do caminho da raiz da partição")

**Lista de ficheiros:** Este é um conjunto de arquivos. Crie um ficheiro de texto que inclua uma lista de ficheiros de caminhos relativos para processar. Aponte para este ficheiro de texto.

**Coluna para armazenar nome de ficheiro:** Guarde o nome do ficheiro de origem numa coluna nos seus dados. Introduza aqui um novo nome de coluna para armazenar a cadeia de nomes de ficheiros.

**Após a conclusão:** Opte por não fazer nada com o ficheiro de origem após o fluxo de dados, eliminar o ficheiro de origem ou mover o ficheiro de origem. Os caminhos para a mudança são relativos.

Para mover ficheiros de origem para outro pós-processamento de localização, selecione primeiro "Mover" para a operação de ficheiros. Em seguida, definir o diretório "de" Se não estiver a utilizar nenhum wildcard para o seu caminho, então a definição "a partir" será a mesma pasta que a sua pasta de origem.

Se tiver um caminho de origem com wildcard, a sua sintaxe será assim:

```/data/sales/20??/**/*.csv```

Pode especificar "a partir" como:

```/data/sales```

E pode especificar "para" como:

```/backup/priorSales```

Neste caso, todos os ficheiros que foram obtidos em /dados/vendas são transferidos para /backup/priorSales.

> [!NOTE]
> As operações de ficheiros só funcionam quando inicia o fluxo de dados a partir de uma execução de gasoduto (um depurar do gasoduto ou execução de execução) que utiliza a atividade do Fluxo de Dados executar num oleoduto. As operações de *ficheiro não* são executadas no modo de depuramento do Fluxo de Dados.

**Filtrar por última modificação:** Pode filtrar quais os ficheiros que processa especificando um intervalo de datas de quando foram modificados pela última vez.  Todas as datas/horas estão em UTC. 

### <a name="sink-properties"></a>Propriedades de pia

Na transformação da pia, pode escrever para um recipiente ou uma pasta no armazenamento da Azure Blob. Utilize o **separador Definições** para gerir a forma como os ficheiros são escritos.

![Opções de pia](media/data-flow/file-sink-settings.png "opções de afundar")

**Limpe a pasta:** Determina se a pasta de destino é ou não apurada antes de os dados serem escritos.

**Opção nome de ficheiro:** Determina como os ficheiros de destino são nomeados na pasta de destino. As opções de nome do ficheiro são:
   * **Predefinição**: Permita que o Spark nomeie ficheiros com base em predefinições PART.
   * **Padrão**: Introduza um padrão que enumera os seus ficheiros de saída por partição. Por exemplo, **os empréstimos[n].csv** criarão loans1.csv, loans2.csv, e assim por diante.
   * **Por partição:** Introduza um nome de ficheiro por partição.
   * **Como dados na coluna**: Desave o ficheiro de saída ao valor de uma coluna. O caminho é relativo ao recipiente do conjunto de dados, não à pasta de destino. Se tiver um caminho de pasta no seu conjunto de dados, será ultrapassado.
   * **Saída para um único ficheiro**: Combine os ficheiros de saída divididos num único ficheiro nomeado. O caminho é relativo à pasta do conjunto de dados. Tenha em atenção que a operação de fusão pode eventualmente falhar com base no tamanho do nó. Não recomendamos esta opção para grandes conjuntos de dados.

**Citar tudo:** Determina se deve incluir todos os valores em aspas.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade getMetadata

Para obter detalhes sobre as propriedades, consulte a [atividade da GetMetadata.](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eliminar propriedades de atividade

Para obter detalhes sobre as propriedades, verifique [a atividade de Excluir](delete-activity.md).

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os modelos seguintes ainda são suportados como é para retrocompatibilidade. Sugerimos que use o novo modelo mencionado anteriormente. A UI de autoria da Data Factory passou a gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** do conjunto de dados deve ser definida para **AzureBlob**. |Sim |
| folderPath | Caminho para o recipiente e pasta no armazenamento blob. <br/><br/>Um filtro wildcard é suportado para o caminho, excluindo o nome do recipiente. Os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais). Use `^` para escapar se o nome da sua pasta tiver um wildcard ou este personagem de fuga no interior. <br/><br/>Um exemplo é: myblobcontainer/myblobfolder/. Veja mais exemplos em [exemplos de pasta e filtro de ficheiros](#folder-and-file-filter-examples). |Sim para a atividade Copy ou Lookup, não para a atividade GetMetadata |
| fileName | Nome ou filtro wildcard para as bolhas sob o valor de **pasta especificadoPapa.** Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todas as bolhas na pasta. <br/><br/>Para o filtro, os wildcards permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caracteres individuais).<br/>- Exemplo 1: `"fileName": "*.csv"`<br/>- Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de ficheiro tiver um wildcard ou este personagem de fuga dentro.<br/><br/>Quando **o data de ficheiro** não é especificado para um conjunto de dados de saída e a **preservaçãoHierarquia** não é especificado na pia de atividade, a atividade Copy gera automaticamente o nome blob com o seguinte padrão: "*Dados." atividade executar ID GUID]. [GUID se FlattenHierarchy]. [formato se configurado]. [compressão se configurado]*". Por exemplo: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". <br/><br/>Se copiar de uma fonte tabular usando um nome de mesa em vez de uma consulta, o padrão de nome é "*[nome de mesa].[ formato]. [compressão se configurado]*". Por exemplo: "MyTable.csv". |Não |
| modificadoDatetimeStart | Os ficheiros são filtrados com base no atributo: última modificada. Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Tenha em atenção que ativar esta definição afetará o desempenho geral do movimento de dados quando pretender filtrar grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser **NUAS,** o que significa que nenhum filtro de atributos de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tiver um valor de data, mas é `modifiedDatetimeEnd` **NU,** serão selecionados os ficheiros cujo último atributo modificado é superior ou igual ao valor da data.  Quando `modifiedDatetimeEnd` tiver um valor de data mas é `modifiedDatetimeStart` **NU,** serão selecionados os ficheiros cujo último atributo modificado é inferior ao valor da data.| Não |
| modificadoDatetimeEnd | Os ficheiros são filtrados com base no atributo: última modificada. Os ficheiros serão selecionados se o seu último tempo modificado estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd` . . O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Tenha em atenção que ativar esta definição afetará o desempenho geral do movimento de dados quando pretender filtrar grandes quantidades de ficheiros. <br/><br/> As propriedades podem ser **NUAS,** o que significa que nenhum filtro de atributos de ficheiro será aplicado no conjunto de dados.  Quando `modifiedDatetimeStart` tiver um valor de data, mas é `modifiedDatetimeEnd` **NU,** serão selecionados os ficheiros cujo último atributo modificado é superior ou igual ao valor da data.  Quando `modifiedDatetimeEnd` tiver um valor de data mas é `modifiedDatetimeStart` **NU,** serão selecionados os ficheiros cujo último atributo modificado é inferior ao valor da data.| Não |
| formato | Se pretender copiar ficheiros como está entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, os seguintes tipos de formato de ficheiros são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat** e **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em **formato.** Para mais informações, consulte o [formato Texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e secções [de formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (apenas para cenário de cópia binária) |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para obter mais informações, consulte [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos suportados são **GZip,** **Deflate,** **BZip2** e **ZipDeflate**.<br/>Os níveis suportados são **ideais** e **mais rápidos.** |Não |

>[!TIP]
>Para copiar todas as bolhas numa pasta, especifique apenas **a pastaPata.**<br>Para copiar uma única bolha com um nome próprio, especifique **a pastaPata** para a parte da pasta e **o nome do ficheiro** para o nome do ficheiro.<br>Para copiar um subconjunto de bolhas sob uma pasta, especifique **a pastaPata** para a parte da pasta e **fileName** com um filtro wildcard. 

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

### <a name="legacy-source-model-for-the-copy-activity"></a>Modelo de origem legado para a atividade copy

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de atividade copy deve ser definida como **BlobSource**. |Sim |
| recursivo | Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. Note que quando **a recursiva** é definida como **verdadeira** e a pia é uma loja baseada em ficheiros, uma pasta ou sub-dobrador vazio não é copiado ou criado na pia.<br/>Os valores permitidos são **verdadeiros** (padrão) e **falsos.** | Não |
| maxConcurrentConnections | O número de ligações simultâneas ao armazenamento. Especifique apenas quando pretende limitar as ligações simultâneas à loja de dados. | Não |

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

### <a name="legacy-sink-model-for-the-copy-activity"></a>Modelo de pia legado para a atividade copy

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** do lavatório de atividade copy deve ser definida para **BlobSink**. |Sim |
| copyOportundo | Define o comportamento da cópia quando a fonte é ficheiros de uma loja de dados baseada em ficheiros.<br/><br/>Os valores permitidos são:<br/><b>- Preservar AHierarquia (predefinição)</b>: Preserva a hierarquia do ficheiro na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro alvo para a pasta alvo.<br/><b>- FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível da pasta alvo. Os ficheiros-alvo têm nomes autogerados. <br/><b>- MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro ou do blob for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, é um nome de ficheiro autogerado. | Não |
| maxConcurrentConnections | O número de ligações simultâneas ao armazenamento. Especifique apenas quando pretende limitar as ligações simultâneas à loja de dados. | Não |

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

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a atividade Copy na Data Factory suporta como fontes e sumidouros, consulte [lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)