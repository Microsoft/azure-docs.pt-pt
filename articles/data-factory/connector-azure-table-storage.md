---
title: Copiar dados de e para o armazenamento da Tabela Azure
description: Saiba como copiar dados de lojas de origem suportadas para o armazenamento da Mesa Azure, ou do armazenamento de mesa para lojas de pias suportadas, utilizando a Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/20/2020
ms.openlocfilehash: b70c08df25f3f5d572f88879f5073756de588d52
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636481"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Copiar dados de/para o armazenamento de Tabelas do Azure com o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-table-connector.md)
> * [Versão atual](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia no Azure Data Factory para copiar dados de/para o armazenamento de Tabelas do Azure. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da Atividade de Cópia que apresenta uma visão geral da Atividade de Cópia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de armazenamento Azure Table é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de qualquer loja de dados de origem suportada para o armazenamento de mesa. Também pode copiar dados do armazenamento de mesa para qualquer loja de dados de pia suportada. Para obter uma lista de lojas de dados que são suportadas como fontes ou sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Azure Table suporta a cópia de dados utilizando a chave de conta e as autenticações de assinatura de acesso partilhado do serviço.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas para o armazenamento de mesa.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

### <a name="use-an-account-key"></a>Use uma chave de conta

Pode criar um serviço ligado ao Azure Storage utilizando a chave da conta. Fornece à fábrica de dados acesso global ao Armazenamento. As seguintes propriedades são suportadas.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida como **AzureTableStorage** . |Sim |
| conexãoStragem | Especifique as informações necessárias para ligar ao Armazenamento para a propriedade connectionString. <br/>Também pode colocar a chave de conta no Cofre da Chave Azure e retirar a `accountKey` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no artigo do Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |Não |

>[!NOTE]
>Se estava a utilizar o serviço de ligação tipo "AzureStorage", este ainda é suportado como está, enquanto é sugerido que utilize este novo tipo de serviço "AzureTableStorage" ligado.

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

**Exemplo: chave da conta da loja no Cofre da Chave Azure**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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

### <a name="use-shared-access-signature-authentication"></a>Utilize a autenticação de assinatura de acesso partilhado

Também pode criar um serviço ligado ao Armazenamento utilizando uma assinatura de acesso partilhado. Fornece à fábrica de dados um acesso restrito/limitado ao tempo a todos os recursos específicos do armazenamento.

Uma assinatura de acesso partilhado proporciona acesso delegado aos recursos na sua conta de armazenamento. Pode usá-lo para conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um tempo especificado e com um conjunto especificado de permissões. Não tens de partilhar as chaves de acesso à tua conta. A assinatura de acesso partilhado é um URI que engloba nos seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com a assinatura de acesso partilhado, o cliente apenas precisa de passar na assinatura de acesso partilhado ao construtor ou método apropriado. Para obter mais informações sobre assinaturas de acesso partilhado, consulte [assinaturas de acesso partilhado: Compreenda o modelo de assinatura de acesso partilhado.](../storage/common/storage-sas-overview.md)

> [!NOTE]
> A Data Factory suporta agora as **assinaturas de acesso partilhado** do serviço e **as assinaturas de acesso partilhado à conta.** Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Grant acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md). 

> [!TIP]
> Para gerar uma assinatura de acesso partilhado de serviço para a sua conta de armazenamento, pode executar os seguintes comandos PowerShell. Substitua os espaços reservados e conceda a permissão necessária.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para utilizar a autenticação de assinatura de acesso partilhado, as seguintes propriedades são suportadas.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida como **AzureTableStorage** . |Sim |
| SasUri | Especifique SAS URI da assinatura de acesso partilhado URI para a tabela. <br/>Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory. Também pode colocar o token SAS no Azure Key Vault para alavancar a rotação automática e remover a porção de token. Consulte as seguintes amostras e [guarde as credenciais no artigo do Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |Não |

>[!NOTE]
>Se estava a utilizar o serviço de ligação tipo "AzureStorage", este ainda é suportado como está, enquanto é sugerido que utilize este novo tipo de serviço "AzureTableStorage" ligado.

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

**Exemplo: chave da conta da loja no Cofre da Chave Azure**

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

Quando criar uma assinatura de acesso partilhado URI, considere os seguintes pontos:

- Descreva permissões de leitura/escrita apropriadas em objetos baseados na forma como o serviço ligado (ler, escrever, ler/escrever) é utilizado na sua fábrica de dados.
- Desavendar **o tempo de validade** adequadamente. Certifique-se de que o acesso aos objetos de armazenamento não expira dentro do período ativo do gasoduto.
- O URI deve ser criado ao nível da mesa certa com base na necessidade.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados da Tabela Azure.

Para copiar dados de e para a Tabela Azure, defina a propriedade tipo do conjunto de dados para **AzureTable** . As seguintes propriedades são suportadas.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida como **AzureTable** . |Sim |
| tableName |O nome da tabela na caixa de dados de armazenamento de tabela a que o serviço ligado se refere. |Sim |

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

### <a name="schema-by-data-factory"></a>Schema by Data Factory

Para lojas de dados isentas de esquemas, como a Tabela Azure, a Data Factory infere o esquema de uma das seguintes formas:

* Se especificar o mapeamento da coluna na atividade da cópia, a Data Factory utiliza a lista de colunas laterais de origem para obter dados. Neste caso, se uma linha não contiver um valor para uma coluna, é fornecido um valor nulo para ela.
* Se não especificar o mapeamento da coluna na atividade da cópia, a Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha não contiver o esquema completo (por exemplo, algumas colunas têm valor nulo), algumas colunas são perdidas no resultado da operação da cópia.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia da Tabela Azure.

### <a name="azure-table-as-a-source-type"></a>Tabela Azure como tipo de fonte

Para copiar os dados da Tabela Azure, desapedaça o tipo de origem na atividade de cópia para **AzureTableSource** . As seguintes propriedades são suportadas na secção **de origem** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para **AzureTableSource** . |Sim |
| azureTableSourceQuery |Utilize a consulta de armazenamento de mesa personalizada para ler dados.<br/>A consulta de origem é um mapa direto da `$filter` opção de consulta suportada pelo Azure Table Storage, saiba mais sobre a sintaxe [deste doc](/rest/api/storageservices/querying-tables-and-entities#supported-query-options), e veja os exemplos na secção de [exemplos azureTableSourceQuery](#azuretablesourcequery-examples). |Não |
| azureTableSourceIgnoreTableNotFound |Indica se permite que a exceção da tabela não exista.<br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão). |Não |

### <a name="azuretablesourcequery-examples"></a>exemplos azureTableSourceQuery

>[!NOTE]
>A azure Table consulta tempos fora em 30 segundos como [imposto pelo serviço Azure Table](/rest/api/storageservices/setting-timeouts-for-table-service-operations). Saiba como otimizar a consulta do [Design para consulta de](../storage/tables/table-storage-design-for-query.md) artigo.

Na Azure Data Factory, se pretender filtrar os dados contra uma coluna do tipo data, consulte este exemplo:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Se pretender filtrar os dados contra uma coluna do tipo de corda, consulte este exemplo:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Se utilizar o parâmetro do gasoduto, lance o valor da data para o formato adequado de acordo com as amostras anteriores.

### <a name="azure-table-as-a-sink-type"></a>Azure Table como um tipo de pia

Para copiar os dados para a Tabela Azure, desaperte o tipo de pia na atividade da cópia para **AzureTableSink** . As seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do lavatório de atividade de cópia deve ser definida para **AzureTableSink** . |Sim |
| azureTableDefaultPartitionKeyValue |O valor da chave de partição padrão que pode ser usado pela pia. |Não |
| azureTablePartitionKeyName |Especificar o nome da coluna cujos valores são utilizados como teclas de partição. Se não for especificado, "AzureTableDefaultPartitionKeyValue" é utilizado como chave de partição. |Não |
| nome azureTableRowKey |Especificar o nome da coluna cujos valores de coluna são utilizados como tecla de linha. Se não for especificado, utilize um GUID para cada linha. |Não |
| azureTableInsertType |O modo de inserir dados na Tabela Azure. Esta propriedade controla se as linhas existentes na tabela de saída com chaves de divisórias e linha correspondentes têm os seus valores substituídos ou fundidos. <br/><br/>Os valores permitidos são **a fusão** (padrão) e **a substituição.** <br/><br> Esta definição aplica-se ao nível da linha e não ao nível da tabela. Nenhuma das opções elimina linhas na tabela de saída que não existem na entrada. Para saber como funcionam as definições de fusão e substituição, consulte [insira ou funda a entidade](/rest/api/storageservices/Insert-Or-Merge-Entity) e [insira ou substitua a entidade.](/rest/api/storageservices/Insert-Or-Replace-Entity) |Não |
| escreverBatchSize |Insere dados na Tabela Azure quando escreverBatchSize ou escreverBatchTimeout é atingido.<br/>Os valores permitidos são inteiros (número de linhas). |Não (o padrão é 10.000) |
| escreverBatchTimeout |Insere dados na Tabela Azure quando escreverBatchSize ou escreverBatchTimeout é atingido.<br/>Os valores permitidos são tempo. Um exemplo é "00:20:00" (20 minutos). |Não (o padrão é de 90 segundos, o tempo limite de tempo do cliente de armazenamento) |

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

Mapear uma coluna de origem para uma coluna de destino utilizando a propriedade **"tradutor"** antes de poder utilizar a coluna de destino como azureTablePartitionKeyName.

No exemplo seguinte, a coluna de origem DivisionID é mapeada para a coluna de destino DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" é especificado como a chave de partição.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapeamento do tipo de dados para a tabela Azure

Quando copia dados de e para Azure Table, os seguintes mapeamentos são usados desde os tipos de dados da Tabela Azure até aos tipos de dados provisórios da Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

Quando move dados de e para a Tabela Azure, os [seguintes mapeamentos definidos pela Tabela Azure](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) são utilizados a partir dos tipos OData da Tabela Azure para .TIPO NET e vice-versa.

| Tipo de dados da tabela Azure | Tipo de dados provisórios da Data Factory | Detalhes |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Uma variedade de bytes até 64 KB. |
| Edm.Boolean |bool |Um valor booleano. |
| Edm.DateTime |DateTime |Um valor de 64 bits expresso como Tempo Universal Coordenado (UTC). A gama de DataTime suportada começa à meia-noite, 1 de janeiro de 1601 d.C. UTC. O alcance termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |GUID |Um identificador globalmente único de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |String |Um valor codificado UTF-16. Os valores das cordas podem chegar aos 64 KB. |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)