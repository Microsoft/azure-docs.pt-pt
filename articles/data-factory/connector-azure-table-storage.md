---
title: Copiar dados de e para o armazenamento da Mesa Azure
description: Saiba como copiar dados de lojas de origem suportadas para armazenamento de mesa seletiva, ou do armazenamento de mesa para lojas de sumidouro suportadas, utilizando data factory.
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
ms.openlocfilehash: cf79a670db4e2729c6e0a5fb7112cdc6114f465a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460728"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Copiar dados de e para o armazenamento da tabela Azure utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-table-connector.md)
> * [Versão atual](connector-azure-table-storage.md)

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para o armazenamento da Tabela Azure. Baseia-se no artigo de visão geral da [Copy Activity](copy-activity-overview.md) que apresenta uma visão geral da Atividade de Cópia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de armazenamento de mesa Azure é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de qualquer loja de dados de origem suportada para armazenamento de tabela. Também pode copiar dados do armazenamento da Tabela para qualquer loja de dados de sumidouro suportado. Para obter uma lista de lojas de dados que sejam suportadas como fontes ou afunda-se pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Azure Table suporta a cópia de dados utilizando a chave de conta e autenticações de assinatura de acesso partilhado de serviço.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas ao armazenamento de tabelas.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

### <a name="use-an-account-key"></a>Use uma chave de conta

Pode criar um serviço ligado ao Armazenamento Azure utilizando a chave da conta. Fornece à fábrica de dados acesso global ao Armazenamento. As seguintes propriedades são suportadas.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para **AzureTableStorage**. |Sim |
| conexãoString | Especifique as informações necessárias para ligar ao Armazenamento para a propriedade de conexãoString. <br/>Também pode colocar a chave de conta `accountKey` no Cofre de Chaves Azure e retirar a configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo do Cofre chave Azure com mais detalhes. |Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

>[!NOTE]
>Se estava a utilizar o serviço ligado ao tipo "AzureStorage", ainda é suportado como está, enquanto é sugerido que utilize este novo tipo de serviço ligado ao "AzureTableStorage" a avançar.

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

**Exemplo: chave da conta da loja no Cofre chave Azure**

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

### <a name="use-shared-access-signature-authentication"></a>Utilizar autenticação de assinatura de acesso partilhado

Também pode criar um serviço ligado ao Armazenamento utilizando uma assinatura de acesso partilhado. Fornece à fábrica de dados um acesso restrito/limitado ao tempo a todos/recursos específicos no armazenamento.

Uma assinatura de acesso partilhado proporciona acesso delegado aos recursos na sua conta de armazenamento. Pode usá-lo para conceder permissões limitadas a objetos na sua conta de armazenamento por um tempo determinado e com um conjunto de permissões especificado. Não tens de partilhar as chaves de acesso à conta. A assinatura de acesso partilhado é um URI que engloba nos seus parâmetros de consulta toda a informação necessária para o acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com a assinatura de acesso partilhado, o cliente apenas precisa passar a assinatura de acesso partilhado ao construtor ou método apropriado. Para obter mais informações sobre assinaturas de acesso partilhado, consulte [assinaturas de acesso partilhado: Compreenda o modelo de assinatura](../storage/common/storage-dotnet-shared-access-signature-part-1.md)de acesso partilhado .

> [!NOTE]
> Data Factory agora suporta tanto **assinaturas** de acesso partilhado de serviço como **assinaturas de acesso partilhado**de conta. Para obter mais informações sobre assinaturas de acesso partilhado, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md). 

> [!TIP]
> Para gerar uma assinatura de acesso partilhado de serviço para a sua conta de armazenamento, pode executar os seguintes comandos PowerShell. Substitua os espaços reservados e conceda a permissão necessária.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para utilizar a autenticação de assinatura de acesso partilhado, são suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para **AzureTableStorage**. |Sim |
| sasUri | Especifique a SAS URI da assinatura de acesso partilhado URI à mesa. <br/>Marque este campo como um SecureString para o armazenar de forma segura na Data Factory. Também pode colocar token SAS no Cofre de Chaves Azure para alavancar a rotação automática e remover a porção simbólica. Consulte as seguintes amostras e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo do Cofre chave Azure com mais detalhes. | Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Integração Auto-Hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

>[!NOTE]
>Se estava a utilizar o serviço ligado ao tipo "AzureStorage", ainda é suportado como está, enquanto é sugerido que utilize este novo tipo de serviço ligado ao "AzureTableStorage" a avançar.

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

**Exemplo: chave da conta da loja no Cofre chave Azure**

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

Quando criar uma assinatura de acesso partilhado URI, considere os seguintes pontos:

- Detete permissões de leitura/escrita adequadas em objetos com base na forma como o serviço ligado (ler, escrever, ler/escrever) é utilizado na sua fábrica de dados.
- Definir o **tempo de validade** adequadamente. Certifique-se de que o acesso aos objetos de armazenamento não expira dentro do período ativo do gasoduto.
- O URI deve ser criado ao nível da mesa certa com base na necessidade.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados da Tabela Azure.

Para copiar dados de e para a Tabela Azure, detete a propriedade tipo do conjunto de dados para **AzureTable**. As seguintes propriedades são suportadas.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para **AzureTable**. |Sim |
| tableName |O nome da tabela na base de dados de armazenamento de mesa a que se refere o serviço ligado. |Sim |

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

Para lojas de dados sem esquemas, como a Tabela Azure, a Data Factory infere o esquema de uma das seguintes formas:

* Se especificar o mapeamento da coluna na atividade da cópia, a Data Factory utiliza a lista de colunas laterais de origem para recuperar dados. Neste caso, se uma linha não contiver um valor para uma coluna, é previsto um valor nulo para ela.
* Se não especificar o mapeamento da coluna na atividade da cópia, a Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha não contiver o esquema completo (por exemplo, algumas colunas têm valor nulo), algumas colunas são perdidas no resultado da operação de cópia.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia da Tabela Azure.

### <a name="azure-table-as-a-source-type"></a>Tabela Azure como tipo de fonte

Para copiar dados da Tabela Azure, delineie o tipo de origem na atividade de cópia para **o AzureTableSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **AzureTableSource**. |Sim |
| azureTableSourceQuery |Utilize a consulta de armazenamento de mesa personalizada para ler dados. Consulte exemplos na secção seguinte. |Não |
| azureTableSourceIgnoreTableNotFound |Indica se permite que a exceção da tabela não exista.<br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão). |Não |

### <a name="azuretablesourcequery-examples"></a>exemplos azureTableSourceQuery

>[!NOTE]
>A operação de consulta da tabela Azure tem tempo de funcionamento em 30 [segundos, conforme imposto pelo serviço De Mesa Azure](https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations). Aprenda a otimizar a consulta do [Design para consulta](../storage/tables/table-storage-design-for-query.md) de artigo.

Na Azure Data Factory, se pretender filtrar os dados numa coluna do tipo data, consulte este exemplo:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Se pretender filtrar os dados contra uma coluna do tipo de cadeia, consulte este exemplo:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Se utilizar o parâmetro do gasoduto, molde o valor da data para o formato adequado de acordo com as amostras anteriores.

### <a name="azure-table-as-a-sink-type"></a>Mesa Azure como um tipo de pia

Para copiar dados para a Tabela Azure, delineie o tipo de pia na atividade de cópia para **AzureTableSink**. As seguintes propriedades são suportadas na secção de **sumidouro** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para **AzureTableSink**. |Sim |
| azureTableDefaultPartitionKeyValue |O valor da chave da divisória padrão que pode ser usado pela pia. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como teclas de partição. Se não especificado, "AzureTableDefaultPartitionKeyValue" é usado como chave de partição. |Não |
| nome chave azureTableRow |Especifique o nome da coluna cujos valores de coluna são utilizados como chave de linha. Se não especificado, utilize um GUID para cada linha. |Não |
| azureTableInsertType |O modo de inserir dados na Tabela Azure. Esta propriedade controla se as linhas existentes na tabela de saída com divisória seletiva e chaves de linha correspondentes têm os seus valores substituídos ou fundidos. <br/><br/>Os valores permitidos são **fundidos** (predefinidos) e **substituem**. <br/><br> Esta definição aplica-se ao nível da linha e não ao nível da tabela. Nenhuma das opções elimina linhas na tabela de saída que não existam na entrada. Para saber como funcionam as definições de fusão e substituição, consulte [Inserir ou fundir entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e inserir ou substituir [entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Não |
| escreverBatchSize |Insere os dados na Tabela Azure quando escreveBatchSize ou escreveBatchTimeout é atingido.<br/>Os valores permitidos são inteiros (número de linhas). |Não (o padrão é de 10.000) |
| escreverBatchTimeout |Insere os dados na Tabela Azure quando escreveBatchSize ou escreveBatchTimeout é atingido.<br/>Os valores permitidos são tempo sem mais tempo. Um exemplo é "00:20:00" (20 minutos). |Não (padrão é de 90 segundos, tempo de paragem padrão do cliente de armazenamento) |

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

Mapeie uma coluna de origem para uma coluna de destino utilizando a propriedade **"tradutor"** antes de poder utilizar a coluna de destino como nome chave tórdia azul.

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

## <a name="data-type-mapping-for-azure-table"></a>Mapeamento de tipo de dados para tabela Azure

Quando copia dados de e para a Tabela Azure, os seguintes mapeamentos são utilizados desde os tipos de dados da Tabela Azure até aos tipos de dados provisórios da Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

Quando move dados de e para a Tabela Azure, os [seguintes mapeamentos definidos pela Tabela Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) são utilizados dos tipos OData de Tabela Azure para o tipo .NET e vice-versa.

| Tipo de dados da tabela azure | Data Factory tipo de dados provisórios | Detalhes |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Uma série de bytes até 64 KB. |
| Edm.Boolean |bool |Um valor booleano. |
| Edm.DateTime |DateTime |Um valor de 64 bits expresso como Tempo Universal Coordenado (UTC). A gama datetime suportada começa à meia-noite, 1 de janeiro de 1601 d.A. (C.E.), UTC. A gama termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |GUID |Um identificador globalmente único de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |Cadeia |Um valor codificado uTF-16. Os valores das cordas podem chegar a 64 KB. |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
