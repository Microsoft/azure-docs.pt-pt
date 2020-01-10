---
title: Copiar dados de e para o armazenamento de tabelas do Azure
description: Saiba como copiar dados de armazenamentos de origem com suporte para o armazenamento de tabelas do Azure ou do armazenamento de tabelas para repositórios de coletor com suporte, usando Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: b64b0f32b7e8d94115facf43646a5a030697d80f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444420"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Copiar dados de e para o armazenamento de tabelas do Azure usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-table-connector.md)
> * [Versão atual](connector-azure-table-storage.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o armazenamento de tabelas do Azure. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de armazenamento de tabela do Azure tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o armazenamento de tabela. Você também pode copiar dados do armazenamento de tabela para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector de tabela do Azure dá suporte à cópia de dados usando autenticações de assinatura de chave de conta e de acesso compartilhado de serviço.

## <a name="get-started"></a>Começar

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao armazenamento de tabelas.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

### <a name="use-an-account-key"></a>Usar uma chave de conta

Você pode criar um serviço vinculado do armazenamento do Azure usando a chave de conta. Ele fornece o data factory com acesso global ao armazenamento. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **AzureTableStorage**. |Sim |
| connectionString | Especifique as informações necessárias para ligar ao armazenamento para a propriedade connectionString. <br/>Você também pode colocar a chave de conta em Azure Key Vault e extrair a configuração de `accountKey` da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Você pode usar os Integration Runtime Azure Integration Runtime ou auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Se você estivesse usando o serviço vinculado de tipo "AzureStorage", ele ainda tem suporte como está, enquanto você é sugerido para usar esse novo tipo de serviço vinculado "AzureTableStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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

**Exemplo: armazenar chave de conta no Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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

### <a name="use-shared-access-signature-authentication"></a>Usar autenticação de assinatura de acesso compartilhado

Você também pode criar um serviço vinculado de armazenamento usando uma assinatura de acesso compartilhado. Ele fornece o data factory com acesso restrito/com limite de tempo a todos/recursos específicos no armazenamento.

Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Você pode usá-lo para conceder a um cliente permissões limitadas a objetos em sua conta de armazenamento por um período especificado e com um conjunto especificado de permissões. Não tem de partilhar as chaves de acesso da conta. A assinatura de acesso partilhado é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a assinatura de acesso partilhado, o cliente precisa apenas passar a assinatura de acesso partilhado para o método ou construtor apropriado. Para obter mais informações sobre assinaturas de acesso partilhado, consulte [assinaturas de acesso partilhado: compreender o modelo de assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> O Data Factory suporta agora ambos **serviço de assinaturas de acesso partilhado** e **assinaturas de acesso partilhado de conta**. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-sas-overview.md). 

> [!TIP]
> Para gerar uma assinatura de acesso partilhado do serviço para a sua conta de armazenamento, pode executar os seguintes comandos do PowerShell. Substitua os marcadores de posição e conceder a permissão necessária.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para usar a autenticação de assinatura de acesso compartilhado, há suporte para as propriedades a seguir.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **AzureTableStorage**. |Sim |
| sasUri | Especifique o URI de SAS do URI de assinatura de acesso compartilhado para a tabela. <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar o token SAS em Azure Key Vault para aproveitar a rotação automática e remover a parte do token. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode usar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Se você estivesse usando o serviço vinculado de tipo "AzureStorage", ele ainda tem suporte como está, enquanto você é sugerido para usar esse novo tipo de serviço vinculado "AzureTableStorage" no futuro.

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

**Exemplo: armazenar chave de conta no Azure Key Vault**

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
- O URI deve ser criado no nível de tabela correto com base na necessidade.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de banco de uma tabela do Azure.

Para copiar dados de e para a tabela do Azure, defina a propriedade Type do DataSet como **azuretable**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como **azuretable**. |Sim |
| tableName |O nome da tabela na instância de banco de dados de armazenamento de tabela à qual o serviço vinculado se refere. |Sim |

**Exemplo:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema pelo Data Factory

Para armazenamentos de dados sem esquema, como a tabela do Azure, Data Factory infere o esquema de uma das seguintes maneiras:

* Se você especificar o mapeamento de coluna na atividade de cópia, Data Factory usar a lista de colunas do lado de origem para recuperar dados. Nesse caso, se uma linha não contiver um valor para uma coluna, um valor nulo será fornecido para ele.
* Se você não especificar o mapeamento de coluna na atividade de cópia, Data Factory inferirá o esquema usando a primeira linha nos dados. Nesse caso, se a primeira linha não contiver o esquema completo (por exemplo, algumas colunas têm um valor nulo), algumas colunas serão perdidas no resultado da operação de cópia.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor da tabela do Azure.

### <a name="azure-table-as-a-source-type"></a>Tabela do Azure como um tipo de origem

Para copiar dados da tabela do Azure, defina o tipo de fonte na atividade de cópia como **AzureTableSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como **AzureTableSource**. |Sim |
| azureTableSourceQuery |Use a consulta de armazenamento de tabela personalizada para ler os dados. Consulte os exemplos na seção a seguir. |Não |
| azureTableSourceIgnoreTableNotFound |Indica se deve permitir que a exceção da tabela não exista.<br/>Valores permitidos são **True** e **falso** (predefinição). |Não |

### <a name="azuretablesourcequery-examples"></a>exemplos de azureTableSourceQuery

Se a coluna da tabela do Azure for do tipo DateTime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Se a coluna da tabela do Azure for do tipo cadeia de caracteres:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Se você usar o parâmetro pipeline, converta o valor DateTime para o formato adequado de acordo com os exemplos anteriores.

### <a name="azure-table-as-a-sink-type"></a>Tabela do Azure como um tipo de coletor

Para copiar dados para a tabela do Azure, defina o tipo de coletor na atividade de cópia como **AzureTableSink**. As propriedades a seguir têm suporte na seção **coletor** de atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do coletor da atividade de cópia deve ser definida como **AzureTableSink**. |Sim |
| azureTableDefaultPartitionKeyValue |O valor da chave de partição padrão que pode ser usado pelo coletor. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são usados como chaves de partição. Se não for especificado, "AzureTableDefaultPartitionKeyValue" será usado como a chave de partição. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores de coluna são usados como a chave de linha. Se não for especificado, use um GUID para cada linha. |Não |
| azureTableInsertType |O modo para inserir dados na tabela do Azure. Essa propriedade controla se as linhas existentes na tabela de saída com as chaves de partição e de linha correspondentes têm seus valores substituídos ou mesclados. <br/><br/>Os valores permitidos são **Merge** (padrão) e **replace**. <br/><br> Essa configuração se aplica ao nível de linha, não ao nível de tabela. Nenhuma opção exclui linhas na tabela de saída que não existem na entrada. Para saber mais sobre como as configurações de mesclagem e substituição funcionam, consulte [Inserir ou mesclar entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Não |
| writeBatchSize |Insere dados na tabela do Azure quando writeBatchSize ou writeBatchTimeout é atingido.<br/>Os valores permitidos são inteiro (número de linhas). |Não (o padrão é 10.000) |
| writeBatchTimeout |Insere dados na tabela do Azure quando writeBatchSize ou writeBatchTimeout é atingido.<br/>Os valores permitidos são TimeSpan. Um exemplo é "00:20:00" (20 minutos). |Não (o padrão é 90 segundos, tempo limite padrão do cliente de armazenamento) |

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

Mapeie uma coluna de origem para uma coluna de destino usando a propriedade **"Translator"** antes de poder usar a coluna de destino como azureTablePartitionKeyName.

No exemplo a seguir, a coluna de origem divisionid é mapeada para a coluna de destino divisionid:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"Divisionid" é especificado como a chave de partição.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapeamento de tipo de dados para a tabela do Azure

Quando você copia dados de e para a tabela do Azure, os seguintes mapeamentos são usados de tipos de dados de tabela do Azure para Data Factory tipos de dados provisórios. Para saber mais sobre como a atividade de cópia mapeia o esquema de origem e o tipo de dados para o coletor, consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

Quando você move dados de e para a tabela do Azure, os seguintes [mapeamentos definidos pela tabela do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) são usados dos tipos OData de tabela do Azure para o tipo .net e vice-versa.

| Tipo de dados de tabela do Azure | Tipo de dados intermediárias de fábrica de dados | Detalhes |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Uma matriz de bytes de até 64 KB. |
| Edm.Boolean |booleano |Um valor booleano. |
| Edm.DateTime |DateTime |Um valor de 64 bits expresso como UTC (tempo Universal Coordenado). O intervalo de DateTime com suporte começa com meia-noite, 1º de janeiro de 1601 D.C. (C.E.), UTC. O intervalo termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |GUID |Um identificador global exclusivo de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |String |Um valor codificado em UTF-16. Os valores de cadeia de caracteres podem ser de até 64 KB. |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
