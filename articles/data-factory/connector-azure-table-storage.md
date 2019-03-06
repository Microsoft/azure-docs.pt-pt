---
title: Copiar dados para e do armazenamento de tabelas do Azure com o Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de arquivos de origem suportada para o armazenamento de tabelas do Azure ou de armazenamento de tabelas em arquivos de sink suportado, com o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 7ef8f80f44c921cc1f2524351c8acb78ebd713bf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57434798"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Copiar dados para e do armazenamento de tabelas do Azure com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-table-connector.md)
> * [Versão atual](connector-azure-table-storage.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados para e do armazenamento de tabelas do Azure. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de qualquer arquivo de dados de origem suportada para o armazenamento de tabela. Também pode copiar dados de armazenamento de tabelas para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector suporta de tabelas do Azure copiar dados utilizando a chave de conta e o serviço partilhado autenticações de assinatura de acesso.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o armazenamento de tabela.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

### <a name="use-an-account-key"></a>Utilizar uma chave de conta

Pode criar um serviço ligado do armazenamento do Azure utilizando a chave de conta. Ele fornece a fábrica de dados com o acesso global para o armazenamento. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **AzureTableStorage**. |Sim |
| connectionString | Especifique as informações necessárias para ligar ao armazenamento para a propriedade connectionString. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também é possível incluir a chave da conta no Azure Key Vault e obter o `accountKey` configuração fora de cadeia de ligação. Consulte os exemplos seguintes e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Se estivesse usando o serviço de ligado de tipo de "AzureStorage", ainda é suportado como-é, ao passo que lhe é sugeridas para utilizar este novo "AzureTableStorage" ligado do tipo de serviço no futuro.

**Exemplo:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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

### <a name="use-shared-access-signature-authentication"></a>Utilizar a autenticação de assinatura de acesso partilhado

Também pode criar um serviço ligado do armazenamento ao utilizar uma assinatura de acesso partilhado. Ele fornece a fábrica de dados com acesso restrito/com limite de tempo específico/todos os recursos no armazenamento do.

Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Pode usá-lo para conceder que um cliente permissões limitadas a objetos na conta de armazenamento durante um período de tempo especificado e com um conjunto especificado de permissões. Não tem de partilhar as chaves de acesso da conta. A assinatura de acesso partilhado é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a assinatura de acesso partilhado, o cliente precisa apenas passar a assinatura de acesso partilhado para o método ou construtor apropriado. Para obter mais informações sobre assinaturas de acesso partilhado, consulte [assinaturas de acesso partilhado: Compreender o modelo de assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> O Data Factory suporta agora ambos **serviço de assinaturas de acesso partilhado** e **assinaturas de acesso partilhado de conta**. Para obter mais informações sobre estes dois tipos e como construí-las, consulte [tipos de assinaturas de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> Para gerar uma assinatura de acesso partilhado do serviço para a sua conta de armazenamento, pode executar os seguintes comandos do PowerShell. Substitua os marcadores de posição e conceder a permissão necessária.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para utilizar a autenticação da assinatura de acesso partilhado, são suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **AzureTableStorage**. |Sim |
| sasUri | Especifique o URI de SAS do URI de assinatura de acesso partilhado para a tabela. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar o SAS token no Azure Key Vault para tirar partido de rotação automática e remover a parte do token. Consulte os exemplos seguintes e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode usar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Se estivesse usando o serviço de ligado de tipo de "AzureStorage", ainda é suportado como-é, ao passo que lhe é sugeridas para utilizar este novo "AzureTableStorage" ligado do tipo de serviço no futuro.

**Exemplo:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
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
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
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
- O URI deve ser criado ao nível da tabela da direita, com base na necessidade.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados de tabelas do Azure.

Para copiar dados de e para tabelas do Azure, defina a propriedade de tipo de conjunto de dados para **Azuretable{0}name**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida **Azuretable{0}name**. |Sim |
| tableName |O nome da tabela em que a instância de base de dados do armazenamento de tabela que o serviço ligado refere-se a. |Sim |

**Exemplo:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema pelo Data Factory

Para arquivos de dados sem esquema, como tabelas do Azure, o Data Factory infere o esquema de uma das seguintes formas:

* Se especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o Data Factory respeita essa estrutura como o esquema. Neste caso, se uma linha não contém um valor para uma coluna, é fornecido um valor nulo para o mesmo.
* Se não especificar a estrutura dos dados ao utilizar o **estrutura** propriedade na definição do conjunto de dados, o Data Factory infere o esquema ao utilizar a primeira linha dos dados. Neste caso, se a primeira linha não contém o esquema completo, algumas colunas seja perdidas no resultado da operação de cópia.

Para origens de dados sem esquema, a prática recomendada é especificar a estrutura de dados utilizando o **estrutura** propriedade.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem de tabelas do Azure e de sink.

### <a name="azure-table-as-a-source-type"></a>Tabela do Azure como um tipo de origem

Para copiar dados de tabelas do Azure, definir o tipo de origem na atividade de cópia para **AzureTableSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida **AzureTableSource**. |Sim |
| azureTableSourceQuery |Utilize a consulta de armazenamento de tabela personalizada para ler os dados. Veja exemplos na secção seguinte. |Não |
| azureTableSourceIgnoreTableNotFound |Indica se pretende permitir que a exceção de uma tabela não existe.<br/>Valores permitidos são **True** e **falso** (predefinição). |Não |

### <a name="azuretablesourcequery-examples"></a>Exemplos de azureTableSourceQuery

Se a coluna de tabelas do Azure é do tipo datetime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Se a coluna de tabelas do Azure é do tipo string:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Se utilizar o parâmetro de pipeline, converta o valor de datetime para o formato correto, de acordo com os exemplos anteriores.

### <a name="azure-table-as-a-sink-type"></a>Tabela do Azure como um tipo de sink

Para copiar dados para tabelas do Azure, defina o tipo de sink na atividade de cópia para **AzureTableSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida **AzureTableSink**. |Sim |
| azureTableDefaultPartitionKeyValue |O padrão partição valor da chave que pode ser utilizado pelo sink. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como as chaves de partição. Se não for especificado, "AzureTableDefaultPartitionKeyValue" é utilizado como a chave de partição. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores de coluna são utilizados como a chave de linha. Se não for especificado, utilize um GUID para cada linha. |Não |
| azureTableInsertType |O modo de inserir dados na tabela do Azure. Esta propriedade controla se as linhas existentes na tabela de saída com correspondentes chaves de partição e de linha têm seus valores substituído ou intercalado. <br/><br/>Valores permitidos são **intercalação** (predefinição) e **substituir**. <br/><br> Esta definição aplica-se ao nível da linha não é o nível de tabela. Nenhuma dessas opções elimina linhas da tabela de saída que não existem na entrada. Para saber mais sobre como funcionam as definições de intercalação e substituição, veja [entity Insert ou merge](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Não |
| writeBatchSize |Insere dados em tabelas do Azure quando writeBatchSize ou writeBatchTimeout for atingido.<br/>Valores permitidos são um número inteiro (número de linhas). |Não (a predefinição é 10 000) |
| writeBatchTimeout |Insere dados em tabelas do Azure quando writeBatchSize ou writeBatchTimeout for atingido.<br/>Valores permitidos são timespan. Um exemplo é "00: 20:00" (20 minutos). |Não (a predefinição é 90 segundos, o tempo limite do cliente de armazenamento predefinido) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Mapear uma coluna de origem a uma coluna de destino com o **"Microsoft translator"** propriedade antes de poder utilizar a coluna de destino como azureTablePartitionKeyName.

No exemplo seguinte, a coluna de origem DivisionID é mapeada para a coluna de destino DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" está especificada como a chave de partição.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapeamento do tipo de dados de tabelas do Azure

Quando copia dados de e para tabelas do Azure, os seguintes mapeamentos são utilizados entre tipos de dados de tabelas do Azure para tipos de dados intermediárias do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink, veja [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md).

Quando move dados de e para tabelas do Azure, o seguinte procedimento [mapeamentos definidos pela tabela do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) servem de tipos de OData de tabela do Azure para o tipo .NET e vice-versa.

| Tipo de dados de tabela do Azure | Tipo de dados intermediárias de fábrica de dados | Detalhes |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Uma matriz de bytes até 64 KB. |
| Edm.Boolean |booleano |Um valor booleano. |
| Edm.DateTime |DateTime |Um valor de 64 bits, expressado como Hora Universal Coordenada (UTC). O intervalo suportado de DateTime começa, 1 de Janeiro de 1601 a.d. (E.C.), UTC. O intervalo de termina a 31 de Dezembro, 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |Guid |Um identificador exclusivo global de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |String |Um valor codificado em UTF-16. Valores de cadeia de caracteres podem ser até 64 KB. |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
